---
title: 如何创建、导入和导出词汇表术语
description: 了解如何在 Azure 监控范围中创建、导入和导出词汇表术语。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 7466e143f345ea305c7e9ef118d09fb6f685ac16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694474"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>如何创建、导入和导出词汇表术语

本文介绍如何在 Azure 监控范围数据目录中创建业务术语表术语，并使用 .csv 文件导入和导出词汇表术语。

## <a name="create-a-new-term"></a>创建新术语

若要创建新的术语表术语，请执行以下步骤：

1. 选择 "主页" 左侧导航栏中的 "术语表" 图标以定位到 "字词列表" 页。

2. 在 " **词汇表术语** " 页上，选择 " **+ 新字词**"。 此时会打开一个页面，其中选择了 **系统默认** 模板。 选择要用来创建词汇表术语的模板，并选择 " **继续**"。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="新术语创建的屏幕截图。" border="true":::

3. 为新术语指定一个名称，该名称在目录中必须是唯一的。 术语名称区分大小写，这意味着在目录中可以有一个名为 **sample** 和 **sample** 的术语。

4. 添加 **定义**。

5. 设置术语的 **状态** 。 新术语默认为 **草稿** 状态。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="状态选项的屏幕截图。" border="true":::

   这些状态标记是与术语关联的元数据。 当前可以在每个术语上设置以下状态：

   - **草稿**：尚未正式实现此术语。
   - **已批准**：此术语为正式/标准/批准。
   - 已 **过期**：不应再使用此术语。
   - **警报**：此项需要注意。

6. 添加 **资源** 和 **首字母缩写词**。 如果该术语是层次结构的一部分，则可以在 "概述" 选项卡中的 **父级** 添加父项。

7. 在相关选项卡中添加 **同义词** 和 **相关术语** 。

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="新术语 > 相关选项卡的屏幕截图。" border="true":::

8. 或者，选择 " **联系人** " 选项卡，将专家和专员添加到你的术语中。

9. 选择 " **创建** " 创建字词。

## <a name="import-terms-into-the-glossary"></a>将术语导入到术语表

Azure 监控范围数据目录提供了一个模板 .csv 文件，用于将术语导入到词汇表。

你可以导入目录中的字词。 将覆盖文件中的重复字词。

请注意，术语名称区分大小写。 例如， `Sample` 和 `saMple` 都可以存在于同一术语表中。

### <a name="to-import-terms-follow-these-steps"></a>若要导入术语，请按照以下步骤操作

1. 在 " **词汇表术语** " 页面中，选择 " **导入字词**"。

2. "术语模板" 页将打开。 将术语模板与的类型相匹配。要导入的 CSV。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="术语表术语页面 &quot;导入字词&quot; 按钮的屏幕截图。":::

3. 下载 csv 模板，并使用它输入你要添加的条款。

   > [!Important]
   > 系统仅支持导入模板中可用的列。 "系统默认" 模板将具有所有默认属性。
   > 但是，自定义术语模板将具有现成的属性和模板中定义的其他自定义属性。 因此，。CSV 文件与列名称和列名的总数不同，具体取决于所选的术语模板。 你还可以在上载后查看文件中的问题。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="术语表术语页的屏幕截图，选择要导入的文件。":::

4. 填写完 .csv 文件后，选择要导入的文件，然后选择 **"确定"**。

5. 系统将上传文件，并将所有条款添加到目录中。

## <a name="export-terms-from-glossary-with-custom-attributes"></a>从具有自定义属性的术语表中导出字词

只要所选字词属于同一术语模板，就可以从词汇表中导出条款。

1. 如果位于词汇表中，则默认情况下，" **导出** " 按钮处于禁用状态。 选择要导出的字词后，如果所选字词属于相同模板，将启用 " **导出** " 按钮。

2. 选择 " **导出** " 下载所选字词。

 > [!Important]
   > 如果层次结构中的字词属于不同的术语模板，则需要将它们拆分为不同的。要导入的 CSV 文件。 此外，当前不支持使用导入过程更新术语的父项。


## <a name="next-steps"></a>后续步骤

遵循 [教程：创建并导入术语表术语](tutorial-import-create-glossary-terms.md) 以了解详细信息。
