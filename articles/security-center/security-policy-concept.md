---
title: 了解 Azure 安全中心内的安全策略、计划和建议
description: 了解 Azure 安全中心内的安全策略、计划和建议。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: e065aa7d3d269632c77a810917ec913c43e518db
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605270"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>什么是安全策略、计划和建议？

安全中心将安全计划应用于你的订阅。 这些计划包含一个或多个安全策略。 这些策略每一个都会产生一个安全建议，用于改进安全态势。 本页详细介绍了这些理念中的每一个。


## <a name="what-is-a-security-policy"></a>什么是安全策略？

一种 Azure Policy 定义，创建于 Azure Policy，是一种有关你要控制的特定安全状况的规则。 内置的定义包括诸如控制可以部署什么类型的资源或在所有资源上强制使用标签之类的事情。 你还可以创建你自己的自定义策略定义。

若要实现这些策略定义（不管是内置定义还是自定义定义），你需要分配它们。 可通过 Azure 门户、PowerShell 或 Azure CLI 来分配上述任意策略。

Azure Policy 中有各种类型的策略。 安全中心主要使用“审核”策略，这类策略检查特定的条件和配置，然后报告合规性。 还有“强制”策略，可以使用这类策略来应用安全设置。

## <a name="what-is-a-security-initiative"></a>什么是安全计划？

Azure Policy 计划是为满足特定目标或目的而组合在一起的 Azure Policy 定义或规则集合。 Azure 计划在逻辑上将一组策略分组为单个项，从而简化了对策略的管理。

安全计划定义工作负荷的所需配置，有助于确保你遵守公司或监管机构的安全要求。

与安全策略一样，安全中心计划也是在 Azure Policy 中创建的。 你可以使用 [Azure Policy](../governance/policy/overview.md) 来管理策略、构建计划，以及将计划分配到多个订阅或整个管理组。

自动分配给 Azure 安全中心内的每个订阅的默认计划是 Azure 安全基准。 此基准是 Microsoft 制定的一组 Azure 专属准则，适合基于常见合规框架的安全性与合规性最佳做法。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。 详细了解 [Azure 安全基准](/security/benchmark/azure/introduction)。

安全中心提供以下选项来让用户使用安全计划和策略：

- **查看和编辑内置默认计划** - 启用安全中心时，会将名为“Azure Security Benchmark”的计划自动分配到所有已在安全中心注册的订阅。 若要自定义此计划，可以通过编辑策略的参数来启用或禁用计划中的各个策略。 要了解现成可用的选项，请参阅[内置安全策略](./policy-reference.md)列表。

- **添加你自己的自定义计划** - 如果希望自定义要应用到自己的订阅的安全计划，可以在安全中心执行此操作。 如果计算机不遵循创建的策略，则你会收到建议。 有关构建和分配自定义策略的说明，请参阅[使用自定义安全计划和策略](custom-security-policies.md)。

- **添加合规性标准作为计划** - 安全中心的合规性仪表板显示环境内的所有评估在特定标准或法规（例如 Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020）上下文中的状态。 有关详细信息，请参阅[改善合规性](security-center-compliance-dashboard.md)。

## <a name="what-is-a-security-recommendation"></a>什么是安全建议？

使用策略，安全中心会定期分析资源的合规性状态，以确定潜在的安全错误配置和弱点。 然后，它会提供有关如何修正这些问题的建议。 建议是根据相关策略对资源进行评估并查明不满足已定义要求的资源时得到的结果。

安全中心根据你所选择的计划提供安全建议。 如果计划中的某个策略在与资源进行比较时发现一个或多个资源不合规，则该策略会作为建议显示在安全中心内。

建议是为了保护和强化资源而需要采取的措施。 每项建议都提供以下信息：

- 问题的简短说明
- 为实施建议而要执行的修正步骤
- 受影响的资源

实际上，它的工作方式如下所述：

1. Azure 安全基准是一个计划
1. 它包括了一个策略，该策略要求所有 Azure 存储帐户限制网络访问以减少其受攻击面。 此策略称为“存储帐户应使用虚拟网络规则限制网络访问”，可以从 Azure Policy 中禁用或启用
1. 如果 Azure 安全中心在任何受保护的订阅中找到 Azure 存储帐户，该中心会评估这些帐户，看它们是否受虚拟网络规则保护。 如果这些帐户不受保护，该中心会显示一个建议，用于纠正这种情况并加强这些资源的安全性。 

因此，计划 (1) 包含策略 (2)，而策略在适当的情况下会生成建议 (3)。 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>查看建议与策略之间的关系

如上所述，安全中心的内置建议基于 Azure 安全基准。 几乎每条建议都有一个基础策略，该策略源自基准中的需求。

查看建议的详细信息时，能够查看基础策略通常会很有帮助。 对于策略支持的每条建议，请使用建议详细信息页中的“查看策略定义”链接直接进入相关策略的 Azure 策略条目：

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="链接到 Azure Policy 页面，了解支持建议的特定策略。":::

使用此链接可查看策略定义和计算逻辑。 

如果查看[安全建议参考指南](recommendations-reference.md)上的建议列表，你还将看到指向策略定义页面的链接：

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="直接从 Azure 安全中心建议参考页访问 Azure Policy 页来了解特定策略。":::


## <a name="next-steps"></a>后续步骤

本页概要解释了策略、计划和建议的基本概念以及它们之间的关系。 如需相关信息，请参阅：

- [创建自定义计划](custom-security-policies.md)
- [禁用安全策略以禁用建议](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [了解如何在 Azure Policy 中编辑安全策略](../governance/policy/tutorials/create-and-manage.md)