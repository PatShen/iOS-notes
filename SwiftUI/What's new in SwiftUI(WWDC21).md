> SwiftUI 中的新功能（WWDC21）

# 进展

* Release 1: 诞生（1.0, WWDC19）
* Release 2: 进阶（2.0, WWDC20），支持 100% 的 SwiftUI 应用
* Release 3: 深度整合

# Agenda
* Better lists & guids
* Beyond lists
* Advanced graphics
* Text and keyboard
* More buttons

# Better lists

## SwiftUI 开始内置支持异步加载图像

* AsyncImage

```Swift
struct ContentView: View {
  @StateObject private var photoStore = PhotoStore()

  var body: some View {
    NavigationView {
      ScrollView {
        LazyVGrid(columns: [GridItem(.adaptive(minimum: 420))]) {
          ForEach(photoStore.photos) { photo in
            AsyncImage(url: photo.url) { image in
              image
                .resizable()
                .aspectRatio(contentMode: .fill)
            } placeholder: {
              randomPlaceholderColor()
                .opacity(0.2)
            }
            .frame(width: 400, height: 266)
            .mask(RoundedRectangle(cornerRadius: 16))
          }
        }
        .padding()
      }
      .navigationTitle("Superhero Recruits")
    }
    .navigationViewStyle(.stack)
  }
}
```

## 可刷新标记

```Swift
List {
  // ...
}
.refreshable {
  // refresh function
  // support await
}
```

## 任务

* 可让你将异步任务附加到视图的生命周期中
* 它会在视图第一次加载时启动，并在视图被移除时自动取消

```Swift
List {
  // ...
}
.task {
  // refresh function
  // 
}
```

## 在集合中访问单个元素的绑定

```Swift
struct DirectionsList: View {
  @Binding var directions: [Direction]
  
  var body: some View {
    List($directions) { $direction in 
      Label {
        TextField(
          "Instructions", text: $direction.text)
      } icon: {
        DirectionsIcon(direction)
      }
    }
  }
}
```

## List 分割线

```Swift
  .listRowSeparatorTint(Color)
  
  .listRowSeparator(.hidden)
```

## Swipe Actions with both edges

```Swift
ForEach($characters) { $characters in 
  CharacterProfile(character)
    .swipeActions (edge: .leading) {
      Button {
        withAnimation { character.isPinned.toggle() }
      } label: {
        if character.isPinned {
          Label("Unpin", systemImage: "pin.slash")
        } else {
          Label("Pin", systemImage: "pin")
        }
      }
      .tint(.yellow)
    }
    .swipeActions (edge: .trailling) {
      // ...
    }
}
```

# Beyond lists

## Sectioned fetch requests (分段请求)

```Swift
@SectionedFetchRequest(
  sectionIdentifier: \.isPinned,
  sortDescriptors: [
    SortDescriptor(\.isPinned, order: .reverse),
    SortDescriptor(\.lastModified)
  ],
  animation: .default)
private var characters: SectionedFetchResults<...>

List {
  ForEach(characters) { section in
    Section(section.id ? "Pinned" : "Heroes & Villains") {
      ForEach(section) { character in
        CharacterRowView(character)
      }
    }
  }
}
```

## Searchable() modifier

```Swift
  .searchable(text: $characters.filterText)
```

## Drag and drop

```Swift
  .preview() // 拖拽预览
  
  .importsItemProviders() // mac 程序直接导入 iPhone 拍照的图片
  
  .exportsItemProviders() // 导出到系统应用
```

# Advanced graphics

## SF Symbols

* 颜色
* 填充样式

## Canvas

## Canvas with TimelineView

# Text and keyboard

## Markdown Text

* 加粗
* 链接
* inline code
* 自定义语法

## Dynamic type

```Swift
var body: some View {
  Header("Today's Activities")
    .dynamicTypeSize(.large ... .extraExtraLarge)
}
```
> SwiftUI从一开始就支持动态类型，并且今年有一个新的API，来允许限制UI支持的类型大小范围，以免它变得太大或太小

## Text selection

> 控制是否允许用户可对不可编辑的内容进行`复制`或`共享`等功能

## Text formatting

* 格式化时间，日期等

```Swift
date.formated()
```
* 格式化列表

```Swift
people.map(\.nameComponents).formated(
  .list(memberStyle: .name(style: .short), type: .and)

// -> Matt and Taylor
```

## Text field formatting 

```Swift
struct ContentView: View {
    @State private var newAttendee = PersonNameComponents()

    var body: some View {
       TextField("New Person", value: $newAttendee,
          format: .name(style: .medium))
    }
}
```
> 允许你添加可编辑的格式化文本，具有与某些基础值的类型安全绑定，新的与会者字段会绑定到 PersonNameComponents 值，
> 并使用标准名称格式对其进行格式化，这负责解析输入，并生成结果人名

## Text field prompts and labels

## Text field submission

```Swift
// 键盘右下角操作按钮可以自定义了
submitLabel(.done)
```

## Keyboard toolbar

> 上一个/下一个（输入框）

## @FocusState

* 控制焦点
* 设置聚焦状态

> 类似 UIKit 中的 becomeFirstResponder / resignFirstResponder

# More Buttons

## 更多属性

```Swift
Button("Add") {
  // ...
}
.buttonStyle(.bordered) // 标注边框按钮
.controlSize(.small)
.controlProminence(.increased)
```
## Confirm dialogs

```Swift
confirmationDialog()
```

## Control group

# 进阶

* Discover concurrency in SwiftUI 
* Swift concurrency: Update a sample app
* Demystify SwiftUI (揭开SwiftUI的面纱）
* Craft search experiences in SwiftUI (运用SwiftUI中的搜索体验)
* Priciples of great widgets (优秀小组件的原则)（eg: 解锁后才显示详情等）

# 参考链接
* https://developer.apple.com/wwdc21/10018
