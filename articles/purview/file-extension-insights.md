---
title: 使用监控范围 Insights 在 Azure 监控范围中报告数据的文件扩展名
description: 本操作方法指南介绍了如何查看和使用数据的监控范围文件扩展。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668563"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>有关 Azure 监控范围中数据的文件扩展见解 

本操作方法指南介绍了如何访问、查看和筛选数据中找到的文件扩展名或文件类型的见解。

支持的数据源包括： Azure Blob 存储、Azure Data Lake Storage (ADLS) 第1代、Azure Data Lake Storage (ADLS) 第2代、Amazon S3 存储桶

在本操作方法指南中，你将学习如何执行以下操作：
> [!div class="checklist"]
> * 从 Azure 启动你的监控范围帐户 
> - 查看文件扩展对数据的见解
> - 向下钻取更多有关数据的文件扩展名详细信息

## <a name="prerequisites"></a>先决条件 

在开始利用监控范围 insights 之前，请确保已完成以下步骤：

- 设置 Azure 资源并用测试数据填充相关帐户

- 为每个数据源中的测试数据设置并完成扫描。 有关详细信息，请参阅 [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md) 并 [创建扫描规则集](create-a-scan-rule-set.md)。

- 使用 [数据读取器或数据陈列角色](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)的帐户登录到监控范围。


有关详细信息，请参阅 [在 Azure 监控范围中管理数据源 (预览) ](manage-data-sources.md)。

## <a name="use-purview-file-extension-insights"></a>使用监控范围文件扩展 insights

在扫描资产时，Azure 监控范围可以检测在数据空间中找到的文件类型，并提供有关每个文件类型的更多详细信息。 详细信息包括每种类型的文件数、这些文件所在的文件以及是否可浏览敏感数据。

> [!NOTE]
> 在扫描了源类型后，请在几个小时内为 **文件扩展名** 见解，以反映新资产。

**查看文件扩展见解：**

1. 在 Azure 门户中转到 **Azure 监控范围**[实例屏幕](https://aka.ms/purviewportal)，并选择监控范围帐户。

1. 在 " **概述** " 页上的 " **开始** " 部分中，选择 " **启动监控范围帐户** " 磁贴。

1. 在监控范围中，选择左侧的 " **insights** " :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 菜单项以访问 **Insights** 区域。
    
1. 在 " **见解**" 中，选择 " **文件扩展名** " 选项卡。

    此报表显示所选时间范围内所选时间范围内所选时间范围内所选时间范围内找到的唯一文件扩展名的摘要， (默认：30天) 。

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="文件扩展名报告-概述" lightbox="media/file-extension-insights/file-extension-overview.png":::

    若要了解详细信息，请执行以下任一操作：

    - 选择报表顶部的 **时间** 选择器，以更改找到文件扩展名的时间范围。
    
    - 选择关系图下方的 " **查看更多** "，以查看找到的文件扩展名的完整列表。 有关详细信息，请参阅 [文件扩展 insights 深化](#file-extension-insights-drilldown)。 

### <a name="file-extension-insights-drilldown"></a>文件扩展 insights 深化

查看了有关在数据空间中找到的文件类型的高级信息后，请向下钻取更多详细信息，了解它们所在的位置，以及是否可以扫描敏感数据。

例如：

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="文件扩展报告-深化" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

该网格将显示找到的每个文件扩展名的详细信息，包括：

- **文件计数**。 具有指定扩展名的文件的数目。
- **内容扫描**。 文件扩展名是否支持扫描敏感数据。
- **订阅**。 在其中找到指定扩展的订阅的数目。
- **源**。 找到具有指定文件扩展名的源的数目。



使用网格上方的筛选器筛选显示的数据：

|选项  |说明  |
|---------|---------|
|**按关键字筛选**     |    在 " **按关键字筛选**  " 框中输入文本，以查看按名称筛选文件类型。 例如，若要仅查看 Pdf，请输入 `PDF` 。     |
|**时间**        | 选择此选择可按特定时间跨度筛选数据的创建时间。 <br>**默认值：** 30 天  |
|**文件扩展名**     |选择此项可按一个或多个文件类型筛选网格。        |
|**Sources**    |选择此可按特定数据源筛选网格。 |
|**内容扫描**     |选择此选择以选择 " **支持** " 或 " **不支持**"，以仅显示可进一步扫描敏感数据的文件类型或无法扫描的数据，例如 **cert** 或 **.jpg** 文件。 |
| | |

在筛选器的上方，选择 " **编辑列** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: " 可在网格中显示更多或更少的列，或更改顺序。 

若要对网格进行排序，请选择列标题以按该列进行排序。
## <a name="next-steps"></a>后续步骤

了解有关 Azure 监控范围见解报表的详细信息
> [!div class="nextstepaction"]
> [词汇表见解](glossary-insights.md)

> [!div class="nextstepaction"]
> [扫描见解](scan-insights.md)

> [!div class="nextstepaction"]
> [分类见解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度标签见解](sensitivity-insights.md)
