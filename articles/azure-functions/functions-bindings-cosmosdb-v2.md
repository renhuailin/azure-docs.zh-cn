---
title: 适用于 Functions 2.x 及更高版本的 Azure Cosmos DB 绑定
description: 了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.openlocfilehash: 5d3513ea7c7e44afde70765bed668c93cf4096bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582594"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>适用于 Azure Functions 2.x 及更高版本的 Azure Cosmos DB 触发器和绑定概述

> [!div class="op_single_selector" title1="选择要使用的 Azure Functions 运行时的版本： "]
> * [版本 1](functions-bindings-cosmosdb.md)
> * [版本 2 及更高版本](functions-bindings-cosmosdb-v2.md)

此系列文章介绍如何在 Azure Functions 2.x 及更高版本中使用 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 绑定。 Azure Functions 支持 Azure Cosmos DB 的触发器、输入和输出绑定。

| 操作 | 类型 |
|---------|---------|
| 创建或修改 Azure Cosmos DB 文档时运行函数 | [触发器](./functions-bindings-cosmosdb-v2-trigger.md) |
| 读取 Azure Cosmos DB 文档 | [输入绑定](./functions-bindings-cosmosdb-v2-input.md) |
| 保存对 Azure Cosmos DB 文档的更改  |[输出绑定](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 此参考适用于 [Azure Functions 2.x 及更高版本](functions-versions.md)。  若要了解如何在 Functions 1.x 中使用这些绑定，请参阅[适用于 Azure Functions 1.x 的 Azure Cosmos DB 绑定](functions-bindings-cosmosdb.md)。
>
> 此绑定最初名为 DocumentDB。 在 Functions 2.x 及更高版本中，触发器、绑定和包均称为 Cosmos DB。

## <a name="supported-apis"></a>受支持的 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="cosmos-db-extension-4x-and-higher"></a>Cosmos DB 扩展 4.x 及更高版本

新版本的 Cosmos DB 绑定扩展可用作[预览版 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB/4.0.0-preview1)。 此预览版引入了[使用标识而不是机密进行连接](./functions-reference.md#configure-an-identity-based-connection)的功能。 对于 .NET 应用程序，它还会更改你可绑定到的类型，并将 v2 SDK `Microsoft.Azure.DocumentDB` 中的类型替换为 v3 SDK [Microsoft.Azure.Cosmos](../cosmos-db/sql/sql-api-sdk-dotnet-standard.md) 中更新的类型。 通过 [SDK 迁移指南](../cosmos-db/sql/migrate-dotnet-v3.md)、[触发器](./functions-bindings-cosmosdb-v2-trigger.md)、[输入绑定](./functions-bindings-cosmosdb-v2-input.md)和[输出绑定](./functions-bindings-cosmosdb-v2-output.md)示例，详细了解这些新类型在哪些方面有变化以及如何迁移它们。

> [!NOTE]
> 目前，仅可在 Elastic Premium 计划中使用 4.x 预览扩展通过标识而非机密进行身份验证。 

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| CosmosDB | [CosmosDB 错误代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍 Azure Functions 版本 2.x 中可用于此绑定的全局配置设置。 若要详细了解 Azure Functions 版本 2.x 中的全局配置设置，请查看 [Azure Functions 版本 2.x 的 host.json 参考](functions-host-json.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|属性  |默认 |说明 |
|----------|--------|------------|
|GatewayMode|网关|连接到 Azure Cosmos DB 服务时该函数使用的连接模式。 选项为 `Direct` 和 `Gateway`|
|协议|Https|连接到 Azure Cosmos DB 服务时该函数使用的连接协议。 请参阅[此处，了解两种模式的说明](../cosmos-db/performance-tips.md#networking)。 <br><br> 此设置在 [4.x 版扩展](#cosmos-db-extension-4x-and-higher)中不可用。 |
|leasePrefix|不适用|应用中所有函数要使用的租用前缀。 <br><br> 此设置在 [4.x 版扩展](#cosmos-db-extension-4x-and-higher)中不可用。|

## <a name="next-steps"></a>后续步骤

- [创建或修改 Azure Cosmos DB 文档时运行函数（触发器）](./functions-bindings-cosmosdb-v2-trigger.md)
- [读取 Azure Cosmos DB 文档（输入绑定）](./functions-bindings-cosmosdb-v2-input.md)
- [保存对 Azure Cosmos DB 文档的更改（输出绑定）](./functions-bindings-cosmosdb-v2-output.md)