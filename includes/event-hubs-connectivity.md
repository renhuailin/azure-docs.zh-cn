---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dac82692c76d9d36b1f25d7b93b5c3a2e2400672
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002779"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>需要在防火墙上打开哪些端口？ 
可以将以下协议与 Azure 事件中心配合使用来发送和接收事件：

- 高级消息队列协议 1.0 (AMQP)
- 具有 TLS 的超文本传输协议 1.1 (HTTPS)
- Apache Kafka

请查看下表，了解需要打开哪些出站端口，以便使用这些协议与 Azure 事件中心通信。 

| 协议 | 端口 | 详细信息 | 
| -------- | ----- | ------- | 
| AMQP | 5671 和 5672 | 请参阅 [AMQP 协议指南](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | 此端口用于 HTTP/REST API 和 AMQP over Websocket。 |
| Kafka | 9093 | 请参阅[使用 Kafka 应用程序中的事件中心](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

当在端口5671上使用 AMQP 时，还需要使用 HTTPS 端口进行出站通信，因为在使用) 通过 HTTPS 运行时，客户端 Sdk 执行多个管理操作并从 Azure Active Directory (获取令牌。 

正式的 Azure Sdk 通常使用 AMQP 协议从事件中心发送和接收事件。 与 HTTP API 一样，AMQP-over-WebSockets 协议选项通过端口 TCP 443 运行，但在功能上与普通 AMQP 相同。 此选项的初始连接延迟较高，因为在共享 HTTPS 端口时要权衡额外的握手往返和额外的开销。 如果选择此模式，TCP 端口 443 足以进行通信。 以下选项允许选择普通 AMQP 或 AMQP WebSockets 模式：

| 语言 | 选项   |
| -------- | ----- |
| .NET     | [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true)或[EventHubsTransportType](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true)的[EventHubConnectionOptions TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true)属性 |
| Java     | [eventhubs. EventProcessorClientBuilder. Transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) 或 [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| 节点  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) 具有 `webSocketOptions` 属性。 |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) ， [Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) 或 [TransportType AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>需要允许哪些 IP 地址？
使用 Azure 时，有时必须在公司防火墙或代理中允许特定的 IP 地址范围或 URL 才能访问你正在使用或尝试使用的所有 Azure 服务。 确认在事件中心使用的 IP 地址上是否允许该流量。 对于 Azure 事件中心使用的 IP 地址：请参阅 [AZURE IP 范围和服务标记-公有云](https://www.microsoft.com/download/details.aspx?id=56519)。

另外，请验证是否允许你的命名空间的 IP 地址。 若要查找允许你的连接的正确 IP 地址，请执行以下步骤：

1. 从命令提示符处运行以下命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 记下 `Non-authoritative answer` 中返回的 IP 地址。 

如果对命名空间使用区域冗余，则需执行一些额外的步骤： 

1. 首先，在命名空间中运行 nslookup。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 记下“非权威回答”部分中的名称，该名称采用下述格式之一： 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 为每一个运行 nslookup，使用后缀 s1、s2 和 s3 获取所有三个在三个可用性区域中运行的实例的 IP 地址。 

    > [!NOTE]
    > `nslookup` 命令返回的 IP 地址不是静态 IP 地址。 但是，在删除基础部署或将其移至其他群集之前，该地址保持不变。

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>在哪里可以找到向命名空间发送消息或从命名空间接收消息的客户端 IP？
首先，在命名空间上启用 [IP 筛选](../articles/event-hubs/event-hubs-ip-filtering.md)。 

然后，按照[启用诊断日志](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)中的说明，为[事件中心虚拟网络连接事件](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)启用诊断日志。 你将看到连接遭到拒绝的 IP 地址。

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> 只有当命名空间允许从特定的 IP 地址（IP 筛选器规则）进行访问时，才会生成虚拟网络日志。 如果不希望使用这些功能限制对命名空间的访问，但仍希望获取虚拟网络日志来跟踪连接到事件中心命名空间的客户端的 IP 地址，则可以使用以下解决方法：启用 IP 筛选并添加整个可寻址 IPv4 范围 (1.0.0.0/1 - 255.0.0.0/1)。 事件中心不支持 IPv6 地址范围。 
