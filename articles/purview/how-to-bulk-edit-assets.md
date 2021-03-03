---
title: 如何将多个术语表术语标记为所选资产的列表
description: 了解如何批量编辑 Azure 监控范围中的资产。
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 77bfa3d22d628cf179e95cd4892ec030f22ab35f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693758"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>如何批量编辑资产以标记术语表术语

本文介绍如何在单个操作中将多个术语表术语标记为所选资产的列表。

### <a name="add-assets-to-view-selected-list-using-search"></a>使用搜索添加资产以查看选定的列表

1. 搜索要添加到列表中的数据资产，以便进行批量编辑。

2. 在 "搜索结果" 页上，将鼠标指针悬停在要添加到 "批量编辑" **视图 "选定** " 列表中的资产上，以显示复选框。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="复选框的屏幕截图。":::

3. 选中相应的复选框，将其添加到 "批量编辑" **视图 "选定** " 列表。 添加后，你将看到页面底部的 "选定的项目" 图标。

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="列表的屏幕截图。":::

4. 重复上述步骤，将所有数据资产添加到列表。

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>添加资产以查看 "资产详细信息" 页中所选列表

1. 在 "资产详细信息" 页中，选择右上角的复选框，以将资产添加到 "批量编辑" **视图 "选定** " 列表。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="资产的屏幕截图。":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>批量编辑 "查看选定项" 列表中的资产以添加、替换或删除术语表术语。

1. 你已完成所有需要批量编辑的数据资产的标识，请选择 "从搜索结果页或资产详细信息页 **查看所选** 列表"。

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="视图的屏幕截图。":::

2. 查看列表并选择 " **删除** " （如果要删除任何条款）。

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="删除的屏幕截图。":::

3. 选择 "批量编辑" 以添加、删除或替换所有选定资产的术语表术语。

4. 若要添加词汇表术语，请选择 "操作" 作为 " **添加**"。 选择要在新值中添加的所有词汇表术语。 完成后选择 "应用"。
    - 添加操作会将新值追加到已标记为数据资产的术语表术语列表。  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="添加的屏幕截图。":::

5. 若要将术语表术语替换为 **替换** 为。 选择要替换为新值的所有词汇表术语。 完成后选择 "应用"。
    - Replace 操作会将所选数据资产的所有词汇表术语替换为在 "新值" 中选择的条款。
   
6. 删除术语表术语选择操作作为 **删除**。 完成后选择 "应用"。
    - 删除操作将删除所选数据资产的所有词汇表术语。
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="删除字词的屏幕截图。":::

7. 对于分类、所有者和专家，请重复上述步骤。

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="删除字词的屏幕截图。":::

8. 完成后，选择 " **关闭** " 或 " **全部删除"，并关闭**"批量编辑" 边栏选项卡。 关闭将不会删除所选的资产，而 "全部删除" 和 "关闭" 会删除所有选定的资产。
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="关闭的屏幕截图。":::

   > [!Important]
   > 用于批量编辑的建议资产数量为15。 选择25个以上可能会导致性能问题。
   > 仅当至少选择了一个资产时，" **所选视图** " 框才可见。


遵循 [教程：创建并导入术语表术语](how-to-create-import-export-glossary.md) 以了解详细信息。
