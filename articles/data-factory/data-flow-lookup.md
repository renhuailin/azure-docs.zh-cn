---
title: 映射数据流中的查找转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 使用映射数据流中的查找转换从另一个源引用数据。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 02/19/2021
ms.openlocfilehash: f6250b15f854870d14d9977c8eebd7c71e565635
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638823"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>映射数据流中的查找转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用查找转换引用数据流中另一个源的数据。 查找转换会将匹配数据中的列追加到源数据中。

查找转换类似于左外部联接。 主流中的所有行都将存在于包含查找流中其他列的输出流中。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>配置

![屏幕截图显示“查找设置”选项卡，其中包含下文所述的标签。](media/data-flow/lookup1.png "查找")

**主流：** 传入的数据流。 此流等效于联接的左侧。

**查找流：** 追加到主流的数据。 查找条件确定要添加的数据。 此流等效于联接的右侧。

**匹配多个行：** 如果启用，则主流中具有多个匹配的行将返回多个行。 否则，将根据“匹配”条件仅返回单个行。

**匹配：** 仅当未选择“匹配多个行”时可见。 选择是否匹配任意行、首次匹配或最后匹配。 建议匹配任意行，因为它执行速度最快。 如果选择了首行或末行，则需要指定排序条件。

**查找条件：** 选择要匹配的列。 如果满足相等条件，则将这些行视为匹配项。 悬停鼠标并选择“计算列”，以使用[数据流表达式语言](data-flow-expression-functions.md)提取值。

输出数据中包含两个流中的所有列。 若要删除重复列或不需要的列，请在查找转换后添加[选择转换](data-flow-select.md)。 还可以在接收器转换中删除或重命名列。

### <a name="non-equi-joins"></a>非等值联接

若要在查找条件中使用条件运算符，例如不等于 (!=) 或大于 (>)，请在两列之间更改运算符下拉菜单。 非等值联接要求使用“优化”选项卡中的“固定”广播来广播两个流中的至少一个流 。

![非等值查找](media/data-flow/non-equi-lookup.png "非等值查找")

## <a name="analyzing-matched-rows"></a>分析匹配的行

查找转换后，可以使用函数 `isMatch()` 来查看查找是否匹配单个行。

![查找模式](media/data-flow/lookup111.png "查找模式")

此模式的一个示例是使用有条件拆分转换来拆分 `isMatch()` 函数。 在上面的示例中，匹配行经过最顶部的流，而不匹配的行则流过 ```NoMatch``` 流。

## <a name="testing-lookup-conditions"></a>测试查找条件

在调试模式下使用数据预览测试查找转换时，请使用一小组已知数据。 对大型数据集中的行进行采样时，无法预测将读取哪些行和键进行测试。 结果是非确定性的，这意味着你的联接条件可能不会返回任何匹配项。

## <a name="broadcast-optimization"></a>广播优化

![广播联接](media/data-flow/broadcast.png "广播联接")

在联接、查找和存在转换中，如果工作器节点内存可容纳一个数据流或同时容纳两个数据流，则可以通过启用“广播”来优化性能。 默认情况下，Spark 引擎将自动决定是否广播一侧。 若要手动选择要广播的一侧，请选择“固定”。

建议不要通过“关闭”选项来禁用广播，除非联接遇到超时错误。

## <a name="cached-lookup"></a>缓存查找

如果要在同一源上执行多次小范围查找，那么相比于查找转换，缓存的接收器和查找可能是一个更佳用例。 缓存接收器的常见示例在以下情况中可能更佳：查找数据存储中的最大值，将错误代码与错误消息数据库进行匹配。 有关详细信息，请了解[缓存接收器](data-flow-sink.md#cache-sink)和[缓存的查找](concepts-data-flow-expression-builder.md#cached-lookup)。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>示例

![屏幕截图显示以下代码的“查找设置”选项卡。](media/data-flow/lookup-dsl-example.png "查找")

以上查找配置的数据流脚本位于下面的代码片段中。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```

## <a name="next-steps"></a>后续步骤

* [联接](data-flow-join.md)和[存在](data-flow-exists.md)转换都采用多个流输入
* 使用带有 ```isMatch()``` 的[有条件拆分转换](data-flow-conditional-split.md)来拆分匹配和不匹配值的行
