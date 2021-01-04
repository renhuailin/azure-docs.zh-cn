---
title: 快速入门 - 加入 Teams 会议
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c72083f205fae77de366125e666cee479fd46805
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97691926"
---
## <a name="prerequisites"></a>必备条件

- 正在运行的[通信服务通话应用](../getting-started-with-calling.md)。
- [Teams 部署](/deployoffice/teams-install)。

## <a name="enable-teams-interoperability"></a>启用 Teams 互操作性

Teams 互操作性功能目前为个人预览版。 要为通信服务资源启用此功能，请向 [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) 发送电子邮件，并提供以下信息：

1. 包含通信服务资源的 Azure 订阅的订阅 ID。
2. 你的 Teams 租户 ID。 获取此信息的最简单方法是[获取并共享指向团队的链接](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f)。

你需要是拥有这两个实体的组织的成员才能使用此功能。

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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
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
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>获取 Teams 会议链接

可以使用图形 API 来检索 Teams 会议链接。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)中对此进行了详细介绍。
通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta)下方获取。你还可以从 Teams 会议邀请信息的“加入会议”URL 中获取所需的会议信息。

## <a name="run-the-code"></a>运行代码

Webpack 用户可以使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 Web 服务器上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

将 Teams 上下文插入文本框，然后按“加入 Teams 会议”，从通信服务应用程序中加入 Teams 会议。
