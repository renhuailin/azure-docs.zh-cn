---
title: Azure Purview 中的数据资产见解
description: 本操作方法指南介绍了如何查看和使用有关数据的 Purview 见解资产报表。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8879edc7d1858cff5871c5339da4857d81f5c2d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216997"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Azure Purview 中的数据资产见解

本操作方法指南介绍了如何访问、查看和筛选有关数据的 Purview 资产见解报表。

> [!IMPORTANT]
> Azure Purview 见解目前处于预览阶段。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本操作指南中，你将学习如何：

> [!div class="checklist"]
> * 通过 Purview 帐户查看见解。
> * 获取数据的宏观视图。
> * 深入了解更多资产计数的详细信息。

## <a name="prerequisites"></a>先决条件

在开始利用 Purview 见解之前，请确保已完成以下步骤：

* 设置 Azure 资源，并在该帐户中填充数据。

* 在源类型上设置并完成扫描。

有关详细信息，请参阅[在 Azure Purview 中管理数据源](manage-data-sources.md)。

## <a name="use-purview-asset-insights"></a>使用 Purview 资产见解

在 Azure Purview 中，可以注册和扫描源类型。 扫描完成后，可以在资产见解中查看资产分布，并按分类和资源集了解数据资产的状态。 还能了解数据大小是否有任何变化。

> [!NOTE]
> 在扫描了源类型后，请等待 3-8 小时，让资产见解反映出新资产。 延迟的原因可能是部署区域中的流量较高或工作负载大小所致。 有关详细信息，请联系现场支持团队。

1. 在 Azure 门户中导航到自己的 Azure Purview 资源。

1. 在“概述”页上的“入门”部分中，选择“打开 Purview Studio”磁贴  。

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="从 Azure 门户启动 Purview":::

1. 在 Purview 主页上，选择左侧菜单中的“见解”。

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="在 Azure 门户中查看见解":::

1. 在“见解”区域中，选择“资产”以显示 Purview“资产见解”报表  。

### <a name="view-asset-insights"></a>查看资产见解

1. “资产见解”主页显示以下区域：

2. 可显示源类型、分类资产和发现资产的高级 KPI
 
3. 第一个图显示了“每个源类型的资产”。

4. 按源类型查看资产分布。 选择某个细类或整个分类，以按源类型查看资产分布。 
 
5. 若要查看详细信息，请选择“查看更多”，其中显示源类型和资产计数的表格格式。 分类筛选器将载入此页。

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="在资产见解中查看 KPI 和关系图":::
 
6. 选择要查看其顶级文件夹和资产计数的特定源。 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="选择源类型":::
 
7. 针对上面选择的源类型中的顶层文件夹选择总资产。

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="查看文件路径":::

8. 查看文件夹中文件的列表。 使用痕迹导航返回到见解。

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="查看资产列表":::  

### <a name="file-based-source-types"></a>基于文件的源类型
资产见解中接下来几个关系图显示了基于文件的源类型的分布。 第一个关系图名为“源类型中文件类型的大小趋势 (GB)”，显示过去 30 天内最顶部的文件类型大小趋势。 
 
1. 选取源类型以查看源中的文件类型。 
 
1. 选择“查看更多”以查看当前数据大小、大小的更改、当前资产计数和资产计数的更改。
 
   > [!NOTE]
   > 如果扫描在过去的 30 天内只运行一次，或任何目录在 30 天内仅发生一次更改（例如分类添加/删除），上述更改信息将显示为空白。

1. 选择源类型时，请查看最高资产计数发生了更改的顶级文件夹。

1. 选择资产列表的查看路径。

基于文件的源类型中的第二个关系图是“不与资源集关联的文件”。 如果希望所有文件都应汇总到资源集中，此图可以帮助你了解哪些资产尚未汇总。 资产丢失可能表明文件夹中的文件模式存在错误。 按照其他关系图中的相同操作步骤查看有关文件的更多详细信息。

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="查看基于文件的资产":::  

## <a name="next-steps"></a>后续步骤

通过[扫描见解](./scan-insights.md)进一步了解 Azure Purview 见解报表
