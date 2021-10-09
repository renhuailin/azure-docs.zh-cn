---
title: 运行或重置索引器
titleSuffix: Azure Cognitive Search
description: 重置索引器、技能或单个文档，以刷新所有或者部分索引或知识存储。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 9ba66a8eb76c2c0bdcc2dd086d3abcfc47bcba65
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678130"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>如何运行或重置索引器、技能或文档

当你首次创建[索引器](search-indexer-overview.md)、按需运行索引器或按计划设置索引器时，可能会发生索引器执行。 在初次运行后，索引器将通过内部的“高水位线”跟踪已编制索引的搜索文档。 该标记永远不会在 API 中公开，但在内部，索引器知道索引编制的停止位置，因此在下一次运行时，它可以从上次中断的位置继续。

如果你要从头开始重新处理，可以通过重置索引器来清除高水位线。 重置 API 可以在对象层次结构中按递减级别提供：

+ 整个搜索集（使用[重置索引器](#reset-indexers)）
+ 特定的文档或文档列表（使用[重置文档 - 预览版](#reset-docs)）
+ 文档中特定的技能或扩充（使用[重置技能 - 预览版](#reset-skills)）

重置 API 用于刷新缓存的内容（适用于 [AI 扩充](cognitive-search-concept-intro.md)方案），或清除高水位线并重建索引。

重置（之后再运行）可以重新处理现有文档和新文档，但不会删除搜索索引中的、在以前的运行中创建的孤立搜索文档。 有关删除操作的详细信息，请参阅[添加、更新或删除文档](/rest/api/searchservice/addupdate-or-delete-documents)。

## <a name="run-indexers"></a>运行索引器

除非你创建处于禁用状态的索引器 ("disabled": true)，否则[创建索引器](/rest/api/searchservice/create-indexer)将创建并运行索引器。 首次运行所需的时间要长一些，因为此过程还包括对象创建操作。

[运行索引器](/rest/api/searchservice/run-indexer)只检测并处理将搜索索引与数据源同步所需的内容。 Blob 存储具有内置的更改检测功能。 对于其他数据源（例如 Azure SQL 或 Cosmos DB），必须为其配置更改检测，才能让索引器只读取新行和已更新的行。

可使用以下任一方法运行索引器：

+ 在 Azure 门户中的索引器页上使用“运行”命令
+ [运行索引器 (REST)](/rest/api/searchservice/run-indexer)
+ Azure .NET SDK 中的 [RunIndexers 方法](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer)（或使用其他 SDK 中等效的 RunIndexer 方法）

索引器执行存在以下限制：

+ 对每个副本可执行的最大索引器作业数为 1。不允许并发作业。

  如果索引器执行已达到容量限制，你将收到以下通知：“无法运行索引器 '\<indexer-name\>'，错误: 另一个索引器调用当前正在进行；不允许并发调用。”

+ 如果使用技能组，则最长运行时间为 2 小时；不使用技能组时为 24 小时。 

  可以通过将索引器排入计划来扩大处理量。 免费层的运行时间限制更低。 有关完整列表，请参阅[索引器限制](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>重置索引器

重置索引器是统括一切的操作。 在搜索索引中，最初由索引器填充的任何搜索文档都将标记为有待完全处理。 找到基础源的任何新文档将作为搜索文档添加到索引中。 如果索引器配置为使用技能组和[缓存](search-howto-incremental-index.md)，则会重新运行该技能组并刷新缓存。

可以使用以下任一方法重置索引器，接着使用上述方法之一运行索引器。

+ 在 Azure 门户中的索引器页上使用“重置”命令
+ [重置索引器 (REST)](/rest/api/searchservice/reset-indexer)
+ Azure .NET SDK 中的 [ResetIndexers 方法](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer)（或使用其他 SDK 中等效的 RunIndexer 方法）

运行完成后，将清除重置标志。 对数据源运行的任何常规更改检测逻辑将在下次运行时恢复，并拾取数据集剩余部分中的所有其他新值或已更新值。

> [!NOTE]
> 重置请求确定要重新处理哪些内容（索引器、技能或文档），但不影响索引器的运行时行为。 如果索引器具有运行时参数、字段映射、缓存、批处理选项等，则当你重置再运行索引器时，这些设置都会生效。

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>重置技能（预览版）

> [!IMPORTANT] 
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，[重置技能](/rest/api/searchservice/preview-api/reset-skills)以公共预览版提供。 [预览版 REST API](/rest/api/searchservice/index-preview) 支持此功能。

对于具有技能组的索引器，可以重置特定的技能，以强制处理该技能，以及依赖于其输出的任何下游技能。 此外还会刷新[缓存的扩充](search-howto-incremental-index.md)。 重置技能会使缓存的技能结果失效，当已部署某个技能的新版本，并且你希望索引器对所有文档重新运行该技能时，此功能非常有用。 

可通过 REST `api-version=2020-06-30-Preview` 使用[重置技能](/rest/api/searchservice/preview-api/reset-skills)。

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

可按以上示例中所示指定单个技能，但如果其中任一技能需要未列出的技能（#2 到 #4）的输出，则除非缓存可以提供必要的信息，否则未列出的技能将会运行。 若要做到这一点，技能 #2 到 #4 的已缓存扩充不得依赖于 #1（列为要重置的技能）。

如果未指定任何技能，则执行整个技能组；如果已启用缓存，则还会刷新缓存。

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>重置文档（预览版）

> [!IMPORTANT] 
> [重置文档](/rest/api/searchservice/preview-api/reset-documents)目前为公共预览版，仅可通过预览版 REST API 使用。 根据[使用条款补充](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览功能按原样提供。

[重置文档 API](/rest/api/searchservice/preview-api/reset-documents) 接受文档键列表，因此你可以刷新特定的文档。 如果已指定重置参数，这些参数将成为要处理哪些内容的唯一决定因素，而不管基础数据发生了其他哪些更改。 例如，如果自上次索引器运行以来添加或更新了 20 个 Blob，但你只重置了一个文档，则只会处理这一个文档。

对于每个文档，将使用数据源中的值刷新该搜索文档中的所有字段。 无法挑选要刷新的字段。 

如果文档已通过技能组扩充并包含缓存的数据，则只会对指定的文档调用该技能组，并为已重新处理的文档更新缓存。

首次测试此 API 时，以下 API 可帮助你验证并测试行为：

+ API 版本为 `2020-06-30-Preview` 的[获取索引器状态](/rest/api/searchservice/get-indexer-status)，用于检查重置状态和执行状态。 可以在状态响应的末尾找到有关重置请求的信息。
+ API 版本为 `2020-06-30-Preview` 的[重置文档](/rest/api/searchservice/preview-api/reset-documents)，用于指定要处理的文档。
+ 用于运行索引器的[运行索引器](/rest/api/searchservice/run-indexer)（任何 API 版本）。
+ [搜索文档](/rest/api/searchservice/search-documents)，用于检查已更新的值，如果你不确定值，此 API 还会返回文档键。 如果你要限制在响应中显示的字段，请使用 `"select": "<field names>"`。

### <a name="formulate-and-send-the-reset-request"></a>构建并发送重置请求

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

请求中提供的文档键是搜索索引中的值，这些值可能不同于数据源中的相应字段。 如果你不确定键值，请[发送查询](search-query-create.md)以返回该值。可以使用 `select` 以便仅返回文档键字段。

对于以分析模式分析成多个搜索文档的 Blob（例如，如果使用了 [jsonLines 或 jsonArrays](search-howto-index-json-blobs.md)，或者 [delimitedText](search-howto-index-csv-blobs.md)），文档键是由索引器生成的，你可能不知道该键。 在这种情况下，对文档键运行查询将有助于提供正确的值。

结合不同的键多次调用 API 会将新键追加到文档键重置列表。 结合设置为 true 的 `overwrite` 参数调用 API 会覆盖要使用请求有效负载重置的文档键的当前列表：

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>检查重置状态

若要检查重置状态并查看哪些文档键已排队等候处理，请使用[获取索引器状态](/rest/api/searchservice/get-indexer-status)并指定 `api-version=06-30-2020-Preview`。 预览版 API 将返回 `currentState` 节，可以在“获取索引器状态”响应的末尾找到该节。

“重置技能”的“模式”将是 `indexingAllDocs`（因为通过 AI 扩充填充的字段所在的所有文档都可能会受影响）。

对于“重置文档”，模式设置为 `indexingResetDocs`。 索引器会将此状态保持到已处理重置文档调用中提供的所有文档键，并且在该操作正在进行时不会执行任何其他索引器作业为止。 在文档键列表中查找所有文档需要破解每个文档，以查找键并根据该键进行匹配，如果数据集较大，这可能需要一段时间。 如果 Blob 容器包含数百个 Blob，而你想要重置的文档放在最后，则索引器只有在事先检查了所有其他 Blob 之后，才能找到匹配的 Blob。

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

重新处理文档后，索引器将恢复 `indexingAllDocs` 模式，并且在下次运行时，将处理任何其他新文档或已更新的文档。

## <a name="next-steps"></a>后续步骤

重置 API 用于告知下次索引器运行的范围。 在实际处理中，你需要调用按需索引器运行，或者允许计划的作业完成工作。 运行完成后，索引器将恢复正常处理，不管是按计划的处理还是按需处理。

重置并重新运行索引器作业后，可以从搜索服务监视状态，或者通过诊断日志记录获取详细信息。

+ [索引器操作 (REST)](/rest/api/searchservice/indexer-operations)
+ [监视搜索索引器状态](search-howto-monitor-indexers.md)
+ [收集和分析日志数据](search-monitor-logs.md)
+ [安排索引器](search-howto-schedule-indexers.md)