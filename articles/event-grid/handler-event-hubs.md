---
title: 事件中心作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将时间中心用作 Azure 事件网格事件的事件处理程序。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 52517980d1c16517bd3b3047c4a56aa5567eb053
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416391"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>事件中心作为 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行操作来处理事件。 几个 Azure 服务已自动配置为处理事件，Azure 事件中心是其中之一。 

如果解决方案从事件网格获取事件的速度快于处理事件的速度，请使用事件中心。 事件位于事件中心后，应用程序可以按自己的计划处理事件中心的事件。 可以通过缩放事件处理来处理传入的事件。

## <a name="tutorials"></a>教程
请看以下示例： 

|标题  |说明  |
|---------|---------|
| [快速入门：使用 Azure CLI 将自定义事件路由到 Azure 事件中心](custom-event-to-eventhub.md) | 将自定义事件发送到事件中心以供应用程序处理。 |
| [资源管理器模板：创建事件网格自定义主题，并将事件发送到事件中心](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| 用于创建自定义主题的订阅的资源管理器模板。 它将事件发送到 Azure 事件中心。 |

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST 示例（适用于 PUT）


### <a name="event-hub"></a>事件中心

```json
{
  "properties": 
  {
    "destination": 
    {
      "endpointType": "EventHub",
      "properties": 
      {
        "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>事件中心 - 使用托管标识传送

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
        "endpointType": "EventHub",
        "properties": 
        {
          "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
        }
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
