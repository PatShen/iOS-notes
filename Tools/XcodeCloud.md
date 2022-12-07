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
  * 减少工作流的数量，在不同条件下开启相同的工作流

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

# 高级功能

## 环境变量

* 切换 API 域名
* 支持加密

```
支持的环境变量

Environment Variables/ CI / CI_AD_HOC_SIGNED_APP_PATH /
CI_APP_STORE_SIGNED_APP_PATH / CI_ARCHIVE_PATH / CI_BRANCH /
CI_BUILD_ID / CI_BUILD_NUMBER / CI_BUNDLE_ID / CI_COMMIT / CI_DERIVED_DATA_PATH / CI_DEVELOPER_ID_SIGNED_APP_PATH/ CI_DEVELOPMENT_SIGNED_APP_PATH / CI_GIT_REF / CI_PRODUCT / CI_PRODUCT_ID / CI_PRODUCT_PLATFORM / CI_PROJECT_FILE_PATH /
CI_PULL_REQUEST_HTML_URL / CI_PULL_REQUEST_NUMBER /
CI_PULL_REQUEST_SOURCE_BRANCH / CI_PULL_REQUEST_SOURCE_COMMIT /
CI_PULL_REQUEST_SOURCE_REPO / CI_PULL_REQUEST_TARGET_BRANCH / CI_PULL_REQUEST_TARGET_COMMIT / CI_PULL_REQUEST_TARGET_REPO /
CI_RESULT_BUNDLE_PATH/ CI_TAG/ CI_TEAM_ID/ CI_TEST_DESTINATION_DEVICE_TYPE / CI_TEST_DESTINATION_RUNTIME/ CI_TEST_DESTINATION_UDID / CI_TEST_PLAN / CI_TEST_PRODUCTS_PATH / CI_START_CONDITION / CI_WORKFLOW / CI_WORKSPACE / CI_XCODE_PROJECT / CI_XCODE_SCHEME / CI_XCODEBUILD_ACTION / CI_XCODEBUILD_EXIT_CODE
```

## 自定义脚本

* Post-clone
* Pre-Xcodebuild
* Post-Xcodebuild

将自定义脚本，写入 `ci_post_clone.sh`，`ci_pre_xcodebuild.sh`或`ci_post_xcodebuid.sh` 并放到 `ci_scripts` 文件夹中即可。

为 Beta 版本替换应用图标

```shell
#!/bin/sh

#  ci_pre_xcodebuild.sh
#  Fruta
#
#  Made in Vancouver, Canada
#  
# 判断是来自于 PR， 且用于分发到 TestFlight 
if [[ -n $CI_PULL_REQUEST_NUMBER && $CI_XCODEBUILD_ACTION = 'archive' ]];
then
    echo "Setting Fruta Beta App Icon"
    APP_ICON_PATH=$CI_WORKSPACE/Shared/Assets.xcassets/AppIcon.appiconset
    
    # Remove existing App Icon
    rm -rf $APP_ICON_PATH
    
    # Replace with Fruta Beta App Icon
    mv "$CI_WORKSPACE/ci_scripts/AppIcon-Beta.appiconset" $APP_ICON_PATH
fi
```

## 使用额外存储库（私有sdk）

* 使用 SPM（Swift Packages Manager）
* AppStoreConnect 网页授权访问新的 git 仓库
* 支持其他所有的 git 仓库
* 支持其他所有的管理工具

## Webhook

* 需要提供一个可以接收 HTTP 请求的服务
* 可在 AWS Lambda 利用 Swift 实现

```Swift
// Handling the webhook from an AWS Lambda
Lambda.run { (context, request: APIGateway. V2.Request, callback: @escaping (Result<APIGateway. V2.Response, Error>) -> Void) in {
  // ...
  let webhook = jsonDecoder. decode (Webhook.self, Data(request .body.utf8))

  if webhook.workflow.content .name == "Release Workflow" &&
     webhook.buildDetails.build.state == .succeeded {
     twitterClient.post (content: """
     🚀 Build #\ (webhook.buildRun.number) of \ (webhook.app.name) is available for testing!
     Get it here: <Testflight public link>
      """
     )
  }
  return callback(. success (APIGateway. V2. Response (statusCode: .ok) ))
}

// Use Swift on AWS Lambda with Xcode (WWDC2020)
```
