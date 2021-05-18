---
title: 如何管理业务术语表的术语模板
description: 了解如何在 Azure Purview 数据目录中管理业务术语表的术语模板。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552339"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>如何管理业务术语表的术语模板

使用 Azure Purview 可以创建对数据扩充非常重要的术语表。 添加到“Purview 数据目录术语表”中的每个新术语都是基于一个术语模板，这个术语模板确定了术语的字段。 本文介绍如何创建术语模板以及能够与术语表术语相关联的自定义属性。

## <a name="manage-term-templates-and-custom-attributes"></a>管理术语模板和自定义属性

使用术语模板，可以创建自定义属性，并将它们分组在一起，并在创建术语时应用模板。 创建术语后，将无法更改与该术语关联的模板。

1. 在“术语表术语”页面上，选择“管理术语模板”。 

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="“术语表术语”>“管理术语模板”按钮的屏幕截图。":::

2. 该页同时显示系统属性和自定义属性。 选择“自定义”选项卡以创建或编辑术语模板。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="“术语表术语”>“管理术语模板”页的屏幕截图。":::

3. 选择“+ 新建术语模板”，并输入模板名称和说明。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="“术语表术语”>“管理术语模板”>“新建术语模板”的屏幕截图":::

4. 选择“+ 新建属性”可为术语模板创建新的自定义属性。 输入属性名称和描述。 自定义属性名称在术语模板内必须是唯一的，但可以在模板之间重复使用同一名称。

   从“文本”、“单选”、“多选”或“日期”选项列表中选择字段类型。    还可以为文本字段类型提供默认值字符串。  还可以将属性标记为“必需”。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="“术语表术语”>“新建属性”页面的屏幕截图。":::

5. 创建所有自定义属性后，选择“预览”以排列自定义属性的顺序。 可以按所需的顺序拖动和删除自定义属性。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="“术语表术语”>“预览术语模板”的屏幕截图。":::

6. 定义所有定制属性后，选择“创建”以创建带有自定义属性的术语模板。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="“术语表术语”>“新建术语模板 - 创建”按钮的屏幕截图。":::

7. 可以通过选中“标记为已过期”可将现有的自定义属性标记为已过期。 过期后，无法重新激活该属性。 对于现有术语，过期属性将灰显。 以后用此术语模板创建的新术语将不再显示已标记为过期的属性。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="“术语表术语”>“编辑属性”以其标记为过期的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

完成[教程：创建并导入术语表术语教程：创建并导入术语表术语](tutorial-import-create-glossary-terms.md)以了解详细信息。
