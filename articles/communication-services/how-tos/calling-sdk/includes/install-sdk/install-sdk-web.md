---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a671eb00f2108b96220e72a32f8dd9f5654a4224
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698905"
---
## <a name="install-the-sdk"></a>安装 SDK

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务通话 SDK 和通用 SDK。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

## <a name="initialize-required-objects"></a>初始化所需的对象

大多数调用操作都需要 CallClient 实例。 让我们创建新的 `CallClient` 实例。 可使用自定义选项（例如 Logger 实例）对其进行配置。

有了 `CallClient` 实例后，你可以通过在 `CallClient` 实例上调用 `createCallAgent` 方法来创建 `CallAgent` 实例。 此方法将异步返回 `CallAgent` 实例对象。

`createCallAgent` 方法使用 `CommunicationTokenCredential` 作为参数。 它接受[用户访问令牌](../../../../quickstarts/access-tokens.md)。

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