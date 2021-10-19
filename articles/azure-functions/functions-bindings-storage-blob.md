---
title: 适用于 Azure Functions 的 Azure Blob 存储触发器和绑定
description: 了解如何在 Azure Functions 中使用 Azure Blob 存储触发器和绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 9ca3471b599e9f0671cd39603d71def344f7d72c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660488"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>适用于 Azure Functions 的 Azure Blob 存储绑定概述

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与 [Azure 存储](../storage/index.yml)集成。 与 Blob 存储集成即可构建函数，用于响应 blob 数据中的更改以及读取和写入值。

| 操作 | 类型 |
|---------|---------|
| 在 blob 存储数据更改时运行函数 | [触发器](./functions-bindings-storage-blob-trigger.md) |
| 在函数中读取 blob 存储数据 | [输入绑定](./functions-bindings-storage-blob-input.md) |
| 允许函数写入 blob 存储数据 |[输出绑定](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

#### <a name="storage-extension-5x-and-higher"></a>存储扩展 5.x 和更高版本

新版本的存储绑定扩展可用作[预览 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3)。 此预览版引入了[使用标识而不是机密进行连接](./functions-reference.md#configure-an-identity-based-connection)的功能。 对于 .NET 应用程序，它还会更改可以绑定到的类型，并将 `WindowsAzure.Storage` 和 `Microsoft.Azure.Storage` 中的类型替换为 [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) 中的新类型。 通过 [Azure.Storage.Blobs 迁移指南](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md)详细了解这些新类型的不同之处以及如何迁移它们。

> [!NOTE]
> 预览包不包括在扩展捆绑包中，必须手动安装。 对于 .NET 应用，请添加对包的引用。 对于所有其他应用类型，请参阅[更新扩展]。

[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[更新扩展]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 应用会自动引用 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 程序包（版本 2.x）。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍可用于此绑定函数的函数应用程序配置设置。 这些设置仅在使用[扩展版本 5.0.0 及更高版本](#storage-extension-5x-and-higher)时才适用。 下面的示例 host.json 文件仅包含此绑定的 2.x 版及更高版本设置。 若要详细了解版本 2.x 及更高版本中的函数应用程序配置设置，请参阅 [Azure Functions 的 host.json 参考](functions-host-json.md)。

> [!NOTE]
> 本部分不适用于 5.0.0 之前的扩展版本。 对于这些早期版本，没有任何适用于 Blob 的函数应用范围的配置设置。

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|maxDegreeOfParallelism|8 *（可用核心数）|每个 blob 触发器函数允许使用的并发调用数（整数）。 允许的最小值为 1。|

## <a name="next-steps"></a>后续步骤

- [在 blob 存储数据更改时运行函数](./functions-bindings-storage-blob-trigger.md)
- [函数运行时读取 blob 存储数据](./functions-bindings-storage-blob-input.md)
- [通过函数写入 blob 存储数据](./functions-bindings-storage-blob-output.md)
