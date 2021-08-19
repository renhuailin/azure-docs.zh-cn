---
title: 在 Azure Purview 中使用 Purview 见解提供针对数据的敏感度标签报表
description: 本操作指南介绍了如何查看和使用针对数据的 Purview 敏感度标签报表。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: d3b828aab13a822c307119dca64dac81c9201d5b
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397800"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>有关 Azure Purview 中的数据的敏感度标签见解

本操作指南介绍了如何访问、查看和筛选应用于数据的敏感度标签提供的安全见解。

支持的数据源包括：Azure Blob 存储、Azure Data Lake Storage (ADLS) GEN 1、Azure Data Lake Storage (ADLS) GEN 2、SQL Server、Azure SQL 数据库、Azure SQL 托管实例、Amazon S3 Bucket

在本操作指南中，你将学习如何：

> [!div class="checklist"]
> - 从 Azure 启动 Purview 帐户。
> - 查看对数据的敏感度标记见解
> - 向下钻取以了解有关数据的更多敏感度标记详细信息

> [!NOTE]
> 在 Purview 扫描的 [Power BI 资产](register-scan-power-bi-tenant.md)上找到的敏感度标签当前未显示在“敏感度标记见解”报表中。 
>
> 若要查看 Power BI 资产上的敏感度标签，请查看 [Purview 数据目录](how-to-search-catalog.md)中的资产。
> 
## <a name="prerequisites"></a>先决条件

在开始利用 Purview 见解之前，请确保已完成以下步骤：

- 设置 Azure 资源并用测试数据填充相关帐户

- [将 Microsoft 365 敏感度标签扩展到 Azure Purview 中的资产](create-sensitivity-label.md)，并创建或选择要应用于数据的标签。

- 对每个数据源中的测试数据设置并完成扫描。 有关详细信息，请参阅[在 Azure Purview（预览版）中管理数据源](manage-data-sources.md)和[创建扫描规则集](create-a-scan-rule-set.md)。

