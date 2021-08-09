---
title: 使用适用于 iOS 的 Azure 通信服务 Teams Embed
description: 本概述将介绍如何使用适用于 iOS 的 Azure 通信服务 Teams Embed 库。
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1a6c8d05da04dc0f32fb278baf946ea363010903
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111545977"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 完成[开始向应用程序添加 Teams Embed](../getting-started-with-teams-embed.md) 快速入门

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Teams Embed 通话或会议状态事件捕获

可以从 `MeetingUIClientCallDelegate` 委托捕获已加入的组通话或会议状态。 状态包括连接状态、参与者计数以及麦克风或相机状态等形式。   

将 `MeetingUIClientCallDelegate` 添加到类并添加所需的变量。

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    //Rest of the UIViewController code
}
```

在成功启动加入通话或会议后，将 `self.meetingUIClientCall?.meetingUIClientCallDelegate` 设置为 `self`。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

实现应用所需的 `MeetingUIClientCallDelegate` 协议方法，并添加不需要的协议方法的存根。

```swift
    func meetingUIClient(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }
    
    func onIsMutedChanged() {
    }
    
    func onIsSendingVideoChanged() {
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
    }
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>将你自己的标识从应用传递给 SDK 通话的参与者。

应用可以将其用户标识值分配给通话或会议的参与者，并重写默认值。 值包括头像、名称、字幕和角色。  

### <a name="assigning-avatars-for-call-participants"></a>为通话参与者分配头像

将 `MeetingUIClientCallIdentityProviderDelegate` 添加到类中。

```swift
class ViewController: UIViewController, MeetingUIClientCallIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

在成功启动加入通话或会议后，将 `self.meetingUIClientCall?.MeetingUIClientIdentityProviderDelegate` 设置为 `self`。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallIdentityProviderDelegate = self
            }
        }
    })
}
```

添加和实现 `avatarFor` 协议方法，并将每个 `identifier` 映射到相应的头像。

```swift
    func avatarFor(identifier: CommunicationIdentifier, size: MeetingUIClientAvatarSize, completionHandler: @escaping (UIImage?) -> Void) {
        if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:teamsvisitor:")) {
                // Anonymous teams user will start with prefix 8:teamsvistor:
                let image = UIImage (named: "avatarPink")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:orgid:")) {
                // OrgID user will start with prefix 8:orgid:
                let image = UIImage (named: "avatarDoctor")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // ACS user will start with prefix 8:acs:
                let image = UIImage (named: "avatarGreen")
                completionHandler(image!)
            } else {
                completionHandler(nil)
            }
        } else {
            completionHandler(nil)
        }
    }

```

将其他必需的 MeetingUIClientCallIdentityProviderDelegate 协议方法添加到该类，但其可能会保留空实现。
```swift
    func displayNameFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func roleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (MeetingUIClientUserRole) -> Void) {
    }
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>在同一应用中使用 Teams Embed SDK 和 Azure 通信通话 SDK

Teams Embed SDK 还提供 Azure 通信通话 SDK (ACS)，允许在同一应用中使用这两个 SDK 功能。 一次只能初始化和使用一个 SDK。 同时初始化和使用这两个 SDK 将导致意外行为。 

导入 `TeamsAppSDK` 以从 Teams Embed SDK 访问 Azure 通信通话 SDK。 
```swift
import TeamsAppSDK
```

为 ACS 使用情况声明变量
```swift
class ViewController: UIViewController {

    private var callClient: CallClient?
    private var callAgent: CallAgent?
    private var call: Call?
    
    //Rest of the UIViewController code
}
    
```

初始化通过创建新的 `CallClient` 完成。 将创建添加到 `viewDidLoad` 任何其他方法。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    self.callClient = CallClient()
}
```
使用所有 ACS API，如文档中所述。 本文档不讨论 API 使用情况。 

释放 ACS SDK，在不再使用或应用需要使用 Teams Embed SDK 后将 `nil` 设置为其变量。
```swift
    public func disposeAcsSdk()
    {
        self.call = nil
        self.callAgent?.dispose()
        self.callClient?.dispose()
        self.callAgent = nil
        self.callClient = nil
    }

```

Teams Embed SDK 初始化也是在创建 `MeetingUIClient` 期间完成的。 将创建添加到 `viewDidLoad` 任何其他方法。
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    do {
        let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
        let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
        meetingUIClient = MeetingUIClient(with: credential)
    }
    catch {
        print("Failed to create communication token credential")
    }
}

private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}

```
使用 Teams Embed SDK API，如文档中所述。 本文档不讨论 API 使用情况。 

释放 Teams Embed SDK，在不再使用或应用需要使用 ACS SDK 后将 `nil` 设置为其变量。
```swift
    private func disposeTeamsSdk() {
        self.meetingUIClient?.dispose(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("Dispose failed: \(error!)")
            } else {
                self.meetingUIClient = nil
                self.meetingUIClientCall = nil
            }
        })        
    }

