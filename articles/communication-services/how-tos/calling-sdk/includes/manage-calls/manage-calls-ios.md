---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2deff9146d0f6ec28e3e37981bcb82d3e056947
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698911"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!NOTE]
> 应用程序在实现事件委托时，必须持有对需要事件订阅的对象的强引用。 例如，如果在调用方法 `call.addParticipant` 时返回 `RemoteParticipant` 对象，并且应用程序将委托设置为侦听 `RemoteParticipantDelegate`，应用程序必须持有对 `RemoteParticipant` 对象的强引用。 否则，如果收集了此对象，则当呼叫 SDK 尝试调用对象时，委托将引发严重异常。

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

## <a name="join-a-group-call"></a>加入群组通话
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

## <a name="subscribe-to-an-incoming-call"></a>订阅来电
订阅来电事件。

```swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didReceiveIncomingCall incomingcall: IncomingCall) {
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

## <a name="manage-remote-participants"></a>管理远程参与者

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过通话实例上的 `remoteParticipants` 集合获得。

### <a name="list-participants-in-a-call"></a>列出通话参与者

```swift
call.remoteParticipants
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