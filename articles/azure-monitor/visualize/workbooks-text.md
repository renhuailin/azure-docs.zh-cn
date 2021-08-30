---
title: Azure Monitor 工作簿文本参数
description: 使用预生成和自定义的参数化工作簿简化复杂的报表。 详细了解工作簿文本参数。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: e3beedff4b9d65f5f0b69b5c60bd67d4b134d096
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287479"
---
# <a name="workbook-text-parameters"></a>工作簿文本参数

使用文本框参数可以方便地从工作簿用户那里收集文本输入。 无法使用下拉列表收集输入（例如任意阈值或常规筛选器）时，可以使用文本框参数。 工作簿允许作者从查询中获取文本框的默认值。 这样，就可以实现一些有意义的方案，例如，根据指标的 p95 设置默认阈值。

文本框的常见用途是可以充当其他工作簿控件使用的内部变量。 若要起到这种效果，可以使用查询来获取默认值，并使输入控件在读取模式下隐藏。 例如，用户可能希望某个阈值来自公式（而不是来自用户），然后在后续查询中使用该阈值。

## <a name="creating-a-text-parameter"></a>创建文本参数
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 选择蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`SlowRequestThreshold`
    2. 参数类型：`Text`
    3. 必需：`checked`
    4. 从以下位置获取数据：`None`
5. 从工具栏中选择“保存”以创建参数。

    :::image type="content" source="./media/workbooks-text/text-create.png" alt-text="显示如何创建文本参数的屏幕截图。":::

这就是工作簿在读取模式下的外观。

:::image type="content" source="./media/workbooks-text/text-readmode.png" alt-text="显示处于读取模式的文本参数的屏幕截图。" border="false":::

## <a name="parameter-field-style"></a>参数字段样式
文本参数支持以下字段样式：

- 标准：单行文本字段。

     :::image type="content" source="./media/workbooks-text/standard-text.png" alt-text="显示标准文本字段的屏幕截图。":::

- 密码：单行密码字段。 仅当用户键入密码时，密码值才会在 UI 中隐藏。 引用该值时，仍可完全将其作为参数值进行访问；保存工作簿时，该值将以不加密的形式存储。

     :::image type="content" source="./media/workbooks-text/password-text.png" alt-text="显示密码字段的屏幕截图。":::

- 多行：一个多行文本字段，支持以下语言的丰富 Intellisense 和语法着色：
    - 文本
    - Markdown
    - JSON
    - SQL
    - TypeScript
    - KQL
    - TOML

    用户还可以指定多行编辑器的高度。

     :::image type="content" source="./media/workbooks-text/kql-text.png" alt-text="显示多行文本字段的屏幕截图。":::

## <a name="referencing-a-text-parameter"></a>引用文本参数
1. 选择蓝色的 `Add query` 链接将查询控件添加到工作簿，然后选择 Application Insights 资源。
2. 在 KQL 框中添加以下片段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 结合查询控件使用值为 500 的文本参数有效运行以下查询：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 运行查询以查看结果

    :::image type="content" source="./media/workbooks-text/text-reference.png" alt-text="显示 KQL 中引用的文本参数的屏幕截图。":::

> [!NOTE]
> 在以上示例中，`{SlowRequestThreshold}` 表示一个整数值。 如果查询类似于 `{ComputerName}` 的字符串，则需要修改 Kusto 查询以添加引号 `"{ComputerName}"`，使参数字段接受不带引号的输入。

## <a name="setting-default-values-using-queries"></a>使用查询设置默认值
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 选择蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`SlowRequestThreshold`
    2. 参数类型：`Text`
    3. 必需：`checked`
    4. 从以下位置获取数据：`Query`
5. 在 KQL 框中添加以下片段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查询将文本框的默认值设置为应用中所有请求的持续时间的 95%。
6. 运行查询以查看结果
7. 从工具栏中选择“保存”以创建参数。

    :::image type="content" source="./media/workbooks-text/text-default-value.png" alt-text="显示使用 KQL 中默认值的文本参数的屏幕截图。":::

> [!NOTE]
> 尽管此示例查询的是 Application Insights 数据，但该方法可用于任何基于日志的数据源 - Log Analytics、Azure Resource Graph 等。

## <a name="adding-validations"></a>添加验证 

对于标准和密码文本参数，用户可添加应用于文本字段的验证规则。 添加包含错误消息的有效正则表达式。 如果设置了消息，则当字段无效时，它将显示为错误。

如果选择了匹配，则该字段在值与正则表达式匹配时有效；否则该字段在与正则表达式不匹配时有效。

:::image type="content" source="./media/workbooks-text/validation-settings.png" alt-text="“文本验证”设置的屏幕截图。":::

## <a name="format-json-data"></a>设置 JSON 数据的格式 

如果选择“JSON”作为多行文本字段的语言，则该字段将提供一个按钮用于设置其 JSON 数据的格式。 用户还可以使用 `(ctrl + \)` 快捷键来设置 JSON 数据的格式。

如果数据来自某个查询，则用户可以选择相应的选项来预先设置该查询返回的 JSON 数据的格式。

:::image type="content" source="./media/workbooks-text/pre-format-json-data.png" alt-text="预先设置 JSON 数据格式的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

* [开始](./workbooks-overview.md#visualizations)详细了解工作簿丰富的可视化效果选项。
* [控制](./workbooks-access-control.md)并共享对工作簿资源的访问权限。