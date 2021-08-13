---
title: 包含文件
description: include 文件
services: azure-communication-services
author: orwatson
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: orwatson
ms.openlocfilehash: 8ad763ed5f0213569682599aaa0fc3cba4651939
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292413"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) Active LTS 和 Maintenance LTS 版本。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。
- 在 Azure 门户中创建 [Application Insights 资源](../../../azure-monitor/app/create-new-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-app"></a>新建 Node.js 应用

如果你已经有一个使用 ACS 库的应用并且能够熟练操作 JavaScript，可以跳转到[设置跟踪程序](#setup-tracer)。

若要开始，需要准备好一个使用我们任一客户端库的 JS 应用。 我们将使用 [@azure/communication-identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/communication/communication-identity) 库来创建应用。 按照以下步骤设置示例应用：

打开终端或命令窗口，创建一个新目录，并导航到该目录。

```console
mkdir export-js-telemetry && cd export-js-telemetry
```
运行 `npm init -y` 以使用默认设置创建 package.json。

```console
npm init -y
```

### <a name="install-dependencies"></a>安装依赖项

使用 `npm install` 命令安装应用将要使用的依赖项。

```bash
npm install @azure/communication-identity @azure/monitor-opentelemetry-exporter @opentelemetry/node@^0.14.0 @opentelemetry/plugins-node-core --save
```

`--save` 选项将库添加为上一步骤创建的 package.json 文件中的依赖项。

请务必不要忘记 `@opentelemetry/node@^0.14.0` 的具体版本。 在编写本指南时，它是该包的最新版本，与 `@azure/monitor-opentelemetry-exporter` 完全兼容。

### <a name="add-main-app-file"></a>添加主应用文件

在根目录中创建名为 token.js 的文件。 向新文件添加以下代码：

```javascript
// token.js

const { CommunicationIdentityClient } = require("@azure/communication-identity");

async function main() {
  const ACS_CONNECTION_STRING = "<your-acs-connection-string>"
  const client = new CommunicationIdentityClient(ACS_CONNECTION_STRING);

  console.log("Creating user...")
  const user = await client.createUser();
  console.log(`User ${user.communicationUserId} was created successfully.\n`);

  console.log(`Issuing token for ${user.communicationUserId}...`);
  const { token } = await client.getToken(user, ["chat"]);
  console.log("Token issued successfully.\n")
  console.log(`${token}\n`);

  console.log(`Revoking token for ${user.communicationUserId}...`);
  await client.revokeTokens(user);
  console.log("Token revoked successfully.\n");

  console.log(`Deleting user ${user.communicationUserId}...`);
  await client.deleteUser(user);
  console.log("User deleted successfully.\n");
}

main().catch((error) => {
  console.log("Encountered an error:", error);
  process.exit(1);
});
```
## <a name="setup-tracer"></a>设置跟踪程序

在根目录中创建名为 tracing.js 的文件。 向新文件添加以下代码：

```javascript
// tracing.js

const azureSdkTracing = require("@azure/core-tracing");
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { NodeTracerProvider } = require("@opentelemetry/node");
const { BatchSpanProcessor } = require("@opentelemetry/tracing");

const AI_CONNECTION_STRING = "<your-application-insights-connection-string>";
const provider = new NodeTracerProvider();
const azExporter = new AzureMonitorTraceExporter({
  connectionString: AI_CONNECTION_STRING
});

provider.addSpanProcessor(
  new BatchSpanProcessor(azExporter, {
    bufferSize: 1000, // 1000 spans
    bufferTimeout: 5000 // 5 seconds
  })
);

provider.register();

const tracer = provider.getTracer("sample tracer");
azureSdkTracing.setTracer(tracer);

exports.default = tracer;
```

### <a name="add-tracer-to-main-app"></a>将跟踪程序添加到主应用

对 token.js 进行以下更新：

需要 tracing.js：

```javascript
const { CommunicationIdentityClient } = require("@azure/communication-identity");
const tracer = require("./tracing.js");

async function main() {
```

创建根范围并导出数据

```javascript
  console.log("User deleted successfully.\n");
}

const rootSpan = tracer.startSpan("Root Identity Span");
tracer.withSpan(rootSpan, async function() {
  try {
    await main();
  } catch (error) {
    console.error("Error running sample:", error);
  } finally {
    // End the optional root span on completion
    rootSpan.end();
  }
}).then(function() {
  console.log("Awaiting batched span processor to export batched spans...");

  setTimeout(function() {
    console.log("Spans exported.");
  }, 6000);
});
```

## <a name="run-the-code"></a>运行代码

请务必将占位符文本替换为有效的连接字符串值。

使用 node 命令运行添加到 token.js 文件中的代码。

```console
node token.js
```