---
title: 如何创建、导入和导出术语表术语
description: 了解如何在 Azure Purview 中创建、导入和导出术语表术语。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: dfb522e5231072e427c14dfe1c8c16889dd3ff9e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214143"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>如何创建、导入和导出术语表术语

本文介绍了如何在 Azure Purview 数据目录中创建业务术语表术语，以及如何使用 .csv 文件导入和导出术语表术语。

## <a name="create-a-new-term"></a>创建新术语

若要创建新的术语表术语，请执行以下步骤：

1. 在主页左侧导航栏中选择“数据目录”，然后选择页面中心的“管理术语表”按钮。

    :::image type="content" source="media/how-to-create-import-export-glossary/find-glossary.png" alt-text="数据目录的屏幕截图，其中突出显示了术语表。" border="true":::

2. 在“术语表术语”页上，选择“+ 新建术语”。 此时会打开一个页面，其中选择了“系统默认”模板。 选择要用来创建术语表术语的模板，然后选择“继续”。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="新建术语的屏幕截图。" border="true":::

3. 为新术语指定一个名称，该名称在目录中必须独一无二。 术语名称区分大小写，这意味着目录中可以有一个名为 Sample 的术语和一个名为 sample 的术语。

4. 添加一个“定义”。

5. 设置术语的“状态”。 新术语默认为“草稿”状态。

   :::image type="content" source="media/how-to-create-import-export-glossary/overview-tab.png" alt-text="选择状态时的屏幕截图。":::

   这些状态标记是与术语关联的元数据。 目前，可以在每个术语上设置以下状态：

   - 草稿：此术语尚未正式实现。
   - 已批准：此术语为正式术语/标准术语/已批准术语。
   - 已过期：不应再使用此术语。
   - 警报：需要注意此术语。

6. 添加“资源”和“首字母缩略词”。 如果此术语是层次结构的一部分，则可以在概述选项卡的“父级”中添加父级术语。

7. 在“相关内容”选项卡中添加“同义词”和“相关术语”。

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="“新建术语”>“相关内容”选项卡的屏幕截图。" border="true":::

8. 或者，选择“联系人”选项卡，向你的术语中添加专家和专员。

9. 选择“创建”来创建你的术语。

## <a name="import-terms-into-the-glossary"></a>将术语导入到术语表中

Azure Purview 数据目录提供了一个模板 .csv 文件，用于将术语导入到术语表中。

你可以导入目录中的术语。 文件中的重复术语会被覆盖。

请注意，术语名称区分大小写。 例如，`Sample` 和 `saMple` 可以存在于同一术语表中。

### <a name="to-import-terms-follow-these-steps"></a>若要导入术语，请按照以下步骤操作：

1. 在“术语表术语”页面中，选择“导入术语”。

2. 术语模板页随即打开。 将术语模板与要导入的 .CSV 的类型进行匹配。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="“术语表术语”页面的“导入术语”按钮的屏幕截图。":::

3. 下载 csv 模板，并使用它输入你要添加的术语。

   > [!Important]
   > 系统仅支持导入模板中提供的列。 “系统默认”模板将具有所有默认属性。
   > 但是，自定义术语模板将具有现成的属性和模板中定义的其他自定义属性。 因此，.CSV 文件的总列数与列名称都因所选术语模板而异。 你还可以在上传后查看文件中的问题。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="“术语表术语”页面的屏幕截图，选择要导入的文件。":::

4. 填写完 .csv 文件后，选择要导入的文件，然后选择“确定”。

5. 系统会上传文件，并将所有术语添加到你的目录。
 
   > [!Important]
   > “专员”和“专家”的电子邮件地址应是 AAD 组中用户的主要地址。 尚不支持备用电子邮件地址、用户主体名称和非 AAD 电子邮件地址。 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>使用自定义属性从术语表导出术语

只要所选术语属于同一术语模板，就应该能够从术语表中导出术语。

1. 在术语表中时，默认情况下，“导出”按钮处于禁用状态。 选择要导出的术语后，如果所选术语属于同一模板，则“导出”按钮处于启用状态。

2. 选择“导出”以下载所选术语。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-export.png" lightbox="media/how-to-create-import-export-glossary/select-term-template-for-export.png" alt-text="“术语表术语”页面的屏幕截图，选择要导出的文件。":::

   > [!Important]
   > 如果某个层次结构中的术语属于不同的术语模板，则需将它们拆分为不同的 .CSV 文件进行导入。 此外，当前不支持使用导入过程更新术语的父级。

## <a name="next-steps"></a>后续步骤

完成[教程：创建并导入术语表术语](tutorial-import-create-glossary-terms.md)以了解详细信息。
