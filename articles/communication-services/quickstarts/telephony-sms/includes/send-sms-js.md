---
title: 包含文件
description: include 文件
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 321e9ae3ccfb994a324b653c3e67fbae9b6b6f4c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111950"
---
通过使用通信服务 JavaScript 短信 SDK 发送短信，开始使用 Azure 通信服务。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/send-sms) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `node --version` 以查看是否安装了 Node.js。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

首先，打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir sms-quickstart && cd sms-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

使用文本编辑器在项目根目录中创建名为“send-sms.js”的文件。 将在以下部分中将此快速入门的所有源代码添加到此文件。

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务短信 SDK。

```console
npm install @azure/communication-sms --save
```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

## <a name="object-model"></a>对象模型

以下类和接口用于处理适用于 Node.js 的 Azure 通信服务短信 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 所有短信功能都需要此类。 使用订阅信息对其进行实例化，然后使用它发送短信。 |
| SmsSendRequest | 此接口是用于生成短信请求的模型（例如 配置收件方和发件方的电话号码和短信内容）。 |
| SmsSendOptions | 此接口提供用于配置传送报告的选项。 如果 `enableDeliveryReport` 设置为 `true`，系统会在传送成功后发出事件。 |
| SmsSendResult               | 此类包含来自短信服务的结果。                                          |

## <a name="authenticate-the-client"></a>验证客户端

从 SDK 导入 SmsClient，并通过连接字符串将其实例化。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。

创建并打开一个名为 send-sms.js 的文件，并添加以下代码：

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>发送 1:N 短信

若要将短信发送给收件人列表，请使用收件人电话号码列表从 SmsClient 调用 `send` 函数（如果要向单个收件人发送消息，则列表中只能包含一个号码）。 将此代码添加到 send-sms.js 的末尾：

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
你应将 `<from-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，并将 `<to-phone-number-1>` 和 `<to-phone-number-2>` 替换为要向其发送消息的电话号码

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

## <a name="send-a-1n-sms-message-with-options"></a>发送包含选项的 1: N 短信

你还可以传入选项对象，一个目的是指定是否应启用传送报告，另一个目的是设置自定义标记。

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

你应将 `<from-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，并将 `<to-phone-number-1>` 和 `<to-phone-number-2>` 替换为要向其发送消息的电话号码

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

`enableDeliveryReport` 参数是一个可选参数，可用于配置传送报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。
`tag` 为可选参数，可用于将标记应用到传送报告。

## <a name="run-the-code"></a>运行代码

使用 `node` 命令运行添加到 send-sms.js 文件中的代码。

```console

node ./send-sms.js

```
