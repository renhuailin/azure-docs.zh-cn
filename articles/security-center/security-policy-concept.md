---
title: 了解 Azure 安全中心的安全策略、计划和建议
description: 了解 Azure 安全中心的安全策略、计划和建议。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176427"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>什么是安全策略、计划和建议？

安全中心将安全方案应用于你的订阅。 这些计划包含一个或多个安全策略。 其中每个策略都将产生安全建议，以提高安全状况。 本页详细说明了其中的每种观点。


## <a name="what-is-a-security-policy"></a>什么是安全策略？

Azure 策略定义是在 Azure 策略中创建的，它是有关要控制的特定安全条件的规则。 内置定义包括控制可部署的资源类型或强制对所有资源使用标记的操作。 您还可以创建自己的自定义策略定义。

若要实现这些策略定义 (内置或自定义) ，需要对其进行分配。 可通过 Azure 门户、PowerShell 或 Azure CLI 来分配上述任意策略。

Azure 策略中有不同类型的策略。 安全中心主要使用 "审核" 策略来检查特定条件和配置，然后报告符合性。 还可以使用 "强制" 策略来应用安全设置。

## <a name="what-is-a-security-initiative"></a>什么是安全计划？

Azure 计划是一组按特定目标或用途分组的 Azure 策略定义或规则。 Azure 计划通过以逻辑方式将一组策略组合为单个项来简化策略管理。

安全计划定义工作负荷的所需配置，并帮助确保符合公司或机构的安全要求。

与安全策略一样，安全中心计划也是在 Azure 策略中创建的。 可以使用 [Azure 策略](../governance/policy/overview.md) 管理策略、构建计划，并为多个订阅或整个管理组分配计划。

自动分配给 Azure 安全中心中的每个订阅的默认计划是 Azure 安全基准。 此基准是 Microsoft 创作的特定于 Azure 的准则集，针对基于通用符合性框架的安全性和符合性最佳实践。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。 详细了解 [Azure 安全基准](../security/benchmarks/introduction.md)。

安全中心提供以下用于处理安全方案和策略的选项：

- **查看和编辑内置默认计划** -启用安全中心时，名为 "Azure 安全基准测试" 的计划会自动分配到所有安全中心注册的订阅。 若要自定义此计划，可以通过编辑策略的参数在其中启用或禁用单个策略。 要了解现成可用的选项，请参阅[内置安全策略](./policy-reference.md)列表。

- **添加你自己的自定义计划** -如果要自定义适用于你的订阅的安全计划，可以在安全中心执行此操作。 如果计算机不遵循创建的策略，则你会收到建议。 有关生成和分配自定义策略的说明，请参阅 [使用自定义安全计划和策略](custom-security-policies.md)。

- **将法规遵从标准添加为计划** -安全中心的法规符合性仪表板显示了环境中的所有评估的状态，这些评估在特定标准或法规的上下文中 (例如，Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020) 。 有关详细信息，请参阅[改善合规性](security-center-compliance-dashboard.md)。

## <a name="what-is-a-security-recommendation"></a>什么是安全建议？

使用策略，安全中心会定期分析资源的符合性状态，以确定潜在的安全错误配置和弱点。 然后，提供有关如何修正这些问题的建议。 建议是根据相关策略评估资源，并确定不满足定义的要求的资源。

安全中心根据所选的计划做出安全建议。 当你的计划中的策略与你的资源进行比较并找到一个或多个不符合的策略时，它将在安全中心显示为建议。

建议是用于保护和强化资源的操作。 每项建议都提供以下信息：

- 问题的简短说明
- 为实施建议而要执行的修正步骤
- 受影响的资源

实际上，它的工作方式如下：

1. Azure 安全基准是一种 ***计划***
1. 它包括要求所有 Azure 存储帐户限制网络访问的 ***策略*** ，以减少其受攻击面。 此策略称为 "存储帐户应使用虚拟网络规则限制网络访问"，可以从 Azure 策略中禁用或启用
1. 如果 Azure 安全中心在任何受保护的订阅中找到 Azure 存储帐户，则会评估这些帐户以查看其是否受虚拟网络规则保护。 如果不是，则会显示一个 ***建议*** ，用于修复这种情况并强化这些资源的安全性。 

因此，计划 (1) 包括 (2) 的策略，这些策略在适当 (3) 时生成建议。 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>查看建议与策略之间的关系

如上所述，安全中心的内置建议基于 Azure 安全基准。 几乎每个建议都有一个从基准中的要求派生的基础策略。

查看建议的详细信息时，能够查看基础策略通常会很有帮助。 对于策略支持的每个建议，请使用 "建议详细信息" 页中的 " **查看策略定义** " 链接直接进入适用于相关策略的 Azure 策略条目：

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="链接到 Azure Policy 页面，了解支持建议的特定策略":::

使用此链接可查看策略定义和计算逻辑。 

如果查看[安全建议参考指南](recommendations-reference.md)上的建议列表，你还将看到指向策略定义页面的链接：

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="直接从 Azure 安全中心建议参考页访问 Azure Policy 页面来了解特定策略":::


## <a name="next-steps"></a>后续步骤

本页从较高层面介绍了策略、计划和建议之间的基本概念和关系。 如需相关信息，请参阅：

- [创建自定义计划](custom-security-policies.md)
- [禁用安全策略以禁用建议](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [了解如何在 Azure Policy 中编辑安全策略](../governance/policy/tutorials/create-and-manage.md)