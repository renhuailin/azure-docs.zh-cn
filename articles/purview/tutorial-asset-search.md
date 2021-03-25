---
title: 教程：导航 Azure Purview 主页并搜索资产
description: 本教程介绍如何使用 Azure Purview 主页上的功能，并在目录中进行搜索。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677840"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>教程：导航 Azure Purview（预览版）主页并搜索资产

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本教程中，你将通过导航主页的功能并在目录中搜索资产来熟悉 Azure Purview。

本文是五部分教程系列的第 2 部分，将介绍有关如何使用 Azure Purview 跨数据资产管理数据治理的基础知识。 本教程的基础是你在[第 1 部分：使用 Azure Purview 扫描数据](tutorial-scan-data.md)

在本教程中，将：

> [!div class="checklist"]
>
> * 导航 Azure Purview 主页。
> * 搜索资产。
> * 添加资产所有者。

## <a name="prerequisites"></a>先决条件

* 完成[教程：使用 Azure Purview 扫描数据](tutorial-scan-data.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="navigate-the-azure-purview-home-page"></a>导航 Azure Purview 主页

以下步骤将引导你使用 Azure Purview 主页。

1. 导航到 Azure 门户中的 Azure Purview 资源，然后选择“打开 Purview Studio”。 你将自动转到 Purview Studio 的主页。

   主页顶部显示目录名称和一组目录分析。 包括源、数据资产和词汇表术语的数量。 在摘要中，可以看到总计有 200 多项资产和 113 条术语。 当组织扫描并将更多条款添加到 Azure Purview 时，此数字会更新。

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="屏幕截图中显示了 Azure Purview 主页。":::

1. 选择“浏览资产”以查看所有资产。

## <a name="search-for-an-asset"></a>搜索资产

在开始之前，请先快速复习一些基本术语：

* 资产：目录中的一个简单的对象，包含数据资产中任何实体的定义。 实体的示例包括 SQL 表、Power BI 报表和数据工厂活动。
  
* **架构**：架构也称为列或属性，表示资产或资源集的结构。

* 资源集：目录中的单个对象，表示存储中的多个物理对象。 这些对象通常共享一个通用架构，并且在大多数情况下，还会共享一个命名约定或文件夹结构。 例如，日期格式为 yyyy/mm/dd。 有关详细信息，请参阅[了解资源集](concept-resource-sets.md)。

* 资产类型：逻辑名称下的一组资产和资源集，通常映射到数据平台的名称。

按照以下步骤搜索资产，并将自己标记为所有者：

1. 在主页上的“搜索目录”框中，输入包含数据资产（在上一教程中创建的资源组）的资源组的名称。 系统将显示建议列表。

1. 选择“查看搜索结果”。 由于所有资产都以资源组的名称开头，因此它们都将显示在搜索结果中。 在本教程之外，你将搜索特定的资产名称，而不是资源组。

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="屏幕截图中显示了目录搜索建议列表。":::

1. 可以使用左侧导航栏中的筛选器，按资产类型、分类、联系人、标签和术语进行筛选。

1. 若要搜索资源集，请开始键入该资源集的名称。 系统将显示包含正确关键字的搜索建议列表。 也可以通过为名称加引号来搜索绝对名称。

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Azure Purview 关键字资产搜索":::

## <a name="edit-an-asset"></a>编辑资产

1. 从搜索结果中选择一个资产。 然后选择“编辑”

1. 在“概述”选项卡上，可以添加说明。

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="屏幕截图显示了“概述”选项卡上的“说明”字段。":::

1. 选择“联系人”选项卡。在“所有者”旁边的“选择用户或组”中，开始键入你公司的电子邮件地址 。

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="屏幕截图中显示了填充字段。":::

    系统将自动显示名称建议。

1. 在“专家”旁边的“选择用户或组”中，输入名称（例如，经理的姓名），然后选择“保存”  。

    现在已填充了“描述”、“所有者姓名”和“专家姓名”字段。

## <a name="next-steps"></a>后续步骤

在继续学习本系列的下一教程之前，请先花一些时间自行浏览 Azure Purview 主页。 在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 导航 Azure Purview 主页。
> * 搜索资产。
> * 添加资产所有者。

进入下一教程，了解如何在 Azure Purview 中浏览资产并发现资产世系。

> [!div class="nextstepaction"]
> [浏览资产并查看其世系](tutorial-browse-and-view-lineage.md)
