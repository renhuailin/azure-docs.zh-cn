---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 8e95261da4bd8f6082d567f90e17cf236b8fe7f1
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111560474"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的 Azure 通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 [获取用户访问令牌](../../../access-tokens.md)。
- 可选：完成[在应用中添加语音呼叫](../../getting-started-with-calling.md)快速入门。

## <a name="set-up-your-system"></a>设置系统

### <a name="create-the-xcode-project"></a>创建 Xcode 项目

在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用”模板。 本快速入门使用 [SwiftUI 框架](https://developer.apple.com/xcode/swiftui/)，因此应将“语言”设置为“Swift”，并将“用户界面”设置为“SwiftUI”   。 

在本快速入门过程中，不会创建单元测试或 UI 测试。 可随意清除“包括单元测试”和“包括 UI 测试”文本框 。

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="显示用于在 Xcode 中创建项目的窗口的屏幕截图。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>使用 CocoaPods 安装包和依赖项

1. 为应用程序创建 Podfile，如下所示：

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

2. 运行 `pod install`。
3. 使用 Xcode 打开 `.xcworkspace`。

### <a name="request-access-to-the-microphone"></a>请求访问麦克风

若要访问设备的麦克风，需要使用 `NSMicrophoneUsageDescription` 更新应用的信息属性列表。 将关联的值设置为将要包含在系统用于向用户请求访问权限的对话框中的 `string`。

右键单击项目树的 `Info.plist` 条目，然后选择“打开为” > “源代码” 。 将以下代码行添加到顶层 `<dict>` 节，然后保存文件。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

打开项目的 ContentView.swift 文件，然后将 `import` 声明添加到文件顶部以导入 `AzureCommunicationCalling` 库。 此外，导入 `AVFoundation`。 你将需要用它来处理代码中的音频权限请求。

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>了解对象模型

以下类和接口处理适用于 iOS 的 Azure 通信服务呼叫 SDK 的某些主要功能。

> [!NOTE]
> 本快速入门使用呼叫 SDK 版本 1.0.0-beta.8。


| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` 是呼叫 SDK 的主入口点。|
| `CallAgent` | `CallAgent` 用于启动和管理呼叫。 |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` 用作实例化 `CallAgent` 的令牌凭据。| 
| `CommunicationIdentifier` | `CommunicationIdentifier` 用于表示用户的标识。 标识可以是 `CommunicationUserIdentifier`、`PhoneNumberIdentifier` 或 `CallingApplication`。 |

> [!NOTE]
> 应用程序在实现事件委托时，必须持有对需要事件订阅的对象的强引用。 例如，如果在调用方法 `call.addParticipant` 时返回 `RemoteParticipant` 对象，并且应用程序将委托设置为侦听 `RemoteParticipantDelegate`，应用程序必须持有对 `RemoteParticipant` 对象的强引用。 否则，如果收集了此对象，则当呼叫 SDK 尝试调用对象时，委托将引发严重异常。

## <a name="initialize-callagent"></a>初始化 CallAgent

若要从 `CallClient` 创建 `CallAgent` 实例，必须使用 `callClient.createCallAgent` 方法，该方法在初始化后异步返回 `CallAgent` 对象。

若要创建通话客户端，必须传递 `CommunicationTokenCredential` 对象。

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

将创建的 `CommunicationTokenCredential` 对象传递给 `CallClient` 并设置显示名称。

```swift

self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>拨出电话

若要创建并发起通话，需要在 `CallAgent` 上调用其中一个 API，并提供已使用通信服务管理 SDK 预配的用户的通信服务标识。

创建和发起呼叫的操作是同步的。 你将收到通话实例，可通过该实例订阅通话中的所有事件。

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>向用户发起一对一通话，或向用户和 PSTN 发起一对多通话

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully started outgoing call")
         self.call = call
     } else {
         print("Failed to start outgoing call")
     }
}

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>向用户和 PSTN 发起一对多通话
若要向 PSTN 发起通话，必须指定通过通信服务获取的电话号码。

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [pstnCallee, callee], options: StartCallOptions()) { (groupCall, error) in
     if error == nil {
         print("Successfully started outgoing call to multiple participants")
         self.call = groupCall
     } else {
         print("Failed to start outgoing call to multiple participants")
     }
}

```

### <a name="place-a-11-call-with-video"></a>发起一对一视频通话
若要获取设备管理器实例，请查看有关[管理设备](#manage-devices)的部分。

```swift

let firstCamera = self.deviceManager!.cameras.first
self.localVideoStreams = [LocalVideoStream]()
self.localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let videoOptions = VideoOptions(localVideoStreams: self.localVideoStreams!)

let startCallOptions = StartCallOptions()
startCallOptions.videoOptions = videoOptions

let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [callee], options: startCallOptions) { (call, error) in
     if error == nil {
         print("Successfully started outgoing video call")
         self.call = call
     } else {
         print("Failed to start outgoing video call")
     }
}

```

### <a name="join-a-group-call"></a>加入群组通话
若要加入通话，需要在 `CallAgent` 上调用其中一个 API。

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string")!)
self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully joined group call")
         self.call = call
     } else {
         print("Failed to join group call")
     }
}

