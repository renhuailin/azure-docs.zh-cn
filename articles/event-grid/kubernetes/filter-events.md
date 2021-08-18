---
title: Kubernetes 上的 Azure 事件网格 - 筛选事件
description: 本文介绍如何在创建 Azure 事件网格订阅时筛选事件。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: e4dbdbb2f46546391b6e749a94bfa655bb9de45e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417336"
---
# <a name="event-grid-on-kubernetes---event-filtering-for-subscriptions"></a>Kubernetes 上的事件网格 - 订阅的事件筛选
通过 Kubernetes 上的事件网格，可对 json 有效负载中的任何属性指定筛选器。 这些筛选器将建模为 AND 条件集，其中每个外部条件都有可选的内部 OR 条件。 对于每个 AND 条件，请指定以下值：

- OperatorType - 比较的类型。
- 键 - 要对其应用筛选器的属性的 json 路径。
- 值 - 运行筛选器所依据的引用值（或）值 - 运行筛选器所依据的引用值集。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="filter-by-event-type"></a>按事件类型筛选
默认情况下，事件源的所有[事件类型](event-schemas.md)（`type` 特性）都将发送到终结点。 可以决定仅将某些事件类型发送到终结点。 按事件类型筛选的 JSON 语法是：

```json
"filter": {
  "includedEventTypes": [
    "orderCreated",
    "orderUpdated"
  ]
}
```

在以上示例中，类型 `orderCreated` 和 `orderUpdated` 事件的唯一事件将发送到订阅服务器终结点。 

下面是一个示例事件：

```json
[{
      "specVersion": "1.0",
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

## <a name="filter-by-subject"></a>按主题筛选
对于按主题的简单筛选，请指定主题的开头值或结尾值。 按主题筛选的 JSON 语法是：

```json
"filter": {
  "subjectBeginsWith": "/account/acct-123224/"
}
``` 

例如，上面配置的筛选器将向订阅服务器终结点发送与帐户 `acct-123224` 关联的所有订单。 

将事件发布到主题时，可为事件创建主题，便于订阅者们了解他们是否对该事件感兴趣。 订阅者使用主题属性来筛选和路由事件。 考虑添加事件发生位置的路径，以便订阅者可以按该路径的段进行筛选。 通过路径，订阅者可精确或宽泛地筛选事件。 如果在主题中提供一个由三个段构成的路径（如 /A/B/C），订阅者可根据第一个段 /A 进行筛选，获取范围较宽泛的一组事件。 这些订阅者会获取主题为 /A/B/C 或 /A/D/E 这样的事件。 其他订阅者可通过 /A/B 进行筛选，这样可以获取范围更精确的一组事件。

## <a name="filter-by-values-in-event-data"></a>按事件数据中的值进行筛选
请参阅[“Azure 上的事件网格”一文中的“高级筛选”部分](../event-filtering.md)，详细了解高级筛选。 Kubernetes 上的事件网格不支持以下功能和运算符。 

- 筛选传入事件键中的数组数据
- 允许筛选 [CloudEvents 扩展上下文特性](https://github.com/cloudevents/spec/blob/v1.0/documented-extensions.md)。
- 以下运算符
    - StringNotContains
    - StringNotBeginsWith
    - StringNotEndsWith
    - NumberInRange
    - NumberNotInRange
    - IsNullOrUndefined
    - IsNotNull
    

## <a name="next-steps"></a>后续步骤
若要了解 Azure Arc for Kubernetes 上的事件网格支持的目标和处理程序，请参阅 [Kubernetes 上的事件网格 - 事件处理程序](event-handlers.md)。