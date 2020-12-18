---
title: 适用于 Azure Functions 的 Azure RabbitMQ 绑定
description: 了解如何在 Azure Functions 中发送 Azure RabbitMQ 触发器和绑定。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: a38015d9f7560930d77d5d50ac70dca5bcdde6a6
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672502"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions 概述的 RabbitMQ 绑定

> [!NOTE]
> 只有 **Windows 高级版和专用** 计划才完全支持 RabbitMQ 绑定。 当前不支持使用和 Linux。

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与[RabbitMQ](https://www.rabbitmq.com/)集成。 Azure Functions RabbitMQ extension 允许使用 RabbitMQ API 和函数来发送和接收消息。

| 操作 | 类型 |
|---------|---------|
| 当 RabbitMQ 消息进入队列时运行函数 | [触发器](./functions-bindings-rabbitmq-trigger.md) |
| 发送 RabbitMQ 消息 |[输出绑定](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

若要开始使用此扩展进行开发，请确保先 [设置 RabbitMQ 终结点](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)。 若要了解有关 RabbitMQ 的详细信息，请参阅 " [入门" 页](https://www.rabbitmq.com/getstarted.html)。

### <a name="functions-3x-and-higher"></a>函数1.x 和更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 4.x | |
| C # 脚本，Java，JavaScript，Python，PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>函数1.x 和2。x

函数1.x 和2.x 不支持 RabbitMQ 绑定扩展。 请使用函数1.x 和更高版本。

## <a name="next-steps"></a>后续步骤

- [ (触发器创建 RabbitMQ 消息时运行函数) ](./functions-bindings-rabbitmq-trigger.md)
- [从 Azure Functions (输出绑定发送 RabbitMQ 消息) ](./functions-bindings-rabbitmq-output.md)