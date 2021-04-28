---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590064"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../access-tokens.md)。
- 可选：完成[向应用程序添加语音呼叫](../getting-started-with-calling.md)的快速入门。

## <a name="install-the-sdk"></a>安装 SDK

> [!NOTE]
> 本文档使用 ACS 通话 Web SDK。

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务通话 SDK 和通用 SDK。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>对象模型

以下类和接口用于处理 Azure 通信服务通话 SDK 的某些主要功能：

| 名称                             | 说明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | 通话 SDK 的主入口点。                                                                       |
| `CallAgent`                        | 用于启动和管理通话。                                                                                            |
| `DeviceManager`                    | 用于管理媒体设备。                                                                                           |
| `AzureCommunicationTokenCredential` | 实现用于实例化 `callAgent` 的 `CommunicationTokenCredential` 接口。 |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>初始化 CallClient 实例，创建 CallAgent 实例，以及访问 deviceManager

创建新的 `CallClient` 实例。 可使用自定义选项（例如 Logger 实例）对其进行配置。

有了 `CallClient` 实例后，你可以通过在 `CallClient` 实例上调用 `createCallAgent` 方法来创建 `CallAgent` 实例。 这将异步返回 `CallAgent` 实例对象。

`createCallAgent` 方法使用 `CommunicationTokenCredential` 作为参数。 它接受[用户访问令牌](../../access-tokens.md)。

可在 `CallClient` 实例上使用 `getDeviceManager` 方法来访问 `deviceManager`。

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>拨打电话

若要创建并启动通话，请在 `callAgent` 上使用其中一个 API，并提供你通过通信服务标识 SDK 创建的用户。

创建和发起呼叫的操作是同步的。 可以通过通话实例订阅通话事件。

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>向用户或 PSTN 发起一对 N 通话

若要呼叫另一个通信服务用户，请在 `callAgent` 上使用 `startCall` 方法，并传递你[使用通信服务管理库创建的](../../access-tokens.md)接收人的 `CommunicationUserIdentifier`。

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

若要向公共交换电话网络 (PSTN) 发起通话，请在 `callAgent` 上使用 `startCall` 方法并传递接收方的 `PhoneNumberIdentifier`。 必须将通信服务资源配置为允许 PSTN 通话。

呼叫 PSTN 号码时，需要指定备用呼叫方 ID。 备用呼叫方 ID 是 PSTN 通话中用于标识呼叫方的电话号码（基于 E.164 标准）。 这是通话接收方看到的来电的电话号码。

