---
title: 快速入门 - 使用 Azure 通信服务将 Microsoft Teams 会议加入到 iOS 应用
description: 本快速入门将介绍如何使用适用于 iOS 的 Azure 通信服务 Teams 嵌入库。
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b960357782ef7bac6d5c3425dbf3f93aa5600640
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218112"
---
本快速入门介绍如何使用适用于 iOS 的 Azure 通信服务 Teams 嵌入库加入 Microsoft Teams 会议。

## <a name="sample-code"></a>代码示例

可以从 [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started) 下载示例应用。

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要具备以下先决条件：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 一部运行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及一个安装到密钥链的有效开发人员证书。
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- Azure 通信服务的[用户访问令牌](../../access-tokens.md)。
- 已为编译环境安装 [CocoaPods](https://cocoapods.org/)。

## <a name="setting-up"></a>设置

### <a name="creating-the-xcode-project"></a>创建 Xcode 项目

在 Xcode 中创建新的 iOS 项目，并选择“应用程序”模板。 我们将使用 UIKit 情节提要。 在此快速入门过程中，无需创建测试。 可以取消选中“包括测试”。

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="显示 Xcode 中“新建项目”模板的屏幕截图。":::

将项目命名为 `TeamsEmbedGettingStarted`。

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="显示 Xcode 中“新建项目”详细信息的屏幕截图。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>使用 CocoaPods 安装包和依赖项

1. 为应用程序创建 Podfile：

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunicationCommon', '1.0.0'
end

azure_libs = [
'AzureCommunicationCommon',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. 运行 `pod install`。
3. 使用 Xcode 打开生成的 `.xcworkspace`。

### <a name="request-access-to-the-microphone-camera-etc"></a>请求访问麦克风、相机等。

若要访问设备的硬件，请更新应用程序的信息属性列表。 将关联的值设置为将要包含在系统用于向用户请求访问权限的对话框中的 `string`。

右键单击项目树的 `Info.plist` 条目，然后选择“打开为” > “源代码” 。 将以下代码行添加到顶层 `<dict>` 节，然后保存文件。

```xml
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
<key>NSPhotoLibraryUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>添加 Teams 嵌入框架

1. 下载并解压缩 `Teams Embed iOS SDK Bundle`。
2. 在项目根中创建 `Frameworks` 文件夹。 例如： `\TeamsEmbedGettingStarted\Frameworks\`
3. 将下载的 `AzureMeetingUIClient.xcframework` 和 `TeamsAppSDK.xcframework` 以及发布捆绑包中提供的其他框架复制到上述文件夹。
4. 在“常规”选项卡下将框架添加到项目目标。使用 `Add Other` -> `Add Files...` 导航到框架文件并添加它们。 
5. 对于所有添加的框架，请选择 `Embed & Sign`。 

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="显示 Xcode 中所添加框架的屏幕截图。":::

5. 如果尚未添加，请将 `$(PROJECT_DIR)/Frameworks` 添加到项目目标生成设置选项卡下面的 `Framework Search Paths`。若要找到此设置，请将筛选器从 `basic` 更改为 `all`。 还可以使用右侧的搜索栏。

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="显示 Xcode 中的框架搜索路径的屏幕截图。":::

### <a name="turn-off-bitcode"></a>关闭 Bitcode

在 `Build Settings` 项目中，将 `Enable Bitcode` 选项设置为 `No`。 若要查找设置，则需要将筛选器从更改 `Basic` 为 `All`，也可以使用右侧的搜索栏。

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="显示 Xcode 中的 BitCode 选项的屏幕截图。":::


### <a name="turn-on-voice-over-ip-background-mode"></a>启用“IP 语音”后台模式。

选择应用程序目标，并单击“功能”选项卡。

单击顶部的 `+ Capabilities` 以打开 `Background Modes`，然后选择 `Background Modes`。

选中复选框内的 `Voice over IP`。

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="显示在 Xcode 中启用 VOIP 的屏幕截图。":::

### <a name="add-a-window-reference-to-appdelegate"></a>添加对 AppDelegate 的窗口引用

打开项目的 AppDelegate.swift 文件并添加 “window” 引用。

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>向 ViewController 添加按钮

在 ViewController.swift 的 `viewDidLoad` 调用中创建按钮。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
}
```

在 ViewController.swift 中为按钮创建出口。

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>设置应用框架

打开项目的 ViewController.swift 文件，将 `import` 声明添加到文件顶部以导入 `AzureCommunicationCommon library` 和 `MeetingUIClient`。 

```swift
import UIKit
import AzureCommunicationCommon
import AzureMeetingUIClient
```

将 `ViewController` 类的实现替换为一个简单的按钮，以允许用户加入会议。 在本快速入门中，我们会将业务逻辑附加到此按钮。

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>对象模型

以下类和接口会处理 Azure 通信服务 Teams 嵌入库的部分主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient 是 Teams 嵌入库的主要入口点。 |
| MeetingUIClientMeetingJoinOptions | MeetingUIClientMeetingJoinOptions 用于可配置的选项，例如显示名称。 |
| MeetingUIClientGroupCallJoinOptions | MeetingUIClientGroupCallJoinOptions 用于可配置的选项，例如显示名称。 |
| MeetingUIClientTeamsMeetingLinkLocator | MeetingUIClientTeamsMeetingLinkLocator 用于设置用于加入会议的会议 URL。 |
| MeetingUIClientGroupCallLocator | MeetingUIClientGroupCallLocator 用于设置要加入的组 ID。 |
| MeetingUIClientIconType | MeetingUIClientIconType 用于指定哪些图标可以替换为特定于应用的图标。 |
| MeetingUIClientCall | MeetingUIClientCall 描述调用，并提供 API 来控制调用。 |
| MeetingUIClientCallState | MeetingUIClientCallState 用于报告调用状态更改。 选项如下：`connecting`、`waitingInLobby`、`connected` 和 `ended`。 |
| MeetingUIClientAudioRoute | MeetingUIClientAudioRoute 用于本地音频路由，如 `Earpiece` 或 `SpeakerOn`。 |
| MeetingUIClientLayoutMode | MeetingUIClientLayoutMode 用于允许在调用 UI 模式下选择不同的布局模式。 |
| MeetingUIClientAvatarSize | MeetingUIClientAvatarSize 是表示由 MeetingUIClientCallIdentityProvider 请求的不同头像大小的枚举。 |
| MeetingUIClientCallDelegate | MeetingUIClientDelegate 用于接收事件，例如调用状态的更改。 |
| MeetingUIClientCallIdentityProviderDelegate | MeetingUIClientIdentityProviderDelegate 用于将用户详细信息映射到会议中的用户。 |
| MeetingUIClientCallUserEventDelegate | MeetingUIClientUserEventDelegate 在 UI 中提供有关用户操作的信息。 |
| MeetingUIClientCallRosterDelegate | MeetingUIClientCallRosterDelegate 提供有关调用名册的信息。 |

## <a name="create-and-authenticate-the-client"></a>创建客户端并对其进行身份验证

通过用户访问令牌初始化 `MeetingUIClient` 实例，以便我们能够加入会议。 将以下代码添加到 ViewController.swift 中的 `viewDidLoad` 调用：

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

将 `<USER_ACCESS_TOKEN>` 替换为资源的有效用户访问令牌。 如果还没有可用的令牌，请参阅[用户访问令牌](../../access-tokens.md)文档。

### <a name="setup-token-refreshing"></a>安装程序令牌刷新

创建 `fetchTokenAsync` 方法。 然后添加 `fetchToken` 逻辑以获取用户令牌。

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

将 `<USER_ACCESS_TOKEN>` 替换为资源的有效用户访问令牌。

## <a name="join-a-meeting"></a>加入会议

`join` 方法设置为在点击“加入会议”按钮时执行的操作。 更新实现，以便通过 `MeetingUIClient` 加入会议：

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
            }
        }
    })
}
```
注意，将 `<MEETING URL>` 替换为 Microsoft Teams 会议链接。

完成事件处理器将返回错误，以防操作失败，如果操作成功，则会返回 `MeetingUIClientCall`。 使用 `MeetingUIClientCall` 控制调用。 

### <a name="get-a-microsoft-teams-meeting-link"></a>获取 Microsoft Teams 会议链接

可以使用图形 API 来检索 Microsoft Teams 会议链接。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)中对此过程进行了详细介绍。
通信服务呼叫 SDK 接受完整的 Microsoft Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下方获取。你还可以从 Teams 会议邀请信息的“加入会议”URL 中获取所需的会议信息。

## <a name="run-the-code"></a>运行代码

可以通过选择“产品” > “运行”或使用 (&#8984;-R) 键盘快捷方式，在 iOS 模拟器上生成并运行应用。

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="快速入门应用的最终外观":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="加入会议后的最终外观和体验":::

> [!NOTE]
> 首次加入会议时，系统将提示你提供麦克风访问权限。 在生产应用程序中，应使用 `AVAudioSession` API [检查权限状态](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)，并在未授予权限时正常更新应用程序的行为。

## <a name="add-localization-support-based-on-your-app"></a>根据应用程序加本地化支持

Microsoft Teams SDK 支持超过 100 个字符串和资源。 框架捆绑包包含基本语言和英语。 包中的 `Localizations.zip` 文件中包含它们的其余部分。

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>根据应用程序用支持的内容将本地化功能添加到 SDK

1. 从应用程序“Xcode 项目”>“信息”>“本地化列表”，确定应用程序支持的本地化类型
2. 解压缩包附带的 Localizations.zip
3. 根据应用程序支持的内容将本地化文件夹从解压缩的文件夹复制到 TeamsAppSDK.framework 的根文件夹
