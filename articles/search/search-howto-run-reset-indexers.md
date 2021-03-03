---
title: 运行或重置索引器
titleSuffix: Azure Cognitive Search
description: 重置索引器、技能或单个文档，以刷新所有或部分和索引或知识库。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667668"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>如何运行或重置索引器、技能或文档

当你首次创建 [索引](search-indexer-overview.md)器、按需运行索引器或按计划设置索引器时，可能会执行索引器。 初始运行后，索引器将跟踪已通过内部 "高水位线" 索引的搜索文档。 标记决不会在 API 中公开，但在内部，索引器会确定索引停止的位置，以便它可以在下一次运行时从中断位置进行选择。

如果要从头开始重新处理，可以通过重置索引器来清除高水位线。 重置 Api 在对象层次结构中的递减级别可用：

+ 整个搜索语料库 (使用 [重置索引器](#reset-indexers)) 
+  (使用 [重置文档的](#reset-docs) 特定文档或文档列表-预览) 
+  (使用 [重置技能-预览](#reset-skills)) 文档中的特定技能或扩充

重置 Api 用于刷新 (适用于 [AI 扩充](cognitive-search-concept-intro.md) 方案) 的缓存内容，或者清除高水位线并重建索引。

重置后，接下来运行，可以重新处理现有文档和新文档，但不会删除在之前运行中创建的搜索索引中的孤立搜索文档。 有关删除的详细信息，请参阅 [添加、更新或删除文档](/rest/api/searchservice/addupdate-or-delete-documents)。

## <a name="run-indexers"></a>运行索引器

[Create 索引](/rest/api/searchservice/create-indexer) 器创建并运行索引器，除非你在禁用状态中创建索引器 ( "disabled"： true) 。 首次运行所花的时间较长，因为它还覆盖了对象创建。

[运行索引器](/rest/api/searchservice/run-indexer) 将检测并仅处理将搜索索引与数据源同步所需的内容。 Blob 存储具有内置的更改检测功能。 其他数据源（如 Azure SQL 或 Cosmos DB）必须配置更改检测，然后索引器才能读取新行和更新的行。

可以使用以下任一方法运行索引器：

+ Azure 门户，使用 "索引器" 页上的 " **运行** " 命令
+ [ (REST) 运行索引器 ](/rest/api/searchservice/run-indexer)
+ Azure .NET SDK 中的[RunIndexers 方法](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) (或在另一 SDK 中使用等效的 RunIndexer 方法) 

索引器执行受以下限制的限制：

+ 每个副本的最大索引器作业数为1，无并发作业。

  如果索引器执行已处于容量中，你将收到此通知： "无法运行索引器 ' <索引器-name>"，错误： "当前正在进行另一个索引器调用;不允许并发调用。 "

+ 如果使用的是技能组合和24小时，最长运行时间为2小时。 

  您可以通过按计划放置索引器来扩展处理。 免费层的运行时间限制较低。 有关完整列表，请参阅 [索引器限制](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>重置索引器

重置索引器全部都包含。 在搜索索引中，索引器最初填充的任何搜索文档都标记为完全处理。 找到的任何新文档将作为搜索文档添加到索引中。 如果索引器配置为使用技能组合和 [缓存](search-howto-incremental-index.md)，则会重新运行技能组合并刷新缓存。

您可以使用这些方法中的任何一种来重置索引器，后跟使用上述方法之一运行索引器。

+ Azure 门户，使用 "索引器" 页上的 " **重置** " 命令
+ [将索引器重置 (REST) ](/rest/api/searchservice/reset-indexer)
+ Azure .NET SDK 中的[ResetIndexers 方法](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) (或在另一 SDK 中使用等效的 RunIndexer 方法) 

运行完成后，将清除重置标志。 对于数据源合适的任何常规更改检测逻辑将在下一次运行时恢复，并在数据集的其余部分中选取所有其他新值或更新的值。

> [!NOTE]
> 重置请求确定 (索引器、技能或文档) 重新处理的内容，但不会影响索引器运行时行为。 如果索引器具有运行时参数、字段映射、缓存、批处理选项等，则在重置索引器后运行索引器时，这些设置都有效。

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a> (预览重置技能) 

> [!IMPORTANT] 
> [重置技能](/rest/api/searchservice/preview-api/reset-skills) 处于公开预览中，仅可通过预览版 REST API 提供。 在 [补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下，预览版功能按原样提供。

对于具有技能集的索引器，你可以重置特定技能来强制处理该技能以及依赖于其输出的任何下游技能。 还会刷新[缓存的根据](search-howto-incremental-index.md)。 重置技能会使缓存的技能结果失效，这在部署新版本的技能，并且你希望索引器重新运行所有文档的技能时非常有用。 

[重置技能](/rest/api/searchservice/preview-api/reset-skills) 可通过 REST 获得 **`api-version=2020-06-30-Preview`** 。

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

您可以指定单个技能，如上面的示例所示，但如果其中的任何技能需要从未列出的技能 (#2 到 #4) ，则未列出的技能将运行，除非缓存可以提供必需的信息。 为实现这一点，通过 #4 缓存的根据 #2 的技能不得依赖于重置) 列出 #1 (。

如果未指定任何技能，则执行整个技能组合，如果启用了缓存，则也会刷新缓存。

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a> (预览重置文档) 

> [!IMPORTANT] 
> [重置文档](/rest/api/searchservice/preview-api/reset-documents) 处于公共预览中，仅可通过预览版 REST API 提供。 在 [补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下，预览版功能按原样提供。

[重置文档 API](/rest/api/searchservice/preview-api/reset-documents)接受文档键的列表，以便您可以刷新特定文档。 如果已指定，则重置参数将成为所处理内容的唯一决定因素，而不考虑基础数据中的其他更改。 例如，如果自上次索引器运行后添加或更新了20个 blob，但你只重置了一个文档，则只会处理一个文档。

对于每个文档，将使用数据源中的值刷新该搜索文档中的所有字段。 不能选取并选择要刷新的字段。 

如果文档通过技能组合进行了充实并包含缓存的数据，则仅为指定的文档调用技能组合，并为重新处理的文档更新缓存的。

首次测试此 API 时，以下 Api 将帮助你验证和测试行为：

+ 通过 API 版本[获取索引器状态](/rest/api/searchservice/get-indexer-status) `2020-06-30-Preview` ，以检查重置状态和执行状态。 你可以在状态响应结束时找到有关重置请求的信息。
+ 用 API 版本[重置文档](/rest/api/searchservice/preview-api/reset-documents) `2020-06-30-Preview` 以指定要处理的文档。
+ [运行索引器](/rest/api/searchservice/run-indexer) ， (任何 API 版本) 运行索引器。
+ [搜索文档](/rest/api/searchservice/search-documents) 以检查是否有更新的值，如果您不确定该值，还会返回文档键。 `"select": "<field names>"`如果要限制显示在响应中的字段，请使用。

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

请求中提供的文档键是搜索索引中的值，这些值可能不同于数据源中的相应字段。 如果你不确定键值，则 [发送查询](search-query-create.md) 以返回值。您可以使用 `select` 只返回文档键字段。

对于分析为多个搜索文档的 blob (例如，如果你使用 [jsonLines 或 jsonArrays](search-howto-index-json-blobs.md)，或 [delimitedText](search-howto-index-csv-blobs.md)) 为分析模式，则文档键是由索引器生成的，可能对你是未知的。 在这种情况下，对文档键的查询将有助于提供正确的值。

多次用不同的键调用 API 会将新密钥追加到文档密钥重置列表。 如果调用 API 并将 **`overwrite`** 参数设置为 true，则将覆盖当前文档键列表，以通过请求的负载进行重置：

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

若要检查重置的状态并查看排队等候处理的文档键，请使用 [获取索引器状态](/rest/api/searchservice/get-indexer-status) **`api-version=06-30-2020-Preview`** 。 预览 API 将返回 **`currentState`** 节，你可以在 Get 索引器状态响应结束时找到该部分。

"模式" 将 **`indexingAllDocs`** 用于重置技能 (因为可能会影响通过 AI 扩充) 填充的字段。

对于 Reset 文档，该模式将设置为 **`indexingResetDocs`** 。 索引器将保持此状态，直到处理重置文档调用中提供的所有文档键，并且在操作执行期间不会执行任何其他索引器作业。 若要查找文档键列表中的所有文档，需要对每个文档进行破解以查找并匹配该密钥，如果数据集很大，则可能需要一段时间。 如果 blob 容器包含数以百计的 blob，而你想要重置的文档位于末尾，则索引器将找不到匹配的 blob，直到首先检查其他 blob。

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

重新处理文档后，索引器将返回到 **`indexingAllDocs`** 模式，并在下次运行时处理任何其他新文档或更新的文档。

## <a name="next-steps"></a>后续步骤

重置 Api 用于通知下一个索引器运行的范围。 对于实际处理，需要调用按需索引器运行或允许计划作业完成工作。 运行完成后，无论是按计划进行还是按需处理，索引器都将返回到正常处理。

重置并重新运行索引器作业后，可以从搜索服务监视状态，或者通过诊断日志记录获取详细信息。

+ [索引器操作 (REST) ](/rest/api/searchservice/indexer-operations)
+ [监视器搜索索引器状态](search-howto-monitor-indexers.md)
+ [收集和分析日志数据](search-monitor-logs.md)
+ [安排索引器](search-howto-schedule-indexers.md)