---
title: 适用于 Azure Functions 的 Azure RabbitMQ 绑定
description: 了解如何在 Azure Functions 中发送 Azure RabbitMQ 触发器和绑定。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746518"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>适用于 Azure Functions 的 RabbitMQ 绑定概述

> [!NOTE]
> 仅高级和专用计划完全支持 RabbitMQ 绑定。 不支持消耗。

Azure Functions 通过[触发器和绑定](./functions-triggers-bindings.md)与 [RabbitMQ](https://www.rabbitmq.com/) 集成。 借助 Azure Functions RabbitMQ 扩展，你可以通过 Functions 使用 RabbitMQ API 来发送和接收消息。

| 操作 | 类型 |
|---------|---------|
| 当 RabbitMQ 消息进入队列时运行函数 | [触发器](./functions-bindings-rabbitmq-trigger.md) |
| 发送 RabbitMQ 消息 |[输出绑定](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>添加到 Functions 应用

若要开始使用此扩展进行开发，请确保先[设置 RabbitMQ 终结点](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)。 若要了解有关 RabbitMQ 的详细信息，请查看其[“入门”页](https://www.rabbitmq.com/getstarted.html)。

### <a name="functions-3x-and-higher"></a>Functions 3.x 及更高版本

使用触发器和绑定需要引用相应的包。 NuGet 包用于 .NET 类库，而扩展捆绑包用于其他所有应用程序类型。

| 语言                                        | 添加方式...                                   | 备注
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | 安装 [NuGet 包]版本 4.x | |
| C# 脚本、Java、JavaScript、Python、PowerShell | 注册[扩展捆绑包]          | 建议将 [Azure Tools 扩展]用于 Visual Studio Code。 |
| C# 脚本（Azure 门户中仅限联机）         | 添加绑定                            | 若要更新现有绑定扩展而不必重新发布函数应用，请参阅[更新扩展]。 |

[NuGet 包]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[扩展捆绑包]: ./functions-bindings-register.md#extension-bundles
[更新扩展]: ./functions-bindings-register.md
[Azure 工具扩展]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Functions 1.x 和 2.x

Functions 1.x 和 2.x 不支持 RabbitMQ 绑定扩展。 请使用 Functions 3.x 及更高版本。

## <a name="next-steps"></a>后续步骤

- [在创建 RabbitMQ 消息时运行函数（触发器）](./functions-bindings-rabbitmq-trigger.md)
- [从 Azure Functions 发送 RabbitMQ 消息（输出绑定）](./functions-bindings-rabbitmq-output.md)