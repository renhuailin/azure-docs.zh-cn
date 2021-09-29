---
title: 服务总线队列和主题作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将服务总线队列和主题用作 Azure 事件网格事件的事件处理程序。
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 5ea44f33bffa0f7158c8e3d1ae4e1cf0776f294e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214952"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>服务总线队列和主题作为 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 几个 Azure 服务已自动配置为处理事件，Azure 服务总线是其中之一。 

可以使用服务队列或主题作为事件网格事件的处理程序。 

## <a name="service-bus-queues"></a>服务总线队列

> [!NOTE]
> 不支持将启用了会话的队列作为 Azure 事件网格事件的事件处理程序
 
可以将事件网格中的事件直接路由到服务总线队列，用于企业应用程序中的缓冲或命令和控制方案。

在 Azure 门户中，请在创建事件订阅时选择“服务总线队列”作为终结点类型，然后单击“选择终结点”以选择服务总线队列。

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>使用 CLI 添加服务总线队列处理程序

在 Azure CLI 中，以下示例可订阅事件网格主题并将其连接到服务总线队列：

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>服务总线主题

可以将事件网格中的事件直接路由到服务总线主题，以使用服务总线主题处理 Azure 系统事件，或者将其用于命令和控制消息方案。

在 Azure 门户中，请在创建事件订阅时选择“服务总线主题”作为终结点类型，然后单击“选择终结点”以选择服务总线主题。

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>使用 CLI 添加服务总线主题处理程序

对于 Azure CLI，以下示例订阅事件网格主题并将其连接到服务总线主题：

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

将事件作为中转消息发送到服务总线队列或主题时，中转消息的 `messageid` 是内部系统 ID。

消息的内部系统 ID 会在事件重新传递期间保留，因此你可以在服务总线实体上启用“重复项检测”，避免重复传递。 建议在服务总线实体上启用重复项检测持续时间，并将其设为事件的生存时间 (TTL) 或最大重试持续时间（以较长者为准）。

## <a name="rest-examples-for-put"></a>REST 示例（适用于 PUT）

### <a name="service-bus-queue"></a>服务总线队列

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>服务总线队列 - 使用托管标识传送

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>服务总线主题

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>服务总线主题 - 使用托管标识传送

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
