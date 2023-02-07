# iOS-notes

## Tiny Tips

### How to use @available to deprecate old APIs

https://www.hackingwithswift.com/example-code/language/how-to-use-available-to-deprecate-old-apis

### 在闭包中强引用 self 的两种方法

```Swift
// Proposal 1
class MyClass {
  prepareVideo {
    self.movePlayhead(to: self.video.lastPlayheadPosition)
    self.startPlaybackIfNeeded()
  }
}

// Proposal 2
class MyClass {
  prepareVideo { [self] in
    movePlayhead(to: video.lastPlayheadPosition)
    startPlaybackIfNeeded()
  }
}
```

### SwiftUI 元素刷新

* SwiftUI View 内部的属性只能通过在 View 内部修改的方式更新 UI

```Swift
struct MyView: View {
  @State var text: String = ""
  
  var body: some View {
    Text(text)
      .onTapGesture {
        // 可以更新
        self.text = "Tapping..."
      }
  }
}

struct XXApp: App {
  var body: some Scene {
    WindowGroup <MyView> {
      let view = MyView()
      DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
        // 无法更新
        view.text = "Update text"
      }
      return view
    }
  }
}

```

如果需要实现在外部修改 SwiftUI View 的元素，需要创建一个 ObservedObject，将其某个属性设置为 Published，再修改这个属性

```Swift
struct MyView: View {
  @ObservedObject var viewModel: MyViewModel
  var body: some View {
    Text(viewModel.text)
  }
}

class MyViewModel: ObservableObject {
  @Published var text: String = ""
}

struct XXApp: App {
  var viewModel: MyViewModel = .init()
  var body: some Scene {
    WindowGroup <MyView> {
      let view = MyView(viewModel: viewModel)
      DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
        // 可以更新
        viewModel.text = "Update text"
      }
      return view
    }
  }

```

## UICollectionView drag and drop

* 主要流程：https://www.jianshu.com/p/db55bd5f5aeb
* 完善放置流程：https://medium.com/hackernoon/how-to-drag-drop-uicollectionview-cells-by-utilizing-dropdelegate-and-dragdelegate-6e3512327202
* UIDragSession 判断拖拽位置：https://www.jianshu.com/p/2cd8e4a6aa57 

## @discardableResult

[@discardableResult in Swift explained: Ignoring return values](https://www.avanderlee.com/swift/discardableresult/)

# 更新记录

时间|内容
---|---
20200823|新增[SwiftUI 中的叠放、网格和大纲(WWDC2020)](https://github.com/PatShen/iOS-notes/blob/main/SwiftUI%20中的叠放、网格和大纲(WWDC2020).md)
20220822|新增[SwiftUI 新功能(WWDC 2020)](https://github.com/PatShen/iOS-notes/blob/main/SwiftUI%20新功能(WWDC%202020).md)
20220816|新增[自定义 Operation 实例](https://github.com/PatShen/iOS-notes/blob/main/自定义%20Operation%20实例.md)
20220714|新增[Swift协议](https://github.com/PatShen/iOS-notes/blob/main/Swift协议.md)
20220706|新增[SwiftUI的FixedSize](https://github.com/PatShen/iOS-notes/blob/main/SwiftUI%20的%20FixedSize.md)
20220530|新增[SwiftUI无法预览](https://github.com/PatShen/iOS-notes/blob/main/SwiftUI无法预览.md)
20220329|新增[SPM加速](https://github.com/PatShen/iOS-notes/blob/main/SPM加速.md)
20220223|新增[SSH连接Git超时](https://github.com/PatShen/iOS-notes/blob/main/SSH连接Git超时.md)
20210905|新增[Swift自定义命名空间](https://github.com/PatShen/iOS-notes/blob/main/Swift自定义命名空间.md)
20210818|新增[UIView](https://github.com/PatShen/iOS-notes/blob/main/UIView.md)
20210818|新增[App启动速度优化（占位）](https://github.com/PatShen/iOS-notes/blob/main/App启动速度优化.md)
20210818|新增[Bug和修复方法](https://github.com/PatShen/iOS-notes/blob/main/Bug和修复方法.md)
20210818|新增[备忘录](https://github.com/PatShen/iOS-notes/blob/main/备忘录.md)