```

只有在没有活动通话的情况下，才能释放 Teams Embed SDK。 如果存在活动通话，则 `meetingUIClient?.dispose` 会在其完成处理程序中返回错误。 如果没有活动通话，请挂起活动通话并调用 `self.disposeTeamsSdk()`。
```swift
    
    private var shouldDispose: Bool = false

    public func endMeeting() {
        meetingUIClientCall?.hangUp(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("End meeting failed: \(error!)")
                // There are no active calls
                self.disposeTeamsSdk()
            } else {
                // Ending active call is in progress.
                self.shouldDispose = true;
            }
        })
    }

```

实现 `MeetingUIClientCallDelegate` 协议方法 `meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState)`，获取有关将结束的活动通话的状态更新，并在其后释放 Teams Embed SDK。
```swift
    func meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
        case .connected:
        case .waitingInLobby:
        case .ended:
            if (self.shouldDispose) {
                self.disposeTeamsSdk()
            }
        @unknown default:
            print("Unsupported state")
        }
    }
```

向类添加其他必需的 `MeetingUIClientCallDelegate` 协议方法

```swift
    func meetingUIClientCall(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }

    func onIsMutedChanged() {
        print("Mute state changed to: \(meetingUIClientCall?.isMuted ?? false)")
    }
    
    func onIsSendingVideoChanged() {
        print("Sending video state changed to: \(meetingUIClientCall?.isSendingVideo ?? false)")
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
        print("Self participant raise hand status changed to: \(meetingUIClientCall?.isHandRaised ?? false)")
    }
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>在 UI 中接收有关用户操作的信息，并添加自己的自定义功能。

在远程参与者的配置文件中为用户操作调用 `MeetingUIClientCallUserEventDelegate` 委托方法。
加入通话或会议时，将加入选项属性 `enableNamePlateOptionsClickDelegate` 设置为 `true`。
设置此属性将在远程参与者的配置文件中启用名称板选项，并启用 `MeetingUIClientCallUserEventDelegate`。

将 `MeetingUIClientCallUserEventDelegate` 添加到类中。

```swift
class ViewController: UIViewController, MeetingUIClientCallUserEventDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

在成功启动加入通话或会议后，将 `self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate` 设置为 `self`。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate = self
            }
        }
    })
}
```

添加和实现 `onNamePlateOptionsClicked` 协议方法，并将每个 `identifier` 映射到相应的通话参与者用户。
在通话主屏幕中一次点击用户磁贴或用户标题文本时将调用此方法。

```swift
func onNamePlateOptionsClicked(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```

添加和实现 `onParticipantViewLongPressed` 协议方法，并将每个 `identifier` 映射到相应的通话参与者用户。
在通话主屏幕中长按用户磁贴时调用此方法。

```swift
func onParticipantViewLongPressed(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
## <a name="user-experience-customization"></a>用户体验自定义

可以通过提供特定于应用的图标或替换通话控件条来自定义 SDK 中的用户体验。 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>自定义通话或会议中的 UI 图标

可以通过 `MeetingUIClient` 中公开的 `public func set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` 方法自定义通话或会议中显示的图标。
`MeetingUIClientIconType` 中提供了可自定义的可能图标列表。

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

初始化 meetingUIClient 后，为 `MeetingUIClientIconType` 中支持的通话图标设置图标配置 `meetingUIClient?.set(iconConfig: self.getIconConfig())`。

```swift
private func initMeetingUIClient() {
    meetingUIClient = MeetingUIClient(with: credential)
    meetingUIClient?.set(iconConfig: self.getIconConfig())
}

func getIconConfig() -> Dictionary<MeetingUIClientIconType, String> {
    var iconConfig = Dictionary<MeetingUIClientIconType, String>()
    iconConfig.updateValue("camera_fill", forKey: MeetingUIClientIconType.VideoOn)
    iconConfig.updateValue("camera_off", forKey: MeetingUIClientIconType.VideoOff)
    iconConfig.updateValue("microphone_fill", forKey: MeetingUIClientIconType.MicOn)
    iconConfig.updateValue("microphone_off", forKey: MeetingUIClientIconType.MicOff)
    iconConfig.updateValue("speaker_fill", forKey: MeetingUIClientIconType.Speaker)
    return iconConfig
}
```

### <a name="customize-main-call-screen"></a>自定义主通话屏幕

`MeetingUIClient` 支持自定义主通话屏幕 UI。 目前，它支持使用 `MeetingUIClientInCallScreenDelegate` 协议方法自定义 UI。
通话屏幕控制操作通过 `MeetingUIClientCall` 中提供的方法公开。

将 `MeetingUIClientInCallScreenDelegate` 添加到类中。

```swift
class ViewController: UIViewController, MeetingUIClientInCallScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

