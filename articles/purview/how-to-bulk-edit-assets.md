---
title: 如何批量编辑资产以标记分类、术语表术语以及修改联系人
description: 了解如何在 Azure Purview 中批量编辑资产。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: cebef52882135d569465f299ab71efcffb931eda
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471239"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>如何批量编辑资产以对分类和术语表术语加注，及修改联系人

本文介绍如何通过单个操作将多个术语表术语、分类、所有者和专家标记到所选资产的列表。

### <a name="add-assets-to-view-selected-list-using-search"></a>添加资产以使用搜索来查看选定的列表

1. 搜索要添加到列表中进行批量编辑的数据资产。

2. 在搜索结果页上，将鼠标悬停在要添加到批量编辑“查看选定项”列表的资产上，以显示一个复选框。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="复选框的屏幕截图。":::

3. 选中该复选框，将该资产添加到批量编辑“查看选定项”列表中。 添加后，在页面底部会看到选定项的图标。

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="列表的屏幕截图。":::

4. 重复上述步骤，将所有数据资产添加到该列表。

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>从资产详细信息页将资产添加到“查看选定项”列表

1. 在资产详细信息页中，选中右上角的复选框，将资产添加到批量编辑“查看选定项”列表。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="资产的屏幕截图。":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>在“查看选定项”列表中批量编辑资产，以添加、替换或删除术语表术语。

1. 完成所有需要批量编辑的数据资产的标识后，从搜索结果页或资产详细信息页选择“查看选定项”列表。

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="查看操作的屏幕截图。":::

2. 检查该列表，如果要删除任何术语，请选择“删除”。

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="删除操作的屏幕截图。":::

3. 选择“批量编辑”以便为所有选定资产添加、删除或替换术语表术语。

4. 若要添加术语表术语，请为“操作”选择“添加”。 在“新值”中选择要添加的所有术语表术语。 完成后选择“应用”。
    - 添加操作会将新值追加到数据资产中已标记的术语表术语列表。  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="添加操作的屏幕截图。":::

5. 若要替换术语表术语，请为“操作”选择“替换为”。 在“新值”中选择要替换的所有术语表术语。 完成后选择“应用”。
    - 替换操作会将选定数据资产的所有术语表术语替换为在“新值”中选择的术语。
   
6. 若要删除术语表术语，请为“操作”选择“删除”。 完成后选择“应用”。
    - 删除操作会删除选定数据资产的所有术语表术语。
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="删除术语的屏幕截图。":::

7. 针对分类、所有者和专家重复上述步骤。

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="分类和联系人的屏幕截图。":::

8. 完成后，选择“关闭”或“全部删除并关闭”来关闭批量编辑边栏选项卡 。 选择“关闭”不会删除选定的资产，而选择“全部删除并关闭”会删除所有选定资产。
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="关闭操作的屏幕截图。":::

   > [!Important]
   > 要批量编辑的建议资产数为 25 个。 选择 25 个以上的资产可能会导致性能出现问题。
   > 仅当至少选择了一个资产时，“查看选定项”框才可见。


请参阅[教程：创建和导入术语表术语](how-to-create-import-export-glossary.md)了解详细信息。
