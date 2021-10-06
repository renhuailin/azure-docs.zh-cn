---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 319571066bd69d1bc80414de7bdb49da27102c18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698909"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="place-a-call"></a>拨打电话

若要创建和发起呼叫，需要调用 `CallAgent.startCall()` 方法并为被叫方提供 `Identifier`。
若要加入群组通话，需要调用 `CallAgent.join()` 方法并提供 groupId。 组 ID 必须采用 GUID 或 UUID 格式。

创建和发起呼叫的操作是同步的。 通过呼叫实例，可订阅呼叫中的所有事件。

### <a name="place-a-11-call-to-a-user"></a>向用户发出一对一呼叫
若要向另一位通信服务用户发出呼叫，请对 `callAgent` 调用 `call` 方法，并传递带有 `communicationUserId` 键的对象。
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>向用户和 PSTN 发起一对多通话
> [!WARNING]
> PSTN 呼叫当前不可用

若要对用户和 PSTN 号码发出一对多呼叫，必须指定被叫方的电话号码。
必须将通信服务资源配置为允许 PSTN 呼叫：
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

## <a name="accept-a-call"></a>接受呼叫
若要接听来电，请对呼叫对象调用“accept”方法。

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

若要接受启用了摄像机的呼叫：

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

可订阅 `callAgent` 对象上的 `onIncomingCall` 事件来获取来电：

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```

## <a name="join-a-group-call"></a>加入群组通话
若要发起新的群组通话或加入正在进行的群组通话，必须调用“join”方法并传递带有 `groupId` 属性的对象。 该值必须为 GUID。
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

## <a name="call-properties"></a>通话属性

获取此呼叫的唯一 ID：

```java
String callId = call.getId();
```

若要了解呼叫中的其他参与者，请检查 `call` 实例上的 `remoteParticipant` 集合：

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

主叫方的标识（如果是来电）：

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

获取呼叫的状态： 

```java
CallState callState = call.getState();
```

它会返回一个表示当前呼叫状态的字符串：
* “NONE”- 初始通话状态
* “EARLY_MEDIA”- 表示在接通电话前播放通知的状态
* “CONNECTING”- 拨打或接听电话后的初始过渡状态
* “RINGING”- 对于去电，表示远程参与者的电话正在响铃
* “CONNECTED”- 已接通电话
* “LOCAL_HOLD”- 本地参与者暂停通话，本地终结点与远程参与者之间没有媒体传输
* “REMOTE_HOLD”- 远程参与者暂停通话，本地终结点与远程参与者之间没有媒体传输
* “DISCONNECTING”- 通话进入“Disconnected”状态前的过渡状态
* “DISCONNECTED”- 最终通话状态
* “IN_LOBBY”- 在大厅中，为了实现 Teams 会议互操作性

若要了解呼叫结束的原因，请检查 `callEndReason` 属性。 它包含代码/子代码： 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

若要查看当前呼叫是来电还是去电，请检查 `callDirection` 属性：

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

若要查看当前麦克风是否静音，请检查 `muted` 属性：

```java
boolean muted = call.isMuted();
```

若要检查活动视频流，请查看 `localVideoStreams` 集合：

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

## <a name="mute-and-unmute"></a>静音和取消静音

若要使本地终结点静音或取消静音，可使用 `mute` 和 `unmute` 异步 API：

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
## <a name="change-the-volume-of-the-call"></a>更改通话音量

在进行通话时，手机上的硬件音量键应允许用户更改通话音量。
为此，通过在通话所在的活动上使用具有流类型 `AudioManager.STREAM_VOICE_CALL` 的方法 `setVolumeControlStream` 来完成。
这允许硬件音量键更改通话音量（由电话图标或音量滑块上的类似图标表示），并防止更改其他声音配置文件的音量，如警报、媒体或系统范围的音量。 有关详细信息，可以查看[处理音频输出中的更改 | Android 开发人员](https://developer.android.com/guide/topics/media-apps/volume-and-earphones)。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

## <a name="remote-participants-management"></a>远程参与者管理

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过呼叫实例上的 `remoteParticipants` 集合获得。

### <a name="list-participants-in-a-call"></a>列出通话参与者
`remoteParticipants` 集合会返回给定通话中远程参与者的列表：
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>向通话添加参与者

若要向通话添加参与者（用户或电话号码），可调用 `addParticipant`。 这会以同步方式返回远程参与者实例。

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>删除通话参与者
若要删除通话参与者（用户或电话号码），可调用 `removeParticipant`。
一旦从呼叫中删除参与者，就会异步解决此问题。
这还将从 `remoteParticipants` 集合中删除该参与者。
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

### <a name="remote-participant-properties"></a>远程参与者属性
任何给定的远程参与者都具有与之关联的一组属性和集合：

* 获取此远程参与者的标识符。
标识其中一种“标识符”类型
    ```java
    CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
    ```

* 获取此远程参与者的状态。
    ```java
    ParticipantState state = remoteParticipant.getState();
    ```
状态可以是下列其中一项
* “IDLE”- 初始状态
* “EARLY_MEDIA”- 在参与者接通电话前播放通知
* “RINGING”- 参与者电话正在响铃
* “CONNECTING”- 参与者正在连接到通话时的过渡状态
* “CONNECTED”- 参与者已接通电话
* “HOLD”- 参与者已暂停通话
* “IN_LOBBY”- 参与者正在大厅中等待许可。 当前仅在 Teams 互操作方案中使用
* “DISCONNECTED”- 最终状态，参与者已与通话断开连接

* 若要了解参与者退出通话的原因，请检查 `callEndReason` 属性：
    ```java
    CallEndReason callEndReason = remoteParticipant.getCallEndReason();
    ```

* 若要检查此远程参与者是否已静音，请检查 `isMuted` 属性：
    ```java
    boolean isParticipantMuted = remoteParticipant.isMuted();
    ```

* 若要检查此远程参与者是否正在讲话，请检查 `isSpeaking` 属性：
    ```java
    boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
    ```

* 若要检查给定参与者在此呼叫中发送的所有视频流，请检查 `videoStreams` 集合：
    ```java
    List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
    ```