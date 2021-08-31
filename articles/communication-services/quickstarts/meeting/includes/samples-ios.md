---
title: 使用适用于 iOS 的 Azure 通信服务 Teams Embed
description: 本概述将介绍如何使用适用于 iOS 的 Azure 通信服务 Teams Embed 库。
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 246c62ec25a788c7767da0c8fdf23b42db321f0b
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215090"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 完成[开始向应用程序添加 Teams Embed](../getting-started-with-teams-embed.md) 快速入门

## <a name="joining-a-group-call"></a>加入群组通话

通过向 `meetingUIClient?.join` API 提供 `MeetingUIClientGroupCallLocator` 和 `MeetingUIClientGroupCallJoinOptions`，可加入群组通话。 群组通话不会拨打其他参与者。 用户将以无提示方式加入通话。

在 ViewController.swift 的 `viewDidLoad` 调用中创建按钮。

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let joinGroupCallButton = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        joinGroupCallButton.backgroundColor = .black
        joinGroupCallButton.setTitle("Join Group Call", for: .normal)
        joinGroupCallButton.addTarget(self, action: #selector(joinGroupCallTapped), for: .touchUpInside)
        
        joinGroupCallButton.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(joinGroupCallButton)
        joinGroupCallButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        joinGroupCallButton.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }
```

在 ViewController.swift 中为按钮创建到 `ViewController` 类的出口。

```swift
@IBAction func joinGroupCallTapped(_ sender: UIButton) {
    joinGroupCall()
}

private func joinGroupCall() {
    // Add join meeting logic
}
```

设置客户端和提供令牌的方式与“会议加入 API”的执行方式相同，这在[快速入门](../getting-started-with-teams-embed.md)中进行了介绍。 

`joinGroupCall` 方法设置为在点击“加入群组通话”按钮时执行的操作。
使用 `MeetingUIClientGroupCallJoinOptions` 创建 `MeetingUIClientGroupCallLocator`，并配置联接选项。
注意，用 UUID 字符串替换 `<GROUP_ID>`。 组 ID 字符串必须采用 GUID 或 UUID 格式。
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: <GROUP_ID>)
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall = meetingUIClientCall
            } else {
                print("Join meeting failed: \(error!)")
            }
        }
    })
}
```

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
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

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-call"></a>将你自己的标识从应用传递给通话的参与者。

应用可以将其用户标识值分配给通话或会议的参与者，并重写默认值。 值包括头像、名称和字幕。  

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
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
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>在 UI 中接收有关用户操作的信息，并添加自己的自定义功能。

### <a name="call-screen"></a>通话屏幕 
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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: false, enableCallRosterDelegate: false)
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
### <a name="call-roster"></a>通话名单 
`MeetingUIClientCallRosterDelegate` 委托方法在对名单中的参与者执行用户操作时调用。
加入通话或会议时，将加入选项属性 `enableCallRosterDelegate` 设置为 `true`。
设置此属性将在远程参与者的配置文件中启用名称板选项，并启用 `MeetingUIClientCallRosterDelegate`。

将 `MeetingUIClientCallRosterDelegate` 添加到类中。

```swift
class ViewController: UIViewController, MeetingUIClientCallRosterDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

在成功启动加入通话或会议后，将 `self.meetingUIClientCall?.meetingUIClientCallRosterDelegate` 设置为 `self`。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallRosterDelegate = self
            }
        }
    })
}
```

添加和实现 `onCallParticipantCellTapped` 协议方法，并将每个 `identifier` 映射到相应的通话参与者用户。
此方法在“通话名单”中单击“参与者”时调用。

```swift
func onCallParticipantCellTapped(identifier: CommunicationIdentifier) {
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

可以通过提供特定于应用的图标来自定义 SDK 中的用户体验。 

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
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true, enableCallRosterDelegate: false)
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
        print("Unmute call failed: \(error!)")
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

// Lower the hand of current user for an active call.
// public func lowerHand(completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hang up the call or leave the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)
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
