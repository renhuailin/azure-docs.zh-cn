---
title: 对 Azure Policy 状态更改事件做出响应
description: 使用 Azure 事件网格订阅应用程序策略事件，无需复杂的代码即可使应用程序对状态更改做出响应。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4cd443882b3d1f9ffdcf0c317b6012654e0780c2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324880"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>对 Azure Policy 状态更改事件做出响应

Azure Policy 事件允许应用程序对状态更改做出响应。 此集成无需复杂的代码或成本高昂但效率低下的轮询服务。 它可以通过 [Azure 事件网格](../../../event-grid/index.yml)向订阅方（如 [Azure Functions](../../../azure-functions/index.yml)、[Azure 逻辑应用](../../../logic-apps/index.yml)）甚至向你自己的自定义 HTTP 侦听器推送事件。
至关重要的是，你只为你使用的资源付费。

Azure Policy 事件会发送到 Azure 事件网格，通过丰富的重试策略和死信传递向应用程序提供可靠的传递服务。 若要了解详细信息，请参阅[事件网格消息传递和重试](../../../event-grid/delivery-and-retry.md)。

常见 Azure Policy 事件场景在策略评估过程中资源的符合性状态发生更改时进行跟踪。 基于事件的体系结构是对这些更改做出响应的有效方法，而不是按固定计划扫描资源的符合性状态。

> [!NOTE]
> [计算触发器](../how-to/get-compliance-data.md#evaluation-triggers)完成资源评估后，会将 Azure Policy 状态更改事件发送到事件网格。

有关完整教程，请参阅[使用 Azure CLI 将策略状态更改事件路由到事件网格](../tutorials/route-state-change-events.md)。

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="源和处理程序的事件网格模型" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>可用 Azure Policy 事件

事件网格使用[事件订阅](../../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Azure Policy 事件订阅包括三类事件：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | 在创建策略符合性状态时引发。 |
| Microsoft.PolicyInsights.PolicyStateChanged | 在策略符合性状态发生更改时引发。 |
| Microsoft.PolicyInsights.PolicyStateDeleted | 在删除策略符合性状态时引发。 |

## <a name="event-schema"></a>事件架构

Azure Policy 事件包含响应数据更改所需的所有信息。 当 `eventType` 属性以“Microsoft.PolicyInsights”开始时可识别 Azure Policy 事件。
关于事件网格事件属性使用情况的其他信息，请参阅文档[事件网格事件架构](../../../event-grid/event-schema.md)。

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `id` | 字符串 | 事件的唯一标识符。 |
| `topic` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 符合性状态更改所针对的资源的完全限定 ID，包括资源名称和资源类型。 使用 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` 格式 |
| `data` | object | Azure Policy 事件数据。 |
| `data.timestamp` | string | Azure Policy 扫描资源的时间（采用 UTC）。 若要对事件进行排序，请使用此属性而不是顶层 `eventTime` 或 `time` 属性。 |
| `data.policyAssignmentId` | string | 策略分配的资源 ID。 |
| `data.policyDefinitionId` | string | 策略定义的资源 ID。 |
| `data.policyDefinitionReferenceId` | string | 若策略分配针对某个计划，则为计划定义中的策略定义的引用 ID。 可能为空。 |
| `data.complianceState` | string | 与策略分配相关的资源的符合性状态。 |
| `data.subscriptionId` | string | 资源的订阅 ID。 |
| `data.complianceReasonCode` | string | 符合性原因代码。 可能为空。 |
| `eventType` | string | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

下面是策略状态更改事件的示例：

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

有关详细信息，请参阅 [Azure Policy 事件架构](../../../event-grid/event-schema-policy.md)。

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理 Azure Policy 事件的应用程序应遵循以下推荐做法：

> [!div class="checklist"]
> - 可配置多个订阅将事件路由至同一事件处理程序，因此不可假定事件均来自某个特定的源。 相反，请检查消息的主题，以确保状态更改事件的策略分配、策略定义和资源。
> - 检查 `eventType` 且不可假定所接收的事件均为预期的类型。
> - 使用 `data.timestamp` 确定事件在 Azure Policy 中的顺序，而不是顶层 `eventTime` 或 `time` 属性。
> - 使用主题字段访问策略状态发生更改的资源。

## <a name="next-steps"></a>后续步骤

要详细了解事件网格并尝试 Azure Policy 状态更改事件，请参阅：

- [使用 Azure CLI 将策略状态更改事件路由到事件网格](../tutorials/route-state-change-events.md)
- [事件网格的 Azure Policy 架构详细信息](../../../event-grid/event-schema-policy.md)
- [关于事件网格](../../../event-grid/overview.md)
