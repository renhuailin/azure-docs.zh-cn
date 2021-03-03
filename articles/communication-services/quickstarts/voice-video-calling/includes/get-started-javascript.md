---
title: 快速入门 - 使用 Azure 通信服务向 Web 应用添加 VOIP 呼叫
description: 本教程介绍如何使用适用于 JavaScript 的 Azure 通信服务呼叫客户端库
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d27a79e180a0219773a3094fb85f842773d75183
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656598"
---
本快速入门将介绍如何使用适用于 JavaScript 的 Azure 通信服务呼叫客户端库开始呼叫。
本文档引用 1.0.0-beta.5 版通话库中的类型。

> [!NOTE]
> 本文档使用 1.0.0-beta.6 版的通话客户端库。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 活动的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 用于实例化呼叫客户端的用户访问令牌。 了解如何[创建和管理用户访问令牌](../../access-tokens.md)。


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

代码如下：

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
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

在名为 client.js 的项目的根目录中创建一个文件，以包含此快速入门的应用程序逻辑。 添加以下代码以导入呼叫客户端，并获取对 DOM 元素的引用，以便我们可以附加业务逻辑。 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>对象模型

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称                             | 说明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient 是呼叫客户端库的主入口点。                                                                       |
| CallAgent                        | CallAgent 用于启动和管理呼叫。                                                                                            |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 类实现用于实例化 CallAgent 的 CommunicationTokenCredential 接口。 |


## <a name="authenticate-the-client"></a>验证客户端

需要将 `<USER_ACCESS_TOKEN>` 替换为资源的有效用户访问令牌。 如果还没有可用的令牌，请参阅[用户访问令牌](../../access-tokens.md)文档。 使用 `CallClient`，通过 `CommunicationTokenCredential` 初始化 `CallAgent` 实例，这将使我们能够启动和接收呼叫。 将下面的代码添加到 client.js：

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>开始呼叫

添加事件处理程序，以便在单击 `callButton` 时启动呼叫：

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>结束呼叫

添加事件侦听器，以便在单击 `hangUpButton` 时结束当前呼叫：

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone` 属性结束所有呼叫参与者的呼叫。

## <a name="run-the-code"></a>运行代码

使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 webserver 上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

可以通过在“文本”字段中提供用户 ID 并单击“开始呼叫”按钮，启动出站 VOIP 呼叫。 呼叫 `8:echo123` 会将你连接到回显机器人，这对于入门和验证音频设备是否正常运行非常有用。
