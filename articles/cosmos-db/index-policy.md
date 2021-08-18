---
title: Azure Cosmos DB 索引编制策略
description: 了解如何配置和更改默认索引策略，以便自动编制索引并提高 Azure Cosmos DB 的性能。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/13/2021
ms.author: tisande
ms.openlocfilehash: c2f380e92693e6ef2d16e74001b2d22d76e6d941
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195365"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引策略
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

在 Azure Cosmos DB 中，每个容器都有一个确定了如何为容器项编制索引的索引策略。 新创建的容器的默认索引策略为每个项的每个属性建立索引，并对任何字符串或数字强制使用范围索引。 这样，无需提前考虑索引编制和索引管理，就能获得良好的查询性能。

在某些情况下，你可能想要替代此自动行为，以便更好地满足自己的要求。 可以通过设置容器索引策略的索引模式来自定义该策略，并可以包含或排除属性路径。 

> [!NOTE]
> 本文所述的更新索引策略的方法仅适用于 Azure Cosmos DB 的 SQL (Core) API。 请在[适用于 MongoDB 的 Azure Cosmos DB 的 API](mongodb/mongodb-indexing.md) 中了解有关索引的信息

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支持两种索引模式：

- **一致**：创建、更新或删除项时，索引将以同步方式更新。 这意味着，读取查询的一致性是[为帐户配置的一致性](consistency-levels.md)。
- **无**：针对该容器禁用索引。 将容器用作单纯的键-值存储时，通常会使用此设置，在此情况下无需使用辅助索引。 它还可用于改善批量操作的性能。 批量操作完成后，可将索引模式设置为“一致”，然后使用 [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) 进行监视，直到完成。

> [!NOTE]
> Azure Cosmos DB 还支持延迟索引模式。 当引擎未执行任何其他工作时，延迟索引将以低得多的优先级对索引执行更新。 这可能导致查询结果 **不一致或不完整**。 如果计划查询 Cosmos 容器，则不应选择“延迟索引”。 新容器不能选择“延迟索引”。 可以通过联系 cosmoslazyindexing@microsoft.com来请求豁免（在不支持延迟索引的[无服务器](serverless.md)模式下使用 Azure Cosmos 帐户的情况除外）。

默认情况下，索引策略设置为 `automatic`。 为此，可将索引策略中的 `automatic` 属性设置为 `true`。 将此属性设置为 `true` 可让 Azure CosmosDB 在写入文档时自动为文档编制索引。

## <a name="index-size"></a><a id="index-size"></a>索引大小

在 Azure Cosmos DB 中，所用存储空间总量是指数据大小和索引大小的总和。 下面是索引大小的一些特性：

* 索引大小取决于索引策略。 如果所有属性都已编制索引，则索引大小可能会大于数据大小。
* 当删除数据时，索引将近乎连续地进行压缩。 但是，对于较小的数据删除，你可能不会立即观察到索引大小的减小。
* 物理分区拆分时，索引大小可能会暂时增大。 索引空间将在分区拆分完成后释放。

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>包含和排除属性路径

