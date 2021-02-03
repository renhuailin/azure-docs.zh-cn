---
title: Azure 安全中心的安全建议
description: 本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 3b2f111f83dbd731b69671e58d4bf9dc648a596f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526482"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 安全中心的安全建议 

本主题说明如何查看和了解 Azure 安全中心内的建议，以帮助你保护 Azure 资源。


## <a name="what-are-security-recommendations"></a>安全建议是什么？

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后会提供有关如何消除这些安全漏洞的建议。

建议是为了保护和强化资源而要采取的措施。 

每项建议都提供：

- 问题的简短说明
- 为实施建议而要执行的修正步骤
- 受影响的资源

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Microsoft 如何决定需要保护和强化哪些内容？

安全中心的建议以 Azure 安全基准为基础。 几乎每个建议都有一个从基准中的要求派生的基础策略。

Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。 详细了解 [Azure 安全基准](../security/benchmarks/introduction.md)。

查看建议的详细信息时，查看底层策略通常很有帮助。 对于策略支持的每个建议，请使用 "建议详细信息" 页中的 " **查看策略定义** " 链接直接进入适用于相关策略的 Azure 策略条目：

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="支持建议的特定策略的 &quot;链接到 Azure 策略&quot; 页":::

使用此链接可查看策略定义并查看评估逻辑。 

如果你查看有关我们的 [安全建议参考指南](recommendations-reference.md)的建议列表，你还将看到策略定义页面的链接：

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="直接从 Azure 安全中心建议参考页访问特定策略的 Azure 策略页":::

## <a name="monitor-recommendations"></a>监视建议 <a name="monitor-recommendations"></a>

安全中心将分析资源的安全状态，以识别潜在的漏洞。 

1. 在安全中心的菜单中，打开“建议”页，查看适用于你的环境的建议。 建议会被分组到各项安全控制中。

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="建议会按安全控制分组" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. 若要查找特定于资源类型、严重性、环境或其他对你很重要的条件的建议，请使用建议列表上方的可选筛选器。

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="用于优化 Azure 安全中心建议列表的筛选器":::

1. 展开一项控制并选择特定的建议，以查看建议详细信息页。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="建议详细信息页。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    该页面包括：

    1. 对于支持的建议，顶部工具栏将显示以下任意或所有按钮：
        - **强制** 和 **拒绝** (参阅 [阻止强制/拒绝建议的错误配置](prevent-misconfigurations.md)) 
        - **查看策略定义** 以直接进入基础策略的 Azure 策略条目
    1. 严重性指标
    1. **刷新间隔** (相关) 
    1. **免除的资源** 数如果此建议存在免除，此值将显示已免除的资源数
    1. 描述 - 问题简述
    1. 修正步骤 - 修正受影响资源的安全问题时所需的手动步骤的说明。 对于带有“快速修复”的建议，可以先选择“查看修正逻辑”，然后再为资源应用建议的修补程序。 
    1. 受影响的资源 - 资源会分组到不同的选项卡中：
        - **正常资源** –不受影响的相关资源，或已修正问题的相关资源。
        - 不 **正常资源**–仍会受到确定问题影响的资源。
        - **不适用的资源** –建议无法提供明确答案的资源。 “不适用”选项卡还会为每个资源提供原因。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="不适用的资源及其原因。":::
    1. 用于修正建议或触发逻辑应用的操作按钮。

## <a name="preview-recommendations"></a>预览建议

计算安全分数时不包括标记为“预览”的建议。

仍应尽可能按这些建议修正，以便在预览期结束时，它们会有助于提升评分。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议":::
 
## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍安全中心的安全建议。 如需相关信息：

- [修正建议](security-center-remediate-recommendations.md) -- 了解如何为 Azure 订阅和资源组配置安全策略。
- [防止对强制/拒绝建议的配置](prevent-misconfigurations.md)错误。
- [自动执行对安全中心触发器的响应](workflow-automation.md) -- 自动响应建议
- [从建议中免除资源](exempt-resource.md)
- [安全建议 - 参考指南](recommendations-reference.md)