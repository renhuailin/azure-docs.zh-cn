---
title: 创建自定义分类和分类规则
description: 了解如何创建自定义分类来定义数据资产中 Azure Purview 的组织独有的数据类型。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 12af9165b8393291b67fd0cbe4cb2ced6ae77a17
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212110"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure Purview 中的自定义分类

本文介绍如何创建自定义分类来定义数据资产中组织独有的数据类型。 此外，还介绍如何创建可用于在整个数据资产中查找指定数据的自定义分类规则。

## <a name="default-system-classifications"></a>默认系统分类

Azure Purview 数据目录提供了大量默认系统分类，这些分类表示你在数据资产中可能具有的典型个人数据类型。 有关可用系统分类的完整列表，请参阅 [Azure Purview 中受支持的分类](supported-classifications.md)。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="选择分类" border="true":::

如果任何默认分类都不能满足需求，还可以创建自定义分类。

> [!Note]
> [数据采样规则](sources-and-scans.md#sampling-within-a-file)同时适用于系统分类和自定义分类。  

## <a name="steps-to-create-a-custom-classification"></a>创建自定义分类的步骤

若要创建自定义分类，请执行以下步骤：

1. 从目录的左侧菜单中选择“数据映射”。

2. 在“注释管理”下选择“分类”。

3. 选择“+ 新建”

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="新建分类" border="true":::

此时将打开“添加新分类”窗格，你可以在其中为分类指定名称和说明。 最好使用名称间距约定，如 `your company name.classification name`。

Microsoft 系统分类按保留的 `MICROSOFT.` 命名空间分组。 例如，MICROSOFT.GOVERNMENT.US.SOCIAL\_SECURITY\_NUMBER。

分类的名称必须以字母开头，后跟一系列字母、数字和句点 (.) 或下划线字符。 不允许包含空格。 键入时，UX 会自动生成一个友好名称。 此友好名称是用户将其应用于目录中的资产时会看到的内容。

为了使名称简短，系统基于以下逻辑创建友好名称：

- 除了命名空间的最后两段之外的所有部分都被剪裁掉。

- 调整大小写，使每个单词的首字母大写。

- 所有下划线 (\_) 都替换为空格。

例如，如果已命名分类 CONTOSO.HR.EMPLOYEE\_ID，则友好名称将在系统中存储为 Hr.Employee ID。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

选择“确定”，然后将新分类添加到分类列表。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="自定义分类" border="true":::

选择列表中的分类会打开分类详细信息页。 可在此处找到有关分类的所有详细信息。

这些详细信息包括实例计数、正式名称、关联的分类规则（如有），以及所有者名称。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="选择分类" border="true":::

## <a name="custom-classification-rules"></a>自定义分类规则

目录服务提供了一组默认分类规则，扫描程序使用这些规则来自动检测某些数据类型。 还可以添加自己的自定义分类规则，以检测你可能有兴趣在整个数据资产中查找的其他数据类型。 当你尝试在数据资产中查找数据时，此功能会非常强大。

例如，假设名为 Contoso 的公司拥有在整个公司中已实现标准化的员工 ID，其中 \"Employee\" 一词后跟 GUID 来创建 EMPLOYEE{GUID}。 例如，员工 ID 的一个实例类似于 `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55`。

Contoso 可以通过创建自定义分类规则将扫描系统配置为查找这些 ID 的实例。 可以提供与数据模式匹配的正则表达式，在本例中为 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$`。 （可选）如果数据通常位于已知名称所在的列中（如 Employee\_ID 或 EmployeeID），则他们可以添加列模式正则表达式，以使扫描更准确。 正则表达式的示例是 Employee\_ID\|EmployeeID。

然后，扫描系统可以使用此规则检查列中的实际数据和列名称来尝试标识找到了员工 ID 模式的每个实例。

## <a name="steps-to-create-a-custom-classification-rule"></a>创建自定义分类规则的步骤

若要创建自定义分类规则，请执行以下操作：

1. 按照上一部分中的说明创建自定义分类。 你将在分类规则配置中添加此自定义分类，以便系统在列中找到匹配项时应用它。

2. 选择“数据映射”图标。

3. 选择“分类规则”部分。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification-rules.png" alt-text="分类规则磁贴" border="true":::

4. 选择“新建”。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification-rule.png" alt-text="添加新的分类规则" border="true":::

5. 此时将打开“新建分类规则”对话框。 填充字段并决定是创建正则表达式规则还是创建字典规则。

   |字段     |说明  |
   |---------|---------|
   |名称   |    必需。 最大值为 100 个字符。    |
   |说明      |可选。 最大值为 256 个字符。    |
   |分类名称    | 必需。 从下拉列表中选择分类的名称，以指示扫描程序在找到匹配项时应用该分类。        |
   |状态   |  必需。 选项处于启用或禁用状态。 默认值为“已启用”。    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="创建新的分类规则" border="true":::

### <a name="creating-a-regular-expression-rule"></a>创建正则表达式规则

1. 如果创建正则表达式规则，则会看到以下屏幕。 你可以选择上传一个文件，该文件将用于为你的规则生成建议的正则表达式模式。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="创建新的正则表达式规则" border="true":::

1. 如果决定生成建议的正则表达式模式，请在上传文件后，选择某个建议的模式，然后选择“添加到模式”以使用建议的数据和列模式。 可以调整建议的模式，也可以键入自己的模式，而无需上传文件。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="生成建议的正则表达式" border="true":::

   |字段     |说明  |
   |---------|---------|
   |数据模式    |可选。 表示存储在数据字段中的数据的正则表达式。 此限制非常大。 在上面的示例中，数据模式测试员工 ID 是否为 `Employee{GUID}` 一词的字面意思。  |
   |列模式    |可选。 表示要匹配的列名称的正则表达式。 此限制非常大。 |

1. 在“数据模式”下，可使用“最小匹配阈值”来设置扫描程序应用分类所必须在列中找到的非重复数据值匹配的最小百分比 。 建议的值为 60%。 如果指定多个数据模式，则此设置处于禁用状态，并且值固定为 60%。

   > [!Note]
   > 最小匹配阈值必须至少为 1%。

1. 你现在可以验证规则并创建它。
1. 在完成创建过程之前测试分类规则，验证是否会对资产应用标记。 规则中的分类将应用于上传的示例数据，就像在扫描中一样。 这意味着所有系统分类和自定义分类都将与文件中的数据一致。

   输入文件可能包含带分隔符的文件（CSV、PSV、SSV、TSV）、JSON 或 XML 内容。 将基于输入文件的文件扩展名对内容进行分析。 带分隔符的数据可能具有与上述所有类型一致的文件扩展名。 例如，TSV 数据可存在于名为 MySampleData.csv 的文件中。 带分隔符的内容必须至少有 3 列。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-screen.png" alt-text="创建前的测试规则" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-uploaded-file-result-screen.png" alt-text="上传测试文件后查看应用的分类" border="true":::

### <a name="creating-a-dictionary-rule"></a>创建字典规则

1. 如果创建字典规则，则会看到以下屏幕。 上传一个包含要在单个列中创建的分类的所有可能值的文件。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="创建字典规则" border="true":::

1. 生成字典后，可以调整最小匹配阈值并提交规则。

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="创建字典规则，并附带“字典已生成”复选标记。" border="true":::

## <a name="next-steps"></a>后续步骤

现在，创建了分类规则后，就可以将其添加到扫描规则集，以便扫描时使用该规则。 有关详细信息，请参阅[创建扫描规则集](create-a-scan-rule-set.md)。
