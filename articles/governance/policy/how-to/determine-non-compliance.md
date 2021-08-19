---
title: 确定导致非符合性的原因
description: 如果资源不符合，可能有很多原因。 找出导致非符合性的原因。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: e09bdaee974e77a3afecaaa35a37c56ed3cd56ec
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324536"
---
# <a name="determine-causes-of-non-compliance"></a>确定导致非符合性的原因

当 Azure 资源被确定为不符合策略规则时，了解该资源的哪一部分不符合规则很有用。 这也有助于了解哪些更改内容更改了以前符合的资源，使其变得不符合。 可通过两种方法查找此信息：

- [符合性详细系信息](#compliance-details)
- [更改历史记录（预览）](#change-history)

## <a name="compliance-details"></a>符合性详细信息

当资源不符合时，“策略符合性”页中将提供该资源的符合性详细信息。 符合性详细信息窗格包含以下信息：

- 名称、类型、位置和资源 ID 等资源详细信息
- 当前策略分配的上一个计算的符合性状态和时间戳
- 资源不符合性的原因列表

> [!IMPORTANT]
> 由于不符合资源的符合性详细信息显示该资源属性的当前值，因此用户必须对资源类型进行读取操作。 例如，如果不符合资源为 Microsoft.Compute/virtualMachines，则用户必须进行 Microsoft.Compute/virtualMachines/read 操作。 如果用户没有进行所需操作，则会显示访问错误。

若要查看符合性详细信息，请执行以下步骤：

1. 在 Azure 门户中选择“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。 

1. 在“概览”或“符合性”页，选择“符合性状态”为“不符合”的策略  。

1. 在“策略合规性”页的“资源合规性”选项卡下，选择并按住（或右键单击）“合规性状态”为“不合规”的资源或选择其对应的省略号  。 然后选择“查看符合性详细信息”。

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="“资源符合性”选项卡上的“查看符合性详细信息”链接的屏幕截图。" border="false":::

1. “符合性详细信息”窗格显示对当前策略分配最近进行的计算得出的信息。 在此示例中，发现字段“Microsoft.Sql/servers/version”为“12.0”，而策略定义预期为“14.0”。 如果资源不符合有多种原因，则此窗格将列出每个原因。

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="符合性详细信息”窗格的屏幕截图，其中显示不符合的原因：当前值为 12，目标值为 14。" border="false":::

   对于“auditIfNotExists”或“deployIfNotExists”策略定义，详细信息包括“details.type”属性和任何可选属性。 有关列表，请参阅 [auditIfNotExists 属性](../concepts/effects.md#auditifnotexists-properties)和 [deployIfNotExists 属性](../concepts/effects.md#deployifnotexists-properties)。 “上一个计算资源”为定义的“详细信息”部分中的相关资源。

   部分“deployIfNotExists”定义示例：

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="ifNotExists 的“符合性详细信息”窗格的屏幕截图，其中包括计算的资源计数。" border="false":::

> [!NOTE]
> 为保护数据，当属性值为“secret”时，当前值显示星号。

这些详细信息解释了资源当前不符合的原因，但没有显示对资源所做的更改导致其不符合的时间。 有关此信息，请参阅下面的[更改历史记录（预览版）](#change-history)。

### <a name="compliance-reasons"></a>符合性原因

以下矩阵将每个可能原因映射到策略定义中的负责[条件](../concepts/definition-structure.md#conditions)：

|原因 | 条件 |
|-|-|
|当前值必须包含目标值作为关键值。 |containsKey 或不为 notContainsKey |
|当前值必须包含目标值。 |contains 或不为 notContains |
|当前值必须等于目标值。 |equals 或不为 notEquals |
|当前值必须小于目标值。 |less 或不为 greaterOrEquals |
|当前值必须大于或等于目标值。 |greaterOrEquals 或不为 less |
|当前值必须大于目标值。 |greater 或不为 lessOrEquals |
|当前值必须小于或等于目标值。 |lessOrEquals 或不为 greater |
|当前值必须存在。 |exists |
|当前值必须在目标值中。 |in 或不为 notIn |
|当前值必须与目标值类似。 |like 或不为 notLike |
|当前值必须与目标值匹配（区分大小写）。 |match 或不为 notMatch |
|当前值必须与目标值匹配（不区分大小写）。 |matchInsensitively 或不为 notMatchInsensitively |
|当前值不得包含目标值作为关键值。 |notContainsKey 或不为 containsKey|
|当前值不得包含目标值。 |notContains 或不为 contains |
|当前值不得等于目标值。 |notEquals 或不为 equals |
|不能存在当前值。 |不能为 exists  |
|当前值不得存在于目标值中。 |notIn 或不为 in |
|当前值不得与目标值类似。 |notLike 或不为 like |
|当前值不得与目标值匹配（区分大小写）。 |notMatch 或不为 match |
|当前值不得与目标值匹配（不区分大小写）。 |notMatchInsensitively 或不为 matchInsensitively |
|没有与策略定义中的效果详细信息匹配的相关资源。 |类型在“then.details.type”中定义，且与策略规则“if”部分定义的资源相关的资源不存在。 |

## <a name="component-details-for-resource-provider-modes"></a>资源提供程序模式的组件详细信息

对于使用[资源提供程序模式](../concepts/definition-structure.md#resource-manager-modes)的分配，请选择不合规的资源，以打开更详细的视图。 在“组件合规性”选项卡下，显示了特定于已分配策略上的资源提供程序模式的其他信息，其中显示了不合规的组件和组件 ID 。

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="资源提供程序模式分配“组件合规性”选项卡及合规性详细信息的屏幕截图。" border="false":::

## <a name="compliance-details-for-guest-configuration"></a>来宾配置的符合性详细信息

对于“来宾配置”类别中的 auditIfNotExists 策略，虚拟机内可能有多个计算设置，你需要查看各个设置的详细信息 。 例如，如果你正在审核一个密码策略列表，其中只有一个密码策略的状态为“不符合”，这时你需要了解具体哪些密码策略不符合以及不符合的原因。

你也可能无权直接登录到虚拟机，但需要报告虚拟机不符合的原因。

### <a name="azure-portal"></a>Azure 门户

首先遵循上述部分中的相同步骤查看策略符合性详细信息。

在“符合性详细信息”窗格视图中，选择“上次计算的资源”链接。

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="查看 auditIfNotExists 定义符合性详细信息的屏幕截图。" border="false":::

“来宾分配”页显示所有可用的符合性详细信息。 视图中的每一行都代表在计算机中执行的计算。 “原因”列中显示描述来宾分配“不符合”原因的短语。 例如，如果要审核密码策略，“原因”列将显示包含每个设置当前值的文本。

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="来宾分配符合性详细信息的屏幕截图。" border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>更改历史记录（预览版）

作为新公开预览版的一部分，前 14 天的更改历史记录适用于所有支持[完整模式删除](../../../azure-resource-manager/templates/deployment-complete-mode-deletion.md)的 Azure 资源。 更改历史记录提供有关何时检测到更改的详细信息，以及每个更改的 _视觉差异_。 添加、删除或修改 Azure 资源管理器属性时，就会触发更改检测。

1. 在 Azure 门户中选择“所有服务”，然后搜索并选择“策略”，以便启动 Azure Policy 服务。 

1. 在“概览”或“符合性”页，选择任一“符合性状态”策略  。

1. 在“策略符合性”页的“资源符合性”选项卡下，选择资源 。

1. 选择“资源符合性”页上的“更改历史记录(预览版)”选项卡 。 此时会显示检测到的更改的列表（如果存在）。

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="“资源合规性”页上的“更改历史记录”选项卡和检测到的更改时间的屏幕截图。" border="false":::

1. 选择其中一个检测到的更改。 资源的“视觉差异”在“更改历史记录”页上显示。

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="“更改历史记录”页上属性前后状态的更改历史记录视觉差异的屏幕截图。" border="false":::

_视觉差异_ 可帮助识别资源的更改。 检测到的更改可能与资源的当前符合性状态不相关。

更改历史记录数据由 [Azure Resource Graph](../../resource-graph/overview.md) 提供。 若要在 Azure 门户之外查询此信息，请参阅[获取资源更改](../../resource-graph/how-to/get-resource-changes.md)。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取符合性数据](get-compliance-data.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。