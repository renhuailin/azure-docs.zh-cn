---
title: 在 Azure Purview 中使用 Purview 见解提供有关数据的分类报表
description: 本操作指南介绍如何查看和使用有关数据的 Purview 分类报表。
author: batamig
ms.author: bagol
ms.service: purview
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 642611a680e0848208aa7e42493fee6fc16fb4d7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208829"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>有关 Azure Purview 中的数据的分类见解

本操作指南介绍如何访问、查看和筛选有关数据的 Purview 分类见解报表。

> [!IMPORTANT]
> Azure Purview 见解目前处于预览阶段。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

支持的数据源包括：Azure Blob 存储、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、Azure Cosmos DB (SQL API)、Azure Synapse Analytics（原 SQL DW）、Azure SQL 数据库、Azure SQL 托管实例、SQL Server、Amazon S3 存储桶

在本操作指南中，你将学习如何：

> [!div class="checklist"]
> - 从 Azure 启动 Purview 帐户
> - 查看数据的分类见解
> - 深入了解更多有关数据的分类详细信息

## <a name="prerequisites"></a>先决条件

在开始利用 Purview 见解之前，请确保已完成以下步骤：

- 设置 Azure 资源并用测试数据填充相关帐户

- 对每个数据源中的测试数据设置并完成扫描。 有关详细信息，请参阅[在 Azure Purview 中管理数据源](manage-data-sources.md)和[创建扫描规则集](create-a-scan-rule-set.md)。

- 使用具有 [Data Reader 或 Data Curator 角色](catalog-permissions.md#roles)的帐户登录到 Purview。

有关详细信息，请参阅[在 Azure Purview 中管理数据源](manage-data-sources.md)。

## <a name="use-purview-classification-insights"></a>使用 Purview 分类见解

在 Azure Purview 中，分类类似于主题标签，用于标记和标识扫描期间在数据资产中找到的特定类型的数据。

Purview 使用与 Microsoft 365 相同的敏感信息类型，可让你将现有的安全策略和保护延伸到整个数据资产。

> [!NOTE]
> 扫描源类型后，留几小时让“分类”见解反映新资产。

**查看分类见解：**

1. [在 Azure 门户中转到“Azure Purview”实例屏幕](https://aka.ms/purviewportal)，并选择 Purview 帐户。

1. 在“概述”页上的“入门”部分中，选择“Purview Studio”磁贴  。

1. 在 Purview 中，选择左侧的“见解”(:::image type="icon" source="media/insights/ico-insights.png" border="false":::) 菜单项以访问“见解”区域。

1. 在“见解”(:::image type="icon" source="media/insights/ico-insights.png" border="false":::) 区域中，选择“分类”以显示 Purview“分类见解”报表  。

   :::image type="content" source="./media/insights/select-classification-labeling.png" alt-text="“分类见解”报表" lightbox="media/insights/select-classification-labeling.png":::

   “分类见解”主页显示以下区域：

   |区域  |说明  |
   |---------|---------|
   |**带分类的源概述**     |显示提供以下信息的磁贴： <br>- 在数据中找到的订阅数 <br>- 在数据中找到的唯一分类数 <br>- 找到的已分类源数 <br>- 找到的已分类文件数 <br>- 找到的已分类表数         |
   |**带有分类数据的热门源(最近 30 天)**     |显示最近 30 天在分类数据中找到的源的数量趋势。            |
   |**按源划分的热门分类类别**     |显示按“Financial”或“Financial”等分类类别找到的源的数量 。      |
   |**文件的热门分类**     |显示应用于数据中的文件的热门分类，例如信用卡号或身份证号。         |
   |**表的热门分类**     | 显示应用于数据中的表的热门分类，例如个人身份信息。 |   
   |  **分类活动** <br>（文件和表） |  针对文件和表显示单独的图，每个图显示在所选时间范围内分类的文件或表的数量。 <br>默认值：30 天<br>选择图上方的“时间”筛选器，可选择要显示的其他时间范围。    |
   |    |    |

## <a name="classification-insights-drilldown"></a>深入了解分类见解

在以下任何“分类见解”图中，选择“查看更多”链接来深入了解更多详细信息 ：

- **按源划分的热门分类类别**
- **文件的热门分类**
- **表的热门分类**
- **分类活动 > 分类数据**

例如：

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="查看所有分类":::

若要了解详细信息，请执行以下任一操作：

|选项  |说明  |
|---------|---------|
|**筛选数据**     |  使用网格上方的筛选器来筛选显示的数据，包括分类名称、订阅名称或源类型。 <br><br>如果你不确定确切的分类名称，可在“按关键字筛选”框中输入部分或完整名称。       |
|**对网格排序** |选择一个列标题，按该列对网格排序。 | 
|**编辑列**     |  若要在网格中显示更多或更少的列，请选择“编辑列”(:::image type="icon" source="media/insights/ico-columns.png" border="false":::)，然后选择要查看或更改其顺序的列。   |
|**深入了解**     | 若要深入了解某个特定分类，请在“分类”列中选择一个名称，以查看“按源分类”报表 。 <br><br>此报表显示所选分类的数据，其中包括源名称、源类型、订阅 ID 以及已分类的文件和表的数量。      |
|**浏览资产**     |  若要浏览所找到的具有特定分类或源的资产，请选择分类或源（具体取决于你正在查看的报表），然后选择筛选器上方的“浏览资产”(:::image type="icon" source="media/insights/ico-browse-assets.png" border="false":::)。 <br><br>搜索结果将显示根据所选筛选器找到的所有已分类的资产。  有关详细信息，请参阅[搜索 Azure Purview 数据目录](how-to-search-catalog.md)。       |
| | |

## <a name="next-steps"></a>后续步骤

详细了解 Azure Purview 见解报表
> [!div class="nextstepaction"]
> [术语表见解](glossary-insights.md)

> [!div class="nextstepaction"]
> [扫描见解](scan-insights.md)

> [!div class="nextstepaction"]
> [敏感度标记见解](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [文件扩展见解](file-extension-insights.md)
