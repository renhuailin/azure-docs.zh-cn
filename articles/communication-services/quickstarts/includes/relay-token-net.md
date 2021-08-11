---
title: 包含文件
description: include 文件
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: df8298416c6acf159d9bdec13e5dfc50db54ed50
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "114471417"
---
### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `dotnet` 命令查看是否安装 .NET SDK。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `RelayTokenQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o RelayTokenQuickstart
```

1. 将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd RelayTokenQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，请使用 `dotnet add package` 命令安装适用于 .NET 包的 Azure 通信服务标识和 NetworkTraversal 库。

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.NetworkTraversal
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在文本编辑器中打开 Program.cs 文件
2. 添加 `using` 指令以包括 `Azure.Communication.Identity`、`System.Threading.Tasks` 和 `System.Net.Http`
3. 更新 `Main` 方法声明以支持异步代码

代码如下：

```csharp
using System;
using Azure.Communication.Identity;
using Azure;
using System.Collections.Generic;
using Azure.Communication.NetworkTraversal;

namespace RelayTokenQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Relay Token Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="authenticate-the-client"></a>验证客户端

使用连接字符串初始化 `CommunicationIdentityClient`。 下面的代码从环境变量中检索资源的连接字符串。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

将以下代码添加到 `Main` 方法中：

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>创建标识

Azure 通信服务维护轻量级标识目录。 使用 `createUser` 方法可在目录中创建具有唯一 `Id` 的新项。 存储收到的标识，并映射到应用程序的用户。 例如，将它们存储在应用程序服务器的数据库中。 稍后颁发访问令牌时需要该标识。

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>使用用户访问令牌交换中继令牌

调用 Azure 通信令牌服务以使用用户访问令牌交换 TURN 服务令牌

```csharp
var relayClient = new CommunicationRelayClient("COMMUNICATION_SERVICES_CONNECTION_STRING");

Response<CommunicationRelayConfiguration> turnTokenResponse = await relayClient.GetRelayConfigurationAsync(identity);
DateTimeOffset turnTokenExpiresOn = turnTokenResponse.Value.ExpiresOn;
IReadOnlyList<CommunicationTurnServer> turnServers = turnTokenResponse.Value.TurnServers;
Console.WriteLine($"Expires On: {turnTokenExpiresOn}");
foreach (CommunicationTurnServer turnServer in turnServers)
{
    foreach (string url in turnServer.Urls)
    {
        Console.WriteLine($"TURN Url: {url}");
    }
    Console.WriteLine($"TURN Username: {turnServer.Username}");
    Console.WriteLine($"TURN Credential: {turnServer.Credential}");
}
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```
