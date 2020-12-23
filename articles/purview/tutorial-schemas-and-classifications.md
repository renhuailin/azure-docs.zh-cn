---
title: 教程：在 Azure Purview（预览版）中浏览资源集、详细信息、架构和分类
description: 本教程介绍如何使用资源集、资产详细信息、架构和分类。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c39ed746630c646b0ce305a9535ae6d610f5161c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549536"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>教程：在 Azure Purview（预览版）中浏览资源集、详细信息、架构和分类

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本教程中，你将探索目录的关键组件：资源集、资产详细信息、架构和分类。

本教程是五部分教程系列的第 4 部分，将介绍有关如何使用 Azure Purview 跨数据资产管理数据治理的基础知识。 本教程的基础是你在[第 3 部分：在 Azure Purview（预览版）中浏览资产并查看其世系](tutorial-browse-and-view-lineage.md)。

在本教程中，你将了解如何：

> [!div class="checklist"]
>
> * 查看资源集。
> * 查看资产详细信息。
> * 编辑架构并添加分类。

## <a name="prerequisites"></a>先决条件

* 完成[教程：在 Azure Purview（预览版）中浏览资产并查看其世系](tutorial-browse-and-view-lineage.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="view-resource-sets"></a>查看资源集

资源集是目录中的单个对象，表示存储中的多个物理对象。 通常，这些对象共享一个通用架构，并且在大多数情况下，还会共享一个命名约定或文件夹结构。 例如，日期格式为 yyyy/mm/dd。 有关资源集的详细信息，请参阅[了解资源集](concept-resource-sets.md)。

1. 导航到 Azure 门户中的 Azure Purview 资源，然后选择“打开 Purview Studio”。 你将自动转到 Purview Studio 的主页。

2. 在“搜索资产”框中输入“contoso_staging_positivecashflow_ n”，然后从搜索结果中选择“Contoso_Staging_PositiveCashFlow_{N}.csv”  。

## <a name="view-asset-details"></a>查看资产详细信息

1. “概述”选项卡显示“说明”、“术语表”和“属性”，例如 qualifiedName    。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="屏幕截图中显示了资源集资产页面的“概述”选项。":::

1. 在“属性”下，注意以下两个字段：

   * partitionCount：指示与此资源集关联的物理文件数。
   * schemaCount：指示在此资源集中找到的架构的变体数。

   在完成[本教程系列的第 1 部分](tutorial-scan-data.md)中的扫描后，Azure Purview 会在 24 小时内填充这些属性。

1. 选择“联系人”选项卡以查看“专家”和“所有者”值  。

## <a name="edit-the-schema-and-add-classifications"></a>编辑架构并添加分类

1. 选择“架构”选项卡。观察与它们关联的列名和分类。 请注意，扫描会自动填充这些属性。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="屏幕截图中显示了“架构”选项卡。":::

1. 若要编辑资产，请选择左上角的“编辑”按钮。 然后，选择“架构”选项卡。

1. 为列添加“说明”或将该列重命名为更易记的名称。

1. 在资产级别添加分类，为没有设定分类的列名选择“列级别分类”下拉菜单。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="架构编辑页":::

1. 完成更改后，选择“保存”。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 查看资源集。
> * 查看资产详细信息。
> * 编辑架构并添加分类。

进入下一教程，了解术语表以及如何为资产定义和导入新术语。

> [!div class="nextstepaction"]
> [创建和导入术语](tutorial-import-create-glossary-terms.md)