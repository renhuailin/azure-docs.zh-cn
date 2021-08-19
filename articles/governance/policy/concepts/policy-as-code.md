---
title: 设计 Azure Policy as Code 工作流
description: 了解如何设计工作流以将 Azure Policy 定义部署为代码并自动验证资源。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: db48579d94263f3b8806c1c78b950b5a9ae19c27
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324819"
---
# <a name="design-azure-policy-as-code-workflows"></a>设计 Azure Policy as Code 工作流

随着你在云治理旅程中取得进展，需要从在 Azure 门户中或通过各种 SDK 手动管理每个策略定义，转变为在企业范围内更易于管理和重复进行的某个过程。 在云中大规模管理系统的两个主要方法是：

- 基础结构即代码：将定义环境的内容（从 Azure 资源管理器模板 [ARM 模板] 到 Azure Policy 定义乃至 Azure 蓝图的所有内容）视为源代码的做法。
- DevOps：人员、过程和产品的联合，以便向最终用户持续交付价值。

Azure Policy as Code 是这些思路的组合。 实质上，是将策略定义保留在源代码管理中，并在每次进行更改后，都测试并验证更改。 但是，这不应是涉及基础结构即代码或 DevOps 的策略的范围。

验证步骤还应是其他持续集成或持续部署工作流的组成部分。 示例包括部署应用程序环境或虚拟基础结构。 通过使 Azure Policy 验证成为生成和部署过程的早期组成部分，应用程序和运营团队可提前很长时间发现他们的更改是否不合规，而不会等到为时已晚或是尝试在生产环境中进行部署时。

## <a name="definitions-and-foundational-information"></a>定义和基本信息

在详细了解 Azure Policy as Code 工作流前，请查看以下定义和示例：

- [策略定义](./definition-structure.md)
- [计划定义](./initiative-definition-structure.md)

文件名与策略或计划定义的一部分保持一致：
- `policy(set).json` - 整个定义
- `policy(set).parameters.json` - 定义的 `properties.parameters` 部分
- `policy.rules.json` - 定义的 `properties.policyRule` 部分
- `policyset.definitions.json` - 定义的 `properties.policyDefinitions` 部分

