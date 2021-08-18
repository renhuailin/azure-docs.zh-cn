---
title: Kubernetes 上的 Azure 事件网格 - 概念
description: 本文介绍了支持 Azure Arc（预览版）的 Kubernetes 上的 Azure 事件网格的核心概念
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 911a67644ac50906ee7e4b3009658043cfa022d6
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414568"
---
# <a name="event-grid-on-kubernetes---concepts"></a>Kubernetes 上的事件网格 - 概念
本文介绍了支持 Azure Arc（预览版）的 Kubernetes 上的事件网格中的主要概念。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="events"></a>事件
事件是一种数据记录，用于公布有关软件系统操作的事实。 通常，事件会因系统发出的信号或系统观察到的信号而公布状态更改。 事件包含两种类型的信息： 

- [事件数据](https://github.com/cloudevents/spec/blob/master/spec.md#event-data)，表示发生状态更改。 
- [上下文属性](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes)，提供有关事件发生的上下文信息。     

    事件数据和上下文属性均可用于筛选事件。 

Kubernetes 上的事件网格支持 [CloudEvents](https://github.com/cloudevents/spec/tree/master) 架构规范。 下面是一个使用 CloudEvents 架构的事件示例。 事件网格支持最大为 1 MB 的事件。

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

## <a name="source"></a>源
“源”属性描述事件发生的上下文。 源可能是事件的发起方。 但在某些情况下，有些生成者可以创建和发布事件。 而且，此类生成者不同于源。 为简单起见，本文假设源是事件的生成者。 

每个事件源生成一个或多个事件类型的事件。 作为事件源，应用程序定义一组相关事件来公布其状态更改。 每个事件都有共同的信息，如事件源、事件发生的时间和唯一标识符。 此外，每个事件都有只与特定类型事件相关的特定信息。 预览版中目前支持最大为 1 MB 的事件。

有关事件中包含的属性，请参阅 [CloudEvents 架构](event-schemas.md#cloudevent-schema)。

## <a name="publishers"></a>发布服务器
事件发布者是一种应用程序或系统，用于将事件发送到事件网格，以传送给事件订阅者。

## <a name="topics"></a>主题
主题是一种输入通道，用于公开发布者向事件网格发送事件的终结点。

主题可用于收集相关事件。 可以为每个类别的相关事件创建一个主题。 在某些情况下，源可用于将事件组织到类别中，因为源通常与一组密切相关的事件类型（如“MyApp.OrderCreated”、“MyApp.OderDeleted”、“MyApp.OrderRejected”等）相关联。 

假设有一个应用程序发送与管理用户帐户和处理订单相关的事件。 事件订阅者不大可能对使用这两类事件感兴趣。 请创建两个自定义主题，让事件处理程序订阅其感兴趣的主题。 对于小型解决方案，可能希望将所有事件发送到单个主题。 

## <a name="event-subscribers"></a>事件订阅者
事件订阅者是一种软件系统，例如微服务，用于公开接收事件网格传送事件的终结点。 

## <a name="event-subscriptions"></a>事件订阅
事件订阅告知事件网格你有兴趣接收主题中的哪些事件（事件筛选）以及将事件发送到哪里（事件路由）。 在创建事件订阅时，需要提供处理事件的终结点。 可以通过在事件订阅上配置筛选子句来选择要传送到终结点的事件。 

## <a name="event-handlers"></a>事件处理程序
事件处理程序是一种软件系统，用于公开事件发送到的终结点。 处理程序接收事件，并执行操作来处理事件。 事件网格支持多个处理程序类型。 作为处理程序，可以使用 Kubernetes 或 Azure 上托管的受支持的 Azure 服务，也可以使用自己的解决方案，此类解决方案应在托管的任何位置公开 Webhook（终结点）。 根据处理程序的类型，事件网格遵循不同机制，以保证事件的传送。 如果目标事件处理程序是 HTTP Webhook，则会重试该事件，直到处理程序返回状态代码 200 – OK。 有关详细信息，请参阅[事件处理程序](event-handlers.md)。

## <a name="sas-authentication"></a>SAS 身份验证
Kubernetes 上的事件网格针对将事件发布到主题，提供基于 SAS 密钥的身份验证。

## <a name="event-delivery"></a>事件传送
Kubernetes 上的事件网格提供了可靠的传送和重试机制。 如果事件网格无法确认事件处理程序终结点已接收到事件，它将重新传送该事件。 有关详细信息，请参阅[事件网格消息传递和重试](delivery-retry.md)。

## <a name="batch-event-publishing"></a>批量事件发布
使用主题时，必须始终在数组中发布事件。 对于低吞吐量方案，数组将只有一个事件。 对于大容量用例，建议在每次发布时对多个事件进行批处理，以实现更高的效率。 批的大小最大可达 1 MB。 每个事件还不应当超过 1 MB。 有关详细信息，请参阅[批量事件传送](batch-event-delivery.md)。

## <a name="event-grid-on-kubernetes-components"></a>Kubernetes 组件上的事件网格

- 事件网格运算符实现了[运算符模式](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)。 它会监视由于向 Kubernetes 的 API 服务器发出控制平面请求而导致的事件网格资源状态更改。 当存在影响任何事件网格资源状态的请求时，事件网格运算符会将该状态与事件网格代理同步。
- 事件网格代理同时用于控制平面和数据平面操作。

   作为控制平面服务，事件网格代理负责使事件网格的状态达到事件网格运算符告知的期望状态。 例如，在发出请求来创建新主题时，它将完成该请求并更新服务元数据。

   作为数据平面服务，事件网格代理为所有事件发布请求提供服务，并将事件传送到事件订阅中配置的目标。

## <a name="next-steps"></a>后续步骤
若要开始，请参阅[创建主题和订阅](create-topic-subscription.md)。