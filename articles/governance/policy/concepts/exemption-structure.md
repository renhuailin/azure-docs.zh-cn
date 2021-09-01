---
title: 策略例外结构的详细信息
description: 介绍 Azure Policy 使用的策略豁免定义，以免除资源对计划或定义的评估。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4c38f872a17869b957bce415204042582454dfd9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324837"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy 豁免结构

Azure Policy 例外（预览版）功能用于免除资源层次结构或单个资源对计划或定义的评估。 免除的资源计入总体符合性，但不能被评估或具有暂时豁免。 有关详细信息，请参阅[了解 Azure Policy 中的范围](./scope.md)。 Azure Policy 例外仅适用于[资源管理器模式](./definition-structure.md#resource-manager-modes)，不适用于[资源提供程序模式](./definition-structure.md#resource-provider-modes)。

> [!IMPORTANT]
> 此功能在预览期间免费。 有关定价详细信息，请参阅 [Azure Policy 定价](https://azure.microsoft.com/pricing/details/azure-policy/)。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 JSON 创建策略例外。 策略例外包含以下各项的元素：

- 显示名称
- description
- metadata
- 策略分配
- 计划中的策略定义
- 例外类别
- expiration

> [!NOTE]
> 策略例外创建为资源层次结构上的子对象，或创建为被授予例外的单个资源，因此目标不包含在例外定义中。

例如，以下 JSON 在名为 `resourceShouldBeCompliantInit` 的计划分配的资源的“豁免”类别中显示了策略例外。 该资源仅受到计划中两个策略定义的免除，即 `customOrgPolicy` 自定义策略定义（引用 `requiredTags`）和“允许的位置”内置策略定义（ID：`e56962a6-4747-49cd-b67b-bf8b01975c4c`，引用 `allowedLocations`）：

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

包含与策略例外使用的匹配的 `policyDefinitionReferenceIds` 的相关计划的代码片段：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>显示名称和说明

使用“显示名称”和“说明”来标识策略例外，并提供其与特定资源配合使用时的上下文 。 **displayName** 的最大长度为 128 个字符，**description** 的最大长度为 512 个字符。

## <a name="metadata"></a>元数据

通过“元数据”属性，可以创建存储相关信息所需的任何子属性。 在上面的示例中，属性“requestedBy”、“approvedBy”、“approvedOn”和“ticketRef”包含客户值，以提供有关免除请求者、批准者、批准时间，以及该请求的内部跟踪工单的信息   。 这些“元数据”属性是示例，但不是必需的，且“元数据”不仅限于这些子属性 。

## <a name="policy-assignment-id"></a>策略分配 ID

此字段必须是策略分配或计划分配的完整路径名称。
`policyAssignmentId` 是字符串，而不是数组。 此属性定义父级资源层次结构或单个资源是从哪个分配免除的。

## <a name="policy-definition-ids"></a>策略定义 ID

如果 `policyAssignmentId` 用于计划分配，则 `policyDefinitionReferenceIds` 属性可用于指定主题资源可豁免计划中的哪些策略定义。 由于可以从一个或多个包含的策略定义中免除资源，因此此属性是一个数组。 这些值必须与 `policyDefinitions.policyDefinitionReferenceId` 字段中的计划定义中的值相匹配。

## <a name="exemption-category"></a>例外类别

存在两个例外类别，用于组例外：

- **缓解**：由于通过另一种方法满足了策略意图，因此授予了豁免。
- **豁免**：由于暂时接受资源的不合规状态，因此授予了豁免。 使用此类别的另一个原因是，对于某些资源或资源层次结构，应将其从计划中的一个或多个定义中排除，但不应将其从整个计划中排除。

## <a name="expiration"></a>过期时间

若要设置不再从分配中豁免资源层次结构或单个资源的时间，请设置 `expiresOn` 属性。 此可选属性必须采用通用 ISO 8601 日期/时间格式 `yyyy-MM-ddTHH:mm:ss.fffffffZ`。

> [!NOTE]
> 达到 `expiresOn` 日期后，策略例外不会被删除。 该对象会保留用于记录，但不再受到免除。

## <a name="required-permissions"></a>所需的权限

管理策略例外对象所需的 Azure RBAC 权限位于 `Microsoft.Authorization/policyExemptions` 操作组中。 内置角色[资源策略参与者](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor)和[安全管理员](../../../role-based-access-control/built-in-roles.md#security-admin)都具有 `read` 和 `write` 权限，[策略见解数据编写者（预览版）](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview)具有 `read` 权限。

鉴于授予免除的影响，免除具有额外的安全措施。 除了要求对资源层次结构或单个资源进行 `Microsoft.Authorization/policyExemptions/write` 操作外，例外的创建者还必须在目标分配上具有 `exempt/Action` 谓词。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。