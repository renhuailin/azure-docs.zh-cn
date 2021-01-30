---
title: 计划索引器执行
titleSuffix: Azure Cognitive Search
description: 计划 Azure 认知搜索索引器，以定期或在特定时间为内容编制索引。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: dfd8526a035d4eef4d07539e541e37c88023b500
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063207"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>如何计划 Azure 认知搜索中的索引器

通常，在创建索引器后，该索引器会紧接着运行一次。 之后，你可以使用 Azure 门户、 [运行索引器 (REST) ](/rest/api/searchservice/run-indexer)或 Azure SDK，按需再次运行该程序。 另外，还可以将索引器配置为按计划运行。 索引器计划有用的一些情况包括：

* 源数据将随着时间的推移而更改，并且你希望搜索索引器自动处理增量。
* 源数据非常大，需要在一段时间内分散索引器处理。 有关对大量数据编制索引的详细信息，请参阅[如何在 Azure 认知搜索中为大型数据集编制索引](search-howto-large-index.md)。
* 搜索索引将从多个数据源进行填充，并且您希望索引器在不同时间运行以减少冲突。

从外观上看，计划可能如下所示：从1月1日开始，每50分钟运行一次。

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> 计划程序是 Azure 认知搜索的内置功能。 不支持外部计划程序。

## <a name="schedule-property"></a>Schedule 属性

计划是索引器定义的一部分。 如果省略 " **schedule** " 属性，则索引器在创建后将立即立即运行一次。 如果添加 " **计划** " 属性，将指定两个部分。

| properties | 说明 |
|----------|-------------|
|**时间间隔** |  (必需) 两次连续索引器执行的开始时间。 允许的最小间隔为5分钟，最长为1440分钟 (24 小时) 。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 <br/><br/>示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。|
| **开始时间(UTC)** |  (可选) 指示何时应开始执行计划的执行。 如果省略，则使用当前的 UTC 时间。 此时间可以是过去的时间，在此情况下，计划的第一次执行的运行方式如同索引器在原始 **startTime** 之后连续运行。<br/><br/>示例：从 `2021-01-01T00:00:00Z` 10:28 年1月1日午夜开始，从 `2021-01-05T22:28:00Z` 下午开始。 1月5日。|

## <a name="scheduling-behavior"></a>计划行为

一次只能运行一个索引器执行。 如果在计划索引器的下一次执行时该索引器已运行，该执行将推迟到下一个计划时间。

让我们考虑更具体的示例。 假设我们要使用每小时 **间隔** 和 **开始时间** 2019 年 6 月 1 日上午 8:00 (UTC) 来配置索引器计划。 下面是索引器运行时间超过一小时时可能出现的情况：

* 第一次索引器执行的开始时间为 2019 年 6 月 1 日上午 8:00 (UTC) 或近似时间。 假设此执行需要 20 分钟（或小于 1 小时的任何时间）。
* 第二次索引器执行的开始时间为 2019 年 6 月 1 日上午 9:00 (UTC) 或近似时间。 假设此执行耗时 70 分钟（超过 1 小时），并且在上午 10:10 (UTC) 之前无法完成。
* 第三次执行的计划开始时间为上午 10:00 (UTC)，但此时上一次执行仍在运行。 那么，将会跳过此计划的执行。 索引器的下一次执行在上午 11:00 (UTC) 之前不会开始。

> [!NOTE]
> 如果索引器设置为某个计划，但每次都在同一文档上重复失败，则索引器将在每24小时的最大时间间隔内开始运行， (至少每24小时) 一次，直到它成功完成进度。 如果您认为您已修复了任何基础问题，则可以手动运行索引器，如果索引成功，索引器将恢复为其常规计划。

## <a name="schedule-using-rest"></a>使用 REST 计划

创建或更新索引器时，请指定 **计划** 属性。

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

## <a name="schedule-using-net"></a>使用 .NET 计划

下面的 c # 示例使用预定义的数据源和索引创建一个 Azure SQL 数据库索引器，并将其计划设置为每天运行一次，立即开始：

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

使用[SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)创建或更新索引器时，使用[IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule)类定义计划。 IndexingSchedule 构造函数需要一个使用 TimeSpan 对象指定的 Interval 参数。 如前所述，允许的最小间隔值为5分钟，最大值为24小时。 指定为 DateTimeOffset 对象的第二个 StartTime 参数是可选的。

## <a name="next-steps"></a>后续步骤

对于按计划运行的索引器，你可以通过从搜索服务检索状态来监视操作，或者通过启用诊断日志记录来获取详细信息。

* [监视器搜索索引器状态](search-howto-monitor-indexers.md)
* [收集和分析日志数据](search-monitor-logs.md)