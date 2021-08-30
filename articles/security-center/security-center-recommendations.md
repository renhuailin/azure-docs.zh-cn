---
title: Azure 安全中心的安全建议
description: 本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 5921ad250934121223c35012d2278165b3dcc262
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237190"
---
# <a name="review-your-security-recommendations"></a>查看安全建议

本主题说明如何查看和了解 Azure 安全中心内的建议，以帮助你保护 Azure 资源。

## <a name="monitor-recommendations"></a>监视建议 <a name="monitor-recommendations"></a>

安全中心将分析资源的安全状态，以识别潜在的漏洞。 

1. 在安全中心的菜单中，打开“建议”页，查看适用于你的环境的建议。 建议会被分组到各项安全控制中。

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="按安全控制分组的建议。" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. 若要查找特定于资源类型、严重性、环境或其他对你很重要的条件的建议，请使用建议列表上方的可选筛选器。

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="用于优化 Azure 安全中心建议列表的筛选器。":::

1. 展开一项控制并选择特定的建议，以查看建议详细信息页。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="建议详细信息页。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    该页面包括：

    1. 对于支持的建议，顶部工具栏将显示以下任意或所有按钮：
        - “强制执行”和“拒绝”（请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)）
        - **查看策略定义**，用于直接进入基础策略的 Azure 策略条目
        - 打开查询 - 所有建议都提供使用 Azure Resource Graph 浏览器查看受影响资源相关详细信息的选项
    1. 严重性指标
    1. 刷新间隔（如果相关）
    1. 已豁免资源计数 如果此建议存在豁免，则显示已豁免的资源数
    1. 描述 - 问题简述
    1. 修正步骤 - 修正受影响资源的安全问题时所需的手动步骤的说明。 对于带有“修复”选项的建议，可以先选择“查看修正逻辑”，然后再为资源应用建议的修补程序 。
    1. 受影响的资源 - 资源会分组到不同的选项卡中：
        - 正常资源 - 相关的资源，这些资源要么未受影响，要么已经修正了问题。
        - 不正常的资源 - 已确定问题仍会影响的资源。
        - 不适用的资源 - 建议无法为其提供明确答案的资源。 “不适用”选项卡还会为每个资源提供原因。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="不适用的资源及其原因。":::
    1. 用于修正建议或触发逻辑应用的操作按钮。


## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>在 Azure Resource Graph 浏览器 (ARG) 中查看建议数据

建议详细信息页上的工具栏包含一个“打开查询”按钮，可用于在 [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)（一项 Azure 服务，可用于在多个订阅中查询安全中心的安全状态数据）中浏览详细信息。

ARG 旨在提高资源探索的效率，能够通过强大的筛选、分组和排序功能跨云环境进行大规模查询。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。

使用 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)，可以交叉引用 ASC 数据与其他资源属性。

例如，此建议详细信息页显示了十五个受影响的资源：

:::image type="content" source="./media/security-center-recommendations/open-query.png" alt-text="建议详细信息页上的“打开查询”按钮。":::

打开基础查询并运行它后，Azure Resource Graph 浏览器会返回与此建议相同的 15 个资源及其运行状况： 

:::image type="content" source="./media/security-center-recommendations/run-query.png" alt-text="Azure Resource Graph 浏览器显示了上一屏幕截图中所示的建议的结果。":::


## <a name="preview-recommendations"></a>预览建议

计算安全分数时不包括标记为“预览”的建议。

仍应尽可能按这些建议修正，以便在预览期结束时，它们会有助于提升评分。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议。":::
 
## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍安全中心的安全建议。 如需相关信息：

- [修正建议](security-center-remediate-recommendations.md) -- 了解如何为 Azure 订阅和资源组配置安全策略。
- [使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。
- [自动执行对安全中心触发器的响应](workflow-automation.md) -- 自动响应建议
- [从建议中免除资源](exempt-resource.md)
- [安全建议 - 参考指南](recommendations-reference.md)