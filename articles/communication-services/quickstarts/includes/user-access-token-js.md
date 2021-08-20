---
title: 包含文件
description: include 文件
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 60de092a25350add4dc5b216bb7d079b866cc1fd
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113659496"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/access-tokens-quickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务标识 SDK。

```console

npm install @azure/communication-identity --save

```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

## <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在代码编辑器中打开新文本文件
1. 添加 `require` 调用以加载 `CommunicationIdentityClient`
1. 为程序创建结构，包括基本的异常处理

使用以下代码以开始执行以下操作：

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. 在 access-tokens-quickstart 目录中将新文件另存为 issue-access-token.js。

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 `CommunicationIdentityClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

将以下代码添加到 `main` 方法中：

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

或者，也可以将终结点和访问密钥分开。
```javascript
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const accessKey = process.env["COMMUNICATION_SERVICES_ACCESSKEY"];
const tokenCredential = new AzureKeyCredential(accessKey);
// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential)
```

如果采用 Azure Active Directory 应用程序设置，请参阅[使用服务主体](../identity/service-principal.md)，也可使用 AD 进行身份验证。
```javascript
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const tokenCredential = new DefaultAzureCredential();
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>创建标识

Azure 通信服务维护轻量级标识目录。 使用 `createUser` 方法可在目录中创建具有唯一 `Id` 的新项。 存储收到的标识，并映射到应用程序的用户。 例如，将它们存储在应用程序服务器的数据库中。 稍后颁发访问令牌时需要该标识。

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>颁发访问令牌

使用 `getToken` 方法为已存在的通信服务标识颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。 参数 `communicationUser` 的新实例可以基于 Azure 通信服务标识的字符串表示形式构造。

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

访问令牌是短期凭据，需要重新颁发。 如果不重新颁发，可能会导致应用程序用户的体验中断。 `expiresOn` 响应属性指示访问令牌的生存期。

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>在同一请求中创建标识并颁发访问令牌

使用 `createUserAndToken` 方法创建通信服务标识并为其颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await identityClient.createUserAndToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>刷新访问令牌

刷新访问令牌与调用 `getToken` 一样简单，使用的标识与用于颁发令牌的相同。 还需要提供已刷新令牌的 `scopes`。

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>撤销访问令牌

在某些情况下可能会显式撤销访问令牌。 例如，当应用程序的用户更改在向服务进行身份验证时所使用的密码时。 `revokeTokens` 方法使颁发给标识的所有活动访问令牌无效。

```javascript
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>删除标识

删除标识将撤销所有活动访问令牌，并阻止你为标识颁发访问令牌。 它还会删除与标识关联的所有保存的内容。

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>运行代码

在控制台提示符下，导航到包含 issue-access-token.js 文件的目录，然后执行以下 `node` 命令来运行应用。

```console
node ./issue-access-token.js
```
