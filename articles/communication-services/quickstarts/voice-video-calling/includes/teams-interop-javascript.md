---
title: 快速入门 - 加入 Teams 会议
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ab13e02c9154dbfc1bd28872f1bd6f993019a604
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915207"
---
## <a name="prerequisites"></a>必备条件

- 正在运行的[通信服务通话应用](../getting-started-with-calling.md)。
- [Teams 部署](/deployoffice/teams-install)。

## <a name="enable-teams-interoperability"></a>启用 Teams 互操作性

Teams 互操作性功能目前为个人预览版。 要为通信服务资源启用此功能，请向 [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) 发送电子邮件，并提供以下信息：

1. 包含通信服务资源的 Azure 订阅的订阅 ID。
2. 你的 Teams 租户 ID。 获取此信息的最简单方法是[获取并共享指向团队的链接](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team)。

你需要是拥有这两个实体的组织的成员才能使用此功能。

## <a name="add-the-teams-ui-controls"></a>添加 Teams UI 控件

在 HTML 中添加新的文本框和按钮。 文本框用于输入 Teams 会议上下文，按钮用于加入指定的会议：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>启用 Teams UI 控件

现在，我们可以将“加入 Teams 会议”按钮绑定到用于加入提供的 Teams 会议的代码：

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>获取会议上下文

可以使用图形 API 来检索 Teams 上下文。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)中对此进行了详细介绍。

还可以从会议邀请本身的“加入会议”URL 中获取所需的会议信息。

## <a name="run-the-code"></a>运行代码

Webpack 用户可以使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 Web 服务器上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

将 Teams 上下文插入文本框，然后按“加入 Teams 会议”，从通信服务应用程序中加入 Teams 会议。