```

### <a name="subscribe-to-an-incoming-call"></a>订阅来电
订阅来电事件。

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingcall: IncomingCall) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func incomingCall(_ incomingCall: IncomingCall, didEnd args: PropertyChangedEventArgs) {
        print("Incoming call was not answered")
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>接听来电
若要接听来电，请对 `IncomingCall` 对象调用 `accept` 方法。

```swift
self.incomingCall!.accept(options: AcceptCallOptions()) { (call, error) in
   if (error == nil) {
       print("Successfully accepted incoming call")
       self.call = call
   } else {
       print("Failed to accept incoming call")
   }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
localVideoStreams = [LocalVideoStream]()
localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions.videoOptions = VideoOptions(localVideoStreams: localVideoStreams!)
if let incomingCall = self.incomingCall {
  incomingCall.accept(options: acceptCallOptions) { (call, error) in
        if error == nil {
            print("Incoming call accepted")
        } else {
            print("Failed to accept incoming call")
        }
    }
} else {
  print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>设置推送通知

移动推送通知是在移动设备中收到的弹出通知。 对于呼叫，我们将重点介绍 VoIP（Internet 语音协议）推送通知。 

以下部分介绍如何注册、处理和取消注册推送通知。 在开始这些任务之前，请先满足以下先决条件：

1. 在 Xcode 中，转到“签名和功能”。 选择“+功能”来添加一项功能，然后选择“推送通知” 。
2. 选择“+功能”来再添加一项功能，然后选择“背景模式” 。
3. 在“背景模式”下，选中“IP 语音”和“远程通知”复选框  。

:::image type="content" source="../../media/ios/xcode-push-notification.png" alt-text="显示如何在 Xcode 中添加功能的屏幕截图。" lightbox="../../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>注册推送通知

若要注册推送通知，请使用设备注册令牌在 `CallAgent` 实例上调用 `registerPushNotification()`。

成功初始化后需要注册推送通知。 销毁 `callAgent` 对象时，将调用 `logout`，这会自动取消注册推送通知。


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>处理推送通知
若要接收来电推送通知，请使用字典有效负载在 `CallAgent` 实例上调用 `handlePushNotification()`。

```swift

let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}

```
### <a name="unregister-push-notifications"></a>取消注册推送通知

应用程序可以随时取消注册推送通知。 在 CallAgent 上调用  方法即可。

> [!NOTE]
> 注销时，应用程序不会自动取消注册推送通知。

```swift

callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}

```

## <a name="perform-mid-call-operations"></a>执行通话中操作

可在通话过程中执行各种操作来管理与视频和音频相关的设置。

### <a name="mute-and-unmute"></a>静音和取消静音

若要将本地终结点静音或取消静音，可使用 `mute` 和 `unmute` 异步 API。

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

使用以下代码以异步方式将本地终结点取消静音。

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频

若要开始向通话中的其他参与者发送本地视频，请使用 `startVideo` API 并使用 `camera` 传递 `localVideoStream`。

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
let localVideoStream = LocalVideoStream(camera: firstCamera!)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

开始发送视频后，`LocalVideoStream` 实例会添加到通话实例上的 `localVideoStreams` 集合中。

```swift

call.localVideoStreams

```

若要停止本地视频，请传递从 `call.startVideo` 调用返回的 `localVideoStream` 实例。 这是异步操作。

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>管理远程参与者

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过通话实例上的 `remoteParticipants` 集合获得。

### <a name="list-participants-in-a-call"></a>列出通话参与者

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>获取远程参与者属性

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identifier

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>向通话添加参与者

若要向通话添加参与者（用户或电话号码），可调用 `addParticipant`。 此命令会同步返回一个远程参与者实例。

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>删除通话参与者
若要删除通话参与者（用户或电话号码），可调用 `removeParticipant` API。 这会异步解析。

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流

远程参与者可在通话期间发起视频或屏幕共享。

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>处理远程参与者的视频共享或屏幕共享流

若要列出远程参与者的流，请检查 `videoStreams` 集合。

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>获取远程视频流属性

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>呈现远程参与者流

若要开始呈现远程参与者流，请使用以下代码。

```swift

let renderer = VideoStreamRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView = renderer?.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>获取远程视频呈现器方法和属性

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>管理设备

借助 `DeviceManager`，可枚举通话中可用于传输音频或视频流的本地设备。 你还可用它来向用户请求访问麦克风或相机的权限。 可访问 `callClient` 对象上的 `deviceManager`。

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>枚举本地设备

若要访问本地设备，可在设备管理器上使用枚举方法。 枚举是同步操作。

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras // [VideoDeviceInfo, VideoDeviceInfo...]

``` 

### <a name="get-a-local-camera-preview"></a>获取本地相机预览

可使用 `Renderer` 开始呈现来自本地相机的流。 此流不会发送给其他参与者；这是一项本地预览源。 这是异步操作。

```swift

let camera: VideoDeviceInfo = self.deviceManager!.cameras.first!
let localVideoStream = LocalVideoStream(camera: camera)
let localRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream)
self.view = try! localRenderer.createView()

```

### <a name="get-local-camera-preview-properties"></a>获取本地相机预览属性

呈现器包含一组属性和方法，可使用它们来控制呈现。

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = VideoStreamRenderer(localVideoStream:localVideoStream)
let remoteRenderer = VideoStreamRenderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [VideoStreamRendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer!.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>订阅通知

可订阅大多数属性和集合，以便在值发生更改时收到通知。

### <a name="properties"></a>属性
若要订阅 `property changed` 事件，请使用以下代码。

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>集合
若要订阅 `collection updated` 事件，请使用以下代码。

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func call(_ call: Call, didUpdateLocalVideoStreams args: LocalVideoStreamsUpdatedEventArgs) {
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
