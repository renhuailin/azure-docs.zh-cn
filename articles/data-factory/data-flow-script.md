---
title: 映射数据流脚本
description: 数据工厂的数据流脚本代码隐藏语言概述
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/22/2021
ms.openlocfilehash: 73fe862475b866e625d4bf2bdce3c044b6dcc87b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061573"
---
# <a name="data-flow-script-dfs"></a>数据流脚本 (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

数据流脚本 (DFS) 是基础元数据，类似于编码语言，用于执行映射数据流中包含的转换。 每次转换均由一系列属性表示，这些属性提供正常运行作业所需的信息。 通过单击浏览器 UI 顶部功能区上的“脚本”按钮，可从 ADF 查看和编辑该脚本。

:::image type="content" source="media/data-flow/scriptbutton.png" alt-text="“脚本”按钮":::

例如，源转换中的 `allowSchemaDrift: true,` 会指示服务将源数据集中的所有列包含在数据流中，即使这些列未包含在架构投影中。

## <a name="use-cases"></a>用例
DFS 由用户界面自动生成。 可以单击“脚本”按钮来查看并自定义该脚本。 还可以在 ADF UI 外部生成脚本，然后将其传递到 PowerShell cmdlet。 调试复杂数据流时，你可能会发现扫描脚本代码隐藏比扫描流的 UI 图形表示形式更容易。

下面是一些示例用例：
- 以编程方式生成许多非常相似的数据流，即“标记”数据流。
- 在 UI 中难以管理或者会导致验证问题的复杂表达式。
- 调试和更好地了解执行过程中返回的各种错误。

生成要与 PowerShell 或 API 结合使用的数据流脚本时，必须将格式化文本折叠为一行。 可以将制表符和换行符作为转义字符。 但必须对文本进行格式化，才能容纳到 JSON 属性中。 脚本编辑器 UI 底部有一个按钮，该按钮可将脚本的格式设置为一行。

:::image type="content" source="media/data-flow/copybutton.png" alt-text="“复制”按钮":::

## <a name="how-to-add-transforms"></a>如何添加转换
添加转换需要三个基本步骤：添加核心转换数据、重新路由输入流，然后重新路由输出流。 通过示例可轻松阐明这些步骤。
假设我们先通过一个简单的源来接收数据流，如下所示：

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

如果我们决定添加派生转换，首先需要创建核心转换文本，其中包含一个简单的表达式，用于添加名为 `upperCaseTitle` 的新大写列：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

然后获取现有 DFS 并添加转换：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

现在，通过确定接下来要在其后进行新转换的转换（在本例中为 `source1`），并将流的名称复制到新转换来重新路由传入流：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最后，确定要在此新转换后进行的转换，并将其输入流（在本例中为 `sink1`）替换为新转换的输出流名称：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 基础知识
DFS 由一系列连接的转换组成，其中包括源、接收器，以及各种其他转换（可添加新列、筛选数据、联接数据以及执行其他操作）。 通常情况下，脚本以一个或多个源开始，后跟许多转换，以一个或多个接收器结束。

所有源都具有相同的基本构造：
```
source(
  source properties
) ~> source_name
```

例如，具有三个列（movieId、title、genres）的简单源将为：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

除源之外的所有转换都具有相同的基本构造：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，采用列 (title) 并使用大写版本覆盖此列的简单派生转换将如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

无架构的接收器将如下所示：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>脚本代码片段

脚本代码片段是可共享的数据流脚本代码，可用于在数据流之间共享。 下面的视频介绍了如何使用脚本代码片段以及使用数据流脚本在数据流图后面复制并粘贴部分脚本：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>聚合汇总统计信息
将聚合转换添加到名为“SummaryStats”的数据流中，然后在脚本中粘贴聚合函数的此代码，并替换现有的 SummaryStats。 这将为数据配置文件摘要统计信息提供通用模式。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
你还可以使用下面的示例来计算数据中的唯一行数和非重复行数。 可以使用名为 ValueDistAgg 的聚合转换将以下示例粘贴到数据流中。 此示例使用名为“title”的列。 请确保将“title”替换为要用于获取值计数的数据中的字符串列。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>包含聚合中的所有列
这是一种通用聚合模式，展示如何在生成聚合时将其余的列保存在输出元数据中。 在此示例中，我们使用 ```first()``` 函数来选择名称不是“movie”的每列中的第一个值。 若要使用此函数，请创建名为 DistinctRows 的聚合转换，然后将其粘贴到现有 DistinctRows 聚合脚本顶部的脚本中。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>创建行哈希指纹 
使用数据流脚本中的此代码创建一个名为 ```DWhash``` 的新派生列，该派生列可生成三列的 ```sha1``` 哈希。

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

