
# 定义和调用异步函数

## 官方示例

```Swift
func listPhotos(inGallery name: String) async -> [String] {
  let result = // ... some asynchronous networking code ...
  return result
}
```
> 例子过于简单，看完后我的第一反应是，我无法想象应该如何重构老代码，或者用一种兼容的方式使用老代码。后续需要更多的例子来解决这个问题。

## await 关键字的使用注意事项

在使用 `await` 的过程中，如果涉及到对异步获取的数据做处理，尽量在其后封装一个函数，避免一些奇怪的 bug。

```Swift
let firstPhoto = await listPhotos(inGallery: "Summer Vacation")[0]
add(firstPhoto toGallery: "Road Trip")
// At this point, firstPhoto is temporarily in both galleries.
remove(firstPhoto fromGallery: "Summer Vacation")
```
因为在上面的 add 和 remove 方法执行完成时，firstPhoto 会暂时同时存在于两个相册中，为了避免在其之间做了其他事，最好将这两个函数的调用封装起来，这样也让人看起来更清晰：

```Swift
func move(_ photoName: String, from source: String, to destination: String) {
  add(photoName, to: destination)
  remove(photoName, from: source)
}
// ...
let firstPhoto = await listPhotos(inGallery: "Summer Vacation")[0]
move(firstPhoto, from: "Summer Vacation", to: "Road Trip")
```

> emmmm...

# 异步序列

## `for-await-in` 循环

就像如果我们需要在 `for-in` 循环里使用自定义类型，我们的自定义类型要遵循 [Sequence](https://developer.apple.com/documentation/swift/sequence) 协议一样，在 `for-await-in` 循环中，我们的自定义类型需要实现 [AsyncSequence](https://developer.apple.com/documentation/swift/asyncsequence) 协议。

## 并行调用异步函数

如果多个任务可以并行执行，可以在调用它们之前使用 `async` 修饰，再在使用它们的返回值时使用 `await`

```Swift
async let firstPhoto = downloadPhoto(named: photoNames[0])
async let secondPhoto = downloadPhoto(named: photoNames[1])
async let thirdPhoto = downloadPhoto(named: photoNames[2])

let photos = await [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```
* 使用 `await` 调用异步代码，，会按照顺序执行。
* 使用 `async let` 调用异步代码，它们会并行执行。
* 以上两种方式都允许其他代码在暂停时运行。
* 用 `await` 标记可能会暂停的地方

以上方法可以混用

# 任务和任务组（Task TaskGroup)

* 有点类似 Operation，Task 可以直接利用异步函数的语法，而 Operation 需要依靠子类或者闭包
* 任务按层次结构排列。任务组中的每个任务都有相同的父任务，每个任务都可以有子任务。由于任务和任务组之间的明确关系，这种方法被称为*结构化并发*。
* 非结构化并发：没有父任务
* 任务取消
  * 检查取消状态：Task.checkCancellation() 
  * 任务已经被取消 Task.checkCancellationError 
  * 检查 Task.isCancelled，用自己的代码处理取消
  * 手动取消：Task.cancel()

# Actor

* 和 Class 一样是引用类型
* 一次只允许一个任务访问其可变状态
* 可用于在任务之间共享一些信息

例如保证读取某个 Actor 对象的属性时是线程安全的

```Swift
actor TemperatureLogger {
    let label: String
    var measurements: [Int]
    private(set) var max: Int

    init(label: String, measurement: Int) {
        self.label = label
        self.measurements = [measurement]
        self.max = measurement
    }
}
```
内部才可修改 max 的值

``` Swift
let logger = TemperatureLogger(label: "Outdoors", measurement: 25)
print(await logger.max)
// Prints "25"
```
外部访问时，需要用 `await` 标记潜在的暂停点。

而在内部，可以直接修改，不需要使用 await

```Swift
extension TemperatureLogger {
    func update(with measurement: Int) {
        measurements.append(measurement)
        if measurement > max {
            max = measurement
        }
    }
}
```

> 提供封装良好的数据隔离，确保并发代码的安全。

# 可发送类型

可以从一个并发域共享到另一个并发域的类型。

> 这个类型看起来是结合 Actor 模型一起用的，目前暂时没怎么看懂，后续边用边学。

官方实例：
```Swift
struct TemperatureReading: Sendable {
    var measurement: Int
}

extension TemperatureLogger {
    func addReading(from reading: TemperatureReading) {
        measurements.append(reading.measurement)
    }
}

let logger = TemperatureLogger(label: "Tea kettle", measurement: 85)
let reading = TemperatureReading(measurement: 45)
await logger.addReading(from: reading)
```

## async & await

### 为什么要使用 async & await

背景

* 以往的业务代码，如果遇到耗时操作，例如加载网络图片，我们一般会采用闭包将数据回调回去。
* 在代码实现的过程中，我们需要针对每一步进行容错处理，每一处容错都是一个逻辑分支，都需要调用回调使之能回到主线程继续后续的逻辑。
* 这会带来两个问题：1.可能会有遗漏，某些情况下回不到主线程，程序看起来像是卡死了；2.调用回调的地方很多，形成所谓“回调地狱”；
* 即便我们在结果里添加了 Result type，虽然会更加安全，但代码看起来让人感觉很多、很臃肿；

```Swift
func fetchThumbnail(for id: String, completion: @escaping (Result<UIImage, Error>) -> Void) {
    let request = thumbnailURLRequest(for: id)
    let task = URLSession.shared.dataTask(with: request) { data, response, error in
        if let error = error {
            completion(.failure(error))
        } else if (response as? HTTPURLResponse)?.statusCode != 200 {
            completion(.failure(FetchError.badID))
        } else {
            guard let image = UIImage(data: data!) else {
                completion(.failure(FetchError.badImage))
                return
            }
            image.prepareThumbnail(of: CGSize(width: 40, height: 40)) { thumbnail in
                guard let thumbnail = thumbnail else {
                    completion(.failure(FetchError.badImage))
                    return
                }
                completion(.success(thumbnail))
            }
        }
    }
    task.resume()
}
```

async & await 能否改善这个现象？当然是可以的。

* async & await 将回调改为了返回值，这样以来所有情况下都必须返回，这个错误会在编译阶段就被发现，避免了遗漏，即便是 `guard let` 句式也是如此；
* async & await 的代码都是线性的，与闭包不同，多个句式前后关联的情况下，async & await 不会出现“阶梯式”代码；

```Swift
func fetchThumbnail(for id: String) async throws -> UIImage {
    let request = thumbnailURLRequest(for: id)  
    let (data, response) = try await URLSession.shared.data(for: request)
    guard (response as? HTTPURLResponse)?.statusCode == 200 else { throw FetchError.badID }
    let maybeImage = UIImage(data: data)
    guard let thumbnail = await maybeImage?.thumbnail else { throw FetchError.badImage }
    return thumbnail
}
```
## async/await facts

* `async` enables a function to suspend
* `await` makes when an async function may suspend execution
* Other work can happen during a suspension
* Once an awaited async call completes, execution resumes after the `await`

# 参考

* [Concurrency-The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
* [Swift 并发初步](https://onevcat.com/2021/07/swift-concurrency/)
