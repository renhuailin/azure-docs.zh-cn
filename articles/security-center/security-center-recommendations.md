---
title: Azure 安全中心的安全建议
description: 本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100332"
---
# <a name="review-your-security-recommendations"></a>查看安全建议

本主题说明如何查看和了解 Azure 安全中心内的建议，以帮助你保护 Azure 资源。

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
        - “强制执行”和“拒绝”（请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)）
        - **查看策略定义**，用于直接进入基础策略的 Azure 策略条目
    1. 严重性指标
    1. 刷新间隔（如果相关）
    1. 已豁免资源计数 如果此建议存在豁免，则显示已豁免的资源数
    1. 描述 - 问题简述
    1. 修正步骤 - 修正受影响资源的安全问题时所需的手动步骤的说明。 对于带有“快速修复”的建议，可以先选择“查看修正逻辑”，然后再为资源应用建议的修补程序。 
    1. 受影响的资源 - 资源会分组到不同的选项卡中：
        - 正常资源 - 相关的资源，这些资源要么未受影响，要么已经修正了问题。
        - 不正常的资源 - 已确定问题仍会影响的资源。
        - 不适用的资源 - 建议无法为其提供明确答案的资源。 “不适用”选项卡还会为每个资源提供原因。 

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
- [使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。
- [自动执行对安全中心触发器的响应](workflow-automation.md) -- 自动响应建议
- [从建议中免除资源](exempt-resource.md)
- [安全建议 - 参考指南](recommendations-reference.md)