---
title: 教程：在 Azure Purview 中浏览资产并查看其世系
description: 本教程介绍如何浏览目录中的资产并查看数据世系。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696092"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>教程：在 Azure Purview（预览版）中浏览资产并查看其世系

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本教程介绍如何在 Azure Purview 中浏览资产并查看其重要详细信息（如世系）。

本教程是五部分教程系列的第 3 部分，将介绍有关如何使用 Azure Purview 跨数据资产管理数据治理的基础知识。 本教程的基础是你在[第 2 部分：导航 Azure Purview（预览版）主页并搜索资产](tutorial-asset-search.md)中完成的工作。

在本教程中，将：

> [!div class="checklist"]
>
> * 在目录中浏览资产。
> * 查看资产的世系。

## <a name="prerequisites"></a>先决条件

* 完成[教程：导航 Azure Purview（预览版）主页并搜索资产](tutorial-asset-search.md)中完成的工作。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="browse-for-assets-in-the-catalog"></a>在目录中浏览资产

在上一教程中，你已了解搜索如何帮助你发现 Azure Purview 目录中的资产。 在目录中发现资产的另一种方法是使用目录的浏览体验。

本部分介绍如何浏览 Azure Purview 目录。

1. 导航到 Azure 门户中的 Azure Purview 资源，然后选择“打开 Purview Studio”。 你将自动转到 Purview Studio 的主页。

1. 在“主页”页面上，选择“浏览资产” 。

   此时将显示“浏览资产”页，其中显示了目录中所有资产类型的摘要。

1. 若要浏览目录中可用的各种 Azure Data Lake Gen2 类型的资产，请选择“Azure Data Lake Gen2”磁贴。

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="屏幕截图中显示了已选中“Azure Data Lake Gen2”的“按资产类型浏览”页面。":::

1. 如果有多个资产，则可以选择“名称”列标题，按字母顺序对资产进行排序。 在本教程中，只有一个 Azure Data Lake Storage Gen2 资产。

1. 选择资产的名称。

1. 选择 Contoso_GrossProfit_{N}.ssv 资源集。 如果你的目录中不存在此资产，请选择另一个资产。

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Azure Data Lake Storage Gen2 资源列表":::

## <a name="view-the-lineage-of-assets"></a>查看资产的世系

在资产详细信息页上，浏览数据源。

1. 选择 Contoso_GrossProfit_{N}.ssv 资源集的“世系”选项卡 。

   系统将显示所选的资产。 你看到的世系信息显示，此资源集已从 Azure Blob 存储帐户复制到 Azure Data Lake Storage Gen2。

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="屏幕截图中显示了资产的世系视图。":::

1. 在此页上选择 blob 资产，然后选择“切换到资产”链接。

   请注意，该窗口已切换到 Azure Blob 资源集，该资源集是原始 Azure Data Lake Storage Gen2 的源。

1. 选择“概述”选项卡以调查资产并确认其详细信息。

有关如何在 Azure Blob 与 Azure Data Lake Storage Gen2 资源集之间创建 Azure 数据工厂数据流活动并观察世系的信息，请参阅 [Azure 数据工厂数据流活动](../data-factory/concepts-data-flow-overview.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 在目录中浏览资产。
> * 查看资产的世系。

进入下一教程，了解资源集、资产详细信息、架构和分类。

> [!div class="nextstepaction"]
> [资源集、资产详细信息、架构和分类](tutorial-schemas-and-classifications.md)
