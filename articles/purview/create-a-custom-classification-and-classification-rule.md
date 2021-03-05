---
title: " (预览创建自定义分类和分类规则) "
description: 本文介绍如何创建自定义分类来定义你的组织独有的数据空间中的数据类型。 还介绍了如何创建自定义分类规则，以便在整个数据空间中查找指定的数据。
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: d1a0873552ac9043d8f584f38ecd41c5e8543489
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202751"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure 监控范围中的自定义分类 

本文介绍如何创建自定义分类来定义你的组织独有的数据空间中的数据类型。 还介绍了如何创建自定义分类规则，以便在整个数据空间中查找指定的数据。

## <a name="default-classifications"></a>默认分类

Azure 监控范围数据目录提供了大量的默认分类，表示你在数据区域中可能具有的典型个人数据类型。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="选择分类" border="true":::

如果有任何默认分类无法满足您的需求，您还可以创建自定义分类。

## <a name="steps-to-create-a-custom-classification"></a>创建自定义分类的步骤

若要创建自定义分类，请执行以下操作：

1. 从目录中，从左侧菜单中选择 " **管理中心** "。

2. 在 "**元数据管理**" 下选择 **分类**。

3. 选择 " **+ 新建**"

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="新分类" border="true":::

此时会打开 " **添加新分类** " 窗格，你可以在其中为你的分类指定名称和描述。 最好使用名称间距约定，如 `your company name.classification name` 。
Microsoft 系统分类按保留 `MICROSOFT.` 命名空间分组。 例如， **MICROSOFT。政府.反馈.社会 \_ 保障 \_ 号**。

分类的名称必须以字母开头，后跟一系列字母、数字和句点 (。 ) 或下划线字符。
不允许使用空格。 键入时，UX 会自动生成一个友好名称。 此友好名称是用户将其应用到目录中的资产时看到的内容。

为了使名称简短，系统基于以下逻辑创建友好名称：

- 除命名空间的最后两个段以外的所有部分都被剪裁掉。

- 调整大小写，使每个单词的首字母大写。 所有其他字母都转换为小写字母。

- 所有下划线 (\_) 均替换为空格。

例如，如果你已将分类 **CONTOSO.HR。员工 \_ id**，友好名称以 **HR. Employee id** 的形式存储在系统中。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

选择 **"确定"**，然后将新分类添加到分类列表。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="自定义分类" border="true":::

选择列表中的分类会打开分类详细信息页。 可在此处找到有关分类的所有详细信息。
这些详细信息包括：有多少个实例、正式名称、关联的分类规则 (如果有任何) ，以及所有者名称。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="选择分类" border="true":::

## <a name="custom-classification-rules"></a>自定义分类规则

目录服务提供了一组默认分类规则，扫描程序使用这些规则来自动检测某些数据类型。 你还可以添加自己的自定义分类规则，以检测你可能感兴趣的数据的其他类型的数据。 当你 \' 尝试在数据空间中查找数据时，此功能会非常强大。

例如，假设 \' 名为 Contoso 的公司拥有在整个公司中标准化的雇员 id，其中的 word \" employee \" 后跟一个 GUID 来创建员工 {GUID}。 例如，"雇员 ID" 的一个实例如下所示： EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55。

Contoso 可以通过创建自定义分类规则将扫描系统配置为查找这些 Id 的实例。 在这种情况下，它们可以提供匹配数据模式的正则表达式 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` 。 （可选）如果数据通常位于他们知道其名称的列中（如雇员 \_ ID 或雇员 id），则他们可以添加列模式正则表达式，以使扫描更准确。 示例 regex 是雇员 \_ ID \| 雇员 id。

然后，扫描系统可以使用此规则检查列中的实际数据，并使用列名称来尝试标识找到了员工 ID 模式的每个实例。

## <a name="steps-to-create-a-custom-classification-rule"></a>创建自定义分类规则的步骤

若要创建自定义分类规则：

1. 按照以上部分中的说明创建自定义分类。 你将在分类规则配置中添加此自定义分类，以便系统在列中找到匹配项时应用此自定义分类。

2. 选择 " **管理中心** " 图标。

3. 选择 " **分类规则** " 部分。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="分类规则磁贴" border="true":::

4. 选择“新建”。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="添加新的分类规则" border="true":::

5. 此时将打开 " **新建分类规则** " 对话框。 填写字段并决定是创建 **正则表达式规则** 还是 **字典规则**。

    |字段     |说明  |
    |---------|---------|
    |名称   |    必需。 最大值为100个字符。    |
    |说明      |可选。 最大值为256个字符。    |
    |分类名称    | 必需。 从下拉列表中选择分类的名称，以指示扫描程序在找到匹配项时应用该分类。        |
    |状态   |  必需。 启用或禁用这些选项。 默认值为 "已启用"。    |

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="创建新的分类规则" border="true":::

### <a name="creating-a-regular-expression-rule"></a>创建正则表达式规则

1. 如果创建正则表达式规则，将看到以下屏幕。 你可以选择上传一个文件，该文件将用于为你的规则 **生成建议的正则表达式模式** 。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="创建新的 regex 规则" border="true":::

1. 如果决定生成建议的正则表达式模式，请在上传文件后，选择建议的模式之一，并单击 " **添加到模式** " 以使用建议的数据和列模式。 您可以调整建议的模式，也可以键入自己的模式而不上载文件。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="生成建议的正则表达式" border="true":::

    |字段     |说明  |
    |---------|---------|
    |数据模式    |可选。 表示存储在数据字段中的数据的正则表达式。 此限制非常大。 在上面的示例中，数据模式测试了原义词的雇员 ID `Employee{GUID}` 。  |
    |列模式    |可选。 表示要匹配的列名称的正则表达式。 此限制非常大。          |

1. 在 **数据模式** 下，可以设置以下两个阈值：

    - **Distinct match 阈值**：在扫描程序运行数据模式之前，需要在列中找到的不同数据值的总数。 建议值为8。 可以手动调整此值，范围为2到32。 系统需要此值以确保列包含足够的数据，以便扫描程序准确地分类。 例如，包含多个包含值1的行的列不会进行分类。 如果列包含一个值，并且行的其余部分具有 null 值，则不会对这些列进行分类。 如果指定多种模式，此值将应用于每个模式。

    - **最小匹配阈值**：可以使用此设置来设置要应用分类的扫描程序必须找到的列中的非重复数据值匹配项的最小百分比。 建议值为60%。 需要注意此设置。 如果降低低于60% 的级别，则可能会向目录中引入误报分类。 如果指定多个数据模式，此设置将被禁用，并且值固定为60%。

1. 你现在可以验证规则并 **创建** 它。
    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="创建前验证规则" border="true":::

### <a name="creating-a-dictionary-rule"></a>创建字典规则

1.  如果创建字典规则，则会看到以下屏幕。 上传一个文件，其中包含要在单个列中创建的分类的所有可能值。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="创建字典规则" border="true":::

1.  生成字典后，可以调整 distinct match 和最小匹配阈值，并提交规则。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="创建字典规则" border="true":::

## <a name="next-steps"></a>后续步骤

创建分类规则后，可以将其添加到扫描规则集，以便扫描时使用该规则。 有关详细信息，请参阅 [创建扫描规则集](create-a-scan-rule-set.md)。
