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
ms.openlocfilehash: 70e49ef548515e842b3838c892570622c8fe950f
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113659571"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/AccessTokensQuickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 适用于操作系统的最新版本 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `AccessTokensQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o AccessTokensQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，使用 `dotnet add package` 命令安装适用于 .NET 包的 Azure 通信服务标识库。

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在文本编辑器中打开 Program.cs 文件
1. 添加 `using` 指令以包括 `Azure.Communication.Identity` 命名空间
1. 更新 `Main` 方法声明以支持异步代码

使用以下代码以开始执行以下操作：

```csharp
using System;
using Azure;
using Azure.Core;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串初始化 `CommunicationIdentityClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

将以下代码添加到 `Main` 方法中：

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

或者，也可以将终结点和访问密钥分开。
```csharp
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
string endpoint = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ENDPOINT");
string accessKey = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ACCESSKEY");
var client = new CommunicationIdentityClient(new Uri(endpoint), new AzureKeyCredential(accessKey));
```

如果采用 Azure Active Directory (AD) 应用程序设置，请参阅[使用服务主体](../identity/service-principal.md)，也可使用 AD 进行身份验证。
```csharp
TokenCredential tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(new Uri(endpoint), tokenCredential);
```

## <a name="create-an-identity"></a>创建标识

Azure 通信服务维护轻量级标识目录。 使用 `createUser` 方法在目录中创建具有唯一 `Id` 的新项。存储收到的标识，并映射到应用程序的用户。 例如，将它们存储在应用程序服务器的数据库中。 稍后颁发访问令牌时需要该标识。

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>颁发标识访问令牌

使用 `GetToken` 方法为已存在的通信服务标识颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。 参数 `communicationUser` 的新实例可以基于 Azure 通信服务标识的字符串表示形式构造。

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

访问令牌是短期凭据，需要重新颁发。 如果不重新颁发，可能会导致应用程序用户的体验中断。 `expiresOn` 响应属性指示访问令牌的生存期。

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>在同一请求中创建标识并颁发访问令牌

使用 `CreateUserAndTokenAsync` 方法创建通信服务标识并为其颁发访问令牌。 参数 `scopes` 定义一组基元，用于授权此访问令牌。 请参阅[受支持的操作列表](../../concepts/authentication.md)。

```csharp
// Issue an identity and an access token with the "voip" scope for the new identity
var identityAndTokenResponse = await client.CreateUserAndTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityAndTokenResponse.Value.User;
var token = identityAndTokenResponse.Value.AccessToken.Token;
var expiresOn = identityAndTokenResponse.Value.AccessToken.ExpiresOn;

Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

## <a name="refresh-access-tokens"></a>刷新访问令牌

若要刷新访问令牌，请将 `CommunicationUserIdentifier` 对象的实例传递到 `GetTokenAsync`。 如果已存储此 `Id` 并且需要新建 `CommunicationUserIdentifier`，可以通过将存储的 `Id` 传递到 `CommunicationUserIdentifier` 构造函数中来实现，如下所示：

```csharp
var identityToRefresh = new CommunicationUserIdentifier(identity.Id);
var tokenResponse = await client.GetTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>撤销访问令牌

在某些情况下可能会显式撤销访问令牌。 例如，当应用程序的用户更改在向服务进行身份验证时所使用的密码时。 `RevokeTokensAsync` 方法使颁发给标识的所有活动访问令牌无效。

```csharp
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>删除标识

删除标识将撤销所有活动访问令牌，并阻止你为标识颁发访问令牌。 它还会删除与标识关联的所有保存的内容。

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```
