---
title: Azure Functions SignalR 服务绑定
description: 了解如何将 Azure Functions 与 SignalR 服务绑定配合使用。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 34460fb0076c4cb666242d996add913cc2b74d22
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108632"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions 的 SignalR Service 绑定

此系列文章介绍如何在 Azure Functions 中使用 SignalR 服务绑定进行身份验证，并向连接到 [Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service/)的客户端发送实时消息。 Azure Functions 支持 SignalR 服务的输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 处理来自 SignalR 服务的消息 | [触发器绑定](./functions-bindings-signalr-service-trigger.md) |
| 返回服务终结点 URL 和访问令牌 | [输入绑定](./functions-bindings-signalr-service-input.md) |
| 发送 SignalR 服务消息 |[输出绑定](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

若要详细了解如何配置 SignalR 服务并将其与 Azure Functions 一起使用，请参阅[通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](../azure-signalr/signalr-concept-serverless-development-config.md)。

### <a name="annotations-library-java-only"></a>注释库（仅限 Java）

若要在 Java 函数中使用 SignalR 服务注释，需将对 azure-functions-java-library-signalr 项目（1.0 或更高版本）的依赖项添加到 pom.xml 文件。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="connection-string-settings"></a>连接字符串设置

将 `AzureSignalRConnectionString` 密钥添加到 host.json 文件中，该文件使用连接字符串指向应用程序设置。 对于本地开发，此值可能存在于 local.settings.json 文件中。

## <a name="next-steps"></a>后续步骤

- [处理来自 SignalR 服务的消息（触发绑定）](./functions-bindings-signalr-service-trigger.md)
- [返回服务终结点 URL 和访问令牌（输入绑定）](./functions-bindings-signalr-service-input.md)
- [发送 SignalR 服务消息（输出绑定）](./functions-bindings-signalr-service-output.md)