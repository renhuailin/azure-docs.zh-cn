---
title: Azure 事件网格中的事件域
description: 本文介绍如何使用事件域来管理各种业务组织、客户或应用程序的自定义事件流。
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: a8f9b21b84b2c764d25943af8988e40672dccd31
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413510"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>了解用于管理事件网格主题的事件域

本文介绍如何使用事件域来管理各种业务组织、客户或应用程序的自定义事件流。 事件域可用于：

* 管理大规模的多租户事件处理体系结构。
* 管理授权和身份验证。
* 对主题进行分区，而不单独管理每个主题。
* 避免单独发布到每个主题终结点。

## <a name="event-domain-overview"></a>事件域概述

事件域是用于管理有关同一应用程序的大量事件网格主题的工具。 可将其视为可包含数千个单独主题的元主题。

事件域提供 Azure 服务（例如存储和 IoT 中心）使用的相同体系结构来发布其事件。 它们可以将事件发布到数千个主题。 域还提供对每个主题的授权和身份验证控制，以便你可对租户进行分区。

## <a name="example-use-case"></a>示例用例
[!INCLUDE [event-grid-domain-example-use-case.md](./includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>访问管理

使用域，可通过 Azure 基于角色的访问控制 (Azure RBAC) 精细控制每个主题的授权和身份验证情况。 这些角色可用于将应用程序中的每个租户限制为仅可访问经授权的主题。

事件域中 Azure RBAC 的工作方式与[托管访问控制](security-authorization.md)在事件网格和 Azure 的其余部分中的工作方式相同。 使用 Azure RBAC 在事件域中创建和强制实施自定义角色定义。

### <a name="built-in-roles"></a>内置角色

事件网格提供两个内置角色定义，使 Azure RBAC 可更方便地用于事件域。 这些角色是“EventGrid EventSubscription 参与者（预览版）”和“EventGrid EventSubscription 读取者（预览版）”。   将这些角色分配到需要订阅事件域中的主题的用户。 将角色分配的范围仅限于用户需要订阅的主题。

有关这些角色的信息，请参阅[事件网格的内置角色](security-authorization.md#built-in-roles)。

## <a name="subscribing-to-topics"></a>订阅主题

订阅事件域中主题的事件与[创建自定义主题的事件订阅](./custom-event-quickstart.md)或订阅 Azure 服务中的事件相同。

> [!IMPORTANT]
> 域主题在事件网格中被视为自动托管资源。 可以在[域范围](#domain-scope-subscriptions)内创建事件订阅，而无需创建域主题。 在这种情况下，事件网格会代表你自动创建域主题。 当然，你仍然可以选择手动创建域主题。 通过此行为，你可以在处理大量域主题时少担心一个资源。 删除域主题的最后一个订阅时，还会删除域主题，无论该域主题是手动创建还是自动创建的。 

### <a name="domain-scope-subscriptions"></a>域范围订阅

事件域还允许域范围订阅。 在事件域上订阅事件后，无论事件发送到哪个主题，都会收到发送到域的所有事件。 域范围订阅可用于管理和审核工作。

## <a name="publishing-to-an-event-domain"></a>发布到事件域

创建事件域时，将获得一个发布终结点，这与在事件网格中创建主题时类似。 

若要将事件发布到事件域中的主题，请将事件推送到域的终结点，[方法与使用自定义主题时相同](./post-to-custom-topic.md)。 唯一的区别是，必须指定要将事件传递到的主题。

例如，发布以下事件数组后，会将带有 `"id": "1111"` 的事件发送到主题 `foo`，带有 `"id": "2222"` 的事件发送到主题 `bar`：

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

事件域会自动处理发布到主题的工作。 可将所有事件发布到域的终结点，而无需将事件发布到单独管理的每个主题。 事件网格确保将每个事件发送到正确的主题。

## <a name="limits-and-quotas"></a>限制和配额
下面是与事件域相关的限制和配额：

- 每个事件域 100,000 个主题 
- 每个 Azure 订阅 100 个事件域 
- 事件域中每个主题 500 个事件订阅
- 50 个域范围订阅 
- 每秒 5,000 个事件的引入速率（引入到域）

如果这些限制不适合你，请开具支持票证或向 [askgrid@microsoft.com](mailto:askgrid@microsoft.com) 发送电子邮件。 

## <a name="pricing"></a>定价
事件域将使用与事件网格中所有其他功能相同的[操作定价](https://azure.microsoft.com/pricing/details/event-grid/)。

操作在事件域中的工作方式与在自定义主题中的相同。 事件域每引入一个事件都为一个操作，每尝试传递一个事件都为一个操作。



## <a name="next-steps"></a>后续步骤

* 若要了解有关设置事件域、创建主题、创建事件订阅和发布事件的信息，请参阅[管理事件域](./how-to-event-domains.md)。
