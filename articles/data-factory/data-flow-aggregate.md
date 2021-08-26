---
title: 映射数据流中的聚合转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用映射数据流聚合转换在 Azure 数据工厂中大规模聚合数据。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/14/2020
ms.openlocfilehash: 7723a7c404b4e2350d9396f017874df49f74d556
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637798"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>映射数据流中的聚合转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

聚合转换可以定义数据流中列的聚合。 使用表达式生成器可以定义不同类型的聚合，如 SUM、MIN、MAX 和 COUNT，并按现有列或计算列进行分组。

## <a name="group-by"></a>Group by

选择现有列或创建新的计算列，以用作聚合的 group by 子句。 若要使用现有列，请从下拉列表中选择它。 若要创建新的计算列，请将鼠标悬停在子句上，并单击“计算列”。 这将打开[数据流表达式生成器](concepts-data-flow-expression-builder.md)。 创建计算列后，请在“命名为”字段下输入输出列名称。 如果要添加其他 group by 子句，请将鼠标悬停在现有子句上，然后单击加号图标。

![聚合转换分组依据设置](media/data-flow/agg.png "聚合转换分组依据设置")

在聚合转换中，group by 子句是可选的。

## <a name="aggregate-columns"></a>聚合列

转到“聚合”选项卡以生成聚合表达式。 可以使用聚合覆盖现有列，也可以使用新名称创建新字段。 在列名选择器旁的右侧框中输入聚合表达式。 若要编辑表达式，请单击文本框并打开表达式生成器。 若要添加更多聚合列，请单击列列表上方的“添加”或现有聚合列旁边的加号图标。 选择“添加列”或“添加列模式”。 每个聚合表达式必须至少包含一个聚合函数。

![聚合设置](media/data-flow/aggregate-columns.png "聚合设置")

> [!NOTE]
> 在“调试”模式下，表达式生成器无法使用聚合函数生成数据预览。 若要查看聚合转换的数据预览，请关闭表达式生成器，然后通过“数据预览”选项卡查看数据。

### <a name="column-patterns"></a>列模式

使用[列模式](concepts-data-flow-column-pattern.md)将相同的聚合应用于一组列。 如果希望保留输入架构中的许多列（默认情况下会将其删除），则此功能很有用。 使用诸如 `first()` 之类的试探法来通过聚合保留输入列。

## <a name="reconnect-rows-and-columns"></a>重新连接行和列

聚合转换类似于 SQL 聚合 select 查询。 未包含在 group by 子句或聚合函数中的列不会流向聚合转换的输出。 如果希望在聚合输出中包括其他列，请执行以下方法之一：

* 使用聚合函数（如 `last()` 或 `first()`）来包含该其他列。
* 使用[自联接模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)将列重新联接到输出流。

## <a name="removing-duplicate-rows"></a>删除重复的行

聚合转换的常见用途是删除或标识源数据中的重复条目。 此过程称为重复数据删除。 基于一组分组依据键，使用你选择的试探法确定要保留的重复行。 常见试探法有 `first()`、`last()`、`max()` 和 `min()`。 使用[列模式](concepts-data-flow-column-pattern.md)将规则应用到除分组依据列之外的每一列。

![重复数据删除](media/data-flow/agg-dedupe.png "重复数据删除")

在上面的示例中，列 `ProductID` 和 `Name` 用于分组。 如果这两列中有两行的值相同，则将其视为重复项。 在此聚合转换中，将保留匹配的第一行的值，并删除所有其他行的值。 使用列模式语法，将名称不是 `ProductID` 和 `Name` 的所有列都映射到它们的现有列名，并为其指定第一个匹配行的值。 输出架构与输入架构相同。

对于数据验证方案，`count()` 函数可用于计算有多少重复项。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>示例

下面的示例采用传入流 `MoviesYear` 并按列 `year` 对行分组。 该转换将创建一个汇总列 `avgrating`，其计算结果为列 `Rating` 的平均值。 此聚合转换名为 `AvgComedyRatingsByYear`。

在数据工厂 UX 中，此转换如下图所示：

![按示例分组](media/data-flow/agg-script1.png "按示例分组")

![聚合示例](media/data-flow/agg-script2.png "聚合示例")

此转换的数据流脚本位于下面的代码片段中。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![聚合数据流脚本](media/data-flow/aggdfs1.png "聚合数据流脚本")

```MoviesYear```：用于定义年份和标题列的派生列 ```AvgComedyRatingByYear```：按年份分组的喜剧的平均评分的聚合转换 ```avgrating```：将为保存聚合值而创建的新列的名称

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>后续步骤

* 使用[窗口转换](data-flow-window.md)定义基于窗口的聚合
