---
title: Azure Cosmos DB 索引指标
description: 了解如何获取和解释 Azure Cosmos DB 中的索引指标
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: tisande
ms.openlocfilehash: 13d667327fde6f55072f40dd6d1f9b7eb07d1214
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615194"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引指标
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 提供了用来显示已利用的索引路径和建议的索引路径的索引指标。 可以使用索引指标优化查询性能（尤其是在不确定如何修改[索引策略](../index-policy.md)时）。

> [!NOTE]
> 索引指标仅在 .NET SDK（版本 3.21.0 或更高版本）和 Java SDK（版本 4.19.0 或更高版本）中受支持

## <a name="enable-indexing-metrics"></a>启用索引指标

可以通过将 `PopulateIndexMetrics` 属性设置为 `true` 来启用查询的索引指标。 如果未指定，`PopulateIndexMetrics` 默认为 `false`。 我们仅建议启用索引指标来排查查询性能问题。 只要查询和索引策略保持不变，索引指标就不太可能更改。 相反，我们建议使用诊断日志监视查询 RU 费用和延迟，以识别耗费大量资源的查询。

### <a name="net-sdk-example"></a>.NET SDK 示例

```csharp
    string sqlQuery = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    List<Result> results = new List<Result>(); //Individual Benchmark results

    QueryDefinition query = new QueryDefinition(sqlQuery);

    FeedIterator<Item> resultSetIterator = exampleApp.container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    double requestCharge = 0;
    tring indexMetrics = "";

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
            response = await resultSetIterator.ReadNextAsync();
            requestCharge = requestCharge + response.RequestCharge;

            if (indexMetrics != "")
                {
                    indexMetrics = response.IndexMetrics;
                }
        }

    Console.WriteLine(response.IndexMetrics);
    Console.WriteLine($"RU charge: " + response.RequestCharge);
```

### <a name="example-output"></a>示例输出

在此示例查询中，我们观察已利用的路径 `/Item/?` 和 `/Price/?` 以及可能的组合索引 `(/Item ASC, /Price ASC)`。

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /Item/?
    Index Impact Score: High
    ---
    Index Spec: /Price/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /Item ASC, /Price ASC
    Index Impact Score: High
    ---
```

## <a name="utilized-indexed-paths"></a>已利用的索引路径

已利用的单一索引和已利用的组合索引分别显示查询使用的包含路径和组合索引。 查询可以使用多个索引路径，以及包含路径和组合索引的组合。 如果索引路径未列为已利用，则删除索引路径不会影响查询的性能。

将已利用的索引路径的列表视为查询使用了这些路径的证据。 如果你不确定新的索引路径是否会提高查询性能，应尝试添加新的索引路径，并检查查询是否使用它们。

## <a name="potential-indexed-paths"></a>可能的索引路径

可能的单一索引和已利用的组合索引分别显示查询可能利用的包含路径和组合索引（如果已添加）。 如果你看到可能的索引路径，应考虑将它们添加到索引策略，并观察它们是否会提高查询性能。

将可能的索引路径的列表视为建议，而不是查询将使用特定索引路径的最终证据。 可能的索引路径不是查询可以使用的索引路径的详尽列表。 此外，某些可能的索引路径可能不会影响查询性能。 [添加建议的索引路径](how-to-manage-indexing-policy.md)并确认它们会提高查询性能。

> [!NOTE]
> 你对指标的索引有何反馈？ 我们想听一听！ 欢迎直接与 Azure Cosmos DB 工程团队分享反馈：cosmosdbindexing@microsoft.com

## <a name="index-impact-score"></a>索引影响分数

索引影响分数是基于查询形状的索引路径对查询性能产生重大影响的可能性。 换句话说，索引影响分数是在没有该特定索引路径的情况下，查询 RU 费用就会高得多的可能性。 

有两个可能的索引影响分数：“高”和“低”。 如果有多个可能的索引路径，我们建议重点关注具有“高”影响分数的索引路径。

索引影响分数中使用的唯一条件是查询形状。 例如，在以下查询中，会为索引路径 `/name/?` 分配“高”索引影响分数：

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

实际影响取决于数据的性质。 如果只有几个项与 `/name` 筛选器匹配，则索引路径将显著提高查询 RU 费用。 但是，如果大多数项最终与 `/name` 筛选器匹配，则索引路径最终可能不会提高查询性能。 不管在哪种情况下，都会为索引路径 `/name/?` 分配“高”索引影响分数，因为根据查询形状，索引路径很有可能会提高查询性能。

## <a name="additional-examples"></a>其他示例

### <a name="example-query"></a>示例查询

```sql
SELECT c.id 
FROM c 
WHERE c.name = 'Tim' AND c.age > 15 AND c.town = 'Redmond' AND c.timestamp > 2349230183
```

### <a name="index-metrics"></a>索引指标

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /name/?
    Index Impact Score: High
    ---
    Index Spec: /age/?
    Index Impact Score: High
    ---
    Index Spec: /town/?
    Index Impact Score: High
    ---
    Index Spec: /timestamp/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /name ASC, /town ASC, /age ASC
    Index Impact Score: High
    ---
    Index Spec: /name ASC, /town ASC, /timestamp ASC
    Index Impact Score: High
    ---
```
这些索引指标显示查询使用了索引路径 `/name/?`、`/age/?`、`/town/?` 和 `/timestamp/?`。 索引指标还表明，添加组合索引（`/name` ASC、`(/town ASC, /age ASC)` 和 `(/name ASC, /town ASC, /timestamp ASC)`）很有可能会进一步提高性能。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引概述](../index-overview.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
