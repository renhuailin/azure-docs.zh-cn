---
title: Azure Cosmos DB Gremlin 支持并兼容 TinkerPop 功能
description: 了解 Apache TinkerPop 的 Gremlin 语言。 了解 Azure Cosmos DB 中可用的功能和步骤，以及 TinkerPop Graph 引擎兼容性差异。
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/06/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 4f4a683c62e6704d6cfeb42c9f78c1acb5b67370
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783214"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Azure Cosmos DB Gremlin 图形支持并兼容 TinkerPop 功能
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB 支持 [Apache Tinkerpop](https://tinkerpop.apache.org) 的图形遍历语言（称为 [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)）。 可以使用 Gremlin 语言创建图形实体（顶点和边缘）、修改这些实体内部的属性、执行查询和遍历，以及删除实体。

Azure Cosmos DB Graph 引擎严格遵循 [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 遍历步骤规范，但在实现中存在特定于 Azure Cosmos DB 的差异。 本文提供 Gremlin 的快速演练，并列举 Gremlin API 支持的 Gremlin 功能。

## <a name="compatible-client-libraries"></a>兼容的客户端库

下表显示可以对 Azure Cosmos DB 使用的常用 Gremlin 驱动程序：

| 下载 | 源 | 入门 | 支持的连接器版本 |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [GitHub 上的 Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [使用 .NET 创建图形](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [使用 Java 创建图形](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [GitHub 上的 Gremlin-JavaScript](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [使用 Node.js 创建图形](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [GitHub 上的 Gremlin-Python](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [使用 Python 创建图形](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [GitHub 上的 Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [使用 PHP 创建图形](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | 此库由外部参与者生成。 Azure Cosmos DB 团队不对该库提供任何支持或维护。 |
| [Gremlin 控制台](https://tinkerpop.apache.org/downloads.html) | [TinkerPop 文档](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [使用 Gremlin 控制台创建图形](create-graph-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>支持的图对象

TinkerPop 是涵盖多种图形技术的标准。 因此，它使用标准的术语来描述图形提供程序提供的功能。 Azure Cosmos DB 提供一个可跨多个服务器或群集分区的持久性、高并发性、可写的图形数据库。 

下表列出了 Azure Cosmos DB 实现的 TinkerPop 功能： 

| 类别 | Azure Cosmos DB 实现 |  说明 | 
| --- | --- | --- |
| 图形功能 | 提供持久性和并发访问。 旨在支持事务 | 可通过 Spark 连接器实现计算机方法。 |
| 变量功能 | 支持布尔值、整数、字节、双精度值、浮点值、长整数和字符串 | 支持基元类型，通过数据模型与复杂类型兼容 |
| 顶点功能 | 支持 RemoveVertices、MetaProperties、AddVertices、MultiProperties、StringIds、UserSuppliedIds、AddProperty、RemoveProperty  | 支持创建、修改和删除顶点 |
| 顶点属性功能 | StringIds、UserSuppliedIds、AddProperty、RemoveProperty、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支持创建、修改和删除顶点属性 |
| 边缘功能 | AddEdges、RemoveEdges、StringIds、UserSuppliedIds、AddProperty、RemoveProperty | 支持创建、修改和删除边缘 |
| 边缘属性功能 | Properties、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支持创建、修改和删除边缘属性 |

## <a name="gremlin-wire-format"></a>Gremlin 网络格式

从 Gremlin 操作返回结果时，Azure Cosmos DB 使用 JSON 格式。 Azure Cosmos DB 目前支持 JSON 格式。 例如，以下代码片段显示了从 Azure Cosmos DB 返回到客户端的某个顶点的 JSON 表示形式。 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

下面介绍了顶点的 JSON 格式使用的属性：

| 属性 | 说明 | 
| --- | --- | --- |
| `id` | 顶点的 ID。 必须唯一（在适用的情况下，可与 `_partition` 的值合并）。 如果未提供任何值，则系统会自动提供一个包含 GUID 的值 | 
| `label` | 顶点的标签。 此属性用于描述实体类型。 |
| `type` | 用于将顶点与非图形文档相区分 |
| `properties` | 与顶点关联的用户定义属性包。 每个属性可以有多个值。 |
| `_partition` | 顶点的分区键。 用于[图形分区](graph-partitioning.md)。 |
| `outE` | 此属性包含顶点中外部边缘的列表。 存储顶点的相邻信息，以便快速执行遍历。 边缘根据其标签分组。 |

每个属性可在一个数组中存储多个值。

| properties | 说明 |
| --- | --- |
| `value` | 属性的值 |

边缘包含以下信息，以方便导航到图形的其他部件。

| properties | 说明 |
| --- | --- |
| `id` | 边缘的 ID。 必须唯一（在适用的情况下，可与 `_partition` 的值合并） |
| `label` | 边缘的标签。 此属性是可选的，用于描述关系类型。 |
| `inV` | 此属性包含边缘的一系列顶点。 存储顶点的相邻信息可以快速执行遍历。 顶点根据其标签分组。 |
| `properties` | 与边缘关联的用户定义属性包。 |

## <a name="gremlin-steps"></a>Gremlin 的步骤

现在，让我们了解 Azure Cosmos DB 支持的 Gremlin 步骤。 有关 Gremlin 的完整参考信息，请参阅 [TinkerPop 参考](https://tinkerpop.apache.org/docs/3.3.2/reference)。

| 步骤 | 说明 | TinkerPop 3.2 文档 |
| --- | --- | --- |
| `addE` | 在两个顶点之间添加边缘 | [addE 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | 将顶点添加到图形 | [addV 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | 确保所有遍历都返回值 | [and 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | 用于向步骤的输出分配变量的步骤调制器 | [as 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | 与 `group` 和 `order` 配合使用的步骤调制器 | [by 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | 返回第一个返回结果的遍历 | [coalesce 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | 返回常量值。 与 `coalesce` 配合使用| [constant 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | 从遍历返回计数 | [count 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | 返回已删除重复内容的值 | [dedup 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | 丢弃值（顶点/边缘） | [drop 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | 创建执行的 Gremlin 步骤生成的所有操作的说明 | [executionProfile 步骤](graph-execution-profile.md) |
| `fold` | 充当用于计算结果聚合值的屏障| [fold 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | 根据指定的标签将值分组| [group 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | 用于筛选属性、顶点和边缘。 支持 `hasLabel`、`hasId`、`hasNot` 和 `has` 变体。 | [has 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | 将值注入流中| [inject 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | 用于通过布尔表达式执行筛选器 | [is 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | 用于限制遍历中的项数| [limit 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | 本地包装遍历的某个部分，类似于子查询 | [local 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | 用于生成筛选器的求反结果 | [not 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | 如果生成了某个结果，则返回指定遍历的结果，否则返回调用元素 | [optional 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | 确保至少有一个遍历会返回值 | [or 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | 按指定的排序顺序返回结果 | [order 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | 返回遍历的完整路径 | [path 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | 将属性投影为映射 | [project 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | 返回指定标签的属性 | [properties 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | 根据指定的值范围进行筛选| [range 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | 将步骤重复指定的次数。 用于循环 | [repeat 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | 用于对遍历返回的结果采样 | [sample 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | 用于投影遍历返回的结果 |  [select 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | 用于遍历返回的非阻塞聚合 | [store 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的开头进行匹配 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的结尾进行匹配 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的内容进行匹配 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不以给定字符串开头的属性 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不以给定字符串结尾的属性 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不包含给定字符串的属性 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | 将顶点中的路径聚合到树中 | [tree 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | 将迭代器作为步骤展开| [unfold 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | 合并多个遍历返回的结果| [union 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | 包括顶点与边缘之间的遍历所需的步骤：`V`、`E`、`out`、`in`、`both`、`outE`、`inE`、`bothE`、`outV`、`inV`、`bothV` 和 `otherV` | [vertex 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | 用于筛选遍历返回的结果。 支持 `eq`、`neq`、`lt`、`lte`、`gt`、`gte` 和 `between` 运算符  | [where 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Azure Cosmos DB 提供的写入优化引擎默认支持自动对顶点和边缘中的所有属性编制索引。 因此，使用筛选器、范围查询、排序或聚合对任何属性执行的查询将从索引处理，并可有效完成。 有关 Azure Cosmos DB 中索引编制的工作原理的详细信息，请参阅有关[架构不可知的索引编制](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)的文章。

## <a name="behavior-differences"></a>行为差异

* Azure Cosmos DB Graph 引擎运行 ***广度优先*** 遍历，而 TinkerPop Gremlin 则深度优先。 这种行为在像 Cosmos DB 这样的水平可缩放系统中可实现更好的性能。

## <a name="unsupported-features"></a>不支持的功能

* ***[Gremlin 字节码](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是与编程语言无关的图遍历规范。 Cosmos DB Graph 尚不支持它。 请使用 `GremlinClient.SubmitAsync()` 并以文本字符串的形式传递遍历。

* ***`property(set, 'xyz', 1)`*** 目前不支持集基数。 请改用 `property(list, 'xyz', 1)`。 若要了解详细信息，请参阅 [TinkerPop 的顶点属性](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)。

* `match()` 步骤当前不可用。 此步骤提供声明性查询功能。

* 不支持顶点或边上的 ***对象作为属性***。 属性只能是基元类型或数组。

* 不支持 ***按数组属性排序*** `order().by(<array property>)`。 只支持按基元类型排序。

* 不支持 ***非基元 JSON 类型***。 使用 `string`、`number` 或 `true`/`false` 类型。 不支持 `null` 值。 

* 目前不支持 ***GraphSONv3*** 序列化程序。 在连接配置中使用 `GraphSONv2` Serializer、Reader 和 Writer 类。 Azure Cosmos DB Gremlin API 返回的结果的格式与 GraphSON 格式不同。 

* 目前不支持 **Lambda 表达式和函数**。 这包括 `.map{<expression>}`、`.by{<expression>}` 和 `.filter{<expression>}` 函数。 若要了解详细信息，并了解如何使用 Gremlin 步骤重写这些函数，请参阅[关于 Lambda 的说明](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)。

* 由于系统具有分布式特性，因此 ***事务*** 不受支持。  在 Gremlin 帐户上配置适当的一致性模型以“读取自己的写入”，并使用乐观并发解决冲突的写入。

## <a name="known-limitations"></a>已知的限制

* **使用中间遍历 `.V()` 步骤的 Gremlin 查询的索引利用**：目前，只有遍历的第一次 `.V()` 调用将使用索引来解析附加到它的任何筛选器或谓词。 后续调用将不会访问索引，因为这可能会增加查询的延迟和成本。
    
假设使用默认索引，以 `.V()` 步骤开始的典型读取 Gremlin 查询将在其附加的筛选步骤中使用参数，例如 `.has()` 或 `.where()`，以优化查询的成本和性能。 例如：

```java
g.V().has('category', 'A')
```

但是，当 Gremlin 查询中包含多个 `.V()` 步骤时，查询的数据解析可能达不到最优效果。 以下列查询为例：

```java
g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
```

此查询将根据名为 `category` 的属性返回两组顶点。 在这种情况下，只有第一次调用 `g.V().has('category', 'A')` 将使用索引根据其属性值解析顶点。

对于此查询，一个解决方法是使用 `.map()` 和 `union()` 等子遍历步骤。 下面来举例说明：

```java
// Query workaround using .map()
g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

// Query workaround using .union()
g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
```

可以使用 [Gremlin `executionProfile()` 步骤](graph-execution-profile.md)查看查询的性能。

## <a name="next-steps"></a>后续步骤

* 开始[使用我们的 SDK](create-graph-dotnet.md) 构建图形应用程序 
* 详细了解 Azure Cosmos DB 中的[图形支持](graph-introduction.md)
