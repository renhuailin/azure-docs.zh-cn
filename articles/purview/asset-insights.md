---
title: 'Azure 监控范围中的数据资产见解 (预览) '
description: 本操作方法指南介绍了如何查看和使用监控范围 Insights 资产报表数据。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 5045a13bb932f5907584bae23e956374a5757a68
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552175"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Azure 监控范围中的数据资产见解

本操作方法指南介绍了如何访问、查看和筛选数据的监控范围资产见解报表。

在本操作方法指南中，你将学习如何执行以下操作：

* 从你的监控范围帐户查看见解。
* 获取数据的鸟瞰视图。
* 向下钻取更多资产计数详细信息。

## <a name="prerequisites"></a>先决条件

在开始利用监控范围 insights 之前，请确保已完成以下步骤：

* 设置你的 Azure 资源，并在该帐户中填充数据。

* 在源类型上设置并完成扫描。

有关详细信息，请参阅 [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md)。

## <a name="use-purview-asset-insights"></a>使用监控范围资产见解

在 Azure 监控范围中，可以注册和扫描源类型。 扫描完成后，可以在资产见解中查看资产分布，这会告诉你数据的分类和资源集的状态。 它还会告诉您数据大小是否有任何变化。

> [!NOTE]
> 在扫描了源类型后，请将资产见解最多为一小时以反映新资产。

1. 在 Azure 门户中导航到 Azure 监控范围资源。

1. 在 " **概述** " 页上的 " **开始** " 部分中，选择 " **打开监控范围 Studio** " 磁贴。

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="从 Azure 门户启动监控范围":::

1. 在监控范围 **主页** 上，选择 " **查看见解** " 磁贴来访问 **insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: 区域。

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="查看 Azure 门户中的见解":::

1. 在 " **见解**" :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: 区域中，选择 " **资产** " 以显示 "监控范围 **资产见解** 报表"。

### <a name="view-asset-insights"></a>查看资产见解

1. " **资产见解** " 页显示以下区域：

2. 用于显示源类型、分类资产和发现资产的高级 KPI
 
3. 第一个图显示了 " **每个源的资产" 类型**。

4. 按源类型查看资产分布。 选择分类或整个分类类别以查看按源类型列出的资产分布。 
 
5. 若要查看详细信息，请选择 " **查看更多**"，其中显示源类型和资产计数的表格格式。 分类筛选器将传送到此页。

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="在资产见解中查看 Kpi 和关系图":::
 
6. 选择要查看其资产计数的顶级文件夹的特定源。 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="选择源类型":::
 
7. 针对上面选择的源类型中的顶层文件夹选择总资产。

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="查看文件路径":::

8. 查看文件夹中的文件列表。 使用 "面包 crumb" 返回到见解。

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="查看资产列表":::  

### <a name="file-based-source-types"></a>基于文件的源类型
资产见解中的下几个图形显示了基于文件的源类型的分布。 第一个关系图（称为 **大小趋势 (GB）在源类型中的文件类型)**，显示过去30天内最顶部的文件类型大小趋势。 
 
1. 选取源类型以查看源中的文件类型。 
 
1. 选择 " **查看更多** " 可查看当前数据大小、更改大小、当前资产计数和资产计数更改。
 
   > [!NOTE]
   > 如果扫描在过去的30天内只运行一次，或任何目录更改（例如分类添加/删除仅在30天内发生一次），则上述更改信息显示为空白。

1. 单击 "源类型" 时，请查看具有更改最高资产计数的顶级文件夹。

1. 选择要查看资产列表的路径。

基于文件的源类型中的第二个关系图是 **_不与资源集关联的文件_**。 如果希望所有文件都应汇总到资源集中，此图可以帮助你了解哪些资产尚未汇总。 缺少的资产可以指示文件夹中的文件模式错误。 按照其他关系图中的相同步骤查看有关文件的更多详细信息。

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="查看基于文件的资产":::  

## <a name="next-steps"></a>后续步骤

详细了解具有[扫描见解](./scan-insights.md)的 Azure 监控范围见解报表
