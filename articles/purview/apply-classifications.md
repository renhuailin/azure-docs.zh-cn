---
title: 对资产应用分类
description: 本文档介绍如何对资产应用分类。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8ec7d456c5bb2eb78c9e1729e38d918c6ac222ee
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210877"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>在 Azure Purview 中对资产应用分类

本文介绍如何对资产应用分类。

## <a name="introduction"></a>简介

分类可以是系统类型或自定义类型。 默认情况下，Purview 中存在系统分类。 可以基于正则表达式模式创建自定义分类。 可以自动或手动对资产应用分类。

本文档介绍如何对数据应用分类。

## <a name="prerequisites"></a>先决条件

- 根据需要创建自定义分类。
- 对数据源设置扫描。

## <a name="apply-classifications"></a>应用分类
在 Azure Purview 中，可以对文件、表或列资产应用系统或自定义分类。 本文介绍了手动对资产应用分类的步骤。

### <a name="apply-classification-to-a-file-asset"></a>对文件资产应用分类
Azure Purview 可以扫描文档文件并自动进行分类。 例如，如果有一个名为 multiple.docx 的文件，并且内容中包含一个国家/地区 ID 号，则 Azure Purview 会向文件资产的详细信息页添加“欧盟国家/地区标识号”分类。

在某些情况下，可能需要手动向文件资产添加分类。 如果有多个文件被分组到一个资源集中，请在资源集级别添加分类。

按照以下步骤向分区资源集添加自定义分类或系统分类：

1. 搜索或浏览分区并导航到资产详细信息页。

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="显示资产详细信息页的屏幕截图。":::

1. 在“概述”选项卡上，查看“分类”部分，查看是否存在任何现有分类。 选择“编辑”。

1. 从“分类”下拉列表中，选择你感兴趣的特定分类。 例如，“信用卡号”是一个系统分类，“客户帐户 ID”是一种自定义分类。

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="显示如何选择要添加到资产的分类的屏幕截图。":::

1. 选择“保存”

1. 在“概述”选项卡上，确认选择的分类是否显示在“分类”部分下。

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="显示如何确认向资产添加了分类的屏幕截图。":::

### <a name="apply-classification-to-a-table-asset"></a>对表资产应用分类

当 Azure Purview 扫描数据源时，它不会自动将分类分配给表资产。 如果希望表资产具有分类，则必须手动添加分类。

要向表资产添加分类，请执行以下操作：

1. 查找感兴趣的表资产。 例如，“客户”表。

1. 确认没有为该表分配分类。 选择“编辑”

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="显示如何查看和编辑表资产分类的屏幕截图。":::

1. 从“分类”下拉列表中，选择一个或多个分类。 此示例使用名为“CustomerInfo”的自定义分类，但用户可以为此步骤选择任何分类。

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="显示如何选择要向表资产添加的分类的屏幕截图。":::

1. 选择“保存”以保存分类。

1. 在“概述”页上，验证 Azure Purview 添加了新的分类。

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="显示如何验证向表资产添加了分类的屏幕截图。":::

### <a name="add-classification-to-a-column-asset"></a>向列资产添加分类

Azure Purview 会自动扫描所有列资产并添加分类。 但是，如果想要更改分类，可以在列级别执行此操作。

要向列添加分类，请执行以下操作：

1. 查找并选择资产列，然后从“概览”选项卡中选择“编辑”。

1. 选择“架构”选项卡

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="显示如何编辑列的架构的屏幕截图。":::

1. 标识感兴趣的列，然后选择“添加分类”。 此示例向“PasswordHash”列添加“常见的密码”分类。

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="显示如何向列添加分类的屏幕截图。":::

1. 选择“保存”

1. 选择“架构”选项卡，并确认向列添加了分类。

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="显示如何确认向列架构添加了分类的屏幕截图。":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>重新扫描现有分类的影响

首次应用分类时是基于对数据的样本集检查，并将其与设置的正则表达式模式进行匹配。 重新扫描时，如果应用新分类，列将获取更多分类。 现有分类仍保留在列中，并且必须手动删除。

## <a name="next-steps"></a>后续步骤
要了解如何创建自定义分类，请参阅[创建自定义分类](create-a-custom-classification-and-classification-rule.md)。