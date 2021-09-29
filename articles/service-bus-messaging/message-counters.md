---
title: Azure 服务总线 - 消息计数
description: 使用 Azure 资源管理器和 Azure 服务总线 NamespaceManager API 检索队列和订阅中保留的消息计数。
ms.topic: article
ms.date: 09/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74038f38f1faca90e4fab7a0d4f94229a1b44429
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576054"
---
# <a name="get-message-counters"></a>获取消息计数器
本文介绍为队列或订阅获取以下消息计数的不同方式。 对于确定队列是否累积了需要的处理资源比当前部署资源更多的积压工作，了解活动消息计数会十分有用。 

| 计数器 | 说明 |
| ----- | ---------- | 
| ActiveMessageCount | 队列或订阅中处于活动状态且已准备发送的消息数。 |
| ScheduledMessageCount | 计划状态中的消息数。 |
| DeadLetterMessageCount | 死信队列中的信息数。 |
| TransferMessageCount | 等待传输到其他队列或主题的消息数。 |
| TransferDeadLetterMessageCount | 未能传输到其他队列或主题且已移动到传输死信队列中的消息数。 |

如果应用程序要基于队列长度缩放资源，则应按照慎重的进度执行此操作。 获取消息计数器是消息代理中成本高昂的操作，频繁执行它会直接对实体性能产生负面影响。

> [!NOTE]
> 发送到服务总线主题的消息将转发到该主题的订阅。 因此，主题本身的活动消息计数为 0，因为这些消息已成功转发到订阅。 获取订阅处的消息计数，并验证它是否大于 0。 即使你在订阅中看到消息，但它们实际上存储在主题所拥有的存储中。 如果查看这些订阅，则它们的消息计数不会为零（整个实体的空间总计达 323 MB）。


## <a name="using-azure-portal"></a>使用 Azure 门户
导航到你的命名空间，然后选择队列。 在队列的“概述”页面上，可以看到消息计数器。

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="“队列概述”页面上的消息计数器":::

导航到你的命名空间，选择主题，然后选择该主题的订阅。 在队列的“概述”页面上，可以看到消息计数器。

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="“订阅概述”页面上的消息计数器":::

## <a name="using-azure-cli"></a>使用 Azure CLI
使用 [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) 命令获取队列的消息计数详细信息，如下方示例中所示。 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

下面是示例输出：

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

使用 [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) 命令获取订阅的消息计数详细信息，如下方示例中所示。 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
使用 PowerShell，可以获取队列的消息计数详细信息，如下所示：

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

下面是示例输出：

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

可以获取订阅的消息计数详细信息，如下所示：

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

返回的 `MessageCountDetails` 对象具有以下属性：`ActiveMessageCount`、`DeadLetterMessageCount`、`ScheduledMessageCount`、`TransferDeadLetterMessageCount`、`TransferMessageCount`。 

## <a name="next-steps"></a>后续步骤

尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
