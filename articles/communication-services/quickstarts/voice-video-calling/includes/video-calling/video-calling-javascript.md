---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: e8a00161e9619a27b371ed80a61d7b3ea5d3e9c6
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112536091"
---
使用 Azure 通信服务呼叫 SDK 向应用添加一对一视频呼叫，开启 Azure 通信服务使用旅程。 你将了解如何使用适用于 JavaScript 的 Azure 通信服务呼叫 SDK 发起和接听视频呼叫。

## <a name="sample-code"></a>代码示例

如果你要向前跳转到末尾，可以从 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) 下载此快速入门示例。

## <a name="prerequisites"></a>必备条件
- 获取具有有效订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/en/) Active LTS 和 Maintenance LTS 版本（8.11.1 和 10.14.1）
- 创建活动的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md?pivots=platform-azp&tabs=windows)。
- 创建用于实例化呼叫客户端的用户访问令牌。 [了解如何创建和管理用户访问令牌](../../../access-tokens.md?pivots=programming-language-csharp)。

## <a name="setting-up"></a>设置
### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序
打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>安装包
使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫 SDK。

> [!IMPORTANT]
> 本快速入门使用 Azure 通信服务呼叫 SDK 版本 `1.1.0-beta.1`。 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling@1.1.0-beta.1 --save
```
### <a name="set-up-the-app-framework"></a>设置应用框架

此快速入门使用 webpack 捆绑应用程序资产。 运行以下命令以安装 `webpack`、`webpack-cli` 和 `webpack-dev-server` npm 包并在 `package.json` 中将其列作开发依赖项：

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
在项目的根目录中创建一个 index.html 文件。 我们将使用此文件来配置可让用户发起 1:1 视频呼叫的基本布局。

代码如下：
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

在名为 client.js 的项目的根目录中创建一个文件，以包含此快速入门的应用程序逻辑。 添加以下代码以导入呼叫客户端，并获取对 DOM 元素的引用。

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>对象模型

以下类和接口用于处理 Azure 通信服务通话 SDK 的某些主要功能：

| 名称      | 说明 | 
| :---        |    :----   |
| CallClient  | CallClient 是通话 SDK 的主入口点。      |
| CallAgent  | CallAgent 用于启动和管理呼叫。        |
| DeviceManager | DeviceManager 用于管理媒体设备。    |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 类实现用于实例化 CallAgent 的 CommunicationTokenCredential 接口。        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>对客户端进行身份验证并访问 DeviceManager

需要将 <USER_ACCESS_TOKEN> 替换为资源的有效用户访问令牌。 如果你还没有可用的令牌，请参阅[用户访问令牌文档](../../../access-tokens.md)。 

使用 `CallClient`，通过 `CommunicationUserCredential` 初始化 `CallAgent` 实例，这将使我们能够启动和接收呼叫。 

若要访问 `DeviceManager`，必须先创建 callAgent 实例。 然后，可以在 `CallClient` 实例上使用 `getDeviceManager` 方法来获取 `DeviceManager`。

将以下代码添加到 `client.js`：

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    // Receive an incoming call
    // To handle incoming calls you need to listen to the `incomingCall` event of `callAgent`. Once there is an incoming call, you need to enumerate local cameras and construct 
    // a `LocalVideoStream` instance to send a video stream to the other participant. You also need to subscribe to `remoteParticipants` to handle remote video streams. You can 
    // accept or reject the call through the `incomingCall` instance. 
    callAgent.on('incomingCall', async e => {
        const videoDevices = await deviceManager.getCameras();
        const videoDeviceInfo = videoDevices[0];
        localVideoStream = new LocalVideoStream(videoDeviceInfo);
        localVideoView();

        stopVideoButton.disabled = false;
        callButton.disabled = true;
        hangUpButton.disabled = false;

        const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
        call = addedCall;

        subscribeToRemoteParticipantInCall(addedCall);  
    });
    
    // Subscribe to call updates
    // You need to subscribe to the event when the remote participant ends the call to dispose of video renderers and toggle button states. 
    callAgent.on('callsUpdated', e => {
        e.removed.forEach(removedCall => {
            // dispose of video renders
            rendererLocal.dispose();
            rendererRemote.dispose();
            // toggle button states
            hangUpButton.disabled = true;
            callButton.disabled = false;
            stopVideoButton.disabled = true;
        })
    })

    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}

init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>向用户发起 1:1 传出视频呼叫

添加事件侦听器，以便在单击 `callButton` 时发起呼叫：

首先，必须使用 deviceManager `getCameraList` API 枚举本地相机。 本快速入门将使用集合中的第一个相机。 选择所需的相机后，就会构造一个 LocalVideoStream 实例，该实例在 `videoOptions` 中作为 localVideoStream 数组中的一个项传递给 call 方法。 呼叫接通后，会自动开始将视频流发送给其他参与者。 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
若要呈现 `LocalVideoStream`，需要创建 `VideoStreamRenderer` 的新实例，然后使用异步 `createView` 方法创建新的 `VideoStreamRendererView` 实例。 然后，可以将 `view.target` 附加到任何 UI 元素。 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
通过呼叫实例上的 `remoteParticipants` 集合提供所有远程参与者。 你需要侦听事件 `remoteParticipantsUpdated`，这样，当有新的远程参与者添加到呼叫中时，系统就会通知你。 你还需要循环访问 `remoteParticipants` 集合来订阅其中的每一项，以便订阅其视频流。 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
需要订阅 `videoStreamsUpdated` 事件以处理已添加的远程参与者的视频流。 可以在遍历当前呼叫的 `remoteParticipants` 集合时检查 `videoStreams` 集合以列出每个参与者的流。

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
必须订阅 `remoteVideoStream` 事件才能呈现 `isAvailableChanged`。 如果该 `isAvailable` 属性更改为 `true`，则远程参与者正在发送流。 每当远程流的可用性发生变化时，可以选择销毁整个 `Renderer`、特定的 `RendererView`，或将其保留，但这将导致显示空白的视频帧。
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
若要呈现 `RemoteVideoStream`，需要创建 `VideoStreamRenderer` 的新实例，然后使用异步 `createView` 方法创建新的 `VideoStreamRendererView` 实例。 然后，可以将 `view.target` 附加到任何 UI 元素。 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```

## <a name="end-the-current-call"></a>结束当前呼叫
添加事件侦听器，以便在单击 `hangUpButton` 时结束当前呼叫：
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```

## <a name="start-and-end-video-during-the-call"></a>在呼叫期间启动和结束视频
在当前呼叫期间，可以通过将事件侦听器添加到“停止视频”按钮来释放 `localVideoStream` 的呈现器，以此停止视频。 
 ```JavaScript
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
在当前呼叫期间，可将事件侦听器添加到“启动视频”按钮，以重新打开视频。 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>运行代码
使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 Web 服务器中捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../../media/javascript/1-on-1-video-calling.png" alt-text="1:1 视频呼叫页":::

可以通过在文本字段中提供用户 ID 并单击“开始呼叫”按钮，发起 1:1 传出视频呼叫。
