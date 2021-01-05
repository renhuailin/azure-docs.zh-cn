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
ms.openlocfilehash: 279a00a6146d756e6a518dbf86b88f471d170b3a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805612"
---
## <a name="what-is-a-replication-task"></a>什么是复制任务？

复制任务从源接收事件并将其转发到目标。
如果源协议和目标协议不同，大多数复制任务会将事件保持不变，并且在元数据结构中最多执行映射。 

复制任务通常是无状态的，这意味着它们不会在任务的顺序或并行执行之间共享状态或其他副作用。 这也适用于批处理和链接，这两种方法都可以在流的现有状态之上实现。 

这使得复制任务不同于聚合任务，这些任务通常是有状态的，并且是分析框架的域和服务（如 [Azure 流分析](/azure/stream-analytics/stream-analytics-introduction)）。

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Functions 中的复制应用程序和任务

在 Azure Functions 中，使用从已配置的源获取一个或多个输入消息的触发器和[输出绑定](/azure/azure-functions/functions-triggers-bindings#binding-direction)来实现复制任务，该[触发器](/azure/azure-functions/functions-triggers-bindings)将从源复制的消息转发到配置的目标。 

| 触发器  | 输出 |
|----------|--------|
| [Azure 事件中心触发器](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Azure 事件中心输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Azure 服务总线触发器](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Azure 服务总线输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Azure IoT 中心触发器](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Azure IoT 中心输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Azure 事件网格触发器](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Azure 事件网格输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Azure 队列存储触发器](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Azure 队列存储输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Apache Kafka 触发器](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka 输出绑定](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ 触发器](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ 输出绑定](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure 通知中心输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Azure SignalR 服务输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Twilio SendGrid 输出绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

复制任务通过与任何其他 Azure Functions 应用程序相同的部署方法部署为复制应用程序。 可以将多个任务配置到同一个应用程序中。 

使用 Azure Functions Premium，多个复制应用程序可以共享相同的基础资源池，称为应用服务计划。 这意味着，你可以轻松地归置以 Java 编写的复制任务（例如）在 .NET 中编写的复制任务。 如果您想要利用仅适用于 Java 的特定库（例如 Apache Camel），并且这些库是特定集成路径的最佳选项，即使您通常更倾向于其他复制任务使用其他语言和运行时，这一点也很重要。 

如果可用，你应该优先使用面向批处理的触发器，这些触发器用于传递单个事件或消息，你应始终获取完整的事件或消息结构，而不是依赖于 Azure 函数的 [参数绑定表达式](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)。

函数的名称应反映正在连接的源和目标对，并且应在应用程序配置文件中以该名称作为对连接字符串或其他配置元素的引用的前缀。 

### <a name="data-and-metadata-mapping"></a>数据和元数据映射

确定一对输入触发器和输出绑定后，必须在不同的事件或消息类型之间执行一些映射，除非触发器的类型和输出是相同的。

对于在事件中心和服务总线之间复制消息的简单复制任务，您无需编写您自己的代码，但可以根据复制示例附带的 [实用工具库](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 进行精益处理。

### <a name="retry-policy"></a>重试策略

若要避免在复制功能的任何一侧的可用性事件期间丢失数据，需要将重试策略配置为可靠。 请参阅有关重试配置重试策略的 [Azure Functions 文档](/azure/azure-functions/functions-bindings-error-pages) 。 

为 [示例存储库](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) 中的示例项目所选择的策略设置将配置一个指数回退策略，重试间隔为5秒到15分钟，以避免数据丢失。 

对于服务总线，查看 ["在触发器复原能力顶部使用重试支持"](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) 部分，了解触发器的交互和为队列定义的最大传递计数。

### <a name="setting-up-a-replication-application-host"></a>设置复制应用程序主机

复制应用程序是一个或多个复制任务的执行主机。 

它是配置为在消耗计划中运行的 Azure Functions 应用程序，或在 Azure Functions 高级计划中 (推荐的) 。 所有复制应用程序都必须在 [系统或用户分配的托管标识](/azure/app-service/overview-managed-identity)下运行。 

链接的 Azure 资源管理器 (ARM) 模板使用以下内容创建和配置复制应用程序：

* 用于跟踪复制进度和日志的 Azure 存储帐户。
* 系统分配的托管标识， 
* 用于监视的 Azure 监视和 Application Insights 集成。

必须访问绑定到 Azure 虚拟网络 (VNet) 的事件中心的复制应用程序必须使用 Azure Functions 高级计划，并将其配置为附加到同一个 VNet，这也是可用的选项之一。


|       | 部署 | 可视化  
|-------|------------------|--------------|---------------|
| **Azure Functions 消耗计划** | [![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![可视化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions 高级计划** |[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![可视化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **通过 VNet Azure Functions 高级计划** | [![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![可视化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>示例

[示例存储库](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/)包含复制任务的几个示例，这些任务复制事件中心和/或服务总线实体之间的事件。

若要在事件中心之间复制事件，请使用事件中心触发器，其中包含事件中心输出绑定：

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

若要了解如何监视复制应用，请参阅 Azure Functions 文档的 " [监视" 部分](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) 。

用于监视复制任务的一个特别有用的可视化工具是 Application Insights [应用程序映射](https://docs.microsoft.com/azure/azure-monitor/app/app-map)，它是从捕获的监视信息自动生成的，并允许浏览复制任务源和目标传输的可靠性和性能。

对于即时的诊断见解，你可以使用 [实时指标](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) 门户工具，该工具提供日志详细信息的低延迟可视化。

## <a name="next-steps"></a>后续步骤

* [Azure Functions 部署](/azure/azure-functions/functions-deployment-technologies)
* [Azure Functions 诊断](/azure/azure-functions/functions-diagnostics)
* [Azure Functions 网络选项](/azure/azure-functions/functions-networking-options)
* [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)