> [!NOTE]
> PSTN 通话目前提供个人预览版。 若要进行访问，[请申请早期采用者计划](https://aka.ms/ACS-EarlyAdopter)。

对于 1:1 通话，请使用以下代码：

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

对于 1:n 通话，请使用以下代码：

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>发起启用相机的一对一通话

> [!IMPORTANT]
> 当前最多只能有一个传出本地视频流。

若要发起视频通话，必须使用 `deviceManager` 中的 `getCameras()` 方法枚举本地相机。

选择相机后，请使用它来构造 `LocalVideoStream` 实例。 在 `videoOptions` 中将该实例作为 `localVideoStream` 数组中的项传递到 `startCall` 方法。


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

在通话接通后，它就会自动将来自所选相机的视频流发送给其他参与者。 这也适用于 `Call.Accept()` 视频选项和 `CallAgent.join()` 视频选项。

### <a name="join-a-group-call"></a>加入群组通话

> [!NOTE]
> `groupId` 参数被视为系统元数据，Microsoft 可能会使用它来执行运行系统所需的操作。 不要在 `groupId` 值中包含个人数据。 Microsoft 不会将此参数视为个人数据，其内容可能会显示给 Microsoft 员工，也可能会被长期存储。
>
> `groupId` 参数要求数据采用 GUID 格式。 建议使用随机生成的 GUID，这些 GUID 在系统中不会被视为个人数据。
>

若要发起新的群组通话或加入正在进行的群组通话，请使用 `join` 方法并传递带有 `groupId` 属性的对象。 `groupId` 值必须为 GUID。

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>加入 Teams 会议
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

若要加入 Teams 会议，请使用 `join` 方法并传递会议链接或会议坐标。

使用会议链接加入：

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

使用会议坐标加入：

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>接听来电

当登录的标识收到来电时，`callAgent` 实例会发出 `incomingCall` 事件。 若要侦听此事件，请使用以下选项之一进行订阅：

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` 事件包括你可以接受或拒绝的一个 `incomingCall` 实例。

## <a name="manage-calls"></a>管理通话

在通话期间，你可以访问通话属性并管理视频和音频设置。

### <a name="check-call-properties"></a>检查通话属性

获取通话的唯一 ID（字符串）：

   ```js
    const callId: string = call.id;
   ```
获取通话信息：
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本
   ```js
   const callInfo = call.info;
   ```

通过检查“call”实例上的 `remoteParticipants` 集合了解通话中的其他参与者：

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

识别来电的呼叫方：

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` 是 `CommunicationIdentifier` 类型之一。

获取通话状态：

   ```js
   const callState = call.state;
   ```

   这会返回一个表示当前通话状态的字符串：

  - `None`：初始通话状态。
  - `Connecting`：拨打或接听电话后的初始过渡状态。
  - `Ringing`：对于去电，表示远程参与者的电话正在响铃。 在远程参与者端，它是 `Incoming`。
  - `EarlyMedia`：表示在接通电话前播放通知的状态。
  - `Connected`：指示通话已连接。
  - `LocalHold`：指示通话被本地参与者暂停。 本地终结点与远程参与者之间没有媒体流动。
  - `RemoteHold`：指示通话被远程参与者暂停。 本地终结点与远程参与者之间没有媒体流动。
  - `InLobby`：指示用户位于会议厅中。
  - `Disconnecting`：在通话进入 `Disconnected` 状态之前的过渡状态。
  - `Disconnected`：最终通话状态。 如果网络连接断开，则两分钟后状态将变为 `Disconnected`。

检查 `callEndReason` 属性来查明通话结束的原因：

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

通过检查 `direction` 属性了解当前通话是来电还是去电。 它将返回 `CallDirection`。

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

检查当前麦克风是否已静音。 它将返回 `Boolean`。

   ```js
   const muted = call.isMuted;
   ```

通过检查 `isScreenSharingOn` 属性了解屏幕共享流是否来自给定终结点。 它将返回 `Boolean`。

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

通过查看 `localVideoStreams` 集合检查活动视频流。 它返回 `LocalVideoStream` 对象。

   ```js
   const localVideoStreams = call.localVideoStreams;
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

若要开始发送视频，必须在 `deviceManager` 对象上使用 `getCameras` 方法来枚举相机。 接下来使用所需相机创建一个新的 `LocalVideoStream` 实例，然后将 `LocalVideoStream` 对象传递给 `startVideo` 方法：

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

当你成功开始发送视频后，系统会将一个 `LocalVideoStream` 实例添加到通话实例上的 `localVideoStreams` 集合。

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
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>管理远程参与者

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过通话实例上的 `remoteParticipants` 集合获得。

### <a name="list-the-participants-in-a-call"></a>列出通话参与者

`remoteParticipants` 集合返回通话中远程参与者的列表：

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>访问远程参与者属性

远程参与者有一组关联的属性和集合：

- `CommunicationIdentifier`：获取远程参与者的标识符。 标识是 `CommunicationIdentifier` 类型之一：

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  它可能是下列 `CommunicationIdentifier` 类型之一：

  - `{ communicationUserId: '<ACS_USER_ID'> }`：一个对象，表示 ACS 用户。
  - `{ phoneNumber: '<E.164>' }`：一个对象，表示采用 E.164 格式的电话号码。
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`：一个对象，表示 Teams 用户。
  - `{ id: string }`：一个对象，表示不属于其他标识符类型的标识符

- `state`：获取远程参与者的状态。

  ```js
  const state = remoteParticipant.state;
  ```

  此状态可能是：

  - `Idle`：初始状态。
  - `Connecting`：参与者正在连接到通话时的过渡状态。
  - `Ringing`：参与者电话正在响铃。
  - `Connected`：参与者已连接到通话。
  - `Hold`：参与者已暂停通话。
  - `EarlyMedia`：在参与者连接到通话之前播放的通知。
  - `InLobby`：指示远程参与者位于会议厅中。
  - `Disconnected`：最终状态。 参与者已断开通话连接。 如果远程参与者断开了其网络连接，则两分钟后其状态将变为 `Disconnected`。

- `callEndReason`：若要了解参与者退出通话的原因，请检查 `callEndReason` 属性：

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` 状态：若要了解远程参与者是否已静音，请检查 `isMuted` 属性。 它将返回 `Boolean`。

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` 状态：若要了解远程参与者是否正在讲话，请检查 `isSpeaking` 属性。 它将返回 `Boolean`。

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`：若要检查给定参与者在此通话中发送的所有视频流，请检查 `videoStreams` 集合。 它包含 `RemoteVideoStream` 对象。

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`：若要获取此远程参与者的显示名称，请检查 `displayName` 属性。它将返回字符串。 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>向通话添加参与者

若要向通话添加参与者（用户或电话号码），可以使用 `addParticipant`。 提供 `Identifier` 类型之一。 它将同步返回 `remoteParticipant` 实例。 当参与者成功添加到通话中时，通话中将引发 `remoteParticipantsUpdated` 事件。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>删除通话参与者

若要从通话中删除参与者（用户或电话号码），可以调用 `removeParticipant`。 你必须传递 `Identifier` 类型之一。 从通话中删除参与者后，就会以异步方式解决此问题。 还将从 `remoteParticipants` 集合中删除该参与者。

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

若要呈现 `RemoteVideoStream`，你必须订阅它的 `isAvailableChanged` 事件。 如果该 `isAvailable` 属性更改为 `true`，则远程参与者正在发送流。 发生该情况后，请创建一个新的 `VideoStreamRenderer` 实例，然后使用异步 `createView` 方法创建一个新的 `VideoStreamRendererView` 实例。  然后，可以将 `view.target` 附加到任何 UI 元素。

每当远程流的可用性发生变化时，可以选择销毁整个 `VideoStreamRenderer`、特定的 `VideoStreamRendererView`，或将其保留，但这将导致显示空白的视频帧。

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>远程视频流属性

远程视频流具有以下属性：

- `id`：远程视频流的 ID。

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`：可以是 `Video` 或 `ScreenSharing`。

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`：指示远程参与者终结点是否正在主动发送流。

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer 方法和属性
创建一个 `VideoStreamRendererView` 实例，该实例可以附加到应用程序 UI 中来呈现远程视频流；使用异步 `createView()` 方法，该方法在流准备好呈现时进行解析，并返回一个具有 `target` 属性（表示 `video` 元素）的对象，该对象可以附加到 DOM 树中的任何位置

  ```js
  videoStreamRenderer.createView()
  ```

处置 `videoStreamRenderer` 和所有关联的 `VideoStreamRendererView` 实例：

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView 方法和属性

创建 `VideoStreamRendererView` 时，你可以指定 `scalingMode` 和 `isMirrored` 属性。 `scalingMode` 可以是 `Stretch`、`Crop` 或 `Fit`。 如果指定了 `isMirrored`，则呈现的流会垂直翻转。

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

每个 `VideoStreamRendererView` 实例都有一个表示呈现图面的 `target` 属性。 在应用程序 UI 中附加此属性：

```js
htmlElement.appendChild(view.target);
```

可以调用 `updateScalingMode` 方法来更新 `scalingMode`：

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>设备管理

在 `deviceManager` 中，你可以枚举可在通话中传输音频和视频流的本地设备。 还可使用它来请求访问本地设备的麦克风和相机的权限。

可以调用 `callClient.getDeviceManager()` 方法来访问 `deviceManager`：

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>获取本地设备

若要访问本地设备，可在 `deviceManager` 上使用枚举方法。 枚举是异步操作

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>设置默认麦克风和扬声器

在 `deviceManager` 中，你可以设置将用来启动通话的默认设备。 如果未设置客户端默认值，则通信服务会使用操作系统默认值。

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>本地相机预览

可使用 `deviceManager` 和 `VideoStreamRenderer` 开始呈现来自本地相机的流。 此流不会发送给其他参与者；这是一项本地预览源。

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>请求对相机和麦克风的权限

提示用户授予相机和麦克风权限：

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

这将使用一个对象进行解析，该对象指示是否授予了 `audio` 和 `video` 权限：

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>录制通话
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

通话录制是核心 `Call` API 的扩展功能。 首先需要获取录制功能 API 对象：

```js
const callRecordingApi = call.api(Features.Recording);
```

然后，若要检查是否正在录制通话，请检查 `callRecordingApi` 的 `isRecordingActive` 属性。 它将返回 `Boolean`。

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

## <a name="transfer-calls"></a>转移呼叫
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

呼叫转移是核心 `Call` API 的扩展功能。 首先需要获取转移功能 API 对象：

```js
const callTransferApi = call.api(Features.Transfer);
```

呼叫转移涉及三个参与方：

- 转移方：发起转移请求的人员。
- 被转移方：被转移的人员。
- 转移目标：被转移到的人员。

转移遵循以下步骤：

1. 转移方和被转移方之间已经存在接通的电话。  转移方决定将呼叫从被转移方转移到转移目标。
1. 转移方调用 `transfer` API。
1. 被转移方通过使用 `transferRequested` 事件来决定是 `accept` 还是 `reject` 到转移目标的转移请求。
1. 只有当被转移方接受转移请求时，转移目标才会收到来电。

若要转移当前呼叫，可以使用 `transfer` API。 `transfer` 接受可选的 `transferCallOptions`，这允许你设置 `disableForwardingAndUnanswered` 标志：

- `disableForwardingAndUnanswered = false`：如果转移目标未接听转移的呼叫，则转移方将遵循转移目标的转发设置和未接听设置。 
- `disableForwardingAndUnanswered = true`：如果转移目标未接听转移的呼叫，则转移尝试结束。

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer` API 允许你订阅 `transferStateChanged` 和 `transferRequested` 事件。 `transferRequested` 事件来自 `call` 实例；`transferStateChanged` 事件与转移 `state` 和 `error` 来自 `transfer` 实例。

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

被转移方可以通过在 `transferRequestedEventArgs` 中使用 `accept()` 或 `reject()` 来接受或拒绝转移方在 `transferRequested` 事件中发起的转移请求。  你可以在 `transferRequestedEventArgs` 中访问 `targetParticipant` 信息和 `accept` 或 `reject` 方法。

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

## <a name="learn-about-eventing-models"></a>了解事件模型

检查当前值，并订阅更新事件以获取将来的值。

### <a name="properties"></a>属性

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>集合

```js
// Inspect the current collection
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
