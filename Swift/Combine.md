# Combine

```Swift
NotificationCenter.default.publisher(for: .customNotification)
  .compactMap { notification in
    notification.userinfo?["data"] as? Data
  }
  .decode(MyClass.self, JSONDecoder())
  .assertNoFailure
```

* 操作符

```
assertNoFailure
retry
catch
mapError
setFailureType
```

* Publisher，从错误回复

```Swift
NotificationCenter.default.publisher(for: .customNotification)
  .compactMap { notification in
    notification.userinfo?["data"] as? Data
  }
  .decode(MyClass.self, JSONDecoder())
  .catch {
    Just(MyClass.placeholder) // Never fail
  }
```

> 这个场景下，订阅也终止了

## flatMap

> 生成新的Publisher

> Note: 10:35
