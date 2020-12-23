---
title: " (预览版对资产应用分类) "
description: 本文档介绍如何对资产应用分类。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552183"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>对 Azure 监控范围中的资产应用分类

本文介绍如何在资产上应用分类。

## <a name="introduction"></a>简介

分类可以是系统或自定义类型。 默认情况下，监控范围中存在系统分类。 可以基于正则表达式模式创建自定义分类。 可以自动或手动将分类应用到资产。

本文档介绍如何对数据应用分类。

## <a name="prerequisites"></a>先决条件

- 根据需要创建自定义分类。
- 对数据源设置扫描。

## <a name="apply-classifications"></a>应用分类
在 Azure 监控范围中，可以对文件、表或列资产应用系统或自定义分类。 本文介绍了在资产上手动应用分类的步骤。

### <a name="apply-classification-to-a-file-asset"></a>将分类应用于文件资产
Azure 监控范围可以扫描文档文件并对其进行自动分类。 例如，如果你有一个名为 *multiple.docx* 的文件，并且它的内容中包含一个国家/地区 ID 号，则 Azure 监控范围会将 **欧盟国家/地区** 的分类标识号添加到文件资产的详细信息页。

在某些情况下，你可能需要手动将分类添加到文件资产。 如果有多个分组到资源集中的文件，请在资源集级别添加分类。

按照以下步骤将自定义分类或系统分类添加到分区资源集：

1. 搜索或浏览分区并导航到 "资产详细信息" 页。

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="显示资产详细信息页的屏幕截图。":::

1. 在 " **概述** " 选项卡上，查看 " **分类** " 部分，查看是否存在任何现有分类。 选择“编辑”。

1. 从 " **分类** " 下拉列表中，选择你感兴趣的特定分类。 例如， **信用卡号** 是一个系统分类和 **CustomerAccountID**，它是一种自定义分类。

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="显示如何选择要添加到资产的分类的屏幕截图。":::

1. 选择“保存”

1. 在 " **概述** " 选项卡上，确认所选的分类出现在 " **分类** " 部分下。

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="显示如何确认将分类添加到资产的屏幕截图。":::

### <a name="apply-classification-to-a-table-asset"></a>向表资产应用分类

当 Azure 监控范围扫描数据源时，它不会自动将分类分配给表资产。 如果希望表资产具有分类，则必须手动添加。

向表资产添加分类：

1. 查找你感兴趣的表资产。 例如， **Customer** 表。

1. 确认没有为表分配分类。 选择“编辑”

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="显示如何查看和编辑表资产分类的屏幕截图。":::

1. 从 " **分类** " 下拉列表中，选择一个或多个分类。 此示例使用名为 **CustomerInfo** 的自定义分类，但你可以为此步骤选择任何分类。

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="显示如何选择要添加到表资产的分类的屏幕截图。":::

1. 选择 " **保存** " 以保存分类。

1. 在 " **概述** " 页上，验证 Azure 监控范围是否添加了新的分类。

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="显示如何验证是否已将分类添加到表资产的屏幕截图。":::

### <a name="add-classification-to-a-column-asset"></a>向列资产添加分类

Azure 监控范围会自动扫描并向所有列资产添加分类。 但是，如果想要更改分类，可以在列级别执行此操作。

向列添加分类：

1. 查找并选择 "资产" 列，然后从 "**概览**" 选项卡中选择 "**编辑**"。

1. 选择 " **架构** " 选项卡

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="显示如何编辑列的架构的屏幕截图。":::

1. 标识你感兴趣的列，然后选择 " **添加分类**"。 此示例将 **常见的密码** 分类添加到 **PasswordHash** 列。

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="显示如何将分类添加到列的屏幕截图。":::

1. 选择“保存”

1. 选择 " **架构** " 选项卡，并确认已将分类添加到列中。

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="显示如何确认向列架构添加了分类的屏幕截图。":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>重新扫描现有分类的影响

基于数据的样本集检查并将其与集正则表达式模式相匹配，首次应用分类。 重新扫描时，如果应用新分类，列将获取更多分类。 现有分类保留在列中，并且必须手动删除。

## <a name="next-steps"></a>后续步骤
若要了解如何创建自定义分类，请参阅 [创建自定义分类](create-a-custom-classification-and-classification-rule.md)。