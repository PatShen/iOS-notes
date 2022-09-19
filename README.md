# iOS-notes

## Tiny Tips

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

## UICollectionView drag and drop

* 主要流程：https://www.jianshu.com/p/db55bd5f5aeb
* 完善放置流程：https://medium.com/hackernoon/how-to-drag-drop-uicollectionview-cells-by-utilizing-dropdelegate-and-dragdelegate-6e3512327202
* UIDragSession 判断拖拽位置：https://www.jianshu.com/p/2cd8e4a6aa57 


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
20210818|新增[App启动速度优化（占位）](https://github.com/PatShen/iOS-notes/blob/main/App启动速度优化)
20210818|新增[Bug和修复方法](https://github.com/PatShen/iOS-notes/blob/main/Bug和修复方法)
20210818|新增[备忘录](https://github.com/PatShen/iOS-notes/blob/main/备忘录.md)
