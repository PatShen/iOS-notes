# Xcode cloud 能做什么？

分析，测试，归档，分发

# 组件

Pull request workflow

## 集成

Xcode cloud 已深度集成到了 Xcode 中，在 `顶部菜单->Product->Xcode Cloud` 中可以进行创建和管理。

* Webhook: 在 App Store Connect 中连接 Webhook
* API: [xcode_cloud_workflows_and_builds](https://developer.apple.com/documentation/appstoreconnectapi/xcode_cloud_workflows_and_builds)

### 依赖

* 内置SPM
* 可以和第三方管理工具一起使用，如 Cocoapods 和 Carthage
  * 需要额外设置一些脚本
  * 文档：[making-dependencies-available-to-xcode-cloud](https://developer.apple.com/documentation/xcode/making-dependencies-available-to-xcode-cloud)

### 实例

* SwiftLint
  * 使用自定义脚本集成
  ```shell
  #!/bin/zsh
  
  # ci_post_clone.sh
  
  # Use Homebrew to install the SwiftLint tool
  brew install swiftlint
  
  # Run SwiftLint
  swiftlint $CI_WORKSPACE
  ```
* Restrict editing: 限制对工作流的编辑
  * 图形化交互
* Multiple start conditions

## 设置条件

* 类型
  * 分支变更
  * tag变更
  * schedule
* 分支
* 文件夹
* 自动取消

## 环境配置

* 可以使用多个 Xcode 或 MacOS 版本进行测试（包括beta）
* clean，是否删除上一次构建。进行增量编译，缩短持续集成的时间

## 创建动作（actions）
* Build
* Analyze
  * 分析代码变更的问题
  * 可以设置为非必要通过
* Test
  * 针对不同的测试阶段，执行不同的自定义测试流程
  * 可以在多种不同的模拟器下测试（包括新老版本）
* Archive
  * TestFlight
  * AppStore

## 推送动作（post-actions）

每个动作完成（成功或失败）都可以执行一个post动作发送结果
* 发送通知
* 使用 TestFlight 部署

### Notification

* 成功 / 失败
  * All events
  * Only fix / Only breaks
  * Don't notify
* 支持发送到 Slack / 邮件
  * Slack：先授权，成功后可选择想要发送到的 channel
  * 邮件：默认情况下所有用户都会收到通知邮件。可以在此添加不在用户范围内的邮箱地址。

### Testflight post-actions

#### Internal testing

* Add an archive action
* Set archive deployment to TestFlight(internal only)
* Add a TestFlight internal testing post-actions

#### External testing

* Start condition type: every change to a branch
* Single branch
* Select clean
* Set archive deployment to TestFlight and App Store

> Can be deployed to individual developers
> We can also use the `App Store Connect` website to mange them

## 推荐策略（recommended strategies）

### Release workflow

* Start condition: changes to 'release' branch
* Execute full set of tests
* Runs archive
* Deploys to external TestFlight group

### Overnight testing workflow
* Runs on a schedule
* Runs on many simulators, multiple platforms
* Notifies the QA team on failure
* No deplovment
