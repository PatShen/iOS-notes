# hitTest(_:with:) 和 point(inside:with:)

这两个方法都是用来处理 UIView 的点击事件的

```swift
// 选择适合的对象（UIView）处理点击事件
hitTest(_:with:)
```

```swift
// 判断触摸点是否在当前对象（UIView）上
point(inside:with:)
```

这两个方法是 `责任链模式` 在 iOS 平台的具体实践。

> 即事件被放到一个可以处理的对象队列（链条）中依次判断是否被执行，直到某个对象执行它为止，整个过程即结束。这个模式可以很好的处理不需要知道具体对象的情况下完成事件的处理，并得到相应的结果，符合面向对象编程的`最少知识原则`。

实际场景下，如果某个全屏 controller 的 view 中有若干子 view，用户点击了屏幕中的任意点，系统会`按照图层从上到下的顺序`依次调用各个对象的 `hitTest(_:with:)` 方法，直到找到符合条件的视图响应点击事件，再在这个函数里调用 `point(inside:with:)`方法，判断是否需要响应。

> 特别说明，有的文章里提到，如果 view 的 isHidden == true 或  isUserInterfaceEnabled == false 或  alpha <= 0.01，系统将忽略这个视图，**这种说法不太准确的**
>
> 如果你重写了 `hitTest(_:with:)`，不论视图对象以何种形式存在，系统都将调用它，而`point(inside:with:)` 方法要看有没有调用 `super.hitTest(point, with: event)`决定。


我们可以利用这个特性，实现让 view 响应在它显示范围之外的点击事件。我们可以实现一个 UIView 的子类，或是直接使用系统定义的 UIView 子类（UIControl、UIButton等），重写它的 `hitTest(_:with:)` 和 `point(inside:with:)` 方法。

> 如果没有调用 `super.hitTest(point, with: event)`，可以不重载 `point(inside:with:)`
>
> 我们可以通过 Runtime（Swift同样支持） 给扩展（Swift的Extension，OC的Category）加属性，来解决只需要处理个别对象的点击范围的情况。
