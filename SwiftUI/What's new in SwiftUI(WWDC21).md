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

# Lists

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

# More things (TBC...)

# 进阶

* Discover concurrency in SwiftUI 
* Swift concurrency: Update a sample app

# 参考链接
* https://developer.apple.com/wwdc21/10018
