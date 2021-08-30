---
title: 使用 Azure Policy 和 Azure 蓝图实现安全合规性
description: 针对 ASD ISM 和 Essential 8，使用 Azure Policy 和 Azure 蓝图为澳大利亚政府机构确保合规性并实施安全性
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 79ee7b4a0d21322e8c2e497eecd1704356282eba
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "117029194"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>使用 Azure Policy 和 Azure 蓝图实现安全合规性

所有组织都面临在 IT 环境（无论是本地、云原生还是混合环境）中实施治理的挑战。 需部署可靠的技术治理框架，确保 Microsoft Azure 环境符合设计、法规和安全要求。

对于澳大利亚政府机构，在评估风险时要考虑的关键控制措施位于[澳大利亚网络安全中心 (ACSC) 信息安全手册](https://acsc.gov.au/infosec/ism/index.htm) (ISM)。 ISM 中详述的大多数控制措施要求有效管理和实施技术治理的应用。 务必使用适当的工具来评估和实施环境中的配置。

Microsoft Azure 提供两项免费服务来帮助应对这些挑战：Azure Policy 和 Azure 蓝图。

## <a name="azure-policy"></a>Azure Policy

Azure Policy 支持应用组织 IT 治理的技术元素。 Azure Policy 包含一个不断增长的内置策略库。 每个策略对目标 Azure 资源实施规则和影响。

将策略分配给资源后，可以评估针对该策略的整体合规性，并在必要时进行修正。

这个内置的 Azure 策略库使组织能够快速实施 ACSC ISM 中的控制措施类型。 控制措施示例包括：

* 监视虚拟机是否缺少系统更新
* 审核具有多重身份验证提升权限的帐户
* 识别未加密的 SQL 数据库
* 监视自定义 Azure 基于角色的访问控制 (Azure RBAC) 的使用
* 限制可创建资源的 Azure 区域

如果内置的 Azure Policy 定义与治理或监管控制措施不符，可以创建和分配自定义的定义。 所有 Azure Policy 定义均以 JSON 定义，并遵循标准[定义结构](../governance/policy/concepts/definition-structure.md)。 还可以复制现有的 Azure Policy 定义，并使用这些定义来构成自定义 Policy 定义的基础。

在复杂环境或具有严格监管要求的环境中将各个 Azure 策略分配给资源尤其可能会给管理员带来巨大的开销。 为应对这些挑战，可以将一组 Azure 策略组合成 Azure Policy 计划。 Policy 计划用于组合相关的 Azure 策略，这些策略以组的形式同时应用时可构成特定安全或合规性目标的基础。 Microsoft 正在添加内置的 Azure Policy 计划定义，包括旨在满足特定监管要求的定义：

![法规合规性策略计划](media/regulatory-initiatives.png)

所有 Azure 策略和计划均分配到一个分配范围。 此范围在 Azure 订阅、Azure 管理组或 Azure 资源组级别定义。 分配所需的 Azure 策略或 Policy 计划后，组织将能够对所有新创建的 Azure 资源实施配置要求。

分配新的 Azure 策略或计划不会影响现有的 Azure 资源。 但 Azure Policy 可使组织能够查看现有 Azure 资源的合规性。 组织可自行决定修正任何已标识为不合规的资源

### <a name="azure-policy-and-initiatives-in-action"></a>实践中的 Azure Policy 和计划

Azure 门户中 Policy 部分的“定义”节点下显示可用的内置 Azure Policy 和计划定义：

![内置的 Azure Policy 定义](media/policy-definitions.png)

使用内置定义库可以快速搜索满足组织要求的策略、查看策略定义并将策略分配给相应的资源。 例如，ISM 要求对所有特权用户和可访问重要数据存储库的所有用户进行多重身份验证 (MFA)。 在 Azure Policy 中，可以在 Azure Policy 定义中搜索“MFA”：

![Azure AD MFA 策略](media/mfa-policies.png)

确定合适的策略后，可以将该策略分配到所需的范围。 如果没有满足要求的内置策略，可以复制现有策略并进行所需的更改：

![复制现有的 Azure Policy](media/duplicate-policy.png)

Microsoft 还在 [GitHub](https://github.com/Azure/azure-policy) 上提供了一系列 Azure Policy 示例，作为生成自定义 Azure 策略的“快速入门”。 这些 Policy 示例可以直接复制到 Azure 门户中的 Azure Policy 编辑器。

创建 Azure Policy 计划时，可以对内置的和自定义的可用策略定义列表进行排序，添加所需的定义。

例如，可以在可用 Azure Policy 定义列表中搜索与 Windows 虚拟机相关的所有策略。 然后，将这些定义添加到旨在实施推荐的虚拟机强化做法的计划：

![Azure 策略列表](media/initiative-definitions.png)

将 Azure Policy 或 Policy 计划分配到分配范围时，可以排除 Azure 管理组或 Azure 资源组，使 Azure 资源不受策略的影响。

### <a name="real-time-enforcement-and-compliance-assessment"></a>实时实施和合规性评估

满足以下条件时，将对范围内的 Azure 资源进行 Azure Policy 合规性扫描：

* 已分配 Azure Policy 或 Azure Policy 计划
* 已更改现有 Azure Policy 或计划的范围
* 按需通过 API 每小时最多扫描 10 次
* 每 24 小时扫描一次 - 默认行为

在更改资源后的第 15 分钟，对单个 Azure 资源进行策略合规性扫描。

可以在 Azure 门户中通过策略合规性仪表板查看资源的 Azure 策略合规性概述：

![Azure 策略合规性分数](media/simple-compliance.png)

总体资源合规性百分数是所有范围内已部署资源针对所有已分配 Azure 策略的合规性的汇总。 这使你可以识别环境中不合规的资源，并制定计划来最有效地修正这些资源。

策略合规性仪表板还包括每个资源的更改历史记录。 如果资源被识别为不再符合已分配的策略，且自动修复未启用，则可以查看进行更改的人员、更改的内容以及对该资源进行更改的时间。

## <a name="azure-blueprints"></a>Azure 蓝图

Azure 蓝图可扩展 Azure Policy 的功能，方式是将其与以下各项结合：

* Azure RBAC
* Azure 资源组
* [Azure 资源管理器模板](../azure-resource-manager/templates/syntax.md)

蓝图允许创建环境设计，这些设计从资源管理器模板部署 Azure 资源、配置 Azure RBAC 并通过分配 Azure Policy 来实施和审核配置。 蓝图可构造可编辑和可重新部署的环境模板。 创建蓝图后，可将其分配给 Azure 订阅。 分配后，将创建蓝图中定义的所有 Azure 资源并应用 Azure 策略。 可以从 Azure 门户中的 Azure 蓝图控制台监视 Azure 蓝图中定义的资源的部署和配置。

已编辑的 Azure 蓝图必须在 Azure 门户中重新发布。 每次重新发布蓝图时，蓝图的版本号都会递增。 根据版本号可确定哪个特定版本的蓝图已部署到组织的 Azure 订阅。 如果需要，可将当前分配的蓝图版本更新为最新版本。

部署时，可以使用 [Azure 资源锁](../azure-resource-manager/management/lock-resources.md)配置使用 Azure 蓝图部署的资源。 资源锁可阻止意外修改或删除资源。

Microsoft 正针对一系列行业和监管要求开发 Azure 蓝图模板。 可以在 Azure 门户中或服务信任门户的 [Azure 安全与合规性蓝图](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/)页面中查看当前的可用 Azure 蓝图定义库。

### <a name="azure-blueprint-artifacts"></a>Azure 蓝图项目

若要创建 Azure 蓝图，可以从一个空白蓝图模板开始，或使用一个现有的示例蓝图作为起点。 可以向将在部署期间配置的蓝图添加项目：

![Azure 蓝图项目](media/blueprint-artifacts.png)

这些项目可能包括 Azure 资源组和资源，以及相关的 Azure Policy 和 Policy 计划，以强制实施使环境符合监管要求所需的配置（例如用于系统强化的 ISM 控制）。

还可以为其中的每一个项目配置参数。 将蓝图分配给 Azure 订阅并部署后，将提供这些值。 参数允许创建并使用单个蓝图将资源部署到不同的环境，且无需编辑底层蓝图。

Microsoft 正在开发 Azure PowerShell 和 CLI cmdlet 来创建和管理 Azure 蓝图，旨在让组织可以通过 CI/CD 管道维护和部署蓝图。

## <a name="next-steps"></a>后续步骤

本文说明了如何使用 Azure Policy 和 Azure 蓝图实施治理和安全性。 大致浏览本文后，请更详细地了解如何使用每项服务：

* [Azure Policy 概述](../governance/policy/overview.md)
* [Azure 蓝图概述](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy 示例](../governance/policy/samples/index.md)
* [Azure Policy 示例存储库](https://github.com/Azure/azure-policy)
* [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)
* [Azure Policy 效果](../governance/policy/concepts/effects.md)