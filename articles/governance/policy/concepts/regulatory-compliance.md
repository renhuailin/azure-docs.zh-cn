---
title: 计划定义中的法规符合性
description: 介绍如何使用计划定义按法规域（例如访问控制、配置管理等）对策略进行分组。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: a9b32f22136b91bc57aee33a5cdfc99f77212436
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323069"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure Policy 中的法规符合性

Azure Policy 中的法规符合性提供内置计划定义用于根据责任（客户、Microsoft、分担责任）查看控制措施和合规性域的列表   。
对于 Microsoft 负责的控制措施，我们会基于第三方证明提供审核结果的其他详细信息，并提供我们为了实现这种合规性而实施的措施细节。
Microsoft 负责的控制措施的 `type` 为[静态](./definition-structure.md#type)。

> [!NOTE]
> 法规符合性目前是一项预览版功能。 对于更新的内置项，计划控制将映射到相应的合规性标准。 现有的合规性标准计划正在更新，以支持法规符合性。

## <a name="regulatory-compliance-defined"></a>定义的法规符合性

法规符合性是基于计划定义的[分组](./initiative-definition-structure.md#policy-definition-groups)部分建立的。 在内置项中，计划定义中的每个分组定义了一个名称（控制措施）和类别（合规性域），并提供对包含控制措施信息的 [policyMetadata](./initiative-definition-structure.md#metadata-objects) 对象的引用  。 法规符合性计划定义中的 `category` 属性必须设置为 Regulatory Compliance。 对于其他的标准计划定义，法规符合性计划支持使用[参数](./initiative-definition-structure.md#parameters)创建动态分配。

客户可以创建其自己的法规符合性计划。 这些定义可以是原始内容，也可以是从现有内置定义复制的内容。 如果使用内置法规符合性计划定义作为参考，我们建议监视 [Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)中法规符合性定义的源。

若要将自定义的法规符合性计划链接到 Azure 安全中心仪表板，请参阅 [Azure 安全中心 - 使用自定义安全策略](../../../security-center/custom-security-policies.md)。

## <a name="regulatory-compliance-in-portal"></a>门户中的“法规符合性”

使用[组](./initiative-definition-structure.md#policy-definition-groups)创建计划定义后，门户中该计划的“合规性”详细信息页将包含更多信息。

该页中添加了新选项卡“控制措施”。 可按合规性域进行筛选；策略定义已按 policyMetadata 对象中的 `title` 字段分组 。 每一行代表一个控制措施，该控制措施会显示其合规状态、所属的合规性域、责任信息，以及构成该控制措施的不合规和合规策略定义数  。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="NIST SP 800-53 R4 内置定义的法规符合性概述屏幕截图，其中显示了合规和不合规的控制措施。":::

选择一个控制措施会打开有关该控制措施的详细信息页。 “概述”包含来自 `description` 和 `requirements` 的信息。 在“策略”选项卡下，是计划中构成此控制措施的各个策略定义 。 “资源合规性”选项卡提供当前所查看的控制措施的成员策略评估的每个资源的粒度视图 。

> [!NOTE]
> “Microsoft 托管”评估类型适用于[静态](./definition-structure.md#type)策略定义 `type`。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="NIST SP 800-53 R4 内置定义的边界保护控制措施的法规符合性详细信息屏幕截图。":::

在同一个控制措施页中，切换到“资源合规性”选项卡会显示此控制措施的策略定义包含的所有资源  。 可按名称或 ID、合规状态、资源类型和位置进行筛选。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="NIST SP 800-53 R4 内置定义的边界保护控制措施的资源合规性屏幕截图。":::

## <a name="regulatory-compliance-in-sdk"></a>SDK 中的“法规符合性”

如果对计划定义启用了法规符合性，则评估扫描记录、事件和策略状态 SDK 都将返回其他属性。 这些附加属性已按合规状态分组，提供有关处于每种状态的组数的信息。

以下代码是调用 `summarize` 后添加的结果示例：

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>后续步骤

- 请参阅[计划定义结构](./initiative-definition-structure.md)
- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](./effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
