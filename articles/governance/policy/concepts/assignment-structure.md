---
title: 策略分配结构的详细信息
description: 介绍策略分配定义，Azure Policy 使用该定义将策略定义和参数关联到资源，以进行评估。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1b29becc3324c0d174db2102c4ae7ac15206e567
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323441"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy 分配结构

Azure Policy 使用策略分配来定义为哪些资源分配了哪些策略或计划。 在分配时，策略分配可以确定该组资源的参数值，因此，可以重复使用能够处理相同资源属性并满足不同合规需求的策略定义。

使用 JSON 创建策略分配。 策略分配包含以下各项的元素：

- 显示名称
- description
- metadata
- 强制模式
- 排除的范围
- 策略定义
- 不符合性消息
- 参数

例如，以下 JSON 显示包含动态参数的、处于 _DoNotEnforce_ 模式的策略分配：

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

所有 Azure Policy 示例均位于 [Azure Policy 示例](../samples/index.md)中。

## <a name="display-name-and-description"></a>显示名称和说明

使用 **displayName** 和 **description** 来标识策略分配，并提供它与特定资源集配合使用时的上下文。 **displayName** 的最大长度为 128 个字符，**description** 的最大长度为 512 个字符。

## <a name="metadata"></a>元数据

`metadata` 可选属性用于存储关于策略分配的信息。 客户可在 `metadata` 中定义对其组织有用的任何属性和值。 但是，Azure Policy 使用一些常见属性。 每个 `metadata` 属性有 1024 个字符的限制。

### <a name="common-metadata-properties"></a>常见元数据属性

- `assignedBy`（字符串）：创建分配的安全主体的友好名称。
- `createdBy`（字符串）：创建分配的安全主体的 GUID。
- `createdOn`（字符串）：分配创建时间的通用 ISO 8601 日期时间格式。
- `parameterScopes`（对象）：键值对的集合，其中密钥与 [strongType](./definition-structure.md#strongtype) 配置的参数名称匹配，值定义门户中使用的资源范围，以通过匹配 strongType 提供来可用资源的列表。 如果范围不同于分配范围，门户将设置此值。 如果已设置，则在门户中编辑策略分配后，会自动将参数的作用域设置为此值。 但是，该作用域不会锁定到值，并且可以更改为其他作用域。

  下面的示例 `parameterScopes` 是针对名为 **BackupPolicyId** 的 _strongType_ 参数，用于在门户中编辑分配时设置资源选择的范围。

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy`（字符串）：更新分配的安全主体的友好名称（如果有）。
- `updatedOn`（字符串）：分配更新时间的通用 ISO 8601 日期时间格式（如果有）。

## <a name="enforcement-mode"></a>强制模式

**enforcementMode** 属性使客户能够测试对现有资源应用某个策略后的结果，而无需启动策略效果，或触发 [Azure 活动日志](../../../azure-monitor/essentials/platform-logs-overview.md)中的条目。 此方案通常称为“What If”，与安全部署做法相符。 **enforcementMode** 不同于 [Disabled](./effects.md#disabled) 效果，后者会彻底阻止资源评估的发生。

此属性具有以下值：

|Mode |JSON 值 |类型 |手动修正 |活动日志条目 |说明 |
|-|-|-|-|-|-|
|Enabled |默认 |string |是 |是 |在创建或更新资源期间强制实施策略效果。 |
|已禁用 |DoNotEnforce |string |是 |否 | 在创建或更新资源期间不强制实施策略效果。 |

如果未在策略或计划定义中指定 **enforcementMode**，则使用值 _Default_。 即使 **enforcementMode** 设置为 _DoNotEnforce_，也可以针对 [deployIfNotExists](./effects.md#deployifnotexists) 策略启动 [修正任务](../how-to/remediate-resources.md)。

## <a name="excluded-scopes"></a>排除的范围

分配的范围包括所有子资源容器和子资源。 如果子资源容器或子资源不应应用定义，则可以通过设置 notScopes 将每个项从计算中排除。 此属性是一个数组，用于从计算中排除一个或多个资源容器或资源。 notScopes 可以在创建初始赋值后添加或更新。

> [!NOTE]
> 排除的资源与免除的资源不同。 有关详细信息，请参阅[了解 Azure Policy 中的范围](./scope.md)。

## <a name="policy-definition-id"></a>策略定义 ID

此字段必须是策略定义或计划定义的完整路径名称。
`policyDefinitionId` 是字符串，而不是数组。 如果经常要一起分配多个策略，我们建议改用[计划](./initiative-definition-structure.md)。

## <a name="non-compliance-messages"></a>不符合性消息

若要设置描述资源为何不符合策略或计划定义的自定义消息，请在分配定义中设置 `nonComplianceMessages`。 此节点是一个 `message` 条目的数组。 此自定义消息是对不符合性默认错误消息的补充，并且是可选的。

> [!IMPORTANT]
> 只有使用[资源管理器模式](./definition-structure.md#resource-manager-modes)定义的定义或计划才支持针对不合规的自定义消息。

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

如果分配是针对某个计划的，则可以为该计划中的每个策略定义配置不同的消息。 消息使用在计划定义中配置的 `policyDefinitionReferenceId` 值。 有关详细信息，请参阅[策略定义属性](./initiative-definition-structure.md#policy-definition-properties)。

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>parameters

此策略分配段为[策略定义或计划定义](./definition-structure.md#parameters)中定义的参数提供值。 通过这种设计，可对不同的资源重复使用某个策略或计划定义，但需要检查不同的业务价值或成果。

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

在此示例中，事先在策略定义中定义的参数为 `prefix` 和 `suffix`。 此特定策略分配将 `prefix` 设置为 **DeptA**，将 `suffix` 设置为 **-LC**。 可对不同部门的一组不同参数重复使用同一个策略定义，以降低策略定义的重复性和复杂性，同时提供灵活性。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
