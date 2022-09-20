# 环境
* MacBook M1 Pro(Apple Silicon)
* MacOS 12.3.1
* Xcode 13.3 (13E113)

# 报错
<img width="571" alt="Screen Shot 2022-05-11 at 16 12 04" src="https://user-images.githubusercontent.com/3346944/170968359-fe52d180-4c59-4e24-94dc-5162f38ea427.png">

# 原因
* 部分第三方 SDK 未适配 M1 的`模拟器`。例如 `GoogleWebRTC`。

# 解决办法
* 设置 Xcode 的打开模式为 `Rosetta`
* 如果工程使用了 `CocoaPods` 管理第三方库，`可能`需要添加以下代码（这一步非必需）：
```Ruby
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
    config.build_settings["VALID_ARCHS"] = "arm64 arm64e x86_64" # Key code. The content is based on the architectures in your project.
    end
  end
end
```

# 参考资料

* https://stackoverflow.com/a/66094347
* https://github.com/firebase/firebase-ios-sdk/issues/6520
