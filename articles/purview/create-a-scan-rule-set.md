---
title: 创建扫描规则集
description: 在 Azure 监控范围中创建一个扫描规则集，以便快速扫描组织中的数据源。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552142"
---
# <a name="create-a-scan-rule-set"></a>创建扫描规则集

在 Azure 监控范围目录中，你可以创建扫描规则集，使你能够快速扫描组织中的数据源。

扫描规则集是一个容器，用于将一组扫描规则组合在一起，以便你可以轻松地将它们与扫描相关联。 例如，您可以为您的每个数据源类型创建默认扫描规则集，然后在您公司内的所有扫描中默认使用这些扫描规则集。 你可能还希望具有适当权限的用户根据业务需求创建具有不同配置的其他扫描规则集。

## <a name="steps-to-create-a-scan-rule-set"></a>创建扫描规则集的步骤

若要创建扫描规则集：

1. 从 Azure 监控范围目录中，选择 " **管理中心**"。

1. 从左窗格中选择 " **扫描规则集** "，并选择 " **新建**"。

1. 从 " **新扫描规则集** " 页中，从 " **源类型** " 下拉列表中选择目录扫描器支持的数据源。 您可以为要扫描的每种类型的数据源创建扫描规则集。

1. 为扫描规则设置一个 **名称**。 最大长度为63个字符，不允许使用空格。 根据需要输入 **说明**。 最大长度为 256 个字符。

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="显示 &quot;扫描规则集&quot; 页的屏幕截图。" border="true":::

1. 选择“继续”。 

   此时将显示 " **选择文件类型** " 页。 请注意，此页上的 "文件类型" 选项因您在上一页中选择的数据源类型而异。 默认情况下，将启用所有文件类型。

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="显示 &quot;选择文件类型&quot; 页的屏幕截图。":::

   在此页上选择的 **文档文件类型** 允许包括或排除以下 office 文件类型：。 doc、. docm、.docx、.、.、.、.、.、.、.、.、. ppsx、. pptm、.. xlc、.xls、.xlsb、. xlsm、.xlsx 和 .xlt 的格式。

1. 通过选中或清除复选框来启用或禁用文件类型磁贴。 如果选择 Data Lake 类型数据源 (例如，Azure Data Lake Storage Gen2 或 Azure Blob) ，请启用要为其提取架构并对其进行分类的文件类型。

1. 对于某些数据源类型，您还可以 [创建自定义文件类型](#create-a-custom-file-type)。

1. 选择“继续”。 

   此时将显示 " **选择分类规则** " 页。 此页显示选定的 **系统规则** 和 **自定义规则** 以及所选的分类规则总数。 默认情况下，所有 **系统规则** 复选框都处于选中状态

1. 对于要包括或排除的规则，可以按类别全局选中或清除 " **系统规则** 分类规则" 复选框。

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="显示 &quot;选择分类规则&quot; 页的屏幕截图。":::

1. 您可以展开 "类别" 节点，并选中或清除各个复选框。 例如，如果 **DNI 号码** 的规则具有较高的假正值，则可以清除该复选框。

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="显示如何选择系统规则的屏幕截图。":::

1. 选择 " **创建** " 以完成扫描规则集的创建。

### <a name="create-a-custom-file-type"></a>创建自定义文件类型

Azure 监控范围支持添加自定义扩展，并在扫描规则集中定义自定义列分隔符。

若要创建自定义文件类型：

1. 执行 [创建扫描规则集](#steps-to-create-a-scan-rule-set) 或编辑现有扫描规则集步骤中的步骤1–5。

1. 在 " **选择文件类型** " 页上，选择 " **新建文件类型** " 以创建新的自定义文件类型。

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="显示如何从 &quot;选择文件类型&quot; 页中选择新文件类型的屏幕截图。":::

1. 输入 **文件扩展名** 和可选 **说明**。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="显示 &quot;新自定义文件类型&quot; 页的屏幕截图。" border="true":::

1. 为 **中的文件内容** 选择以下选项之一，以指定文件中的文件内容类型：

   - 选择 " **自定义分隔符** "，然后输入你自己的 **自定义分隔符** (仅) 单字符。

   - 选择 "系统文件 **类型** "，然后从 " **系统文件类型** " 下拉列表中选择一个系统文件类型 (例如 XML) 。

1. 选择 " **创建** " 以保存自定义文件。

   系统返回到 " **选择文件类型** " 页，并插入新的自定义文件类型作为新磁贴。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="显示 &quot;选择文件类型&quot; 页上新的自定义文件类型磁贴的屏幕截图。":::

1. 如果要更改或删除该文件，请在 "新建文件类型" 磁贴中选择 " **编辑** "。

1. 选择 " **继续** " 以完成扫描规则集配置。

## <a name="system-scan-rule-sets"></a>系统扫描规则集

系统扫描规则集是为每个 Azure 监控范围目录自动创建的 Microsoft 定义扫描规则集。 每个系统扫描规则集均与特定的数据源类型相关联。 创建扫描时，可以将其与系统扫描规则集相关联。 每次 Microsoft 对这些系统规则集进行更新时，你可以在目录中更新它们，并将更新应用于所有关联的扫描。

1. 若要查看系统扫描规则集列表，请选择 "**管理中心**" 中的 "**扫描规则集**"，然后选择 "**系统**" 选项卡。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="显示系统扫描规则集列表的屏幕截图。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. 每个系统扫描规则集都有一个 **名称**、 **源类型** 和一个 **版本**。 如果选择在 " **版本** " 列中设置的扫描规则的版本号，则会看到与当前版本和以前版本关联的规则 (如果有任何) 。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="显示 &quot;系统扫描规则集&quot; 页的屏幕截图。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. 如果更新可用于系统扫描规则集，则可以在 "**版本**" 列中选择 "**更新**"。 在 "系统扫描规则" 页中，从 " **选择要更新的新版本** " 下拉列表中选择一个版本。 页面提供与新版本和当前版本关联的系统分类规则列表。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="显示如何更改系统扫描规则集的版本的屏幕截图。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>将扫描与系统扫描规则集相关联

[创建扫描](tutorial-scan-data.md#scan-data-into-the-catalog)时，可以选择将其与系统扫描规则集相关联，如下所示：

1. 在 " **选择扫描规则集** " 页上，选择 "系统扫描" 规则集。

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="显示如何为扫描选择系统扫描规则集的屏幕截图。" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. 选择 " **继续**"，然后选择 " **保存并运行**"。
