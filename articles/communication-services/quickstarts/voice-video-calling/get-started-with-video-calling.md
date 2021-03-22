---
title: 快速入门 - 在应用中添加视频呼叫 (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: 本快速入门介绍如何使用 Azure 通信服务在应用中添加视频呼叫功能。
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 82f4d9028fa94d4df0ff089fda213d64e13d56ec
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487864"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>快速入门：在应用中添加 1:1 视频呼叫 (JavaScript)

## <a name="download-code"></a>下载代码

在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件
- 获取具有有效订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/en/) Active LTS 和 Maintenance LTS 版本（8.11.1 和 10.14.1）
- 创建活动的通信服务资源。 [创建通信服务资源](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp)。
- 创建用于实例化呼叫客户端的用户访问令牌。 [了解如何创建和管理用户访问令牌](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-csharp)。

## <a name="setting-up"></a>设置
### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序
打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>安装包
使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫客户端库。

本快速入门使用了 Azure 通信呼叫客户端库 `1.0.0.beta-6`。 

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
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
import { CallClient, CallAgent, Renderer, LocalVideoStream } from "@azure/communication-calling";
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

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称      | 说明 | 
| :---        |    :----   |
| CallClient  | CallClient 是呼叫客户端库的主入口点。      |
| CallAgent  | CallAgent 用于启动和管理呼叫。        |
| DeviceManager | DeviceManager 用于管理媒体设备。    |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 类实现用于实例化 CallAgent 的 CommunicationTokenCredential 接口。        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>对客户端进行身份验证并访问 DeviceManager

需将 <USER_ACCESS_TOKEN> 替换为资源的有效用户访问令牌。 如果还没有可用的令牌，请参阅用户访问令牌文档。 使用 CallClient 通过 CommunicationUserCredential 初始化一个 CallAgent 实例，该实例使我们能够拨打和接听电话。 若要访问 DeviceManager，必须先创建 callAgent 实例。 然后，可以在 `CallClient` 实例上使用 `getDeviceManager` 方法来获取 `DeviceManager`。

将以下代码添加到 `client.js`：

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>向用户发起 1:1 传出视频呼叫

添加事件侦听器，以便在单击 `callButton` 时发起呼叫：

首先必须使用 deviceManager getCameraList API 枚举本地相机。 本快速入门将使用集合中的第一个相机。 选择所需的相机后，将在 videoOptions 内部构造一个 LocalVideoStream 实例，并将其作为呼叫方法的 localVideoStream 数组中的项传入。 呼叫接通后，会自动开始将视频流发送给其他参与者。 

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
若要呈现 `LocalVideoStream`，需要创建 `Renderer` 的新实例，然后使用异步 `createView` 方法创建新的 RendererView 实例。 然后，可以将 `view.target` 附加到任何 UI 元素。 

```JavaScript
async function localVideoView() {
    rendererLocal = new Renderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
通过呼叫实例上的 `remoteParticipants` 集合提供所有远程参与者。 需要订阅当前呼叫的远程参与者，并侦听事件 `remoteParticipantsUpdated` 以订阅添加的远程参与者。

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.remoteParticipants.forEach( p => {
        subscribeToRemoteParticipant(p);
    })
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToRemoteParticipant(p);
        })
    });   
}
```
可以订阅当前呼叫的 `remoteParticipants` 集合，并检查 `videoStreams` 集合以列出每个参与者的流。 还需要订阅 remoteParticipantsUpdated 事件以处理添加的远程参与者。 

```JavaScript
function subscribeToRemoteParticipant(remoteParticipant) {
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
}
```
必须订阅 `remoteVideoStream` 事件才能呈现 `isAvailableChanged`。 如果该 `isAvailable` 属性更改为 `true`，则远程参与者正在发送流。 每当远程流的可用性发生变化时，可以选择销毁整个 `Renderer`、特定的 `RendererView`，或将其保留，但这将导致显示空白的视频帧。
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
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
若要呈现 `RemoteVideoStream`，需要创建 `Renderer` 的新实例，然后使用异步 `createView` 方法创建新的 `RendererView` 实例。 然后，可以将 `view.target` 附加到任何 UI 元素。 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new Renderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>接听来电
若要处理来电，需要侦听 `callAgent` 的 `incomingCall` 事件。 接到来电后，需要枚举本地相机并构造一个 `LocalVideoStream` 实例，以将视频流发送到其他参与者。 还需要订阅 `remoteParticipants` 以处理远程视频流。 可以通过 `incomingCall` 实例接受或拒绝呼叫。 

将实现置于 `init()` 中可处理来电。 

```JavaScript
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
## <a name="subscribe-to-call-updates"></a>订阅呼叫更新
当远程参与者结束呼叫时，你需要订阅该事件，以释放视频呈现器和切换按钮状态。 

将实现置于 init() 中可订阅 `callsUpdated` 事件。 
 ```JavaScript 
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
使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 webserver 上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1:1 视频呼叫页":::

可以通过在文本字段中提供用户 ID 并单击“开始呼叫”按钮，发起 1:1 传出视频呼叫。 

## <a name="sample-code"></a>代码示例
可以从 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) 下载示例应用。

## <a name="clean-up-resources"></a>清理资源
如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下文章：
- 查看[网络呼叫示例](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- 了解[呼叫客户端库功能](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- 了解有关[呼叫工作原理](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)的详细信息
