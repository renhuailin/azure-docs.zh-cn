---
title: Azure Functions 概述
description: 了解 Azure Functions 如何帮助构建可靠的无服务器应用。
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8562acbf0b7dc6cac0ef03e6d0ca41691f752cdf
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729789"
---
# <a name="introduction-to-azure-functions"></a>Azure Functions 简介

Azure Functions 是一种无服务器解决方案，可以使用户减少代码编写、减少需要维护的基础结构并节省成本。 无需担心部署和维护服务器，云基础结构提供保持应用程序运行所需的所有最新资源。

你只需专注于对你最重要的代码，Azure Functions 处理其余代码。<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

我们通常会构建系统来应对一系列的关键事件。 无论是要构建 Web API、响应数据库更改还是处理 IoT 数据流，甚或是管理消息队列，每个应用程序都需要一种在执行这些操作时运行某些代码的方式。

为了满足此需求，Azure Functions 通过两种重要方式提供了“按需计算”。

首先，通过 Azure Functions 可将系统的逻辑实现为可直接使用的代码块。 这些代码块称为“函数”。 当需要对关键事件作出响应时，可以随时运行不同的函数。

其次，当请求增加时，Azure Functions 可以根据需要（但仅在需要时）提供尽可能多的资源和函数实例以满足需求。 当请求减少时，所有额外的资源和应用程序实例都会自动减少。

所有的计算资源来自何处？ Azure Functions [根据需要提供任意数量的计算资源](./functions-scale.md)以满足应用程序的需求。

按需提供计算资源是 Azure Functions 中[无服务器计算](https://azure.microsoft.com/solutions/serverless/)最根本的特性。

## <a name="scenarios"></a>方案

在许多情况下，函数[与一系列云服务相集成](./functions-triggers-bindings.md)，可提供功能丰富的实现。

以下是一组常见 _但并非详尽的_ Azure Functions 方案集。

| 若希望... | 然后... |
| --- | --- |
| **生成 Web API** | 使用 [HTTP 触发器](./functions-bindings-http-webhook.md)为 Web 应用程序实现终结点 |
| **处理文件上传** | 在 [blob 存储](./functions-bindings-storage-blob.md)中上传或更改文件时运行代码 |
| **构建无服务器工作流** | 使用[持久函数](./durable/durable-functions-overview.md)将一系列函数链接在一起 |
| **响应数据库更改** | 在 [Cosmos DB](./functions-bindings-cosmosdb-v2.md) 中创建或更新文档时运行自定义逻辑 |
| **运行计划任务** | 按照[预定义的时间间隔](./functions-bindings-timer.md)执行代码 |
| **创建可靠消息队列系统** | 使用[队列存储](./functions-bindings-storage-queue.md)、[服务总线](./functions-bindings-service-bus.md)或[事件中心](./functions-bindings-event-hubs.md)来处理消息队列 |
| **分析 IoT 数据流** | 收集并处理[来自 IoT 设备的数据](./functions-bindings-event-iot.md) |
| **实时处理数据** | 使用 [Functions 和 SignalR](./functions-bindings-signalr-service.md) 即时响应数据 |

生成函数时，可以使用以下选项和资源：

- **使用首选语言**：使用 [C#、Java、JavaScript、PowerShell 或 Python](./supported-languages.md) 编写函数，或通过 [自定义处理程序](./functions-custom-handlers.md)来利用几乎所有其他语言。

- **自动进行部署**：从基于工具的方法到使用外部管道，有 [多种部署选项](./functions-deployment-technologies.md)可供选择。

- **对函数进行故障排除**：使用 [监视工具](./functions-monitoring.md)和 [测试策略](./functions-test-a-function.md)来深入了解应用。

- **灵活定价选项**：使用 [消耗](./pricing.md)计划，只需在运行函数时付费，而 [高级](./pricing.md)和 [应用服务](./pricing.md)计划则提供满足特定需求的功能。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过课程、示例和交互式教程开始入门](./functions-get-started.md)
