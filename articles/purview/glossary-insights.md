---
title: 使用监控范围 Insights 对你的数据进行术语表报告
description: 本操作方法指南介绍了如何查看和使用监控范围 Insights 词汇表报表数据。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 9f7d81423c75c3e1a51f5b5d5f37c54307488eb3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551717"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>有关 Azure 中数据的术语表见解监控范围

本操作方法指南介绍了如何访问、查看和筛选数据的监控范围词汇表见解报表。

在本操作方法指南中，你将学习如何执行以下操作：

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

**术语表见解** 页显示以下区域：
1. 用于显示术语表术语和目录用户的 **高级 kpi**

2. **最常见的术语表术语和资产计数** 显示附加了资产的前5个术语表术语。 所有其他资产都在关系图中的 "其他" 类别中进行。

3. 术语 "**词汇表** 术语" 按 "草稿"、"已批准"、"警报" 和 "过期" 等状态显示词汇表术语的分布情况。 

1. 悬停或单击具有状态的关系图切片，并记下具有该状态的字词的计数。

1. **按用户数量分配角色** 会显示在监控范围中按角色的用户数分配角色。

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="查看术语表见解":::

## <a name="next-steps"></a>后续步骤

了解有关 Azure 监控范围洞察报表的详细信息，请参阅 [资产见解](./asset-insights.md)