# iOS-notes

学习/工作过程中遇到的知识点

## UICollectionView scroll to item at indexPath 失效

### 现象

当 UICollectionView 调用方法：

```Swift
collectionView.scrollToItem(at: indexPath, at: .centeredHorizontally, animated: true)
```

发现不起作用。经过检查：

* `indexPath` 合法，它的值在 collectionView 的数据范围之内
* 是在主线程调用的方法
* collectionView.isPagingEnabled = true：在 iOS14 中，如果这个属性是 true，无法触发相应的事件。[具体说明](https://developer.apple.com/forums/thread/663156)


### 解决办法

```Swift

// 暂时关闭翻页
collectionView.isPagingEnabled = false

collectionView.scrollToItem(at: indexPath, at: .centeredHorizontally, animated: true)

// 还原翻页
collectionView.isPagingEnabled = true

```

> 可以通过方法交换，无感知修复这个问题。

## OC 和 Swift 中判断是否引入了 framework/module 的方法

老是记不住，记下来

OC：`#if __has_include(xxx)`

```objc
#if __has_include(<SomeModule/SomeModule.h>)
#import <SomeModule/SomeModule.h>
#define __HAS_SOME_MODULE_FRAMEWORK__
#endif
```

```objc
/// 使用 api 时
- (void)doSomething {
    #ifdef __HAS_SOME_MODULE_FRAMEWORK__
    // with  SomeModule framework
    #else
    // without  SomeModule framework
    #endif
}
```

Swift：`#if canImport(xxx)`

```swift
#if canImport(SomeModule)
@import SomeModule;
#endif
```

[参考链接](https://stackoverflow.com/questions/52605302/swifts-canimport-analogue-in-objective-c)
