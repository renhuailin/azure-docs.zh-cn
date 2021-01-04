---
title: 教程：在 Azure Purview（预览版）中创建和导入术语表术语
description: 本教程介绍如何创建术语表术语、将术语表术语添加到资产中以及导入术语表术语。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 0ea6fcaff1ec699431da8b67adee68735a8611a8
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696079"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>教程：在 Azure Purview（预览版）中创建和导入术语表术语

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

术语表是用于维护和组织目录的重要工具。 可通过以下方式构建术语表：定义新术语或导入术语列表，然后将这些术语应用于资产。

本教程是“五部分教程系列的第 5 部分”，该系列介绍如何使用 Azure Purview 跨数据资产管理数据治理的基础知识。 本教程以下面的内容为基础：[第 4 部分：在 Azure Purview（预览版）中浏览资源集、详细信息、架构和分类](tutorial-schemas-and-classifications.md)。

在本教程中，你将了解如何：

> [!div class="checklist"]
>
> * 创建术语表术语。
> * 将术语表术语添加到资产。
> * 导入术语表术语。

## <a name="prerequisites"></a>先决条件

* 完成[教程：在 Azure Purview（预览版）中浏览资源集、详细信息、架构和分类](tutorial-schemas-and-classifications.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-glossary-terms"></a>创建术语表术语

可在术语表中创建业务或技术术语。 还可通过对资产应用术语来对资产进行注释。

以下是“术语表术语”页面上一些最常见字段的摘要：

* **状态**：为术语（“草稿”、“已批准”、“已过期”或“警报”）分配状态   。

* **定义**：输入术语的定义。

* **首字母缩写词**：使用每个单词的首字母输入该术语的缩写版本。

* **同义词**：选择具有相同或相似定义的其他术语。

* **相关术语**：在术语表中选择与此相关但定义不同的其他术语。 示例包括与业务术语、代码名称或其他应与术语关联的术语相关的技术术语。

按照以下步骤创建术语表术语：

1. 在 Azure 门户中导航到 Azure Purview 资源，然后选择“打开 Purview Studio”。 你将自动转到 Purview Studio 的主页。

1. 从左窗格选择“术语表”，打开“术语表术语”页面 。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="显示“术语表术语”页的屏幕截图。":::

1. 选择“新建术语” > “系统默认” > “继续”  。

1. 在“新建术语”页面的“概述”选项卡上，输入新术语的名称  ：财务。

1. 输入定义：此术语表示 Contoso Inc. 的财务信息。

1. 在“状态”下拉列表中，选择“已批准” 。

1. 完成后，请选择“创建”。

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="显示如何创建新术语表术语的屏幕截图。":::

## <a name="add-glossary-terms-to-an-asset"></a>将术语表术语添加到资产

1. 使用[本教程系列的第 1 部分](tutorial-scan-data.md)中学到的步骤来查找资产。 例如“Contoso_CashFlow_{N}.csv”。

1. 在资产详细信息页面上，选择“编辑”。

1. 在“概述”选项卡中的“术语表术语”下拉列表中，选择“财务”，然后选择“保存”   。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="显示如何向资产添加术语表术语的屏幕截图。":::

1. 转到“概述”选项卡上的“术语表术语”部分，请注意，术语“财务”已应用于资产 。

## <a name="import-glossary-terms"></a>导入术语表术语

若要批量导入术语或更新现有术语，请将预填充的模板上传到术语表中。

在此过程中，将通过 .csv 文件导入术语表术语：

1. 请注意 StarterKitTerms.csv 文件的存储位置，该文件是你在[本教程系列的第 1 部分](tutorial-scan-data.md)中下载的初学者工具包的一部分。

   此文件包含与数据资产相关的预填充术语列表。

1. 若要开始导入，请选择“术语表”，然后选择“导入术语” 。

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="显示如何导入术语表术语的屏幕截图。":::

1. 在“导入术语”页面上，选择“系统默认”，然后选择“继续”  。

1. 选择“浏览”，转到下载 StarterKitTerms.csv 的位置，然后选择“打开”。

1. 选择“确定”，开始导入术语。

   导入完成后，新的术语表术语将显示在“术语表术语”页面上。

1. 查看每个新导入的术语，了解其定义方式。

## <a name="create-custom-term-templates"></a>创建自定义术语模板

本部分将介绍如何创建具有自定义属性的自定义术语模板，以及如何使用模板 csv 文件导入数据。

有几个现有的系统术语模板，你可通过选择“术语表” > “管理术语模板” > 系统”来查看这些模板  。

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="系统术语模板。":::

若要创建新的自定义术语模板，请执行以下步骤：

1. 从左侧菜单中选择“术语表”。
1. 选择“管理术语模板” > “自定义” > “新建术语模板”  

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="创建新的自定义术语模板。":::

在“新建术语模板”屏幕上，执行以下步骤：

1. 输入模板名称：`Sensitivity level`。
1. 输入所需的说明，例如 `Indicates the level of sensitivity for this data.`
1. 选择“+ 新建属性”，打开用于添加属性的对话框。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="新术语模板屏幕启动。":::

1. 在“新建属性”屏幕上，输入以下参数：

   |设置|建议的值|
   |---------|-----------|
   |属性名称|是敏感信息|
   |字段类型|dropdown|单项选择|
   |标记为必需|选中此框。|
   |+ 添加选择| 添加两个选择。 “是”和“否”。|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="添加新属性。":::

1. 重复前面的步骤以添加另一个名为 `can be shared externally` 的属性。 添加完这两个属性后，最后选择“创建”。

## <a name="import-terms-from-a-template"></a>从模板导入术语

接下来，使用你创建的“敏感级别”模板导入一个新术语。 

1. 从“术语表术语”屏幕中，选择“导入术语” 。

1. 从“导入术语”屏幕中选择“敏感级别” 。 选择“继续”。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="选择“敏感级别”。":::

1. “敏感级别”的术语模板包含默认的系统属性，以及你添加的新属性：`can be shared externally` 和 `is sensitive information`。 选择“下载示例 .CSV”文件。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="下载示例 csv 文件。":::

1. 将下载模板文件，让你可编辑和上传带有客户属性的新术语表术语。 打开你下载的 CSV 文件。 在 CSV 文件中添加新术语及其相应的值作为新行。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="在 csv 文件中创建术语。":::

   上传文件时，将创建“相关术语”或“同义词”列中不存在的任何术语 。 将使用“系统默认”模板创建这些术语。

1. 若要上传文件，请返回到“导入术语”屏幕，并选择“选择要上传的完整 .CSV 文件”框旁边的“浏览”  。 在打开的对话框中选择文件，然后选择“确定”。

1. 新术语现在列在“术语表术语”屏幕上。 通过单击列表中的术语名称，可以查看有关新术语的详细信息。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 创建术语表术语。
> * 将术语表术语添加到资产。
> * 导入术语表术语。

进入下一篇文章，了解不同的目录见解。

> [!div class="nextstepaction"]
> [了解 Azure Purview 中的见解](concept-insights.md)
