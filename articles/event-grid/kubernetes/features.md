---
title: Kubernetes 上的 Azure 事件网格 - 功能
description: 本文比较 Kubernetes 上的事件网格与 Azure 上的事件网格的功能。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 2eea263517d718effc03382e3d8209a9a11f17e7
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415851"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-features"></a>具有 Azure Arc 功能的 Kubernetes 上的事件网格
Kubernetes 上的事件网格提供一组丰富的功能，有助于集成 Kubernetes 工作负载并实现混合体系结构。 它与 Azure 事件网格（同一服务的其他版本）共享相同的 [rest API](/rest/api/eventgrid/version2020-10-15-preview/topics)（从版本 2020-10-15-preview 开始）、[事件网格 CLI](/cli/azure/eventgrid)、Azure 门户体验、[管理 SDK](../sdk-overview.md#management-sdks) 和[数据平面 SDK](../sdk-overview.md#data-plane-sdks)。 准备好发布事件时，可以使用[以不同语言提供的数据平面 SDK 示例](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)，这些语言适用于两个版本的事件网格。

尽管 Kubernetes 上的事件网格和 Azure 事件网格共享许多功能，并且目标是提供相同的用户体验，但考虑到它们寻求满足的独特要求及其在软件生命周期中的阶段，它们之间存在一些差异。 例如，Kubernetes 上的事件网格中唯一可用的主题类型是事件网格主题，有时也称为自定义主题。 其他主题类型（见下文）要么不适用，要么尚未提供支持。 下表中显示了两个版本的事件网格之间的主要差异。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="event-grid-on-kubernetes-vs-event-grid-on-azure"></a>Kubernetes 上的事件网格与 Azure 上的事件网格

| 功能 | Kubernetes 上的事件网格 | Azure 事件网格 |
|:--|:-:|:-:|
| [事件网格主题](/rest/api/eventgrid/version2020-10-15-preview/topics) | ✔ | ✔ |
| [CNCF 云事件架构](https://github.com/cloudevents/spec/blob/master/spec.md) | ✔ | ✔ |
| 事件网格和自定义架构 | ✘* | ✔ |
| 可靠的传递 | ✔ | ✔ |
| 指标  | ✔** | ✔ |
| Azure Monitor  | ✘ | ✔ |
| [死信位置](../manage-event-delivery.md#set-dead-letter-location) | ✘ | ✔ |
| [将事件转发到另一个事件网格主题](event-handlers.md#azure-event-grid) | ✔ | ✘ |
| [系统主题](../system-topics.md) | ✘ | ✔ |
| [域主题](../event-domains.md) | ✘ | ✔ |
| [合作伙伴事件](../partner-events-overview.md) | ✘ | ✔ |
| [目标终结点验证](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) | ✘ | ✔ |
| [Azure Functions 的 Azure 事件网格触发器](../../azure-functions/functions-bindings-event-grid-trigger.md) | ✘ | ✔ |
| Azure 中继的混合连接作为目标 | ✘ | ✔ |
| [高级筛选](filter-events.md) | ✔*** | ✔ |
| [使用 AAD 的 Webhook AuthN/AuthZ](../secure-webhook-delivery.md) | ✘ | ✔ |
| [使用标识进行事件传送](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#deliverywithresourceidentity) | ✘ | ✔ |
| 同一组数据平面 SDK | ✔ | ✔ |
| 同一组管理 SDK | ✔ | ✔ |
| 相同事件网格 CLI | ✔ | ✔ |

\* Cloud Events 1.0 架构提供一种可扩展性机制，并且是一种开放标准。 事件网格或自定义架构不提供这些特性或功能。 Cloud Events 1.0 架构是从事件网格架构演变而来的。

\** 使用 [Prometheus 阐释格式](https://prometheus.io/docs/instrumenting/exposition_formats/)提供主题和事件订阅的指标。 Azure 门户上的指标或其他监视功能目前在预览版本中还不可用。

\*** Kubernetes 上的事件网格与 Azure 上的事件网格一样，支持基于事件数据中的值对事件进行高级筛选，但存在一些 Kubernetes 上的事件网格不支持的功能和运算符。 有关详细信息，请参阅[“高级模式”](filter-events.md#filter-by-values-in-event-data)。

## <a name="next-steps"></a>后续步骤
若要了解有关 Kubernetes 上事件网格的详细信息，请参阅 [Azure Arc 的 Kubernetes 上的事件网格（预览版） - 概述](overview.md)。