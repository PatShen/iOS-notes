> WWDC21 AsyncSequence

# 可以干什么

* 从 URL 中读取行。

```Swift
let url = URL(fileURLWithPaths: "/tmp/somefile.txt")
for try await line in url.lines {
  
}
```
> 可以是本地文件，也可以是网络。

* 使用URLSession异步地读取数据

```Swift
func bytes(from: URL) async throws -> (AsyncBytes, URLResponse)
func bytes(from URLRequest) async throws -> (AsyncBytes, URLResponse)
```
> 更多信息可以查看座谈 `《通过URLSession使用async/await》Use async/await with URLSession`

# 可以在哪使用 AsyncSequence

## 系统
**Manipulating Asynchronous Sequences** / map / allSatisfy / max(by:) / prefix / joined / max / dropFirst / flatMap / zip / compactMap / prefix(while:) / min/ reduce / min(by:) / filter / contains

# 自定义

* Calblacks
* Some Delegates

# AsyncStream 

> 将现有代码调整为 AsyncSequence 的良好实践

* 实例

```Swift
// Example of a common hanlder pattern
class QuakeMonitor {
    var quakeHandler: (Quake) -> Void
    func startMonitoring()
    func stopMonitoring()
}
```
```Swift
// Handlers can often times be greate candidates for AsyncSequence
let monitor = QuakeMonitor()
monitor.quakeHandler = { quake in
  // ...
}
monitor.startMonitoring()
// ...
monitor.stopMonitoring()
```
```Swift
// Use AsyncStream to adopt existing callbacks or handlers to AsyncSequence
// 使用 AsyncStream 改造后
let quakes = AsyncStream(Quake.self) { continuation in
    let monitor = QuakeMonitor()
    monitor.quakeHandler = { quake in
        continuation.yield(quake)
    }
    continuation.onTermination = { @Sendable _ in
        monitor.stopMonitoring()
    }
    monitor.startMonitoring()
}

// Using an AsyncStream
// 其他用法
let significantQuakes = quakes.filter { quake in
    quake.magnitude > 3
}

for await quake in significantQuakes {
    ...
}
```

## 作用

* AsyncSequence created with callbacks or delegates
* Handles buffering 
* handles cancellation

# WWDC21 进阶

* Use async/await with URLSession （使用带有 URLSession 的 async/await)
* Meet async/await in Swift (认识Swift中的 async/await)
* Explore structured concurrency in Swift（探索 Swift 中的结构化并发）


# 参考链接
* https://developer.apple.com/wwdc21/10058