在加入通话或会议之前，将 `meetingUIClient?.meetingUIClientInCallScreenDelegate` 设置为 `self`。

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientInCallScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

添加和实现 `provideControlTopBar` 协议方法以提供主通话屏幕顶部信息栏。

```swift
func provideControlTopBar() -> UIView? {
    let topView = UIStackView.init()
    // add your customization here
    return topView
}
```

将其他必需的 `MeetingUIClientInCallScreenDelegate` 协议方法添加到类，它们可能会保留空实现以返回 nil。
```swift
func provideControlBottomBar() -> UIView? {
    return nil
}

func provideScreenBackgroudColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-staging-call-screen"></a>在暂存通话屏幕上自定义

`MeetingUIClient` 支持自定义暂存通话屏幕 UI。 目前，它支持使用 `MeetingUIClientStagingScreenDelegate` 协议方法自定义 UI。
加入通话或会议时，将加入选项属性 `enableCallStagingScreen` 设置为 `true` 以显示暂存屏幕。

将 `MeetingUIClientStagingScreenDelegate` 添加到类中。

```swift
class ViewController: UIViewController, MeetingUIClientStagingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

在加入通话或会议之前，将 `meetingUIClient?.meetingUIClientStagingScreenDelegate` 设置为 `self`。

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientStagingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

添加和实现 `provideJoinButtonCornerRadius` 协议方法以修改加入按钮角，使其具有圆角外观。

```swift
func provideJoinButtonCornerRadius() -> CGFloat {
    return 24
}
```

将其他必需的 `MeetingUIClientStagingScreenDelegate` 协议方法添加到类，它们可能会保留空实现以返回 nil。
```swift
func provideJoinButtonBackgroundColor() -> UIColor? {
    return nil
}

func provideStagingScreenBackgroundColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-connecting-call-screen"></a>在加入通话屏幕上自定义

`MeetingUIClient` 支持自定义连接通话屏幕 UI。 目前，它支持使用 `MeetingUIClientConnectingScreenDelegate` 协议方法自定义 UI。
使用 `MeetingUIClient` 中公开的图标配置方法 `set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` 仅更改显示的图标，并使用 `MeetingUIClient` 提供的功能。


将 `MeetingUIClientConnectingScreenDelegate` 添加到类中。

```swift
class ViewController: UIViewController, MeetingUIClientConnectingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

在加入通话或会议之前，将 `meetingUIClient?.meetingUIClientConnectingScreenDelegate` 设置为 `self`。

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientConnectingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

添加和实现 `provideConnectingScreenBackgroundColor` 协议方法以修改连接屏幕的背景色。

```swift
func provideConnectingScreenBackgroundColor() -> UIColor?
    return 24
}
```

## <a name="perform-operations-with-the-call"></a>对通话执行操作

通话控制操作通过 `MeetingUIClientCall` 中提供的方法公开。
如果 UI 是使用 `MeetingUIClient` 自定义委托自定义的，则这些方法可用于控制通话操作。

向通话添加了所需的变量。
```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

从 `join` 方法 `completionHandler` 中为 `self.meetingUIClientCall` 变量分配 `meetingUIClientCall` 值
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```
### <a name="mute-and-unmute"></a>静音和取消静音

调用 `mute` 方法将麦克风静音，以进行活动通话（如果存在）。
在 `MeetingUIClientCallDelegate` 的 `onIsMutedChanged` 方法中通知麦克风状态更改

```swift
// Mute the microphone for an active call.
public func mute(completionHandler: @escaping (Error?) -> Void)

    meetingUIClientCall?.mute { [weak self] (error) in
        if error != nil {
            print("Mute call failed: \(error!)")
        }
}
```

调用 `unmute` 方法取消麦克风静音，以进行活动通话（如果存在）。

```swift
// Unmute the microphone for an active call.
public func unmute(completionHandler: @escaping (Error?) -> Void)

meetingUIClientCall?.unmute { [weak self] (error) in
    if error != nil {
        print("Mute call failed: \(error!)")
    }
}
```

### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>`MeetingUIClientCall` 类中提供的其他操作。

```swift
// Start the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Stop the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Set the preferred audio route in the call for self user.
public func setAudio(route: MeetingUIClientAudioRoute, completionHandler: @escaping (Error?) -> Void)

// Raise the hand of current user for an active call.
public func raiseHand(completionHandler: @escaping (Error?) -> Void)

// Lower the hand of user provided in the identifier for an active call.
// public func lowerHand(identifier: CommunicationIdentifier, completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hangs up the call or leaves the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)

// Set the user role for an active call.
public func setRoleFor(identifier: CommunicationIdentifier, userRole: MeetingUIClientUserRole, completionHandler: @escaping (Error?) -> Void)
```
