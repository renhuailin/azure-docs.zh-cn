---
title: Azure Purview 中使用 Purview 见解的数据的文件扩展名报表
description: 本操作指南介绍了如何查看并使用数据的 Purview 文件扩展名报表。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 706c27603233369836368239645cf28222da8eaa
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208515"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Azure Purview 中有关数据的文件扩展名见解 

本操作指南介绍了如何访问、查看和筛选数据中找到的有关文件扩展名或文件类型的见解。

> [!IMPORTANT]
> Azure Purview 见解目前处于预览阶段。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

支持的数据源包括：Azure Blob 存储、Azure Data Lake Storage (ADLS) 第 1 代、Azure Data Lake Storage (ADLS) 第 2 代、Amazon S3 Bucket

在本操作指南中，你将学习如何：
> [!div class="checklist"]
> * 从 Azure 启动 Purview 帐户 
> - 查看有关数据的文件扩展名见解
> - 向下钻取更多有关数据的文件扩展名详细信息

## <a name="prerequisites"></a>先决条件

在开始利用 Purview 见解之前，请确保已完成以下步骤：

- 设置 Azure 资源并用测试数据填充相关帐户

- 对每个数据源中的测试数据设置并完成扫描。 有关详细信息，请参阅[在 Azure Purview 中管理数据源](manage-data-sources.md)和[创建扫描规则集](create-a-scan-rule-set.md)。

- 使用具有 [Data Reader 或 Data Curator 角色](catalog-permissions.md#roles)的帐户登录到 Purview。


有关详细信息，请参阅[在 Azure Purview 中管理数据源](manage-data-sources.md)。

## <a name="use-purview-file-extension-insights"></a>查看 Purview 文件扩展名见解

在扫描资产时，Azure Purview 可以检测在数据资产中找到的文件类型，并提供每个文件类型的相关详细信息。 详细信息包括每种类型的文件数、这些文件所在的位置以及是否可以扫描其中的敏感数据。

> [!NOTE]
> 扫描源类型后，留几小时让“文件扩展名”见解反映新资产。

**查看文件扩展名见解：**

1. [在 Azure 门户中转到“Azure Purview”实例屏幕](https://aka.ms/purviewportal)，并选择 Purview 帐户。

1. 在“概述”页面上的“入门”部分中，选择“启动 Purview 帐户”磁贴。  

1. 在 Purview 中，选择左侧的“见解”:::image type="icon" source="media/insights/ico-insights.png" border="false"::: 菜单项以访问“见解”区域。
    
1. 在“见解”中，选择“文件扩展名”选项卡。

    此报表显示所选时间范围内（默认：30 天）找到的唯一文件扩展名数量摘要以及找到的前 10 个扩展名的图。

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="文件扩展名报表 - 概述" lightbox="media/file-extension-insights/file-extension-overview.png":::

    要了解详细信息，请执行以下任一操作：

    - 选择报表顶部的“时间”选择器，以更改找到文件扩展名的时间范围。
    
    - 选择图下方的“查看更多”，以查看找到的文件扩展名的完整列表。 有关详细信息，请参阅[文件扩展名见解向下钻取](#file-extension-insights-drilldown)。 

### <a name="file-extension-insights-drilldown"></a>文件扩展名见解向下钻取

查看了有关在数据资产中找到的文件类型的高级信息后，请向下钻取，了解有关它们所在的位置，以及是否可以扫描其中敏感数据的详细信息。

例如：

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="文件扩展名报表 - 向下钻取" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

该网格显示找到的每个文件扩展名的详细信息，包括：

- **文件计数**。 具有指定扩展名的文件的数目。
- **内容扫描**。 文件扩展名是否支持扫描敏感数据。
- **订阅**。 在其中找到指定扩展名的订阅的数目。
- **源**。 找到具有指定文件扩展名的源的数目。



使用网格上方的筛选器筛选显示的数据：

|选项  |说明  |
|---------|---------|
|**按关键字筛选**     |    在“按关键字筛选”框中输入文本，以查看按名称筛选的文件类型。 例如，要仅查看 PDF，请输入 `PDF`。     |
|**时间**        | 选择此选项可按创建数据的特定时间范围筛选。 <br>**默认：** 30 天  |
|**文件扩展名**     |选择此选项可按一个或多个文件类型筛选网格。        |
|**源**    |选择此选项可按特定数据源筛选网格。 |
|**内容扫描**     |选择此选项可选择“支持”或“不支持”，以仅显示可进一步扫描其敏感数据的文件类型或无法扫描的数据，例如 .cert 或 .jpg 文件。 |
| | |

在筛选器的上方，选择“编辑列” :::image type="icon" source="media/insights/ico-columns.png" border="false"::: 可在网格中显示更多或更少的列，或更改顺序。 

要对网格进行排序，选择列标头以按该列进行排序。
## <a name="next-steps"></a>后续步骤

详细了解 Azure Purview 见解报表
> [!div class="nextstepaction"]
> [术语表见解](glossary-insights.md)

> [!div class="nextstepaction"]
> [扫描见解](scan-insights.md)

> [!div class="nextstepaction"]
> [分类见解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度标签见解](sensitivity-insights.md)
