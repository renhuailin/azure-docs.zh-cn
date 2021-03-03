---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 3830025d761c94e2b0b0bc3e66389d66794b946c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661511"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 可选：完成[开始向应用程序添加通话功能](../getting-started-with-calling.md)快速入门

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

> [!NOTE]
> 本文档使用 1.0.0-beta.6 版的通话客户端库。

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务通话客户端库和通用客户端库。
本文档引用 1.0.0-beta.5 版通话库中的类型。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>对象模型

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称                             | 说明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient 是呼叫客户端库的主入口点。                                                                       |
| CallAgent                        | CallAgent 用于启动和管理呼叫。                                                                                            |
| DeviceManager                    | DeviceManager 用于管理媒体设备                                                                                           |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 类实现用于实例化 CallAgent 的 CommunicationTokenCredential 接口。 |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>初始化 CallClient，创建 CallAgent 并访问 DeviceManager

实例化新的 `CallClient` 实例。 可使用自定义选项（例如 Logger 实例）对其进行配置。
实例化 `CallClient` 后，可以通过在 `CallClient` 实例上调用 `createCallAgent` 方法来创建 `CallAgent` 实例。 这将异步返回 `CallAgent` 实例对象。
`createCallAgent`方法采用 `CommunicationTokenCredential` 作为参数，该参数接受[用户访问令牌](../../access-tokens.md)。
若要访问 `DeviceManager`，必须先创建 callAgent 实例。 然后可以使用 `CallClient` 实例上的 `getDeviceManager` 方法来获取 DeviceManager。

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>拨出电话

若要创建并启动呼叫，需要在 CallAgent 上使用其中一个 Api，并提供通过通信服务标识客户端库创建的用户。

通话的创建和启动是同步的。 通过通话实例，可以订阅通话事件。

## <a name="place-a-call"></a>拨打电话

### <a name="place-a-11-call-to-a-user-or-pstn"></a>向用户或 PSTN 发起一对一通话
若要呼叫另一个通信服务用户，请在 `callAgent` 上调用 `startCall` 方法，并传递[使用通信服务管理库创建的](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens)被呼叫方 CommunicationUserIdentifier。

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

若要呼叫 PSTN，请调用 `callAgent` 上的 `startCall` 方法，并传递被呼叫方的 PhoneNumberIdentifier。
必须将通信服务资源配置为允许 PSTN 通话。
呼叫 PSTN 号码时，必须指定备用呼叫方 ID。 备用呼叫方 ID 是指 PSTN 通话中用于标识呼叫方的电话号码（基于 E.164 标准）。 例如，当你向 PSTN 通话提供备用呼叫方 ID 时，该电话号码将在来电时显示给被呼叫方。

