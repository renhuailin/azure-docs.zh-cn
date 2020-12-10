---
title: '使用监控范围 Insights (预览版对你的数据进行分类报告) '
description: 本操作方法指南介绍了如何查看和使用监控范围 Insights 分类报表数据。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: bb3c7cc3f51eae90c5b712d224407e639b232fbc
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938879"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>有关 Azure 监控范围中的数据的分类见解

本操作方法指南介绍了如何访问、查看和筛选数据的监控范围分类见解报表。

支持的数据源包括： Azure Blob 存储、Azure Data Lake Storage (ADLS) 第1代、Azure Data Lake Storage (ADLS) GEN 2、Azure Cosmos DB (SQL API) 、Azure Synapse Analytics (以前的 SQL DW) 、Azure SQL 数据库、Azure SQL 托管实例、SQL Server

在本操作方法指南中，你将学习如何执行以下操作：

> [!div class="checklist"]
> - 从 Azure 启动你的监控范围帐户
> - 查看数据的分类见解
> - 向下钻取更多有关你的数据的分类详细信息

## <a name="prerequisites"></a>先决条件

在开始利用监控范围 insights 之前，请确保已完成以下步骤：

- 设置 Azure 资源并用测试数据填充相关帐户

- 为每个数据源中的测试数据设置并完成扫描 

有关详细信息，请参阅 [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md)。

## <a name="use-purview-classification-insights"></a>使用监控范围分类见解

在 Azure 监控范围中，分类类似于使用者标记，用于标记和标识在扫描过程中在数据空间内找到的特定类型的数据。

监控范围使用与 Microsoft 365 相同的敏感信息类型，使你能够在整个数据空间中延伸现有的安全策略和保护。

> [!NOTE]
> 扫描源类型后，请在几个小时内给予 **分类** 见解，以反映新资产。

**查看分类见解：**

1. 在 Azure 门户中转到 **Azure 监控范围**[实例屏幕](https://aka.ms/purviewportal)，并选择监控范围帐户。

1. 在 " **概述** " 页上的 " **开始** " 部分中，选择 " **启动监控范围帐户** " 磁贴。

1. 在监控范围中，选择左侧的 " **insights** " :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 菜单项以访问 **Insights** 区域。

1. 在 " **见解**" :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 区域中，选择 " **分类** " 以显示 "监控范围 **分类见解** 报表"。

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="分类见解报告" lightbox="media/insights/select-classification-labeling.png":::

   "主要 **分类见解** " 页显示以下区域：

   |区域  |说明  |
   |---------|---------|
   |**分类的源概述**     |显示提供的磁贴： <br>-在数据中找到的订阅数 <br>-在数据中找到的唯一分类数 <br>-找到的已分类源的数目 <br>-找到的已分类文件数 <br>-找到的已分类表的数目         |
   |**已分类数据 (过去30天的热门源)**     |显示过去30天内的趋势，其中包含已分类数据的源的数目。            |
   |**按源列出的顶级分类类别**     |显示分类类别找到的源的数量，如 **财务** 或 **政府**。      |
   |**文件的顶级分类**     |显示应用于数据中文件的顶级分类，例如信用卡号或国家标识号。         |
   |**表的顶级分类**     | 显示应用于数据中的表的顶级分类，例如个人标识信息。 |   
   |  **分类活动** <br> (文件和表)  |  显示文件和表的单独图形，每个图形显示在所选时间范围内分类的文件或表的数量。 <br>**默认值**：30天<br>选择图表上方的 **时间** 筛选器，以选择要显示的其他时间范围。    |
   |    |    |

## <a name="classification-insights-drilldown"></a>分类见解深化

在以下任何 **分类见解** 图中，选择 " **查看更多** " 链接以了解更多详细信息：

- **按源列出的顶级分类类别**
- **文件的顶级分类**
- **表的顶级分类**
- **分类活动 > 分类数据**

例如：

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="查看所有分类" lightbox="media/insights/view-classifications.png":::

若要了解详细信息，请执行以下任一操作：

|选项  |说明  |
|---------|---------|
|**筛选数据**     |  使用网格上方的筛选器筛选显示的数据，包括分类名称、订阅名称或源类型。 <br><br>如果你不确定确切的分类名称，则可以在 " **按关键字筛选** " 框中输入部分或全部名称。       |
|**对网格排序** |选择列标题可按该列对网格进行排序。 | 
|**编辑列**     |  若要在网格中显示更多或更少的列，请选择 " **编辑列** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: "，然后选择要查看或更改其顺序的列。   |
|**进一步向下钻取**     | 若要向下钻取到特定的分类，请在 " **分类** " 列中选择一个名称，查看 " **按源分类** " 报表。 <br><br>此报表显示所选分类的数据，其中包括源名称、源类型、订阅 ID 以及已分类文件和表的数量。      |
|**浏览资产**     |  若要浏览使用特定分类或源找到的资产，请根据所查看的报表选择分类或源，然后选择 " **浏览** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: " 筛选器之上的 "资产"。 <br><br>搜索结果将显示为所选筛选器找到的所有分类资产。  有关详细信息，请参阅 [搜索 Azure 监控范围 Data Catalog](how-to-search-catalog.md)。       |
| | |

## <a name="next-steps"></a>后续步骤

了解有关 Azure 监控范围见解报表的详细信息
> [!div class="nextstepaction"]
> [词汇表见解](glossary-insights.md)

> [!div class="nextstepaction"]
> [扫描见解](scan-insights.md)

> [!div class="nextstepaction"]
> [敏感度标记见解](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [文件扩展见解](file-extension-insights.md)