---
title: 有关数据的使用 Purview 见解的术语表报表
description: 本操作指南介绍了如何查看和使用有关数据的 Purview 见解术语表报表。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 315cfdc60de77ab4d6345c5d579351049c5129a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218893"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Azure Purview 中有关数据的术语表见解

本操作指南介绍了如何访问、查看和筛选有关数据的 Purview 术语表见解报表。

> [!IMPORTANT]
> Azure Purview 见解目前处于预览阶段。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本操作指南中，你将学习如何：

> [!div class="checklist"]
> - 从 Purview 帐户转到见解
> - 获取数据的鸟瞰视图

## <a name="prerequisites"></a>先决条件

在开始利用 Purview 见解之前，请确保已完成以下步骤：

- 设置 Azure 资源，并在该帐户中填充数据

- 设置并完成对源类型的扫描

- 设置术语表并将资产附加到术语表术语

有关详细信息，请参阅[在 Azure Purview 中管理数据源](manage-data-sources.md)。

## <a name="use-purview-glossary-insights"></a>使用 Purview 术语表见解

在 Azure Purview 中，可以创建术语表术语并将其附加到资产。 稍后可以在术语表见解中查看术语表的分布。 这将根据附加到资产的术语告诉你术语表的状态。 它还根据状态和用户数告诉你术语和角色分布。

要查看术语表见解：

1. [在 Azure 门户中转到“Azure Purview”实例屏幕](https://aka.ms/purviewportal)，并选择 Purview 帐户。

1. 在“概述”页上的“入门”部分中，选择“打开 Purview Studio”帐户磁贴  。

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="从 Azure 门户启动 Purview":::

1. 在 Purview 主页上，选择左侧菜单中的“见解”。

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="在 Azure 门户中查看见解":::

1. 在“见解”区域中，选择“术语表”，显示 Purview“术语表见解”报表  。

术语表见解为企业用户提供有价值的信息，以便为组织维护定义完善的术语表。

1. 报表从“综合 KPI”开始，其中显示 Purview 帐户中的“术语总数”、“没有资产的已批准术语”和“有资产的已到期术语” 。 其中每个值都有助于识别术语表的运行状况。

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="查看术语表见解 KPI"::: 


2. 术语快照部分（如上所示）显示有资产和无资产的术语的状态为“草稿”、“已批准”、“警报”、“已过期” 。

3. 选择“查看更多”以查看术语名称和对应的各种状态，以及关于专员和专家的更多详细信息 。 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="有和没有资产的术语的快照":::  

4. 针对“批准的资产条款”，选择“查看更多”时，见解使你可以导航到“术语表术语详细信息”页，从中可进一步导航到具有附加术语的资产列表。 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="术语表见解"::: 

4. 在“术语表见解”页中，查看“不完整的术语”的分布（按缺少的信息）。 图中显示具有“缺少定义”、“缺少专家”、“缺少专员”和“缺少多项”字段的术语的计数。

1. 在“不完整的术语”中选择“查看更多”，以查看缺少信息的术语。 你可以导航到“术语表术语详细信息”页，输入缺少的信息并确保术语表术语是完整的。

## <a name="next-steps"></a>后续步骤

详细了解如何通过[术语表](./how-to-create-import-export-glossary.md)创建术语表术语
