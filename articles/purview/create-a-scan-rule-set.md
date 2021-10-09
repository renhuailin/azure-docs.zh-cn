---
title: 创建扫描规则集
description: 在 Azure Purview 中创建一个扫描规则集，以便快速扫描组织中的数据源。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0b9175a2795ac926c7adf93dc81f84ff7b5a4472
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207356"
---
# <a name="create-a-scan-rule-set"></a>创建扫描规则集

在 Azure Purview 目录中，可以创建扫描规则集，以便能够快速扫描组织中的数据源。

扫描规则集是一个容器，用于将一组扫描规则组合在一起，以便轻松地将这些扫描规则与扫描相关联。 例如，可以为每种数据源类型创建一个默认扫描规则集，然后对公司内的所有扫描中默认使用这些扫描规则集。 你可能还希望具有适当权限的用户也能根据业务需求创建具有不同配置的其他扫描规则集。

## <a name="steps-to-create-a-scan-rule-set"></a>创建扫描规则集的步骤

要创建扫描规则集，请执行以下操作：

1. 在 Azure [Purview Studio](https://web.purview.azure.com/resource/) 中，选择“数据映射”。

1. 从左窗格中选择“扫描规则集”，然后选择“新建”。 

1. 在“新建扫描规则集”页上，从“源类型”下拉列表中选择目录扫描程序支持的数据源。  可以为要扫描的每种类型的数据源创建扫描规则集。

1. 给扫描规则集一个“名称”。 最大长度为 63 个字符，不允许使用空格。 （可选）输入说明。 最大长度为 256 个字符。

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="显示“扫描规则集”页的屏幕截图。" border="true":::

1. 选择“继续”。

   此时将显示“选择文件类型”页。 请注意，此页上的文件类型选项会根据在上一页中选择的数据源类型而有所不同。 默认启用所有文件类型。

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="显示“选择文件类型”页的屏幕快照。":::

   通过此页面上的“文档文件类型”选项，可以包括或排除以下 Office 文件类型：.doc、.docm、.docx、.dot、.odp、.ods、.odt、.pdf、.pot、.pps、.ppsx、.ppt、.pptm、.pptx、.xlc、.xls、.xlsb、.xlsm、.xlsx 和 .xlt。

1. 通过选中或清除复选框来启用或禁用文件类型磁贴。 如果选择“数据湖”类型数据源（例如，Azure Data Lake Storage Gen2 或 Azure Blob），请启用提取架构并进行分类的文件类型。

1. 对于某些数据源类型，还可以[创建自定义文件类型](#create-a-custom-file-type)。

1. 选择“继续”。

   此时将显示“选择分类规则”页。 该页面显示所选的“系统规则”和“自定义规则”，以及所选分类规则的总数。  默认选中所有“系统规则”复选框

1. 对于要包括或排除的规则，可以按类别全局选中或清除“系统规则”分类规则复选框。

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="显示“选择分类规则”页的屏幕截图。":::

1. 可以展开类别节点，并选中或清除各个复选框。 例如，如果“Argentina.DNI 编号”的规则具有较高的误报率，则可以清除该特定复选框。

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="显示如何选择系统规则的屏幕截图。":::

1. 选择“创建”完成创建扫描规则集。

### <a name="create-a-custom-file-type"></a>创建自定义文件类型

Azure Purview 支持添加自定义扩展，并支持在扫描规则集中定义自定义列分隔符。

要创建自定义文件类型，请执行以下操作：

1. 执行[创建扫描规则集的步骤](#steps-to-create-a-scan-rule-set)中的步骤 1 到 5，或者编辑现有的扫描规则集。

1. 在“选择文件类型”页上，选择“新建文件类型”以创建新的自定义文件类型。 

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="显示如何从“选择文件类型”页中选择“新建文件类型”的屏幕截图。":::

1. 输入“文件扩展名”和可选的“描述”。 

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="显示“新建自定义文件类型”页的屏幕截图。" border="true":::

1. 对“以下位置的文件内容”选择以下一项，以指定文件中文件内容的类型：

   - 选择“自定义分隔符”，然后输入自己的“自定义分隔符”（仅单个字符）。 

   - 选择“系统文件类型”，然后从“系统文件类型”下拉列表中选择系统文件类型（例如 XML）。 

1. 选择“创建”保存自定义文件。

   系统返回到“选择文件类型”页，并作为新磁贴来插入新的自定义文件类型。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="显示“选择文件类型”页上的新自定义文件类型磁贴的屏幕快照。":::

1. 如果要更改或删除文件类型，请在新文件类型磁贴中选择“编辑”。

1. 选择“继续”以完成扫描规则集的配置。

## <a name="system-scan-rule-sets"></a>系统扫描规则集

系统扫描规则集是由 Microsoft 定义的扫描规则集，将为每个 Azure Purview 目录自动创建。 每个系统扫描规则集均与特定的数据源类型相关联。 创建扫描时，可以将其与系统扫描规则集相关联。 每次 Microsoft 对这些系统规则集进行更新时，用户可以在目录中更新它们，并将更新应用于所有关联的扫描。

1. 要查看系统扫描规则集的列表，请在“管理中心”中选择“扫描规则集”，然后选择“系统”选项卡。  

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="显示系统扫描规则集列表的屏幕截图。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. 每个系统扫描规则集都有一个“名称”、“源类型”和“版本”。   如果在“版本”列中选择扫描规则集的版本号，则会看到与当前版本和先前版本（如果有）关联的规则。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="显示系统扫描规则集页的屏幕截图。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. 如果更新适用于系统扫描规则集，则可以在“版本”列中选择“更新”。  在系统扫描规则页中，从“选择要更新的新版本”下拉列表中选择一个版本。 页面提供与新版本和当前版本关联的系统分类规则列表。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="显示如何更改系统扫描规则集的版本的屏幕截图。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>将扫描与系统扫描规则集相关联

[创建扫描](tutorial-scan-data.md#scan-data-into-the-catalog)时，可以将其与系统扫描规则集相关联，如下所示：

1. 在“选择扫描规则集”页上，选择系统扫描规则集。

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="显示如何为扫描选择系统扫描规则集的屏幕截图。" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. 选择“继续”，然后选择“保存并运行”。
