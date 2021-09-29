---
title: 将 Azure Policy 用作事件网格源
description: 本文介绍了如何将 Azure Policy 用作事件网格源。 其中提供了架构，以及教程和操作指南文章的链接。
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/15/2021
ms.openlocfilehash: 3b5c984ca1374c50a312665c3f5e3bb063ed8feb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589770"
---
# <a name="azure-policy-as-an-event-grid-source"></a>将 Azure Policy 用作事件网格源

本文提供 [Azure Policy](../governance/policy/index.yml) 事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](./event-schema.md)。 它还提供了一系列有关如何将 Azure Policy 用作事件网格源的快速入门和教程。

## <a name="available-event-types"></a>可用事件类型

Azure Policy 发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | 在创建策略符合性状态时引发。 |
| Microsoft.PolicyInsights.PolicyStateChanged | 在策略符合性状态发生更改时引发。 |
| Microsoft.PolicyInsights.PolicyStateDeleted | 在删除策略符合性状态时引发。 |

## <a name="example-event"></a>示例事件

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
以下示例显示了策略状态创建事件的架构： 

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
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

策略状态更改事件的架构类似： 

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
# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示了策略状态创建事件的架构： 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
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
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

策略状态更改事件的架构类似： 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
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
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>事件属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 符合性状态更改所针对的资源的完全限定 ID，包括资源名称和资源类型。 使用格式 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | string | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | object | Azure Policy 事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `source` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 符合性状态更改所针对的资源的完全限定 ID，包括资源名称和资源类型。 使用格式 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | string | 此事件源的一个注册事件类型。 |
| `time` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | object | Azure Policy 事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `timestamp` | string | Azure Policy 扫描资源的时间（采用 UTC）。 若要对事件进行排序，请使用此属性而不是顶层 `eventTime` 或 `time` 属性。 |
| `policyAssignmentId` | string | 策略分配的资源 ID。 |
| `policyDefinitionId` | string | 策略定义的资源 ID。 |
| `policyDefinitionReferenceId` | string | 若策略分配针对某个计划，则为计划定义中的策略定义的引用 ID。 可能为空。 |
| `complianceState` | string | 与策略分配相关的资源的符合性状态。 |
| `subscriptionId` | string | 资源的订阅 ID。 |
| `complianceReasonCode` | string | 符合性原因代码。 可能为空。 |

## <a name="next-steps"></a>后续步骤

- 如需获取有关路由 Azure Policy 状态更改事件的演练，请参阅[将事件网格用于策略状态更改通知](../governance/policy/tutorials/route-state-change-events.md)。
- 有关将 Azure Policy 与事件网格集成的概述，请参阅[使用事件网格响应 Azure Policy 事件](../governance/policy/concepts/event-overview.md)。
- 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](./overview.md)
- 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](./subscription-creation-schema.md)。