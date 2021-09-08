---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 1bba08e751afa5e3cd5c51401ca92e3b5bd9def1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453668"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已部署的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../../access-tokens.md)。
- 可选：完成[向应用程序添加语音呼叫](../../getting-started-with-calling.md)的快速入门。

## <a name="install-the-sdk"></a>安装 SDK

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务通话 SDK 和通用 SDK。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
必须通过 `https` 使用通信服务 Web 通话 SDK。 对于本地开发，请使用 `localhost` 或本地“file:”

## <a name="documentation-support"></a>文档支持
- [发行说明](https://github.com/Azure/Communication/blob/master/releasenotes/acs-javascript-calling-library-release-notes.md)
- [在 github 上提交问题/错误](https://github.com/Azure/Communication/issues)
- [示例应用程序](../../../../samples/overview.md)
- [API 参考](/javascript/api/azure-communication-services/@azure/communication-calling/?preserve-view=true&view=azure-communication-services-js)

## <a name="models"></a>模型
### <a name="object-model"></a>对象模型

以下类和接口用于处理 Azure 通信服务通话 SDK 的某些主要功能：

| 名称                                | 说明                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                        | 通话 SDK 的主入口点。                                                                                                 |
| `AzureCommunicationTokenCredential` | 实现用于实例化 `callAgent` 的 `CommunicationTokenCredential` 接口。                                       |
| `CallAgent`                         | 用于启动和管理通话。                                                                                                          |
| `DeviceManager`                     | 用于管理媒体设备。                                                                                                            |
| `Call`                              | 用于表示通话                                                                                                              |
| `LocalVideoStream`                  | 用于为本地系统上的相机设备创建本地视频流。                                                          |
| `RemoteParticipant`                 | 用于表示通话中的远程参与者                                                                                   |
| `RemoteVideoStream`                 | 用于表示来自远程参与者的远程视频流。                                                                  |

> [!NOTE]
> 不应将通话 SDK 对象实例视为纯 JavaScript 对象。 这些是各种类的实际实例，因此无法序列化。

### <a name="events-model"></a>事件模型
通话 sdk 中的每个对象都有属性和集合值，这些值会在对象的整个生命周期中发生变化。
使用 `on()` 方法订阅对象的事件，使用 `off()` 方法取消订阅对象的事件。

#### <a name="properties"></a>属性
- 必须检查其初始值，并订阅 `'<property>Changed'` 事件，以便未来对值进行更新。

#### <a name="collections"></a>集合
- 必须检查其初始值，并订阅 `'<collection>Updated'` 事件，以便未来对值进行更新。
- `'<collection>Updated'` 事件的负载具有 `added` 数组，其中包含添加到集合的值。
- `'<collection>Updated'` 事件的负载还具有 `removed` 数组，其中包含从集合中删除的值。

```js
/*************************************
 * Example code - client.js          *
 * Convert this script into a        *
 * bundle.js that your html index    *
 * page can use.                     *
 *************************************/

// Make sure to install the necessary dependencies
const { CallClient, VideoStreamRenderer, LocalVideoStream } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential } = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the log level and output
setLogLevel('verbose');
AzureLogger.log = (...args) => {
    console.log(...args);
};

// Calling web sdk objects
let callAgent;
let deviceManager;
let call;
let incomingCall;
let localVideoStream;
let localVideoStreamRenderer;

// UI widgets
let userAccessToken = document.getElementById('user-access-token');
let calleeAcsUserId = document.getElementById('callee-acs-user-id');
let initializeCallAgentButton = document.getElementById('initialize-call-agent');
let startCallButton = document.getElementById('start-call-button');
let acceptCallButton = document.getElementById('accept-call-button');
let startVideoButton = document.getElementById('start-video-button');
let stopVideoButton = document.getElementById('stop-video-button');
let connectedLabel = document.getElementById('connectedLabel');
let remoteVideoContainer = document.getElementById('remoteVideoContainer');
let localVideoContainer = document.getElementById('localVideoContainer');

// Instantiate the Call Agent.
initializeCallAgentButton.onclick = async () => {
    try {
        const callClient = new CallClient(); 
        tokenCredential = new AzureCommunicationTokenCredential(userAccessToken.value.trim());
        callAgent = await callClient.createCallAgent(tokenCredential)
        // Set up a camera device to use.
        deviceManager = await callClient.getDeviceManager();
        await deviceManager.askDevicePermission({ video: true });
        await deviceManager.askDevicePermission({ audio: true });
        // Listen for an incoming call to accept.
        callAgent.on('incomingCall', async (args) => {
            try {
                incomingCall = args.incomingCall;
                acceptCallButton.disabled = false;
                startCallButton.disabled = true;
            } catch (error) {
                console.error(error);
            }
        });

        startCallButton.disabled = false;
        initializeCallAgentButton.disabled = true;
    } catch(error) {
        console.error(error);
    }
}

// Start an out-going call.
startCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = callAgent.startCall([{ communicationUserId: calleeAcsUserId.value.trim() }], { videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Accept the incoming call.
acceptCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = await incomingCall.accept({ videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a call obj.
// Listen for property changes and collection udpates.
subscribeToCall = (call) => {
    try {
        // Inspect the initial call.id value.
        console.log(`Call Id: ${call.id}`);
        //Subsribe to call's 'idChanged' event for value changes.
        call.on('idChanged', () => {
            console.log(`Call Id changed: ${call.id}`); 
        });

        // Inspect the initial call.state value.
        console.log(`Call state: ${call.state}`);
        // Subscribe to call's 'stateChanged' event for value changes.
        call.on('stateChanged', async () => {
            console.log(`Call state changed: ${call.state}`);
            if(call.state === 'Connected') {
                connectedLabel.hidden = false;
                acceptCallButton.disabled = true;
                startCallButton.disabled = true;
                startVideoButton.disabled = false;
                stopVideoButton.disabled = false
            } else if (call.state === 'Disconnected') {
                connectedLabel.hidden = true;
                startCallButton.disabled = false;
                console.log(`Call ended, call end reason={code=${call.callEndReason.code}, subCode=${call.callEndReason.subCode}}`);
            }   
        });

        call.localVideoStreams.forEach(async (lvs) => {
            localVideoStream = lvs;
            await displayLocalVideoStream();
        });
        call.on('localVideoStreamsUpdated', e => {
            e.added.forEach(async (lvs) => {
                localVideoStream = lvs;
                await displayLocalVideoStream();
            });
            e.removed.forEach(lvs => {
               removeLocalVideoStream();
            });
        });
        
        // Inspect the call's current remote participants and subscribe to them.
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        // Subscribe to the call's 'remoteParticipantsUpdated' event to be
        // notified when new participants are added to the call or removed from the call.
        call.on('remoteParticipantsUpdated', e => {
            // Subscribe to new remote participants that are added to the call.
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            // Unsubscribe from participants that are removed from the call
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant obj.
// Listen for property changes and collection udpates.
subscribeToRemoteParticipant = (remoteParticipant) => {
    try {
        // Inspect the initial remoteParticipant.state value.
        console.log(`Remote participant state: ${remoteParticipant.state}`);
        // Subscribe to remoteParticipant's 'stateChanged' event for value changes.
        remoteParticipant.on('stateChanged', () => {
            console.log(`Remote participant state changed: ${remoteParticipant.state}`);
        });

        // Inspect the remoteParticipants's current videoStreams and subscribe to them.
        remoteParticipant.videoStreams.forEach(remoteVideoStream => {
            subscribeToRemoteVideoStream(remoteVideoStream)
        });
        // Subscribe to the remoteParticipant's 'videoStreamsUpdated' event to be
        // notified when the remoteParticiapant adds new videoStreams and removes video streams.
        remoteParticipant.on('videoStreamsUpdated', e => {
            // Subscribe to new remote participant's video streams that were added.
            e.added.forEach(remoteVideoStream => {
                subscribeToRemoteVideoStream(remoteVideoStream)
            });
            // Unsubscribe from remote participant's video streams that were removed.
            e.removed.forEach(remoteVideoStream => {
                console.log('Remote participant video stream was removed.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant's remote video stream obj.
// Listen for property changes and collection udpates.
// When their remote video streams become available, display them in the UI.
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.hidden = false;
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}

// Start your local video stream.
// This will send your local video stream to remote participants so they can view it.
startVideoButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        await call.startVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Stop your local video stream.
// This will stop your local video stream from being sent to remote participants.
stopVideoButton.onclick = async () => {
    try {
        await call.stopVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Create a local video stream for your camera device
createLocalVideoStream = async () => {
    const camera = (await deviceManager.getCameras())[0];
    if (camera) {
        return new LocalVideoStream(camera);
    } else {
        console.error(`No camera device found on the system`);
    }
}

// Display your local video stream preview in your UI
displayLocalVideoStream = async () => {
    try {
        localVideoStreamRenderer = new VideoStreamRenderer(localVideoStream);
        const view = await localVideoStreamRenderer.createView();
        localVideoContainer.hidden = false;
        localVideoContainer.appendChild(view.target);
    } catch (error) {
        console.error(error);
    } 
}

// Remove your local video stream preview from your UI
removeLocalVideoStream = async() => {
    try {
        localVideoStreamRenderer.dispose();
        localVideoContainer.hidden = true;
    } catch (error) {
        console.error(error);
    } 
}
```

可使用从上述 JavaScript 示例 (`client.js`) 中生成的捆绑包的 HTML 示例代码。
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>Azure Communication Services - Calling Web SDK</title>
    </head>
    <body>
        <h4>Azure Communication Services - Calling Web SDK</h4>
        <input id="user-access-token"
            type="text"
            placeholder="User access token"
            style="margin-bottom:1em; width: 500px;"/>
        <button id="initialize-call-agent" type="button">Initialize Call Agent</button>
        <br>
        <br>
        <input id="callee-acs-user-id"
            type="text"
            placeholder="Enter callee's ACS user identity in format: '8:acs:resourceId_userId'"
            style="margin-bottom:1em; width: 500px; display: block;"/>
        <button id="start-call-button" type="button" disabled="true">Start Call</button>
        <button id="accept-call-button" type="button" disabled="true">Accept Call</button>
        <button id="start-video-button" type="button" disabled="true">Start Video</button>
        <button id="stop-video-button" type="button" disabled="true">Stop Video</button>
        <br>
        <br>
        <div id="connectedLabel" style="color: #13bb13;" hidden>Call is connected!</div>
        <br>
        <div id="remoteVideoContainer" style="width: 40%;" hidden>Remote participants' video streams:</div>
        <br>
        <div id="localVideoContainer" style="width: 30%;" hidden>Local video stream:</div>
        <!-- points to the bundle generated from client.js -->
        <script src="./bundle.js"></script>
    </body>
</html>
```
请注意，开始通话、加入通话或接受通话后，还可以使用 callAgent 的“callsUpdated”事件以接受来自新的 Call 对象的通知，并开始订阅该对象。
```js
callAgent.on('callsUpdated', e => {
    // New Call object is added to callAgent.calls collection
    e.added.forEach(call => {
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        call.on('remoteParticipantsUpdated', e => {
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    })
})
```

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>初始化 CallClient 实例，创建 CallAgent 实例，以及访问 deviceManager

创建新的 `CallClient` 实例。 可使用自定义选项（例如 Logger 实例）对其进行配置。

有了 `CallClient` 实例后，你可以通过在 `CallClient` 实例上调用 `createCallAgent` 方法来创建 `CallAgent` 实例。 此方法将异步返回 `CallAgent` 实例对象。

`createCallAgent` 方法使用 `CommunicationTokenCredential` 作为参数。 它接受[用户访问令牌](../../../access-tokens.md)。

可在 `CallClient` 实例上使用 `getDeviceManager` 方法来访问 `deviceManager`。

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the logger's log level
setLogLevel('verbose');
// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};
const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>拨打电话

若要创建并启动通话，请在 `callAgent` 上使用其中一个 API，并提供你通过通信服务标识 SDK 创建的用户。

创建和发起呼叫的操作是同步的。 可以通过 `call` 实例订阅通话事件。

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>向用户或 PSTN 发起一对 N 通话

若要呼叫另一个通信服务用户，请在 `callAgent` 上使用 `startCall` 方法，并传递你[使用通信服务管理库创建的](../../../access-tokens.md)接收人的 `CommunicationUserIdentifier`。

对于与用户之间的一对一通话，请使用以下代码：

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

若要向公共交换电话网络 (PSTN) 发起通话，请在 `callAgent` 上使用 `startCall` 方法并传递接收方的 `PhoneNumberIdentifier`。 必须将通信服务资源配置为允许 PSTN 通话。

呼叫 PSTN 号码时，需要指定备用呼叫方 ID。 备用呼叫方 ID 是 PSTN 通话中用于标识呼叫方的电话号码（基于 E.164 标准）。 这是通话接收方看到的来电的电话号码。

> [!NOTE]
> PSTN 通话目前提供个人预览版。 若要进行访问，[请申请早期采用者计划](https://aka.ms/ACS-EarlyAdopter)。

对于与 PSTN 号码之间的一对一通话，请使用以下代码：
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

对于与用户和 PSTN 号码之间的一对 N 通话，请使用以下代码：

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
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
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- 在通话接通后，它就会自动将来自所选相机的视频流发送给其他参与者。 这也适用于 `Call.Accept()` 视频选项和 `CallAgent.join()` 视频选项。


### <a name="join-a-group-call"></a>加入群组通话

> [!NOTE]
> `groupId` 参数被视为系统元数据，Microsoft 可能会使用它来执行运行系统所需的操作。 不要在 `groupId` 值中包含个人数据。 Microsoft 不会将此参数视为个人数据，其内容可能会显示给 Microsoft 员工，也可能会被长期存储。
>
> `groupId` 参数要求数据采用 GUID 格式。 建议使用随机生成的 GUID，这些 GUID 在系统中不会被视为个人数据。
>

若要发起新的群组通话或加入正在进行的群组通话，请使用 `join` 方法并传递带有 `groupId` 属性的对象。 `groupId` 值必须为 GUID。

```js

const context = { groupId: '<GUID>'}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>加入 Teams 会议
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

若要加入 Teams 会议，请使用 `join` 方法并传递会议链接或会议坐标。

使用会议链接加入：

```js
const locator = { meetingLink: '<MEETING_LINK>'}
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

开始/加入/接受视频通话时，如果指定的视频相机设备被另一个进程占用或在系统中被禁用，则通话将以视频关闭的形式开始，并且将引发 cameraStartFailed: true 通话诊断。

请参阅“通话诊断”部分，了解如何处理此通话诊断。

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
// or
await call.stopVideo(call.localVideoStreams[0]);
```
请注意，你可以在以下 4 个 api 中传递 localVideoStream 实例来启动通话中的视频：callAgent.startCall() api、callAgent.join() api、call.accept() api 和 call.startVideo() api。 对于 call.stopVideo() api，必须传递在这 4 个 api 中传递的同一 localVideoStream 实例。

对 `localVideoStream` 实例调用 `switchSource` 来发送视频时，可切换到不同的相机设备：

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

如果指定的视频设备被另一个进程占用或在系统中被禁用：
- 通话中，如果视频关闭并且你使用 call.startVideo() api 启动视频，则此 API 将引发 SourceUnavailableError 并将引发 cameraStartFiled: true 通话诊断。
- 调用 localVideoStream.switchSource() api 将引发 cameraStartFailed: true 通话诊断。
请参阅“通话诊断”部分，了解如何处理通话诊断。

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
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>删除通话参与者

若要从通话中删除参与者（用户或电话号码），可以调用 `removeParticipant`。 你必须传递 `Identifier` 类型之一。 从通话中删除参与者后，此方法异步进行解析。 还将从 `remoteParticipants` 集合中删除该参与者。

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
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
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
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
  await videoStreamRenderer.createView()
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

提示用户授予相机和/或麦克风权限：

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

这将使用一个对象进行解析，该对象指示是否授予了 `audio` 和 `video` 权限：

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>说明
- 插入/拔出视频设备时会触发“videoDevicesUpdated”事件。
- 插入音频设备时会触发“audioDevicesUpdated”事件
- 创建 DeviceManager 时，如果尚未授予权限，则开始时它无法识别出任何设备，因此最初其设备列表为空。 如果随后调用 DeviceManager.askPermission() API，则系统会提示用户进行设备访问，如果用户单击“允许”以授权访问，则设备管理器将识别出系统上的设备，更新其设备列表并发出“audioDevicesUpdated”和“videoDevicesUpdated”事件。 假设随后刷新页面并创建设备管理器，则因为用户之前已授权访问，所以设备管理器将能够识别出设备，因此开始时设备列表会填满，并且不会发出“audioDevicesUpdated”和“videoDevicesUpdated”事件。
- Android Cheome、iOS Safari 和 macOS Safari 都不支持扬声器枚举/选择项。

## <a name="call-feature-extensions"></a>通话功能扩展

### <a name="record-calls"></a>录制通话
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

### <a name="transfer-calls"></a>转移呼叫
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

### <a name="dominant-speakers-of-a-call"></a>通话中的主导说话人
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

通话中的主导说话人是核心 `Call` API 的扩展功能，可用于获取通话中的活跃说话人列表。 

这是一个排名列表，列表中的第一个元素表示通话中的最后一个活跃说话人，依次类推。

为获取通话中的主导说话人，首先需获取通话主导说话人功能 API 对象：

```js
const callDominantSpeakersApi = call.api(Features.CallDominantSpeakers);
```

然后，通过调用 `dominantSpeakers` 获取主导说话人列表。 其类型为 `DominantSpeakersInfo`，具有以下成员：

- `speakersList` 包含通话中已排名的主导说话人列表。 这些由其参与者 ID 表示。
- `timestamp` 是通话中的主导发言人的最新更新时间。

```js
let dominantSpeakers: DominantSpeakersInfo = callDominantSpeakersApi.dominantSpeakers;
```
此外，可以订阅 `dominantSpeakersChanged` 事件，了解主导说话人列表何时发生变化

```js
const dominantSpeakersChangedHandler = () => {
    // Get the most up to date list of dominant speakers
    let dominantSpeakers = callDominantSpeakersApi.dominantSpeakers;
};
callDominantSpeakersApi.api(Features.CallDominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);
``` 
#### <a name="handle-the-dominant-speakers-video-streams"></a>处理主导说话人的视频流

应用程序可以使用 `DominantSpeakers` 功能呈现一个或多个主导说话人的视频流，并在主导说话人列表更新时不断更新 UI。 这可以通过以下代码示例来实现。

```js
// RemoteParticipant obj representation of the dominant speaker
let dominantRemoteParticipant: RemoteParticipant;
// It is recommended to use a map to keep track of a stream's associated renderer
let streamRenderersMap: new Map<RemoteVideoStream, VideoStreamRenderer>();

function getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier) {
    let dominantRemoteParticipant: RemoteParticipant;
    switch(dominantSpeakerIdentifier.kind) {
        case 'communicationUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as CommunicationUserIdentifier).communicationUserId === dominantSpeakerIdentifier.communicationUserId
            });
            break;
        }
        case 'microsoftTeamsUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as MicrosoftTeamsUserIdentifier).microsoftTeamsUserId === dominantSpeakerIdentifier.microsoftTeamsUserId
            });
            break;
        }
        case 'unknown': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as UnknownIdentifier).id === dominantSpeakerIdentifier.id
            });
            break;
        }
    }
    return dominantRemoteParticipant;
}
// Handler function for when the dominant speaker changes
const dominantSpeakersChangedHandler = async () => {
    // Get the new dominant speaker's identifier
    const newDominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];

     if (newDominantSpeakerIdentifier) {
        // Get the remote participant object that matches newDominantSpeakerIdentifier
        const newDominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(newDominantSpeakerIdentifier);

        // Create the new dominant speaker's stream renderers
        const streamViews = [];
        for (const stream of newDominantRemoteParticipant.videoStreams) {
            if (stream.isAvailable && !streamRenderersMap.get(stream)) {
                const renderer = new VideoStreamRenderer(stream);
                streamRenderersMap.set(stream, renderer);
                const view = await videoStreamRenderer.createView();
                streamViews.push(view);
            }
        }

        // Remove the old dominant speaker's video streams by disposing of their associated renderers
        if (dominantRemoteParticipant) {
            for (const stream of dominantRemoteParticipant.videoStreams) {
                const renderer = streamRenderersMap.get(stream);
                if (renderer) {
                    streamRenderersMap.delete(stream);
                    renderer.dispose();
                }
            }
        }

        // Set the new dominant remote participant obj
        dominantRemoteParticipant = newDominantRemoteParticipant

        // Render the new dominant remote participant's streams
        for (const view of streamViewsToRender) {
            htmlElement.appendChild(view.target);
        }
     }
};

// When call is disconnected, set the dominant speaker to undefined
currentCall.on('stateChanged', () => {
    if (currentCall === 'Disconnected') {
        dominantRemoteParticipant = undefined;
    }
});

const dominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];
dominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier);
currentCall.api(Features.DominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);

subscribeToRemoteVideoStream = async (stream: RemoteVideoStream, participant: RemoteParticipant) {
    let renderer: VideoStreamRenderer;

    const displayVideo = async () => {
        renderer = new VideoStreamRenderer(stream);
        streamRenderersMap.set(stream, renderer);
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }

    stream.on('isAvailableChanged', async () => {
        if (dominantRemoteParticipant !== participant) {
            return;
        }

        renderer = streamRenderersMap.get(stream);
        if (stream.isAvailable && !renderer) {
            await displayVideo();
        } else {
            streamRenderersMap.delete(stream);
            renderer.dispose();
        }
    });

    if (dominantRemoteParticipant !== participant) {
        return;
    }

    renderer = streamRenderersMap.get(stream);
    if (stream.isAvailable && !renderer) {
        await displayVideo();
    }
}
```
### <a name="call-diagnostics"></a>通话诊断
通话诊断是核心 `Call` API 的扩展功能，可用于诊断活跃通话。

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

提供了以下面向用户的诊断：

| 类型    | 名称                           | 说明                                                     | 可能值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | 用例                                                                       |
| ------- | ------------------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| 网络 | noNetwork                      | 无可用网络。                                  | - 当通话因无可用网络而无法启动时，设为 `True`。 <br/> - 当存在 ICE 候选人时，设为 `False`。                                                                                                                                                                                                                                                                                                                                                      | 设备未连接到网络。                                           |
| 网络 | networkRelaysNotReachable      | 网络问题。                                        | - 当网络存在一些不允许访问 ACS 中继的限制时，设为 `True`。 <br/> - 进行新通话时，设为 `False`。                                                                                                                                                                                                                                                                                                                                                    | 在 WiFi 信号打开和关闭时的通话期间。                             |  |
| 网络 | networkReconnect               | 连接断开，我们正在重新连接到网络。 | - 当媒体传输连接断开时，设为 `Poor`                                                                                                                                 <br/> - 网络断开时，设为 `Bad` <br/> - 连接新会话时，设为 `Good`。                                                                                                                                                                                           | 低带宽、没有 Internet                                                      |
| 网络 | networkReceiveQuality          | 关于传入流质量的指标。                 | - 当接收流存在严重问题时，设为 `Bad`。 质量                                                                                                                           <br/> - 当接收流存在轻微问题时，设为 `Poor`。 质量                                                                                                                           <br/> - 当接收流不存在问题时，设为 `Good`。 | 低带宽                                                                   |
| 媒体   | noSpeakerDevicesEnumerated     | 用户系统上没有音频输出设备（扬声器）。 | - 当系统上没有扬声器设备并且支持扬声器选择项时，设为 `True`。 <br/> - 当系统上至少有 1 个扬声器设备并且支持扬声器选择项时，设为 `False`。                                                                                                                                                                                                                                                                                | 拔下所有扬声器                                                      |
| 媒体   | speakingWhileMicrophoneIsMuted | 静音时说话。                                   | - 当本地麦克风静音且本地用户正在说话时，设为 `True`。 <br/> - 当本地用户停止说话或取消麦克风静音时，设为 `False`。 <br/> * 请注意：到目前为止，Safari 尚不支持此功能，因为音频电平示例取自 webrtc. 统计信息。                                                                                                                                                                                                     | 在通话过程中，将麦克风静音并对其讲话。                          |
| 媒体   | noMicrophoneDevicesEnumerated  | 用户系统上没有音频捕获设备（麦克风）      | - 当系统上没有麦克风设备时，设为 `True`。 <br/> - 当系统上至少有 1 个麦克风设备时，设为 `False`。                                                                                                                                                                                                                                                                                                                                                 | 在通话期间，拔掉了所有麦克风。                                  |
| 媒体   | cameraFreeze                   | 相机停止生成帧，且时长超过 5 秒。          | - 当本地视频流处于冻结状态时，设为 `True`。 这意味着远程端会发现视频在其屏幕上被冻结，或意味着远程参与者没有在其屏幕上呈现视频。 <br/> - 当冻结结束并且用户可以照常观看视频时，设为 `False`。                                                                                                                                                                                         | 通话过程中相机断开连接或网络故障导致相机冻结。 |
| 媒体   | cameraStartFailed              | 一般相机故障。                                         | - 当因系统禁用相机设备或因其他进程占用相机设备而无法开始发送本地视频时，设为 `True`。 <br/> - 当选定的相机设备成功发送本地视频时，设为 `False`。 。                                                                                                                                                                                                                                           | 相机故障                                                                 |
| 媒体   | cameraStartTimedOut            | 相机处于故障状态的常见场景。                   | - 当相机设备超时并开始发送视频流时，设为 `True`。 <br/> - 当选定的相机设备再次成功发送本地视频时，设为 `False`。                                                                                                                                                                                                                                                                                                                                 | 相机故障                                                                 |
| 媒体   | microphoneNotFunctioning       | 麦克风无法正常工作。                                  | - 当因系统禁用麦克风设备或因其他进程占用麦克风设备而无法开始发送本地音频流时，设为 `True`。 引发此 UFD 需要大约 10 秒。 <br/> - 当麦克风再次开始成功发送音频流时，设为 `False`。                                                                                                                                                                                    | 无可用麦克风，系统中已禁用麦克风访问                |
| 媒体   | microphoneMuteUnexpectedly     | 麦克风静音                                             | - 当麦克风意外进入静音状态时，设为 `True`。 <br/> - 当麦克风开始成功发送音频流时，设为 `False`                                                                                                                                                                                                                                                                                                                                                     | 从系统将麦克风静音。                                            |
| 媒体   | screenshareRecordingDisabled   | “设置”中的首选项拒绝系统屏幕共享。     | - 当系统设置（共享）拒绝屏幕共享权限时，设为 `True`。 <br/> - 成功获取流时，设为 `False`。 <br/> 请注意：此诊断仅适用于 macOS.Chrome。                                                                                                                                                                                                                                                                                               | “设置”中禁用屏幕录制。                                       |
| 媒体   | microphonePermissionDenied     | 设备音量低或 macOS 上几乎无声。 | - 当系统设置（音频）拒绝音频权限时，设为 `True`。 <br/> - 成功获取流时，设为 `False`。 <br/> 请注意：此诊断仅适用于 macOS。                                                                                                                                                                                                                                                                                                                | “设置”中禁用麦克风权限。                            |
| 媒体   | cameraPermissionDenied         | “设置”中拒绝相机权限。                     | - 当系统设置（视频）拒绝相机权限时，设为 `True`。 <br/> - 成功获取流时，设为 `False`。 <br> 请注意：此诊断仅适用于 macOS Chrome                                                                                                                                                                                                                                                                                                          | “设置”中禁用相机权限。                                |

- 订阅 `diagnosticChanged` 事件，监视任何通话诊断何时发生更改。
```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` + 
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

- 获取引发的最新通话诊断值。 如果诊断未定义，则是因为从未引发该诊断。
```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();
    
console.log(`noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`);
            
console.log(`networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`);
            
console.log(`networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`);


const latestMediaDiagnostics = callDiagnostics.media.getLatest();
    
console.log(`speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`);
            
console.log(`cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`);
            
console.log(`microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`);
```
## <a name="releasing-resources"></a>释放资源
1. 如何在通话结束时正确地释放资源：
    - 通话结束时，SDK 将终止信号传递和媒体会话，保留一个保存通话最后状态的通话实例。 你仍可查看 callEndReason。 如果应用不保存对通话实例的引用，则 JavaScript 垃圾回收器将清除所有内容，因此在内存使用方面，应用应从通话前返回到初始状态。
2. 哪些资源类型是长期的（应用生命周期），哪些是短期的（通话生命周期）：
    - 以下资源被视为“长期”资源 - 可以创建它们并长期引用，它们对资源（内存）的消耗非常少，因此不会影响性能：
        - CallClient
        - CallAgent
        - DeviceManager
    - 以下资源被视为“短期”资源，它们在调用自身方面发挥着某些作用，可以向应用程序发出事件或与本地媒体设备交互。 它们会消耗更多的 cpu 和内存，但通话结束后，SDK 将清理所有状态并释放资源：
        - Call - 因为它会保存通话的实际状态（信号传递和媒体）。
        - RemoteParticipants - 表示通话中的远程参与者。
        - VideoStreamRenderer 及其 VideoStreamRendererViews - 处理视频渲染。