还可以使用以下脚本，使用流中的所有列生成行哈希，而无需命名每一列：

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg 等效项
此代码的行为类似于 T-SQL ```string_agg()``` 函数，并会将字符串值聚合到一个数组中。 这样，你便可以将该数组强制转换为要与 SQL 目标一起使用的字符串。

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>更新、更新插入、插入、删除的次数
使用更改行转换时，可能需要计算由更改行策略导致的更新、更新插入、插入、删除的次数。 更改行之后添加聚合转换，然后将此数据流脚本粘贴到这些计数的聚合定义中。

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>使用所有列的非重复行
此代码片段会向采用所有传入列的数据流添加一个新的聚合转换、生成用于分组以消除重复项的哈希，然后提供每个重复项的第一个匹配项作为输出。 不需要显式命名列，系统会自动根据传入的数据流生成列。

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>检查所有列中是否存在 NULL 值
这是一个代码片段，你可以将其粘贴到数据流中，通常可检查所有列中是否存在 NULL 值。 此方法利用架构偏差来浏览所有行中的所有列，并使用有条件拆分将具有 NULL 值的行与不具有 NULL 值的行分隔开。 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>使用 select 自动映射架构偏差
如果需要从未知或动态的传入列集中加载现有的数据库架构，则必须在接收器转换中映射右侧的列。 仅在加载现有表时才需要执行此操作。 在接收器之前添加此代码片段，以创建自动映射列的 Select。 将接收器映射保留为自动映射。

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>保留列数据类型
将此脚本添加到派生列定义中，以使用接收器将数据流中的列名称和数据类型存储到永久存储区中。

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>向下填充
此部分介绍当你想要使用序列中之前的非 NULL 值替换 NULL 值时，如何在数据集中解决常见的“向下填充”问题。 请注意，此操作可能会对性能造成负面影响，因为必须使用“虚拟”类别值在整个数据集中创建合成窗口。 此外，必须按值进行排序才能创建适当的数据序列以查找之前的非 NULL 值。 下面的代码片段创建合成类别作为“虚拟”类别，并按代理键进行排序。 可以删除代理键并使用你自己的数据特定排序键。 此代码片段假设你已添加名为 ```source1``` 的源转换

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>移动平均线
可通过使用 Windows 转换在数据流中轻松实现移动平均线。 下面的示例创建了 Microsoft 股价的 15 天移动平均线。

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

### <a name="distinct-count-of-all-column-values"></a>所有列值的非重复计数
可以使用此脚本来识别键列，并使用单个脚本片段查看流中所有列的基数。 将此脚本作为聚合转换添加到数据流中，它会自动提供所有列的非重复计数。

```
aggregate(each(match(true()), $$ = countDistinct($$))) ~> KeyPattern
```

### <a name="compare-previous-or-next-row-values"></a>比较上一个或下一个行值
此示例代码片段演示如何使用窗口转换将当前行上下文中的列值与当前行之前和之后的行中的列值进行比较。 在此示例中，派生列用于生成虚拟值以启用跨整个数据集的窗口分区。 代理键转换用于为每一行分配一个唯一的键值。 将此模式应用于数据转换时，如果你希望按某个列排序，则可以删除代理键，如果有某些列可用于数据分区依据，则可以删除派生列。

```
source1 keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey1
SurrogateKey1 derive(dummy = 1) ~> DerivedColumn1
DerivedColumn1 window(over(dummy),
    asc(sk, true),
    prevAndCurr = lag(title,1)+'-'+last(title),
        nextAndCurr = lead(title,1)+'-'+last(title)) ~> leadAndLag
```

## <a name="next-steps"></a>后续步骤

通过[数据流概述文章](concepts-data-flow-overview.md)了解数据流
