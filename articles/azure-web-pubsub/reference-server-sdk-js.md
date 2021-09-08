---
title: 参考 - 适用于 Azure Web PubSub 服务的 JavaScript SDK
description: 本参考文章介绍适用于 Azure Web PubSub 服务的 JavaScript SDK
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d507f29b184403cb9ef31d84111af60f75c1584
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113335"
---
# <a name="javascript-sdk-for-the-azure-web-pubsub-service"></a>适用于 Azure Web PubSub 服务的 JavaScript SDK

为 JavaScript 提供了 2 个库：
- [服务客户端库](#service-client-library)，可用于
    - 将消息发送到中心和组。
    - 将消息发送到特定用户和连接。
    - 将用户和连接组织到组中。
    - 关闭连接
    - 授予/撤销/检查现有连接的权限
- [Express 中间件](#express)，可用于处理传入的客户端事件
  - 处理滥用验证请求
  - 处理客户端事件请求

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>适用于 JavaScript 的 Azure Web PubSub 服务客户端库
使用该库可以：

- 将消息发送到中心和组。
- 将消息发送到特定用户和连接。
- 将用户和连接组织到组中。
- 关闭连接
- 授予/撤销/检查现有连接的权限

[源代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[包 (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API 参考文档](/javascript/api/@azure/web-pubsub/) |
[产品文档](https://aka.ms/awps/doc) |
[示例][samples_ref]

### <a name="getting-started"></a>入门

#### <a name="currently-supported-environments"></a>目前支持的环境

- [Node.js](https://nodejs.org/) 8.x.x 或更高版本

#### <a name="prerequisites"></a>先决条件

- 一个 [Azure 订阅][azure_sub]。
- 一个现有的 Azure Web PubSub 服务实例。

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. 安装 `@azure/web-pubsub` 包

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. 创建 WebPubSubServiceClient 并对其进行身份验证

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

还可以使用终结点和 `AzureKeyCredential` 对 `WebPubSubServiceClient` 进行身份验证：

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>关键概念

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>示例

#### <a name="broadcast-a-json-message-to-all-users"></a>向所有用户广播 JSON 消息

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>向所有用户广播纯文本消息

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>向所有用户广播二进制消息

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>疑难解答

#### <a name="enable-logs"></a>启用日志

使用此库时，可设置以下环境变量来获取调试日志。

- 从 SignalR 客户端库获取调试日志

```bash
export AZURE_LOG_LEVEL=verbose
```

有关如何启用日志的更详细说明，请查看 [@azure/logger 包文档](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)。

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>适用于 Express 的 Azure Web PubSub CloudEvents 处理程序

使用 Express 库可以：
- 添加 Web PubSub CloudEvents 中间件来处理传入的客户端事件
  - 处理滥用验证请求
  - 处理客户端事件请求

[源代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[包 (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API 参考文档](/javascript/api/@azure/web-pubsub-express/) |
[产品文档](https://aka.ms/awps/doc) |
[示例][samples_ref]

### <a name="getting-started"></a>入门

#### <a name="currently-supported-environments"></a>目前支持的环境

- [Node.js](https://nodejs.org/) 8.x.x 或更高版本
- [Express](https://expressjs.com/) 4.x.x 或更高版本

#### <a name="prerequisites"></a>先决条件

- 一个 [Azure 订阅][azure_sub]。
- 一个现有的 Azure Web PubSub 终结点。

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. 安装 `@azure/web-pubsub-express` 包

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. 创建 WebPubSubEventHandler

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>关键概念

#### <a name="client-events"></a>客户端事件

在客户端连接生命周期内会创建事件。 例如，某个简单的 WebSocket 客户端连接在尝试连接到服务时会创建 `connect` 事件，在成功连接到服务时会创建 `connected` 事件，在向服务发送消息时会创建 `message` 事件，在从服务断开连接时会创建 `disconnected` 事件。

#### <a name="event-handler"></a>事件处理程序

事件处理程序包含用于处理客户端事件的逻辑。 需要提前通过门户或 Azure CLI 在服务中注册和配置事件处理程序。 用于托管事件处理程序逻辑的位置通常被视为服务器端。

### <a name="troubleshooting"></a>疑难解答

#### <a name="dump-request"></a>转储请求

将 `dumpRequest` 设置为 `true` 可查看传入的请求。

#### <a name="live-trace"></a>实时跟踪

在 Web PubSub 服务门户中使用“实时跟踪”可查看实时流量。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
