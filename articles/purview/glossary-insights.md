---
title: 使用监控范围 Insights 对你的数据进行术语表报告
description: 本操作方法指南介绍了如何查看和使用监控范围 Insights 词汇表报表数据。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095844"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>有关 Azure 中数据的术语表见解监控范围

本操作方法指南介绍了如何访问、查看和筛选数据的监控范围词汇表见解报表。

在本操作方法指南中，你将学习如何执行以下操作：

> [!div class="checklist"]
> - 从你的监控范围帐户中转到 Insights
> - 获取数据的鸟瞰视图

## <a name="prerequisites"></a>先决条件

在开始利用监控范围 insights 之前，请确保已完成以下步骤：

- 设置你的 Azure 资源，并为该帐户填充数据

- 在源类型上设置并完成扫描

- 设置术语表并将资产附加到词汇表术语

有关详细信息，请参阅 [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md)。

## <a name="use-purview-glossary-insights"></a>使用监控范围术语表见解

在 Azure 监控范围中，可以创建词汇表术语并将其附加到资产。 稍后，可以在词汇表见解中查看术语表分布。 这会告诉你术语表的状态（按附加到资产的术语）。 它还告诉你按用户数的状态和角色分配的条款。

**查看词汇表见解：**

1. 在 Azure 门户中转到 **Azure 监控范围**[实例屏幕](https://aka.ms/purviewportal)，并选择监控范围帐户。

1. 在 " **概述** " 页上的 " **开始** " 部分中，选择 " **启动监控范围** 帐户" 磁贴。

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="从 Azure 门户启动监控范围":::

1. 在监控范围 **主页** 上，选择 " **查看见解** " 磁贴来访问 **insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 区域。

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="查看 Azure 门户中的见解":::

1. 在 " **见解**" :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 区域中，选择 " **术语表** " 以显示 "监控范围 **词汇表见解** 报表"。

**术语表见解** 为你的业务用户提供了一个重要信息，以便为你的组织维护一个完善定义的术语表。

1. 该报表从 **高级 kpi** 开始，其中显示了 ***你的监控范围帐户中* 的 _所有条款_ _，_ _已获批准的条款，无资产_*_ 和 _* 已 _过期条款与资产_**。 其中每个值都有助于标识术语表的运行状况。

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="查看术语表见解 KPI"::: 


2. 如上所示 (**术语的快照**) 将你的条款状态显示为 **_草稿_*_、_*_批准_ 的 *_、_*_Alert_*_ 和 _* 已 _过期_，** 但不包含资产的条款和条款。

3. 单击 " **查看更多** " 可查看术语名称，其中包含各种状态以及有关 **_专员_*_ 和 _*_专家_** 的更多详细信息。 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="具有和不包含资产的术语的快照":::  

4. 当你单击 "查看更多" 以获取有关 "资产" 的 **批准的条款** 时，Insights 允许你导航到 _ *词汇表** 条款详细信息页，你可以在该页面上进一步导航到包含附加字词的资产列表。 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="见解到术语表"::: 

4. 在 "词汇表见解" 页面中，按缺少的信息类型查看 **未完成条款** 的分布。 此图显示了 **_缺少定义_ 的字词计数 *_，*_缺少专家_*_ _，_*_缺少专员_*_ 和 _*_缺少多个_** 字段。

1. 单击 "**查看详细** 信息" _ "不完整的字词 * *" 以查看缺少信息的字词。 你可以导航到术语表术语详细信息页来输入缺少的信息，并确保术语表术语已完成。

## <a name="next-steps"></a>后续步骤

详细了解如何通过[词汇表](./how-to-create-import-export-glossary.md)创建词汇表术语