---
title: 快速入门 - 从 Web 应用加入 Teams 会议
description: 本教程介绍如何使用适用于 JavaScript 的 Azure 通信服务通话 SDK 加入 Teams 会议
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5cc27cd600d8e8717a70deefc801b3f500792e11
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112535901"
---
本快速入门介绍如何使用适用于 JavaScript 的 Azure 通信服务通话 SDK 加入 Teams 会议。

## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-calling-to-teams-meeting) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件

- 正常工作的[通信服务通话 Web 应用](../../getting-started-with-calling.md)。
- [Teams 部署](/deployoffice/teams-install)。

## <a name="add-the-teams-ui-controls"></a>添加 Teams UI 控件

将 index.html 中的代码替换为以下代码片段。
文本框用于输入 Teams 会议上下文，按钮用于加入指定的会议：

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>启用 Teams UI 控件

将 client.js 文件中的内容替换为以下代码片段。

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>获取 Teams 会议链接

可以使用图形 API 来检索 Teams 会议链接。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)中对此进行了详细介绍。
通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下方获取。你还可以从 Teams 会议邀请信息的“加入会议”URL 中获取所需的会议信息。

## <a name="run-the-code"></a>运行代码

Webpack 用户可以使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 Web 服务器上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

将 Teams 上下文插入文本框，然后按“加入 Teams 会议”，从通信服务应用程序中加入 Teams 会议。
