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

