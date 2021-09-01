---
title: 快速入门 - 使用 Azure 通信服务向 iOS 应用添加呼叫
description: 本快速入门介绍如何使用适用于 iOS 的 Azure 通信服务通话 SDK。
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 2c1340e41471ab25f00d7ad0a9e678dfeb65470f
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970216"
---
本快速入门介绍如何使用适用于 iOS 的 Azure 通信服务通话 SDK 开始通话。

## <a name="sample-code"></a>代码示例

可以从 [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling) 下载示例应用。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要具备以下先决条件：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 一部运行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及一个安装到密钥链的有效开发人员证书。
- 已部署的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- Azure 通信服务的[用户访问令牌](../../../access-tokens.md)。

## <a name="setting-up"></a>设置

### <a name="creating-the-xcode-project"></a>创建 Xcode 项目

在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用”模板。 本教程使用 [SwiftUI 框架](https://developer.apple.com/xcode/swiftui/)，因此应将“语言”设置为“Swift”，并将“用户界面”设置为“SwiftUI”   。 在此快速入门过程中，不会创建测试。 可以取消选中“包括测试”。

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="显示 Xcode 中“新建项目”窗口的屏幕截图。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>使用 CocoaPods 安装包和依赖项

1. 若要为应用程序创建 Podfile，请打开终端并导航到项目文件夹，然后运行 ```pod init```
3. 将以下代码添加到 Podfile 并保存（确保“目标对象”与项目名称匹配）：

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

3. 运行 `pod install`。
3. 使用 Xcode 打开 `.xcworkspace`。

### <a name="request-access-to-the-microphone"></a>请求访问麦克风

若要访问设备的麦克风，需要使用 `NSMicrophoneUsageDescription` 更新应用的信息属性列表。 将关联的值设置为将要包含在系统用于向用户请求访问权限的对话框中的 `string`。

右键单击项目树的 `Info.plist` 条目，然后选择“打开为” > “源代码” 。 将以下代码行添加到顶层 `<dict>` 节，然后保存文件。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

打开项目的 ContentView.swift 文件，然后将 `import` 声明添加到文件顶部以导入 `AzureCommunicationCalling library`。 此外，导入 `AVFoundation`，我们需要此内容以便在代码中进行音频权限请求。

```swift
import AzureCommunicationCalling
import AVFoundation
```

将 `ContentView` 结构的实现替换为使用户可以启动和结束呼叫的一些简单 UI 控件。 我们会在本快速入门中将业务逻辑附加到这些控件。

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>对象模型

以下类和接口用于处理 Azure 通信服务通话 SDK 的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient 是通话 SDK 的主入口点。|
| CallAgent | CallAgent 用于启动和管理呼叫。 |
| CommunicationTokenCredential | CommunicationTokenCredential 用作实例化 CallAgent 的令牌凭据。| 
| CommunicationUserIdentifier | CommunicationUserIdentifier 用于表示用户的标识，可以是以下项之一：CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication。 |

## <a name="authenticate-the-client"></a>验证客户端

通过用户访问令牌初始化 `CallAgent` 实例，这使我们可以发出和接收呼叫。 

在以下代码中，需要将 `<USER ACCESS TOKEN>` 替换为资源的有效用户访问令牌。 如果还没有可用的令牌，请参阅[用户访问令牌](../../../access-tokens.md)文档。

将以下代码添加到 ContentView.swift 中的 `onAppear` 回调：

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }
    else {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```



## <a name="start-a-call"></a>开始呼叫

`startCall` 方法设置为在点击“开始呼叫”按钮时执行的操作。 更新实现，以便使用 `ASACallAgent` 开始呼叫：

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
            self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
                if (error == nil) {
                    self.call = call
                } else {
                    print("Failed to get call object")
                }
            }
        }
    }
}
```

还可以使用 `StartCallOptions` 中的属性来设置呼叫的初始选项（例如，它允许在麦克风静音的情况下开始呼叫）。

## <a name="end-a-call"></a>结束呼叫

实现 `endCall` 方法，以便在点击“结束呼叫”按钮时结束当前呼叫。

```swift
func endCall()
{    
    self.call!.hangUp(options: HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>运行代码

可以通过选择“产品” > “运行”或使用 (&#8984;-R) 键盘快捷方式，在 iOS 模拟器上生成并运行应用。

:::image type="content" source="../../media/ios/quick-start-make-call.png" alt-text="快速入门应用的最终外观":::

可以通过在“文本”字段中提供用户 ID 并点击“开始呼叫”按钮，建立出站 VOIP 呼叫。 呼叫 `8:echo123` 会将你连接到回显机器人，这对于入门和验证音频设备是否正常运行非常有用。 

> [!NOTE]
> 首次发出呼叫时，系统将提示你提供麦克风访问权限。 在生产应用程序中，应使用 `AVAudioSession` API [检查权限状态](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)，并在未授予权限时正常更新应用程序的行为。
