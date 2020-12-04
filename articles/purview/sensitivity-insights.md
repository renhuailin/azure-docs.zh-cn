---
title: 对 Azure Blob 存储中的数据进行敏感标签报告
description: 本操作方法指南介绍了如何在 Azure Blob 存储中查看和使用数据的监控范围敏感度标签报告。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 74f4391fcd071ac510fc66b2f8bc36ec4549dc47
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575748"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>有关 Azure 监控范围中的数据的敏感度标签见解

本操作方法指南介绍了如何访问、查看和筛选应用于数据的敏感性标签提供的安全见解。

支持的数据源包括： Azure Blob 存储、Azure Data Lake Storage (ADLS) 第1代、Azure Data Lake Storage (ADLS) 第2代、SQL Server、Azure SQL 数据库、Azure SQL 托管实例

在本操作方法指南中，你将学习如何执行以下操作：

> [!div class="checklist"]
> - 从 Azure 启动你的监控范围帐户。
> - 查看对数据的敏感度标记见解
> - 向下钻取有关数据的更多敏感度标记详细信息

## <a name="prerequisites"></a>先决条件

在开始利用监控范围 insights 之前，请确保已完成以下步骤：

- 设置 Azure 资源并用测试数据填充相关帐户

- [对 Azure 监控范围中的资产进行扩展 Microsoft 365 敏感度标签](create-sensitivity-label.md)，并创建或选择要应用于数据的标签。

- 为每个数据源中的测试数据设置并完成扫描

有关详细信息，请参阅 [在 azure 中管理数据源 (预览) ](manage-data-sources.md) 并 [自动在 azure 监控范围中标记数据](create-sensitivity-label.md)。

## <a name="use-purview-sensitivity-labeling-insights"></a>使用监控范围敏感度标记见解

在监控范围中，分类类似于主题标记，用于标记和标识在扫描过程中发现的特定类型的数据。

敏感性标签使你可以陈述组织中特定数据的敏感程度。 例如，特定的项目名称在你的组织中可能高度保密，而此同一术语对于其他组织不是机密的。 

尽管分类直接匹配 (社会保障号码) **社会安全号码** 的分类，但当找到一个或多个分类和方案时，将应用敏感标签。 

监控范围使用与 Microsoft 365 相同的分类（也称为敏感信息类型）。 这使你能够在 Azure 监控范围资产之间扩展现有敏感度标签。

**查看敏感度标记见解：**

1. 请参阅 **Azure 监控范围** 主页。

1. 在 " **概述** " 页上的 " **开始** " 部分中，选择 " **启动监控范围帐户** " 磁贴。

1. 在监控范围中，选择左侧的 " **insights** " :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 菜单项以访问 **Insights** 区域。

1. 在 " **见解**" :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 区域中，选择 " **敏感度标签** " 以显示监控范围 **敏感度标记见解** 报告。

    > [!NOTE]
    > 如果此报告为空，则您可能尚未将敏感度标签扩展到 Azure 监控范围。 有关详细信息，请参阅 [在 Azure 中自动标记数据监控范围](create-sensitivity-label.md)。

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="敏感度标记见解" lightbox="media/insights/sensitivity-labeling-insights.png":::

   主要 **敏感度标记见解** 页显示以下区域：

   |区域  |说明  |
   |---------|---------|
   |**敏感度标签的源概述**     |显示提供的磁贴： <br>-在数据中找到的订阅数。 <br>-应用于数据的唯一敏感度标签数 <br>-应用了敏感度标签的源的数目 <br>-应用了敏感度标签的文件和表的数目|
   |**带有标记的数据 (过去30天的热门源)**     | 显示过去30天内应用了敏感度标签的源的数量趋势。       |
   |**跨源应用的顶部标签**     |显示在所有监控范围数据资源上应用的顶部标签。 |
   |**应用于文件的顶部标签**     |显示应用于数据中文件的顶部敏感度标签。          |
   |**表中应用的顶部标签**     | 显示应用于数据中的数据库表的顶部敏感度标签。 |   
   |  **标记活动**  |  显示文件和表的单独图形，每个图形显示在所选时间范围内标记的文件或表的数量。 <br>**默认值**：30天<br>选择图表上方的 **时间** 筛选器，以选择要显示的其他时间范围。    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>敏感度标签 insights 深化

在以下任何 **敏感度标记见解** 关系图中，选择 " **查看更多** " 链接以了解更多详细信息：

- **跨源应用的顶部标签**
- **应用于文件的顶部标签**
- **表中应用的顶部标签**
- **标记活动 > 标记的数据**

例如：

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="敏感度标签明细" lightbox="media/insights/sensitivity-label-drilldown.png":::

若要了解详细信息，请执行以下任一操作：

|选项  |描述  |
|---------|---------|
|**筛选数据**     |  使用网格上方的筛选器筛选显示的数据，包括标签名称、订阅名称或源类型。 <br><br>如果不确定确切的标签名称，可以在 " **按关键字筛选** " 框中输入部分或全部名称。       |
|**对网格排序** |选择列标题可按该列对网格进行排序。 | 
|**编辑列**     |  若要在网格中显示更多或更少的列，请选择 " **编辑列** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: "，然后选择要查看或更改其顺序的列。    <br><br>选择列标题可按该列对网格进行排序。   |
|**进一步向下钻取**     | 若要向下钻取到特定标签，请在 " **敏感度标签** " 列中选择一个名称，以 **按源** 报表查看标签。 <br><br>此报表显示所选标签的数据，其中包括源名称、源类型、订阅 ID 以及已分类文件和表的数量。      |
|**浏览资产**     |  若要浏览使用特定标签或源找到的资产，请选择一个或多个标签或源（具体取决于你正在查看的报表），然后选择 " **浏览** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: " 筛选器之上的 "资产"。 <br><br>搜索结果将显示为所选筛选器找到的所有标记的资产。  有关详细信息，请参阅 [搜索 Azure 监控范围 Data Catalog](how-to-search-catalog.md)。       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>敏感度标签与 Microsoft 365 相容性集成

与在 Microsoft 365 中提供的 [Microsoft 信息保护](/microsoft-365/compliance/information-protection) 实现了集成，这意味着监控范围启用了直接的方式来扩展数据空间，并对数据进行分类和标记。

若要将 Microsoft 365 敏感度标签扩展到 Azure 监控范围中的资产，必须在 Microsoft 365 相容性中心为 Azure 监控范围主动启用信息保护。

有关详细信息，请参阅 [在 Azure 中自动标记数据监控范围](create-sensitivity-label.md)。

## <a name="next-steps"></a>后续步骤

了解有关这些 Azure 监控范围见解报表的详细信息：

- [词汇表见解](glossary-insights.md)
- [扫描见解](scan-insights.md)
- [分类见解](./classification-insights.md)
- [文件扩展见解](file-extension-insights.md)
