---
title: 将映射数据流参数化
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何参数化来自 Azure 数据工厂和 Azure Synapse Analytics 管道的映射数据流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d01e1ca8d3eb0ca2e345d42118ad5f1463c3139f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750463"
---
# <a name="parameterizing-mapping-data-flows"></a>将映射数据流参数化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Azure 数据工厂和 Synapse 管道中的映射数据流支持使用参数。 定义数据流定义内的参数并在整个表达式中使用它们。 参数值由调用管道通过“执行数据流”活动设置。 你可以使用三个选项来设置数据流活动表达式中的值：

* 使用管道控制流表达式语言来设置动态值
* 使用数据流表达式语言来设置动态值
* 使用任一表达式语言来设置静态文本值

使用此功能可以使数据流具有通用性、灵活性和可重用性。 可以使用这些参数将数据流设置和表达式参数化。

## <a name="create-parameters-in-a-mapping-data-flow"></a>在映射数据流中创建参数

若要将参数添加到数据流，请单击数据流画布的空白部分，查看常规属性。 在设置窗格中，你将看到名为“参数”的选项卡。 选择“新建”，生成新参数。 对于每个参数，必须分配名称，选择类型，然后根据需要设置默认值。

:::image type="content" source="media/data-flow/create-params.png" alt-text="创建数据流参数":::

## <a name="use-parameters-in-a-mapping-data-flow"></a>在映射数据流中使用参数 

可以在任意数据流表达式中引用参数。 参数以 $ 开头，并且不可改变。 可以在“参数”选项卡下发现表达式生成器内的可用参数的列表。

:::image type="content" source="media/data-flow/parameter-expression.png" alt-text="屏幕截图显示了“参数”选项卡中的可用参数。":::

可以通过选择“新建参数”并指定名称和类型来快速添加更多参数。

:::image type="content" source="media/data-flow/new-parameter-expression.png" alt-text="屏幕截图显示了“参数”选项卡中的参数以及新添加的参数。":::

## <a name="assign-parameter-values-from-a-pipeline"></a>从管道分配参数值

使用参数创建数据流后，可以通过“执行数据流活动”从管道执行该数据流。 将活动添加到管道画布后，活动的“参数”选项卡中将显示可用的数据流参数。

分配参数值时，可以根据 Spark 类型使用[管道表达式语言](control-flow-expression-language-functions.md)或[数据流表达式语言](data-flow-expression-functions.md)。 所有映射数据流都可以具有管道和数据流表达式参数的任意组合。

:::image type="content" source="media/data-flow/parameter-assign.png" alt-text="屏幕截图显示了“参数”选项卡，其中为 myparam 的值选择了“数据流表达式”。":::

### <a name="pipeline-expression-parameters"></a>管道表达式参数

使用管道表达式参数，可以引用系统变量、函数、管道参数以及类似于其他管道活动的变量。 单击“管道表达式”可打开侧导航栏，可以通过该导航栏使用表达式生成器输入表达式。

:::image type="content" source="media/data-flow/parameter-pipeline.png" alt-text="屏幕截图显示了表达式生成器窗格。":::

引用管道参数时，系统将计算该参数的值，然后在数据流表达式语言中使用其值。 管道表达式类型不需要与数据流参数类型匹配。 

#### <a name="string-literals-vs-expressions"></a>字符串文本与表达式

分配字符串类型的管道表达式参数时，默认情况下将添加引号，并将值作为文本评估。 若要将参数值作为数据流表达式读取，请勾选参数旁的表达式框。

:::image type="content" source="media/data-flow/string-parameter.png" alt-text="屏幕截图显示了“数据流参数”窗格，其中为参数选择了“表达式”。":::

对于数据流参数，`stringParam` 引用值为 `upper(column1)` 的管道参数。 

- 如果勾选了表达式，`$stringParam` 的计算结果为 column1 的值（全部大写）。
- 如果未勾选表达式（默认行为），择 `$stringParam` 的计算结果为 `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>传入时间戳

在管道表达式语言中，`pipeline().TriggerTime` 等系统变量和 `utcNow()` 等函数以“yyyy-MM-dd\'T\'HH:mm:ss.SSSSSSZ”格式的字符串形式返回时间戳。 若要将其转换为时间戳类型的数据流参数，请使用字符串内插在 `toTimestamp()` 函数中加入所需时间戳。 例如，若要将管道触发时间转换为数据流参数，可以使用 `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`。 

:::image type="content" source="media/data-flow/parameter-timestamp.png" alt-text="屏幕截图显示了可在其中输入触发时间的“参数”选项卡。":::

> [!NOTE]
> 数据流最多支持表示 3 毫秒的数字。 `left()` 函数用于裁剪超出部分的数字。

#### <a name="pipeline-parameter-example"></a>管道参数示例

假设整数参数 `intParam` 引用字符串类型的管道参数 `@pipeline.parameters.pipelineParam`。 

:::image type="content" source="media/data-flow/parameter-pipeline-2.png" alt-text="屏幕截图显示了“参数”选项卡，其中包含名为 stringParam 和 intParam 的参数。":::

在运行时为 `@pipeline.parameters.pipelineParam` 分配值 `abs(1)`。

:::image type="content" source="media/data-flow/parameter-pipeline-4.png" alt-text="屏幕截图显示了“参数”选项卡，其中选择了 a b s (1) 的值。":::

在派生列等表达式中引用 `$intParam` 时，它将计算 `abs(1)` 的结果，并返回 `1`。 

:::image type="content" source="media/data-flow/parameter-pipeline-3.png" alt-text="屏幕截图显示了列值。":::

### <a name="data-flow-expression-parameters"></a>数据流表达式参数

选择“数据流表达式”可打开数据流表达式生成器。 你将能够在整个数据流中引用函数、其他参数和任何已定义的架构列。 在引用此表达式时，系统将计算其值。

> [!NOTE]
> 如果传入的表达式无效，或引用该转换中不存在的架构列，则参数的计算结果为 null。


### <a name="passing-in-a-column-name-as-a-parameter"></a>以参数的形式传入列名

通常将列名作为参数值传入。 如果在数据流架构中定义了列，则可以直接将其作为字符串表达式引用。 如果未在架构中定义列，请使用 `byName()` 函数。 请记住使用 `toString()` 等转换函数将列转换为适当的类型。

例如，如果要基于参数 `columnName` 映射字符串列，可以添加派生列转换 `toString(byName($columnName))`。

:::image type="content" source="media/data-flow/parameterize-column-name.png" alt-text="以参数的形式传入列名":::

## <a name="next-steps"></a>后续步骤
* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