自定义索引策略可以指定要在索引编制中显式包含或排除的属性路径。 通过优化已编制索引的路径数，可以显著减少写入操作的延迟和 RU 费用。 这些路径是遵循[索引概述部分所述的方法](index-overview.md#from-trees-to-property-paths)定义的，补充要求如下：

- 指向标量值（字符串或数字）的路径以 `/?` 结尾
- 数组中的元素通过 `/[]` 表示法（而不是 `/0`、`/1` 等）统一寻址
- 可以使用 `/*` 通配符来匹配节点下的任意元素

沿用前面的示例：

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters` 的 `employees` 路径是 `/headquarters/employees/?`

- `locations` 的 `country` 路径是 `/locations/[]/country/?`

- `headquarters` 下的任何内容的路径是 `/headquarters/*`

例如，可以包含 `/headquarters/employees/?` 路径。 此路径确保为 employees 属性编制索引，但不会为此属性中的其他嵌套 JSON 编制索引。

## <a name="includeexclude-strategy"></a>包含/排除策略

任何索引策略必须包含根路径 `/*` 作为包含或排除的路径。

- 包含根路径可以选择性地排除不需要编制索引的路径。 这是建议的方法，因为这样可以让 Azure Cosmos DB 主动为可以添加到模型的任何新属性编制索引。
- 排除根路径可以选择性地包含需要编制索引的路径。

- 对于包含常规字符（包括字母数字字符和下划线 _）的路径，无需在双引号中转义路径字符串（例如 "/path/?"）。 对于包含其他特殊字符的路径，需要在双引号中转义路径字符串（例如 "/\"path-abc\"/?"）。 如果预期路径中会出现特殊字符，出于安全考虑，可以转义每个路径。 在功能上，转义每个路径与仅转义包含特殊字符的路径没有任何差别。

- 默认情况下，系统属性 `_etag` 被排除在索引之外，除非将 etag 添加到索引所包含的路径中。

- 如果将索引模式设为“一致”，则会自动为系统属性 `id` 和 `_ts` 编制索引。

包含和排除路径时，可能会遇到以下属性：

- `kind` 可以是 `range` 或 `hash`。 哈希索引支持仅限于相等筛选器。 范围索引功能提供了哈希索引的所有功能，以及高效排序、范围筛选器和系统函数。 我们始终建议你使用范围索引。

- `precision` 在包含的路径的索引级别定义的一个数字。 `-1` 值表示最大精度。 我们建议始终将此值设置为 `-1`。

- `dataType` 可以是 `String` 或 `Number`。 这表示要编制索引的 JSON 属性的类型。

不再需要设置这些属性。 如果未指定，这些属性将使用以下默认值：

| **属性名称**     | **默认值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

有关包含和排除路径的索引策略示例，请参阅[此部分](how-to-manage-indexing-policy.md#indexing-policy-examples)。

## <a name="includeexclude-precedence"></a>包含/排除优先级

如果包含路径和排除路径有冲突，则以更精确的路径优先。

下面是一个示例：

**包含的路径**：`/food/ingredients/nutrition/*`

**排除的路径**：`/food/ingredients/*`

在这种情况下，包含路径优先于排除路径，因为它更精确。 根据这些路径，位于或嵌套在 `food/ingredients` 路径中的任何数据都将从索引中排除。 异常是包含的路径 `/food/ingredients/nutrition/*` 中的数据，该路径将被索引。

下面是有关 Azure Cosmos DB 中包含和排除路径优先级的一些规则：

- 较深的路径比较窄的路径更精确。 例如：`/a/b/?` 比 `/a/?` 更精确。

- `/?` 比 `/*` 更精确。 例如，`/a/?` 比 `/a/*` 更精确，因此 `/a/?` 优先。

- 路径 `/*` 必须是包含路径或排除路径。

## <a name="spatial-indexes"></a>空间索引

在索引策略中定义空间路径时，应定义要将哪个索引 ```type``` 应用到该路径。 空间索引的可能类型包括：

* 点

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB 默认不会创建任何空间索引。 若要使用空间 SQL 内置函数，应该对所需的属性创建空间索引。 有关添加空间索引的索引策略示例，请参阅[此部分](sql-query-geospatial-index.md)。

## <a name="composite-indexes"></a>组合索引

包含 `ORDER BY` 子句（该子句包含两个或更多个属性）的查询需要一个组合索引。 还可以定义一个组合索引来改善许多相等性和范围查询的性能。 默认情况下不会定义组合索引，因此，应根据需要[添加组合索引](how-to-manage-indexing-policy.md#composite-index)。

与指定包含或排除路径不同，不能创建包含 `/*` 通配符的路径。 每个复合路径的末尾都有一个不需要指定的隐式 `/?`。 复合路径会导致一个标量值，这是复合索引中包含的唯一值。

定义组合索引时，请指定：

- 两个或更多个属性路径。 属性路径的定义顺序非常重要。

- 顺序（升序或降序）。

> [!NOTE]
> 添加组合索引时，该查询将利用现有范围索引，直到新的组合索引添加已完成。 因此，在添加组合索引时，可能不会立即观察到性能改进。 可以[使用某个 SDK](how-to-manage-indexing-policy.md) 跟踪索引转换的进度。

### <a name="order-by-queries-on-multiple-properties"></a>针对多个属性的 ORDER BY 查询：

对包含 `ORDER BY` 子句（该子句包含两个或更多个属性）的查询使用组合索引时，请注意以下事项：

- 如果组合索引路径与 `ORDER BY` 子句中的属性顺序不匹配，则组合索引无法支持查询。

- 组合索引路径的顺序（升序或降序）还应与 `ORDER BY` 子句中的 `order` 相匹配。

- 组合索引还支持在所有路径中使用反向顺序的 `ORDER BY` 子句。

考虑以下示例，其中针对属性 name、age 和 _ts 定义了组合索引：

| **组合索引**     | **示例 `ORDER BY` 查询**      | **是否受组合索引的支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

应该自定义索引策略，以便可为所有必要的 `ORDER BY` 查询提供服务。

### <a name="queries-with-filters-on-multiple-properties"></a>包含针对多个属性的筛选器的查询

如果查询包含针对两个或更多个属性的筛选器，为这些属性创建组合索引可能会有帮助。

例如，请考虑以下同时包含相等性筛选器和范围筛选器的查询：

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

如果能够针对 `(name ASC, age ASC)` 利用组合索引，则此查询将更加高效：花费的时间更少，且消耗的 RU 更少。

具有多个范围筛选器的查询也可使用组合索引进行优化。 不过，每个单独的组合索引只能优化一个范围筛选器。 范围筛选器包括 `>`、`<`、`<=`、`>=` 和 `!=`。 范围筛选器应在组合索引中最后定义。

考虑以下查询，其中包含一个相等性筛选器和两个范围筛选器：

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

如果针对 `(name ASC, age ASC)` 和 `(name ASC, _ts ASC)` 使用组合索引，此查询会更高效。 但是，该查询不会针对 `(age ASC, name ASC)` 利用组合索引，因为相等性筛选器的属性必须在组合索引中首先定义。 需要两个单独的组合索引，而不是针对 `(name ASC, age ASC, _ts ASC)` 的单个组合索引，因为每个组合索引只能优化一个范围筛选器。

为包含针对多个属性的筛选器的查询创建组合索引时，请注意以下事项

- 筛选表达式可以使用多个复合索引。
- 查询筛选器中的属性应与组合索引中的属性相匹配。 如果某个属性在组合索引中，但未作为筛选器包含在查询中，则查询不会利用该组合索引。
- 如果查询包含筛选器中的其他属性，但这些属性未在组合索引中定义，则会结合使用组合索引和范围索引来评估查询。 这样，所需的 RU 数就比专门使用范围索引更少。
- 如果某个属性包含范围筛选器（`>`、`<`、`<=`、`>=` 或 `!=`），则此属性应在组合索引中最后定义。 如果查询有多个范围筛选器，则可能会受益于多个组合索引。
- 创建组合索引来优化包含多个筛选器的查询时，组合索引的 `ORDER` 不会对结果造成任何影响。 此属性是可选的。

考虑以下示例，其中针对属性 name、age 和 timestamp 定义了组合索引：

| **组合索引**     | **示例查询**      | **是否受组合索引的支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>使用筛选器和 ORDER BY 的查询

如果查询针对一个或多个属性进行筛选，并在 ORDER BY 子句中包含不同的属性，则将筛选器中的属性添加到 `ORDER BY` 子句可能会有帮助。

例如，通过将筛选器中的属性添加到 `ORDER BY` 子句，可以重写以下查询来利用组合索引：

使用范围索引的查询：

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

使用组合索引的查询：

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

相同的查询优化可以针对带有筛选器的任何 `ORDER BY` 查询进行通用化，请记住，单个组合索引最多只能支持一个范围筛选器。

使用范围索引的查询：

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

使用组合索引的查询：

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

此外，还可以使用组合索引来优化具有系统函数和 ORDER BY 的查询：

使用范围索引的查询：

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

使用组合索引的查询：

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

创建组合索引以优化具有筛选器和 `ORDER BY` 子句的查询时，请注意以下事项：

* 对于包含针对一个属性的筛选器并包含一个使用不同属性的独立 `ORDER BY` 子句的查询，如果未为它定义组合索引，该查询仍会成功。 但是，使用组合索引可以减少查询的 RU 开销，尤其是 `ORDER BY` 子句中的属性具有较高的基数时。
* 如果查询针对属性进行筛选，应该首先将这些属性包含在 `ORDER BY` 子句中。
* 如果查询针对多个属性进行筛选，则相等性筛选器必须是 `ORDER BY` 子句中的第一个属性。
* 如果查询针对多个属性进行筛选，则每个组合索引最多可以使用一个范围筛选器或系统函数。 在范围筛选器或系统函数中使用的属性应在组合索引中最后定义。
* 有关为包含多个属性的 `ORDER BY` 查询，以及为包含针对多个属性的筛选器的查询创建组合查询的所有注意事项仍然适用。


| **组合索引**                      | **示例 `ORDER BY` 查询**                                  | **是否受组合索引的支持？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

### <a name="queries-with-a-filter-and-an-aggregate"></a>使用筛选器和聚合的查询 

如果查询针对一个或多个属性进行筛选并且具有聚合系统函数，则为筛选器和聚合系统函数中的属性创建组合索引可能会很有用。 此优化适用于 [SUM](sql-query-aggregate-sum.md) 和 [AVG](sql-query-aggregate-avg.md) 系统函数。

创建组合索引以优化具有筛选器和聚合系统函数的查询时，请注意以下事项。

* 在运行包含聚合的查询时，组合索引是可选的。 但是，使用组合索引通常可以显著降低查询的 RU 成本。
* 如果查询针对多个属性进行筛选，则相等性筛选器必须是组合索引中的第一个属性。
* 每个组合索引最多可以有一个范围筛选器，并且它必须针对聚合系统函数中的属性。
* 聚合系统函数中的属性应在组合索引中最后定义。
* `order`（`ASC` 或 `DESC`）并不重要。

| **组合索引**                      | **示例查询**                                  | **是否受组合索引的支持？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name > "John"``` | `No` |
| ```(name ASC, age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age = 25``` | `Yes` |
| ```(age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age > 25``` | `No` |

## <a name="modifying-the-indexing-policy"></a><a id=index-transformation></a>修改索引策略

随时可以[使用 Azure 门户或某个支持的 SDK](how-to-manage-indexing-policy.md) 更新容器的索引策略。 更新索引策略会触发从旧索引向新索引转换，这个转换是在线就地执行的（因而在该操作期间不会消耗更多存储空间）。 旧的索引策略会高效地向新策略转换，而不会影响写入可用性、读取可用性或针对容器预配的吞吐量。 索引转换是一个异步操作，完成该操作所需的时间取决于预配的吞吐量、项的数目及其大小。

> [!IMPORTANT]
> 索引转换是一种使用[请求单位](request-units.md)的操作。 如果使用[无服务器](serverless.md)容器，索引转换使用的请求单位目前不会计费。 在无服务器模式正式提供之后，这些请求单位将会计费。

> [!NOTE]
> 可以在 Azure 门户中或[使用其中一个 SDK](how-to-manage-indexing-policy.md) 来跟踪索引转换的进度。

在任何索引转换过程中，对写入可用性都没有影响。 索引转换使用预配的 RU，但优先级低于 CRUD 操作或查询。

添加新索引路径时，对读取可用性没有影响。 索引转换完成之后，查询将只利用新索引路径。 换句话说，添加新索引路径时，利用该索引路径的查询在索引转换之前和期间的性能相同。 索引转换完成后，查询引擎将开始使用新的索引路径。

删除索引路径时，应将所有更改分组到一个索引策略转换中。 如果删除多个索引，并且是在一次索引策略更改中执行此操作，则查询引擎会在整个索引转换中提供一致且完整的结果。 但是，如果通过多个索引策略更改来删除索引，则在所有索引转换完成之前，查询引擎将无法提供一致或完整的结果。 大多数开发人员都不会在删除索引之后立即尝试运行利用这些索引的查询，所以，这种情况实际上不太可能发生。

删除索引路径时，查询引擎将立即停止使用该索引路径，改为执行完全扫描。

> [!NOTE]
> 如果可能，应始终尝试将多个索引更改组合成一次索引策略修改

## <a name="indexing-policies-and-ttl"></a>索引策略和 TTL

使用[生存时间 (TTL) 功能](time-to-live.md)需要编制索引。 这意味着：

- 无法在索引模式设置为“`none`”的容器中激活 TTL。
- 无法在已激活 TTL 的容器中将索引模式设置为“无”。

对于不需要为任何属性路径编制索引，但需要 TTL 的情况，可以使用索引模式设置为 `consistent`、没有包含的路径并且将 `/*` 作为唯一排除的路径的索引策略。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引概述](index-overview.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
