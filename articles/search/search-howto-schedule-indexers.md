---
title: 计划索引器执行
titleSuffix: Azure Cognitive Search
description: 计划 Azure 认知搜索索引器，以定期或在特定时间为内容编制索引。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097970"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>如何计划 Azure 认知搜索中的索引器

通常，在创建索引器后，该索引器会紧接着运行一次。 之后，可以根据需要通过 Azure 门户、[运行索引器 (REST)](/rest/api/searchservice/run-indexer) 或 Azure SDK 再次运行索引器。 另外，还可以将索引器配置为按计划运行。 适合配置按计划运行索引器的一些情况包括：

* 源数据会随时间而变化，需要搜索索引器来自动处理差别。

* 源数据非常大，需要在随时间推移展开索引器处理。 对于常规数据源，索引器作业的运行时间最长为 24 小时；对于处理技能集的索引器，运行时间最长为 2 小时。 如果无法在最长间隔内完成编制索引，则可以配置一项计划，每 2 小时运行一次。 索引器可以根据内部高位线标记的上次结束的索引位置，自动从停用位置开始。 按计划每 2 小时循环运行索引器，可以处理非常大的数据集（数百万个文档），超出允许的单个作业间隔的处理能力。 有关对大量数据编制索引的详细信息，请参阅[如何在 Azure 认知搜索中为大型数据集编制索引](search-howto-large-index.md)。

* 搜索索引根据多个数据源填充，需要索引器在不同时间运行，以减少冲突。

计划看上去可能如下所示：从 1 月 1 日开始，每 50 分钟运行一次。

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> 计划程序是 Azure 认知搜索的内置功能。 不支持外部计划程序。

## <a name="schedule-property"></a>计划属性

计划是索引器定义的一部分。 如果忽略计划属性，则索引器仅在创建后立即运行一次。 如果添加计划属性，则需指定两个部分。

| 属性 | 说明 |
|----------|-------------|
|**时间间隔** | （必需）连续两次执行索引器间隔的时间量。 允许的最短时间间隔为 5 分钟，最长为 1440 分钟（24 小时）。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 <br/><br/>示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。|
| **开始时间(UTC)** | （可选）指示何时应开始计划的执行。 如果忽略，则使用当前 UTC 时间。 此时间可以是过去的时间，在此情况下，计划的第一次执行的运行方式如同索引器在原始 **startTime** 之后连续运行。<br/><br/>示例：`2021-01-01T00:00:00Z` 从 1 月 1 日午夜开始，`2021-01-05T22:28:00Z` 从 1 月 5 日下午 10:28 开始。|

## <a name="scheduling-behavior"></a>计划行为

一次只能运行一个索引器执行。 如果在计划索引器的下一次执行时该索引器已运行，该执行将推迟到下一个计划时间。

让我们考虑更具体的示例。 假设我们要使用每小时 **间隔** 和 **开始时间** 2019 年 6 月 1 日上午 8:00 (UTC) 来配置索引器计划。 下面是索引器运行时间超过一小时时可能出现的情况：

* 第一次索引器执行的开始时间为 2019 年 6 月 1 日上午 8:00 (UTC) 或近似时间。 假设此执行需要 20 分钟（或小于 1 小时的任何时间）。
* 第二次索引器执行的开始时间为 2019 年 6 月 1 日上午 9:00 (UTC) 或近似时间。 假设此执行耗时 70 分钟（超过 1 小时），并且在上午 10:10 (UTC) 之前无法完成。
* 第三次执行的计划开始时间为上午 10:00 (UTC)，但此时上一次执行仍在运行。 那么，将会跳过此计划的执行。 索引器的下一次执行在上午 11:00 (UTC) 之前不会开始。

> [!NOTE]
> 如果已为索引器设置特定计划，但每次都在同一文档上反复失败，则索引器的运行频率将开始降低（取决于最大时间间隔，至少每 24 小时运行一次），直到可以重新成功取得进展。 如果认为基本问题已修复，则可以手动运行索引器，如果索引成功，索引器将恢复为其常规计划。

## <a name="schedule-using-rest"></a>使用 REST 进行计划

在创建或更新索引器时指定计划属性。

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>使用 .NET 进行计划

以下 C# 示例将使用预定义数据源和索引创建一个 Azure SQL 数据库索引器，并将其计划设置为从现在开始每天运行一次：

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

在使用 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) 创建或更新索引器时，可使用 [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) 类定义计划。 IndexingSchedule 构造函数需要一个使用 TimeSpan 对象指定的 Interval 参数。 如前所述，允许的最小间隔值为 5 分钟，最大间隔值为 24 小时。 指定为 DateTimeOffset 对象的第二个 StartTime 参数是可选的。

## <a name="next-steps"></a>后续步骤

对于按计划运行的索引器，可以通过从搜索服务检索状态来监视操作，也可以通过启用诊断日志记录来获取详细信息。

* [监视搜索索引器状态](search-howto-monitor-indexers.md)
* [收集和分析日志数据](search-monitor-logs.md)