SwiftUI 在图层布局的时候，每个 View 都会为它的子视图提供一个显示区域。默认的情况下，子视图会自动接受父视图提供的大小来展示。

也就是说，默认情况下，子视图的最大显示范围就是父视图的大小。但是在某些情况下，例如文本需要显示多行，此时如果父视图的大小不够，就无法将文字显示完整。

为了解决这个问题，我们可以用到 `fixedSize()` 函数。

这个函数的作用是让子视图不受父视图提供的区域限制，以自身最佳的显示区域来显示内容。如以下两张图所示：

![默认情况下文本的展示效果](https://docs-assets.developer.apple.com/published/ca930efefe15afab8a62157417aee574/SwiftUI-View-fixedSize-1@2x.png)

![设置 fixedSize 之后的显示效果](https://docs-assets.developer.apple.com/published/39fd04b5cd61b452f33e4b492d96759c/SwiftUI-View-fixedSize-2@2x.png)

在实际使用过程中，我们一般会在水平/垂直方向之一做限制，来实现内容的横向/纵向延伸。就是 `fixedSize(horizontal:vertical:)` 来控制其中一个方向是否需要继承父视图的范围。

参考文档：
* https://developer.apple.com/documentation/swiftui/link/fixedsize()
* https://swiftwithmajid.com/2020/04/29/the-magic-of-fixed-size-modifier-in-swiftui/
* https://developer.apple.com/documentation/swiftui/view/fixedsize(horizontal:vertical:)
