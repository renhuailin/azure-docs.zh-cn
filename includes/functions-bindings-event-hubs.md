---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623503"
---
## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[扩展捆绑包]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[更新扩展]: ../articles/azure-functions/functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>事件中心扩展 5.x 和更高版本

新版本的事件中心绑定扩展可用作[预览 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1)。 此预览版引入了[使用标识而不是机密进行连接](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection)的功能。 对于 .NET 应用程序，它还会更改可以绑定到的类型，并将 `Microsoft.Azure.EventHubs` 的类型替换为 [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs) 中的新类型。

> [!NOTE]
> 预览包不包括在扩展捆绑包中，必须手动安装。 对于 .NET 应用，请添加对包的引用。 对于所有其他应用类型，请参阅[更新扩展]。

[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

## <a name="hostjson-settings"></a>host.json 设置
<a name="host-json"></a>

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。 配置因 Azure Functions 版本而异。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]