---
title: 适用于 Azure Functions 的 Azure 队列存储触发器和绑定概述
description: 了解如何在 Azure Functions 中使用 Azure 队列存储触发器和输出绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 252a3316ae14e17c2712547f088477d74da2c66e
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658667"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>适用于 Azure Functions 的 Azure 队列存储触发器和绑定概述

Azure Functions 可以在创建新的 Azure 队列存储消息时运行，并可在函数中写入队列消息。

| 操作 | 类型 |
|---------|---------|
| 在队列存储数据更改时运行函数 | [触发器](./functions-bindings-storage-queue-trigger.md) |
| 写入队列存储消息 |[输出绑定](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 3.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

#### <a name="storage-extension-5x-and-higher"></a>存储扩展 5.x 和更高版本

新版本的存储绑定扩展可用作[预览 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3)。 此预览版引入了[使用标识而不是机密进行连接](./functions-reference.md#configure-an-identity-based-connection)的功能。 对于 .NET 应用程序，它还会更改可以绑定到的类型，并将 `WindowsAzure.Storage` 和 `Microsoft.Azure.Storage` 中的类型替换为 [Azure.Storage.Queues](/dotnet/api/azure.storage.queues) 中的新类型。

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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|maxPollingInterval|00:01:00|队列轮询的最大间隔时间。 最小间隔为 00:00:00.100（100 毫秒）。 间隔最多可递增到 `maxPollingInterval`。 `maxPollingInterval` 的默认值为 00:01:00（1 分钟）。 `maxPollingInterval` 不得小于 00:00:00.100（100 毫秒）。 在 Functions 2.x 及更高版本中，数据类型为 `TimeSpan`。 在 Functions 1.x 中，它以毫秒为单位。|
|visibilityTimeout|00:00:00|消息处理失败时的重试间隔时间。 |
|batchSize|16|Functions 运行时同时检索并并行处理的队列消息数。 当处理的数量下降到 `newBatchThreshold` 时，运行时可获取另一个批，并开始处理这些消息。 因此，每个函数处理的最大并发消息数是 `batchSize` 加上 `newBatchThreshold`。 此限制分别应用于各个队列触发的函数。 <br><br>如果要避免对队列上收到的消息并行执行，可以将 `batchSize` 设置为 1。 但是，只有在函数应用仅于单个虚拟机 (VM) 上运行时，此设置才可消除并发。 如果函数应用横向扩展到多个 VM，每个 VM 可运行每个队列触发的函数的一个实例。<br><br>`batchSize` 的最大值为 32。 |
|maxDequeueCount|5|在将某个消息移到有害队列之前，尝试处理该消息的次数。|
|newBatchThreshold|N*batchSize/2|只要同时处理的消息数下降到此数值，运行时即检索另一个批次。<br><br>`N` 表示在应用服务或高级版计划上运行时可用的 vCPU 数目。 对于消耗计划，其值为 `1`。|
|messageEncoding|base64| 此设置仅适用于[扩展版本 5.0.0 和更高版本](#storage-extension-5x-and-higher)。 它表示消息的编码格式。 有效值为 `base64` 和 `none`。|

## <a name="next-steps"></a>后续步骤

- [在队列存储数据更改时运行函数（触发器）](./functions-bindings-storage-queue-trigger.md)
- [向队列存储写入消息（输出绑定）](./functions-bindings-storage-queue-output.md)
