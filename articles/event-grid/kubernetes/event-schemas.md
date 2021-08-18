---
title: Kubernetes 上的 Azure 事件网格 - 事件架构
description: 本文介绍 Azure Arc for Kubernetes 上事件网格支持的 Azure Arc 架构
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 4ec482a0cf8c0b418d2cd6ec11d1afd56273b681
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415617"
---
# <a name="event-schemas-in-event-grid-on-kubernetes"></a>Kubernetes 上事件网格中的事件架构
Kubernetes 上事件网格接受并传递 JSON 格式的事件。 它支持[云事件 1.0 架构规范](https://github.com/cloudevents/spec/blob/v1.0/spec.md)，这是将事件发布到事件网格时应该使用的架构。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="cloudevent-schema"></a>CloudEvent 架构
[CloudEvents](https://cloudevents.io/) 是一种用于描述事件数据的开放规范。 它提供通用的事件架构以供发布和使用事件，可简化互操作性。 请参阅 [CloudEvents 规范](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)了解有关强制上下文属性的信息。

## <a name="example--event-using-cloudevents-schema"></a>示例 - 使用 CloudEvents 架构的事件

```json
[{
      "specversion": "1.0",
      "type" : "orderCreated",
      "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
      "id" : "eventId-n",
      "time" : "2020-12-25T20:54:07+00:00",
      "subject" : "account/acct-123224/order/o-123456",
      "dataSchema" : "1.0",
      "data" : {
         "orderId" : "123",
         "orderType" : "PO",
         "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="next-steps"></a>后续步骤
若要了解 Azure Arc for Kubernetes 上的事件网格支持的目标和处理程序，请参阅 [Kubernetes 上的事件网格 - 事件处理程序](event-handlers.md)。