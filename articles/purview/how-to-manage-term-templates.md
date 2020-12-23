---
title: 如何管理业务术语表的术语模板
description: 了解如何为 Azure 监控范围数据目录中的业务术语表管理术语模板。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552339"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>如何管理业务术语表的术语模板

使用 Azure 监控范围可以创建对数据进行浓缩非常重要的术语术语表。 添加到监控范围数据目录词汇表的每个新术语都基于确定术语字段的术语模板。 本文介绍如何创建可与词汇表术语关联的术语模板和自定义属性。

## <a name="manage-term-templates-and-custom-attributes"></a>管理术语模板和自定义属性

使用术语模板，可以创建自定义属性，并将它们组合在一起，并在创建术语时应用模板。 创建字词后，将无法更改与该术语关联的模板。

1. 在 " **词汇表术语** " 页上，选择 " **管理术语模板**"。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="术语表术语 > 管理术语模板 &quot;按钮的屏幕截图。":::

2. 该页同时显示系统和自定义属性。 选择 " **自定义** " 选项卡以创建或编辑术语模板。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="术语表术语 > 管理术语模板 &quot;页的屏幕截图。":::

3. 选择 " **+ 新建术语模板** "，并输入模板名称和说明。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="术语表术语 > 管理术语模板 > 新术语模板的屏幕截图":::

4. 选择 " **+ 新建属性** "，为术语模板创建新的自定义属性。 输入属性名称、说明。 自定义属性名称在术语模板内必须是唯一的，但可以在模板之间重复使用同一名称。

   从选项列表中选择 " **文本**"、" **单项** 选择"、" **多选择**" 或 "  **日期**"。 您还可以为文本字段类型提供默认值字符串。  还可以将该属性标记为 **必需**。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="术语表术语 > 新属性页的屏幕截图。":::

5. 创建所有自定义属性后，选择 " **预览** " 以排列自定义属性的顺序。 您可以按所需的顺序拖动和删除自定义属性。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="术语表术语 > 预览术语模板的屏幕截图。":::

6. 定义所有自定义属性后，选择 " **创建** " 以创建具有自定义属性的术语模板。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="术语表术语 > 新术语模板-创建按钮的屏幕截图。":::

7. 通过选中 " **标记为已过期**"，可将现有的自定义属性标记为过期。 过期后，无法重新激活该属性。 对于现有术语，过期属性将灰显。 以后用此术语模板创建的新条款将不再显示已标记为过期的属性。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="术语表术语 > 编辑属性的屏幕截图，以将其标记为过期。":::

## <a name="next-steps"></a>后续步骤

遵循 [教程：创建并导入术语表术语](tutorial-import-create-glossary-terms.md) 以了解详细信息。
