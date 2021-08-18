---
title: include 文件
description: include 文件
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 38a2f3cd5f1b391b651c89801e4ab3abe92ed448
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255328"
---
## <a name="what-is-a-replication-task"></a>什么是复制任务？

复制任务是从源接收事件并将其转发到目标。
大多数复制任务会不作任何更改地转发事件，如果源协议和目标协议不同，最多会在元数据结构之间执行映射。 

复制任务通常是无状态的，这意味着它们在按顺序或并行执行任务中不会共享状态或其他附带后果。 批处理和链接也是如此，它们都可在流的现有状态的基础上实现。 

这使得复制任务不同于聚合任务，聚合任务通常是有状态的，属于分析框架和服务（如 [Azure 流分析](../articles/stream-analytics/stream-analytics-introduction.md)）的领域。

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Functions 中的复制应用程序和任务

在 Azure Functions 中，实现复制任务需要两个条件，一个是[触发器](../articles/azure-functions/functions-triggers-bindings.md)，用于从已配置的源获取一个或多个输入消息，另一个是[输出绑定](../articles/azure-functions/functions-triggers-bindings.md#binding-direction)，用于将从源复制的消息转发到配置的目标。 

| 触发器  | 输出 |
|----------|--------|
| [Azure 事件中心触发器](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure 事件中心输出绑定](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure 服务总线触发器](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure 服务总线输出绑定](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Azure IoT 中心触发器](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT 中心输出绑定](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Azure 事件网格触发器](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure 事件网格输出绑定](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Azure 队列存储触发器](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure 队列存储输出绑定](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Apache Kafka 触发器](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka 输出绑定](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ 触发器](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ 输出绑定](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure 通知中心输出绑定](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Azure SignalR 服务输出绑定](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Twilio SendGrid 输出绑定](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

复制任务通过与任何其他 Azure Functions 应用程序相同的部署方法部署为复制应用程序。 可以将多个任务配置到同一个应用程序中。 

使用 Azure Functions Premium，多个复制应用程序可以共享相同的基础资源池（称为应用服务计划）。 这意味着可以轻松地并置复制任务，例如将用 .NET 编写的复制任务与用 Java 编写的复制任务并置。 如果你想利用仅适用于 Java 的特定库（例如 Apache Camel），并且这些库是特定集成路径的最佳选项，但你通常更喜欢使用其他语言和运行时来完成其他复制任务时，这一点就很重要了。 

在任何可用的情况下，都应优先使用面向批处理的触发器，而不是传递单个事件或消息的触发器，并且应始终获取完整的事件或消息结构，而不是依赖于 Azure 函数的[参数绑定表达式](../articles/azure-functions/functions-bindings-expressions-patterns.md)。

函数的名称应反映正在连接的源和目标对，你应该在应用程序配置文件中的连接字符串或其他配置元素的引用前加上该名称。 

### <a name="data-and-metadata-mapping"></a>数据和元数据映射

确定一对输入触发器和输出绑定后，在不同的事件类型或消息类型之间必须执行映射，除非触发器的类型和输出是相同的。

对于在事件中心和服务总线之间复制消息的简单复制任务，你无需自己编写代码，可以依靠提供了复制示例的[实用工具库](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)。

### <a name="retry-policy"></a>重试策略

为了避免在可用性事件期间在复制函数的任何一侧丢失数据，需要配置重试策略以获得可靠性。 请参阅[有关重试的 Azure Functions 文档](../articles/azure-functions/functions-bindings-error-pages.md)，以配置重试策略。 

为[样本存储库](https://github.com/Azure-Samples/azure-messaging-replication-dotnet)中的示例项目选择的策略设置配置了指数退避策略，重试间隔从 5 秒到 15 分钟，并无限重试，以避免数据丢失。 

对于服务总线，请查看[“在触发器复原能力的基础上使用重试支持”](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience)部分，以了解触发器的交互和为队列定义的最大传递计数。

### <a name="setting-up-a-replication-application-host"></a>设置复制应用程序主机

复制应用程序是一个或多个复制任务的执行主机。 

它是一个 Azure Functions 应用程序，配置为按消耗计划或 Azure Functions 高级计划（推荐）运行。 所有复制应用程序都必须在[系统或用户分配的托管标识](../articles/app-service/overview-managed-identity.md)下运行。 

链接的 Azure 资源管理器 (ARM) 模板使用以下项创建和配置复制应用程序：

* 用于跟踪复制进度和日志的 Azure 存储帐户、
* 系统分配的托管标识，以及 
* 用于监视的 Azure 监视和 Application Insights 集成。

事件中心如果必须访问绑定到 Azure 虚拟网络 (VNet) 的事件中心，则必须使用 Azure Functions 高级计划并配置为附加到同一个 VNet，这也是可用选项之一。


|       | 部署 | 可视化  
|-------|------------------|--------------|---------------|
| **Azure Functions 消耗计划** | [![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)|[![可视化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions 高级计划** |[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![可视化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **VNet 的 Azure Functions 高级计划** | [![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![可视化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>示例

[样本存储库](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/)包含多个在事件中心和/或服务总线实体之间复制事件的复制任务示例。

若要在事件中心之间复制事件，请配合使用事件中心触发器和事件中心输出绑定：

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

若要在服务总线实体之间复制消息，请使用服务总线触发器和输出绑定：

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

利用帮助程序方法，可以轻松地在事件中心和服务总线之间进行复制：

| Source      | 目标      | 入口点 
|-------------|-------------|------------------------------------------------------------------------
| 事件中心   | 事件中心   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 事件中心   | 服务总线 | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| 服务总线 | 事件中心   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| 服务总线 | 服务总线 | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>监视

要了解如何监视复制应用，请参阅 Azure Functions 文档的[“监视”部分](../articles/azure-functions/configure-monitoring.md)。

用于监视复制任务的一个特别有用的可视化工具是 Application Insights [应用程序映射](../articles/azure-monitor/app/app-map.md)，它根据捕获的监视信息自动生成，并允许浏览复制任务的源和目标传输的可靠性和性能。

若要获得即时诊断见解，可以使用[实时指标](../articles/azure-monitor/app/live-stream.md)门户工具，该工具可以提供低延迟的日志详细信息可视化效果。

## <a name="next-steps"></a>后续步骤

* [Azure Functions 部署](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions 诊断](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions 网络选项](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)