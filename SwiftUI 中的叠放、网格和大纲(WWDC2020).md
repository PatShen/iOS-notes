> iOS 14 & macOS Big Sur

# Stacks

实现懒加载
* LazyVStack
* LazyHStack

> 限制：只能针对一个方向的所有 stack 子视图，无法控制子视图中的其他 stack 及其子视图不进行懒加载。例如 TableViewCell 中的横向、相对固定的布局。

# Grids

* LazyVGrid
* LazyHGrid

自适应 GridItem
```Swift
GridItem(.adaptive(minimun: 300), spacing: 0)
```

# Lists

* 始终延迟加载
* children, 实现无限向下分组(类似QQ好友列表)

# Outlines

* OutlineGroup
  * 有点类似 foreach
  * 不会对扁平集合进行遍历，而对树结构进行遍历
* DisclosureGroup 
  * 默认收起
  * 可以通过绑定属性控制默认状态

# 更多

* App Essentials in SwiftUI
* Data Essentials in SwiftUI