> [!WARNING]
> PSTN 通话目前提供个人预览版。 对于访问权限，[适用于早期采用者计划](https://aka.ms/ACS-EarlyAdopter)。
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>向用户和 PSTN 发起一对多通话
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>发起启用相机的一对一通话
> [!WARNING]
> 当前最多只能有一个传出本地视频流。
若要发起视频通话，必须使用 deviceManager `getCameras()` API 枚举本地相机。
选择所需的相机后，使用它来构造 `LocalVideoStream` 实例，并在 `videoOptions` 中将其作为 `localVideoStream` 数组中的项传递给 `startCall` 方法。
通话接通后，会自动开始将来自所选相机的视频流发送给其他参与者。 这也适用于 Call.Accept() 视频选项和 CallAgent.join() 视频选项。
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>加入群组通话
若要发起新的群组通话或加入正在进行的群组通话，请使用“join”方法并传递带有 `groupId` 属性的对象。 该值必须为 GUID。
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>加入 Teams 会议
若要加入 Teams 会议，请使用“join”方法并传递会议链接或会议坐标
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>接听来电

当登录者收到来电时，`CallAgent` 实例将发出 `incomingCall` 事件。 若要侦听此事件，请按以下方式订阅：

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` 事件将提供 `IncomingCall` 的一个实例，你可以在该实例上接受或拒绝通话。


## <a name="call-management"></a>通话管理

可在通话过程中访问通话属性并执行各种操作来管理与视频和音频相关的设置。

### <a name="call-properties"></a>通话属性
* 获取此通话的唯一 ID（字符串）。
```js

const callId: string = call.id;

```

* 若要了解通话中的其他参与者，请检查 `call` 实例上的 `remoteParticipant` 集合。 数组包含列表 `RemoteParticipant` 对象
```js
const remoteParticipants = call.remoteParticipants;
```

* 呼叫方的标识符（如果是来电）。 标识符是 `CommunicationIdentifier` 类型之一
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
这会返回一个表示当前通话状态的字符串：
* “None”- 初始通话状态
* “Incoming”- 表示有来电，必须接听或拒绝
* “Connecting”- 拨打或接听电话后的初始过渡状态
* “Ringing”- 对于去电，表示远程参与者的电话正在响铃，对方的状态是“Incoming”
* “EarlyMedia”- 表示在接通电话前播放通知的状态
* “Connected”- 已接通电话
* “LocalHold”- 本地参与者暂停通话，本地终结点与远程参与者之间没有媒体传输
* “RemoteHold”- 远程参与者暂停通话，本地终结点与远程参与者之间没有媒体传输
* “Disconnecting”- 通话进入“Disconnected”状态前的过渡状态
* “Disconnected”- 最终通话状态
  * 如果网络连接断开，大约 2 分钟后状态变为“Disconnected”。

* 若要了解给定通话为何终止，请检查 `callEndReason` 属性。
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 若要了解当前通话是来电还是去电，请检查 `direction` 属性，它将返回 `CallDirection`。
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  若要检查当前麦克风是否静音，请检查 `muted` 属性，它将返回 `Boolean`。
```js

const muted = call.isMicrophoneMuted;

```

* 若要了解屏幕共享流是否来自给定终结点，请检查 `isScreenSharingOn` 属性，它将返回 `Boolean`。
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* 若要检查活动视频流，请查看 `localVideoStreams` 集合，其中包含 `LocalVideoStream` 对象
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>通话结束事件

通话结束时，`Call` 实例将发出 `callEnded` 事件。 若要侦听此事件，请按以下方式订阅：

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>静音和取消静音

若要使本地终结点静音或取消静音，可使用 `mute` 和 `unmute` 异步 API：

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频


若要开始发送视频，必须在 `deviceManager` 对象上使用 `getCameras` 方法来枚举相机。 然后创建新的 `LocalVideoStream` 实例，将所需相机作为参数传递给 `startVideo` 方法：


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

成功开始发送视频后，会将 `LocalVideoStream` 实例添加到通话实例上的 `localVideoStreams` 集合。

```js

call.localVideoStreams[0] === localVideoStream;

```

若要停止本地视频，请传递 `localVideoStreams` 集合中可用的 `localVideoStream` 实例：

```js

await call.stopVideo(localVideoStream);

```

对 `localVideoStream` 实例调用 `switchSource` 来发送视频时，可切换到不同的相机设备：

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>远程参与者管理

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过通话实例上的 `remoteParticipants` 集合获得。

### <a name="list-participants-in-a-call"></a>列出通话参与者
`remoteParticipants` 集合会返回给定通话中远程参与者的列表：

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>远程参与者属性
远程参与者具有一组关联的属性和集合
#### <a name="communicationidentifier"></a>CommunicationIdentifier
获取此远程参与者的标识符。
标识是“CommunicationIdentifier”类型之一：
```js
const identifier = remoteParticipant.identifier;
```
它可以是“CommunicationIdentifier”类型之一：
  * { communicationUserId: '<ACS_USER_ID'> } - 表示 ACS 用户的对象
  * { phoneNumber: '<E.164>' } - 以 E.164 格式表示电话号码的对象
  * { microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" } - 表示 Teams 用户的对象

#### <a name="state"></a>状态
获取此远程参与者的状态。
```js

const state = remoteParticipant.state;
```
状态可以是下列其中一项
* “Idle”- 初始状态
* “Connecting”- 参与者正在连接到通话时的过渡状态
* “Ringing”- 参与者电话正在响铃
* “Connected”- 参与者已接通电话
* “Connected”- 参与者已暂停通话
* “EarlyMedia”- 在参与者接通电话前播放通知
* “Disconnected”- 最终状态：参与者已与通话断开连接
  * 如果远程参与者失去其网络连接，则远程参与者状态在大约 2 分钟后变为“已断开连接”。

#### <a name="call-end-reason"></a>通话结束原因
若要了解参与者退出通话的原因，请检查 `callEndReason` 属性：
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>已静音
若要检查此远程参与者是否已静音，请检查 `isMuted` 属性，它返回 `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>正在讲话
若要检查此远程参与者是否正在讲话，请检查 `isSpeaking` 属性，它返回 `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>视频流
若要检查给定参与者在此通话中发送的所有视频流，请检查 `videoStreams` 集合，其中包括 `RemoteVideoStream` 对象
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>向通话添加参与者

若要向通话添加参与者（用户或电话号码），可调用 `addParticipant`。
提供一种“标识符”类型。
这会以同步方式返回远程参与者实例。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>删除通话参与者

若要删除通话参与者（用户或电话号码），可调用 `removeParticipant`。
必须传递一个“Identifier”类型。从通话中删除参与者后将异步解析它。
还将从 `remoteParticipants` 集合中删除该参与者。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流

若要列出远程参与者的视频流和屏幕共享流，请检查 `videoStreams` 集合：

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

若要呈现 `RemoteVideoStream`，必须订阅 `isAvailableChanged` 事件。
如果该 `isAvailable` 属性更改为 `true`，则远程参与者正在发送流。
一旦发生这种情况，请创建新的 `Renderer` 实例，然后使用异步 `createView` 方法创建新的 `RendererView` 实例。  然后，可以将 `view.target` 附加到任何 UI 元素。
当远程流的可用性发生变化时，可选择销毁整个呈现器、销毁特定的 `RendererView`，也可保留它们，但这将导致显示空白的视频帧。

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>远程视频流属性
远程视频流具有以下属性：

* `Id` - 远程视频流的 ID
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` - 远程视频流的大小（宽度/高度）
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` - 可以是“Video”或“ScreenSharing”
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` - 指示远程参与者终结点是否正在主动发送流
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>呈现器方法和属性

* 创建一个 `RendererView` 实例，随后可将其附加到应用程序 UI 中来呈现远程视频流。
```js
renderer.createView()
```

* 处置呈现器和所有关联的 `RendererView` 实例。
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView 方法和属性
创建 `RendererView` 时，可以指定 `scalingMode` 和 `isMirrored` 属性。
缩放模式可以是“Stretch”、“Crop”或“Fit”。如果指定了 `isMirrored`，呈现的流将会垂直翻转。

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
任何给定的 `RendererView` 实例都有一个表示呈现图面的 `target` 属性。 必须将其附加到应用程序 UI 中：
```js
document.body.appendChild(rendererView.target);
```

稍后可调用 `updateScalingMode` 方法来更新缩放模式。
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>设备管理

借助 `DeviceManager`，可枚举能够在通话中用于传输音频/视频流的本地设备。 你还可用它来通过本机浏览器 API 向用户请求访问其麦克风和相机的权限。

可调用 `callClient.getDeviceManager()` 方法来访问 `deviceManager`。
> [!WARNING]
> 当前必须先实例化 `callAgent` 对象才能获得 DeviceManager 的访问权限

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>枚举本地设备

若要访问本地设备，可在设备管理器上使用枚举方法。 枚举是异步操作。

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>设置默认麦克风/扬声器

借助设备管理器，可设置发起通话时要使用的默认设备。
如果未设置客户端默认值，通信服务将回退到 OS 默认值。

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>本地相机预览

可使用 `DeviceManager` 和 `Renderer` 开始呈现来自本地相机的流。 此流不会发送给其他参与者；这是一项本地预览源。 这是异步操作。

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>请求使用相机/麦克风的权限

使用以下内容提示用户授予相机/麦克风权限：

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
这将异步解析，并通过一个对象指示是否已授予 `audio` 和 `video` 权限：
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>通话录制管理

通话录制是核心 `Call` API 的扩展功能。 首先需要获取录制功能 API 对象：

```js
const callRecordingApi = call.api(Features.Recording);
```

然后，若要检查是否正在录制通话，请检查 `callRecordingApi` 的 `isRecordingActive` 属性，它将返回 `Boolean`。

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

还可以订阅录制更改：

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>呼叫转移管理

呼叫转移是核心 `Call` API 的扩展功能。 首先需要获取转移功能 API 对象：

```js
const callTransferApi = call.api(Features.Transfer);
```

呼叫转移涉及三方：转接方、被转接方和转接目标  。 转接流的原理如下：

1. 转接方和被转接方之间已经存在接通的电话 
2. 转接方决定转接通话（被转接方  ->  转接目标  ）
3. 转接方调用 `transfer` API
4. 被转接方通过 `transferRequested` 事件决定是 `accept` 还是 `reject` 向转接目标转接的请求 。
5. 只有被转接方 `accept` 转接请求，转接目标才会收到来电 

### <a name="transfer-terminology"></a>转接术语

- 转接方 - 发起转接请求的用户
- 被转接方 - 由转接方转接给转接目标的用户
- 转接目标 - 要转接给的目标用户

若要转接当前通话，可以使用 `transfer` 同步 API。 `transfer` 可接受可选的 `TransferCallOptions`，因此你可以设置 `disableForwardingAndUnanswered` 标志：

- `disableForwardingAndUnanswered` = false - 如果转接目标未接听转接的电话，它将遵循转接目标转接且未接听设置 
- `disableForwardingAndUnanswered` = true - 如果转接目标未接听转接的电话，转接尝试将结束

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

转接允许订阅 `transferStateChanged` 和 `transferRequested` 事件。 `transferRequsted` 事件来自 `call` 实例，`transferStateChanged` 事件以及转移 `state` 和 `error` 来自 `transfer` 实例

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

被转接方可以通过 `transferRequestedEventArgs` 中的 `accept()` 或 `reject()` 来接受或拒绝转接方在 `transferRequested` 事件中发起的转接请求。 可以在 `transferRequestedEventArgs` 中访问 `targetParticipant` 信息、`accept` 和 `reject` 方法。

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>事件模型
必须检查当前值，并订阅更新事件以获取未来的值。

### <a name="properties"></a>属性

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>集合
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
