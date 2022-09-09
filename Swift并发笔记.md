
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

# 任务和任务组

> TBC...

# 参考

* [Concurrency-The Swift Programming Language](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