[Azure Policy GitHub 存储库](https://github.com/Azure/azure-policy/)中提供这些文件格式的示例：

- Policy 定义：[将标记添加到资源](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- 计划定义：[计费标记](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>工作流概述

Azure Policy as Code 的建议一般工作流如下图所示：

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="图中显示了从创建到测试到部署的“Azure Policy as Code”工作流框。" border="false":::
   图中显示了“Azure Policy as Code”工作流框。 创建包括策略和计划定义的创建。 测试包括已禁用强制模式的分配。 在网关检查合规性状态之后，向分配授予 MSI 权限并对资源进行修正。 部署包括在启用强制模式的情况下更新分配。
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>创建和更新策略定义

策略定义使用 JSON 进行创建，并存储在源代码管理中。 每个策略都具有自己的文件集（如参数、规则和环境参数），它们应存储在同一个文件夹中。 下面的结构是将策略定义保留在源代码管理中的建议方法。

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

添加新策略或更新现有策略时，工作流应在 Azure 中自动更新策略定义。 新的或更新的策略定义的测试将在后面的步骤中进行。

此外，请查看[导出 Azure Policy 资源](../how-to/export-resources.md)，以将现有定义和分配信息导入源代码管理环境 [GitHub](https://www.github.com) 中。

### <a name="create-and-update-initiative-definitions"></a>创建和更新计划定义

同样，计划具有自己的 JSON 文件和相关文件，它们应存储在同一个文件夹中。 计划定义需要策略定义已存在，因此在源代码管理中更新策略的源，然后在 Azure 中进行更新之后，才能创建或更新计划定义。 下面的结构是将计划定义保留在源代码管理中的建议方法：

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

与策略定义一样，添加计划或更新现有计划时，工作流应在 Azure 中自动更新计划定义。 新的或更新的计划定义的测试将在后面的步骤中进行。

### <a name="test-and-validate-the-updated-definition"></a>测试并验证更新的定义

自动采用新创建或更新的策略或计划定义并在 Azure 中更新对象后，便可以测试所进行的更改。 随后应将策略或它所属的计划分配给离生产最远的环境中的资源。 此环境通常是开发环境。

分配应使用值为 disabled 的 [enforcementMode](./assignment-structure.md#enforcement-mode)，以便不会阻止资源创建和更新，但仍会审核该现有资源是否符合更新的策略定义。 即使使用 enforcementMode，也建议分配范围是专用于验证策略的资源组或订阅。

> [!NOTE]
> 尽管强制模式非常有用，但它不能替代在各种条件下对策略定义进行全面测试。 应使用 `PUT` 和 `PATCH` REST API 调用、合规与不合规的资源以及边缘事例（如资源中缺少属性）对策略定义进行测试。

部署分配后，使用 Azure Policy SDK、[Azure Policy 合规性扫描 GitHub 操作](https://github.com/marketplace/actions/azure-policy-compliance-scan)或 [Azure Pipelines 安全性与合规性评估任务](/azure/devops/pipelines/tasks/deploy/azure-policy)来[获取新分配的合规性数据](../how-to/get-compliance-data.md)。 用于测试策略和分配的环境应同时具有合规与不合规的资源。
与用于代码的良好单元测试一样，需要测试资源是否如同预期，并且是否也没有假正或假负。 如果仅针对期望的内容进行测试和验证，则策略可能会产生意外和无法识别的影响。 有关详细信息，请参阅[评估新 Azure Policy 定义的影响](./evaluate-impact.md)。

### <a name="enable-remediation-tasks"></a>启用修正任务

如果对分配的验证达到预期，则下一步是验证修正。
使用 [deployIfNotExists](./effects.md#deployifnotexists) 或 [modify](./effects.md#modify) 的策略可能会转变为修正任务，纠正不合规状态的资源。

修正资源的第一步是向策略分配授予在策略定义中定义的角色分配。 此角色分配会为策略分配托管标识提供足够权限来进行所需更改，以使资源合规。

策略分配具有适当权限后，请使用 Policy SDK 针对已知不合规的一组资源触发修正任务。 继续之前，应针对这些修正任务完成三个测试：

- 验证修正任务是否已成功完成
- 运行策略评估以查看策略合规性结果是否按预期进行更新
- 直接对资源运行环境单元测试，以验证其属性是否已更改

同时测试更新的策略评估结果和环境可直接提供确认，表示修正任务已更改了预期内容，并且策略定义按预期见证了合规性更改。

### <a name="update-to-enforced-assignments"></a>更新为强制分配

所有验证入口都已完成之后，将分配更新为使用值为 enabled 的 **enforcementMode**。 建议最初在远离生产环境的相同环境中进行此更改。 验证该环境按预期方式工作后，随后应将更改的范围限定为包括下一个环境，以此类推，直到策略部署到生产资源。

## <a name="process-integrated-evaluations"></a>过程集成评估

Azure Policy as Code 的一般工作流用于在环境中大规模开发和部署策略与计划。 但是，对于在 Azure 中部署或创建资源的任何工作流（例如部署应用程序，或运行 ARM 模板创建基础结构），策略评估应是部署过程的一部分。

在这些情况下，在对测试订阅或资源组进行应用程序或基础结构部署之后，应针对该范围进行策略评估，以检查现有策略和计划的验证。 虽然其 enforcementMode 在此类环境中可以配置为 disabled，但尽早知道应用程序或基础结构部署是否违反策略定义会十分有用。 因此，此策略评估应是这些工作流中的一个步骤，并使创建不合规资源的部署失败。

## <a name="review"></a>审阅

本文介绍了Azure Policy as Code 的一般工作流，以及在哪种情况下策略评估应是其他部署工作流的一部分。 此工作流可在支持基于触发器的脚本化步骤和自动化的任何环境中使用。 有关在 GitHub 上使用此工作流的教程，请参阅[教程：通过 GitHub 实现 Azure Policy as Code](../tutorials/policy-as-code-github.md)。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解[策略分配结构](./assignment-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