- 已使用具有[“数据读取者”或“数据策展者”角色](catalog-permissions.md#roles)的帐户登录到 Purview。

有关详细信息，请参阅[在 Azure Purview（预览版）中管理数据源](manage-data-sources.md)和[在 Azure Purview 中自动标记数据](create-sensitivity-label.md)。

## <a name="use-purview-sensitivity-labeling-insights"></a>使用 Purview 敏感度标记见解

在 Purview 中，分类类似于主题标签，用于标记和标识扫描期间在数据资产中发现的特定类型的数据。

可以使用敏感度标签来说明组织中某些数据的敏感度。 例如，特定项目名称在你的组织内可能是高度机密的，而同一术语对其他组织则不是机密。 

分类是直接匹配的，例如社会安全号码，它属于“社会安全号码”分类。 

相反，如果一个或多个分类和条件同时出现，将应用敏感度标签。 在这种情况下，[条件](/microsoft-365/compliance/apply-sensitivity-label-automatically)指的是可为非结构化数据定义的所有参数，例如与另一分类的接近度以及置信度百分比 。 

Purview 使用与 Microsoft 365 相同的分类，也称为[敏感信息类型](/microsoft-365/compliance/sensitive-information-type-entity-definitions)。 这使你可以将现有的敏感度标签扩展到整个 Azure Purview 资产。

> [!NOTE]
> 扫描源类型后，留几小时让“敏感度标记”见解反映新资产。

若要查看敏感度标记见解，请执行以下操作：

1. 转到 Azure Purview 主页。

1. 在“概述”页面的“入门”部分，选择“启动 Purview 帐户”磁贴。  

1. 在 Purview 中，选择左侧的“见解”(:::image type="icon" source="media/insights/ico-insights.png" border="false":::) 菜单项以访问“见解”区域。

1. 在“见解”(:::image type="icon" source="media/insights/ico-insights.png" border="false":::) 区域中，选择“敏感度标签”以显示 Purview 的“敏感度标记见解”报表。

    > [!NOTE]
    > 如果此报表为空，则你可能尚未将敏感度标签扩展到 Azure Purview。 有关详细信息，请参阅[在 Azure Purview 中自动标记数据](create-sensitivity-label.md)。

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="敏感度标记见解" lightbox="media/insights/sensitivity-labeling-insights.png":::

   “敏感度标记见解”主页面显示以下区域：

   |区域  |说明  |
   |---------|---------|
   |具有敏感度标签的源的概述     |显示提供以下信息的磁贴： <br>- 在数据中找到的订阅数。 <br>- 应用于数据的唯一敏感度标签数 <br>- 应用了敏感度标签的源的数量 <br>- 找到的应用了敏感度标签的文件数和表数|
   |具有带标签的数据的主要源（过去 30 天）     | 显示过去 30 天内应用了敏感度标签的源的数量趋势。       |
   |在各个源中应用的主要标签     |显示在所有 Purview 数据资源中应用的主要标签。 |
   |在文件上应用的主要标签     |显示应用于数据中文件的主要敏感度标签。          |
   |在表上应用的主要标签     | 显示应用于数据中的数据库表的主要敏感度标签。 |   
   |  标记活动  |  针对文件和表显示单独的图，每个图显示在所选时间范围内标记的文件或表的数量。 <br>默认值：30 天<br>选择图上方的“时间”筛选器，以选择要显示的其他时间范围。    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>敏感度标记见解向下钻取

在以下任何“敏感度标记见解”图中，选择“查看更多”链接来向下钻取，以便了解更多详细信息：

- 在各个源中应用的主要标签
- 在文件上应用的主要标签
- 在表上应用的主要标签
- 标记活动 > 标记的数据

例如：

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="敏感度标签向下钻取" lightbox="media/insights/sensitivity-label-drilldown.png":::

若要了解详细信息，请执行以下任一操作：

|选项  |说明  |
|---------|---------|
|**筛选数据**     |  使用网格上方的筛选器来筛选显示的数据，包括标签名称、订阅名称或源类型。 <br><br>如果你不确定确切的标签名称，可以在“按关键字筛选”框中输入部分或完整名称。       |
|对网格排序 |选择一个列标题，按该列对网格排序。 | 
|**编辑列**     |  若要在网格中显示更多或更少的列，请选择“编辑列”(:::image type="icon" source="media/insights/ico-columns.png" border="false":::)，然后选择要查看或更改其顺序的列。    <br><br>选择一个列标题，按该列对网格排序。   |
|进一步向下钻取     | 若要向下钻取到特定标签，请在“敏感度标签”列中选择一个名称，以查看“按源进行标记”报表。 <br><br>此报表显示所选标签的数据，其中包括源名称、源类型、订阅 ID 以及已分类的文件和表的数量。      |
|浏览资产     |  若要浏览所找到的具有特定标签或源的资产，请选择一个或多个标签或源（具体取决于你正在查看的报表），然后选择筛选器上方的“浏览资产”(:::image type="icon" source="media/insights/ico-browse-assets.png" border="false":::)。 <br><br>搜索结果将显示根据所选筛选器找到的所有带标签的资产。  有关详细信息，请参阅[搜索 Azure Purview 数据目录](how-to-search-catalog.md)。       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>敏感度标签与 Microsoft 365 合规性的集成

Microsoft 365 中提供了与 [Microsoft 信息保护](/microsoft-365/compliance/information-protection)的紧密集成，这意味着 Purview 能够直接扩展数据资产的可见性，并对你的数据进行分类和标记。

若要将 Microsoft 365 敏感度标签扩展到 Azure Purview 中的资产，必须在 Microsoft 365 合规性中心内主动为 Azure Purview 启用信息保护。

有关详细信息，请参阅[在 Azure Purview 中自动标记数据](create-sensitivity-label.md)。

## <a name="next-steps"></a>后续步骤

详细了解以下 Azure Purview 见解报表：

- [术语表见解](glossary-insights.md)
- [扫描见解](scan-insights.md)
- [分类见解](./classification-insights.md)
- [文件扩展见解](file-extension-insights.md)
