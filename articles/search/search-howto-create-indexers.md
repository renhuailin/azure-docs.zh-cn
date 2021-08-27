---
title: 创建索引器
titleSuffix: Azure Cognitive Search
description: 在索引器中设置属性以确定数据源和目标。 可以设置参数来修改运行时行为。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 666582e7f774d95d61da63d4cd31a7f4893a6a6b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772814"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>在 Azure 认知搜索中创建索引器

搜索索引器提供自动化工作流用于将外部数据源中文档和内容传输到搜索服务上的搜索索引。 索引器最初的设计是从 Azure 数据源提取文本和元数据，将文档序列化为 JSON，然后将生成的文档传递到搜索引擎以编制索引。 后来索引器已经过扩展，支持使用 [AI 扩充](cognitive-search-concept-intro.md)进行深度内容处理。 

使用索引器能够显著减少需要编写的代码的数量和复杂性。 本文重点介绍索引器的创建机制，使用户能够为使用源特定的索引器和[技能组](cognitive-search-working-with-skillsets.md)开展更高级工作做好准备。

## <a name="whats-an-indexer-definition"></a>什么是索引器定义？

索引器用于基于文本的编制索引，以便将源字段中的字母数字内容拉取到索引字段中；或者用于基于 AI 的处理，以便分析无差别文本中的结构，或者分析图像中的文本和信息，并将内容添加到索引。 以下索引定义是可为任一方案创建的典型定义。

### <a name="indexers-for-text-content"></a>文本内容的索引器

索引器的最初目的是简化复杂的索引加载过程，为此它提供了一个机制用于连接和读取数据源字段中的文本和数字内容，将这些内容序列化为 JSON 文档，然后将这些文档移交给搜索引擎以编制索引。 至今这仍是一个主要用例，对于此操作，需要使用以下示例中定义的属性创建索引器。

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

`name`、`dataSourceName` 和 `targetIndexName` 属性是必需的。根据索引器的创建方式，在运行索引器之前，数据源和索引必须已存在于服务中  。 

`parameters` 属性修改运行时行为，例如，在使整个作业失败之前可接受多少个错误。 还可以通过参数指定特定于源的行为。 例如，如果源是 Blob 存储，则你可以设置一个参数用于根据文件扩展名进行筛选：`"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }`。

`field mappings` 属性用于显式映射源到目标的字段（如果这些字段的名称或类型不同）。 其他属性（未显示）用于[指定计划](search-howto-schedule-indexers.md)、创建处于禁用状态的索引器，或者为静态数据的补充加密指定[加密密钥](search-security-manage-encryption-keys.md)。

### <a name="indexers-for-ai-indexing"></a>AI 索引编制的索引器

由于索引器是搜索服务发出出站请求所用的机制，因此索引器已扩展为支持 AI 扩充，并添加了基础结构和对象来实现此用例。

以上所有属性和参数均适用于执行 AI 扩充的索引器。 以下属性特定于 AI 扩充：`skillSets`、`outputFieldMappings`、`cache`（仅限预览版和 REST）  。 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI 扩充超出了本文的范畴。 有关详细信息，请从以下文章入手：[AI 扩充](cognitive-search-concept-intro.md)、[Azure 认知搜索中的技能组](cognitive-search-working-with-skillsets.md)和[创建技能组 (REST)](/rest/api/searchservice/create-skillset)。

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>选择索引器客户端并创建索引器

准备好在远程搜索服务中创建索引器时，需要提供一个工具形式的搜索客户端（例如 Azure 门户或 Postman），或者一段可以实例化索引器客户端的代码。 建议使用 Azure 门户或 REST API 进行早期开发和概念证明测试。

### <a name="permissions"></a>权限

与索引器相关的所有操作（包括对状态或定义的 GET 请求）都需要在请求中包含[管理员 api-key](search-security-api-keys.md)。

### <a name="limits"></a>限制

所有[服务层都会限制](search-limits-quotas-capacity.md#indexer-limits)你可以创建的对象数量。 如果在免费层上进行试验，只能为每种类型创建 3 个对象，而索引器处理只能持续 2 分钟（不包括技能组处理）。

### <a name="use-azure-portal-to-create-an-indexer"></a>使用 Azure 门户创建索引器

门户中提供了两个选项用于创建索引器：[导入数据向导](search-import-data-portal.md)，以及“新建索引器”（提供用于指定索引器定义的字段） 。 向导的独特之处在于它能创建全部所需元素。 其他方法需要预定义数据源和索引。

以下屏幕截图显示了可以在门户中的哪个位置找到这些功能。 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="hotels 索引器" border="true":::

### <a name="use-a-rest-client"></a>使用 REST 客户端

Postman 和 Visual Studio Code（带有 Azure 认知搜索的扩展）都可以充当索引器客户端。 使用任一工具都可以连接到搜索服务，并发送[创建索引器 (REST)](/rest/api/searchservice/create-indexer) 请求。 有许多教程和示例演示了用于创建对象的 REST 客户端。 

请从以下任一文章开始阅读，了解每个客户端：

+ [使用 REST 和 Postman 创建搜索索引](search-get-started-rest.md)
+ [Visual Studio Code 和 Azure 认知搜索入门](search-get-started-vs-code.md)

参阅[索引器操作 (REST)](/rest/api/searchservice/Indexer-operations)，获取有关构建索引器请求的帮助。

### <a name="use-an-sdk"></a>使用 SDK

对于认知搜索，Azure SDK 实现了正式发布的功能。 因此，可以使用任何 SDK 来创建索引器相关的对象。 所有这些 SDK 都提供一个 SearchIndexerClient，其中包含用于创建索引器和相关对象（包括技能组）的方法。

| Azure SDK | 客户端 | 示例 |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [索引器](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>运行索引器

在服务中创建索引器时，索引器将自动运行。 正是在此一刻，你可以确定是否存在数据源连接错误、字段映射问题或技能组问题。 

可通过多种方式运行索引器：

+ 发送[创建索引器](/rest/api/searchservice/create-indexer)或[更新索引器](/rest/api/searchservice/update-indexer) 的 HTTP 请求以添加或更改定义，并运行索引器。

+ 发送[运行索引器](/rest/api/searchservice/run-indexer) 的 HTTP 请求，以便在不更改定义的情况下执行索引器。

+ 运行一个可调用 SearchIndexerClient 方法来执行创建、更新或运行操作的程序。

> [!NOTE]
> 若要避免在创建后立即运行索引器，请在索引器定义中包含 `disabled=true`。

或者，将索引器纳入[计划](search-howto-schedule-indexers.md)，以定期调用处理。 

按计划的处理通常也意味着需要对已更改的内容进行增量式索引编制。 更改检测逻辑是内置于源平台中的一项功能。 索引器会自动检测 Blob 容器中的更改。 有关利用其他数据源中的更改检测的指导，请参阅特定数据源的索引器文档：

+ [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure 表存储](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>更改检测和索引器状态

索引器可以检测基础数据的更改，索引器每次运行时只会处理新文档或已更新的文档。 例如，如果索引器状态指出运行成功并已处理 `0/0` 个文档，则意味着索引器在基础数据源中未发现任何新的或者已更改的行或 Blob。

索引器支持更改检测的方式因数据源而异：

+ Azure Blob 存储、Azure 表存储和 Azure Data Lake Storage Gen2 为每项 Blob 或行更新加上日期和时间戳记。 不同的索引器使用此信息来确定要在索引中更新哪些文档。 内置更改检测意味着索引器可以识别新的和已更新的文档，而不需要你进行任何额外的配置。

+ Azure SQL 和 Cosmos DB 在其平台中提供更改检测功能。 你可以在数据源定义中指定更改检测策略。

对于较大的索引编制负载，索引器还会通过内部“高水位线”跟踪它所处理的最后一个文档。 该标记永远不会在 API 中公开，但在内部，索引器会跟踪它的停止位置。 当索引编制通过按计划的运行或按需调用恢复时，索引器将引用高水位线，以便可以从上次停止的位置继续。

如果你需要清除高水位线以便彻底重新编制索引，可以使用[重置索引器](/rest/api/searchservice/reset-indexer)。 若要更加有选择性地重新编制索引，请使用[重置技能](/rest/api/searchservice/preview-api/reset-skills)或[重置文档](/rest/api/searchservice/preview-api/reset-documents)。 可以通过重置 API 清除内部状态，如果已启用[增量扩充](search-howto-incremental-index.md)，则还可以刷新缓存。 有关每个重置选项的更多背景信息和比较，请参阅[运行或重置索引器、技能和文档](search-howto-run-reset-indexers.md)。

## <a name="know-your-data"></a>了解数据

索引器需要一个表格行集，其中每一行将成为索引中的完整或部分搜索文档。 通常，某行与生成的搜索文档之间存在一对一的对应关系，其中，行集中的所有字段将完全填充每个文档。 但是，你可以使用索引器仅生成文档的一部分，例如，使用多个索引器或方法生成索引时。 

若要将关系数据平展为行集，应创建一个 SQL 视图，或生成一个查询用于返回同一行中的父记录和子记录。 例如，内置的酒店 (hotels) 示例数据集是包含 50 条记录（每家酒店对应一条记录）的 SQL 数据库，它已链接到相关表中的客房记录。 将统一数据平展为行集的查询在每条酒店记录中的 JSON 文档内嵌入所有客房信息。 嵌入的客房信息是使用 FOR JSON AUTO 子句的查询生成的。 可以在[定义返回嵌入式 JSON 的查询](index-sql-relational-data.md#define-a-query-that-returns-embedded-json)中详细了解此方法。 这只是其中的一个示例；还可以找到其他可达到相同效果的方法。

除了平展化数据以外，仅拉取可搜索的数据也很重要。 可搜索的数据为字母数字。 认知搜索无法搜索任何格式的二进制数据，不过，它可以提取并推断图像文件的文本说明（请参阅 [AI 扩充](cognitive-search-concept-intro.md)）以创建可搜索的内容。 同样，使用 AI 扩充可以通过自然语言模型分析大文本，以查找结构或相关信息，并生成可添加到搜索文档中的新内容。

如果索引器不能解决数据问题，则可能需要其他形式的数据清理或处理。 有关详细信息，请参阅 [Azure 数据库产品](../index.yml?product=databases)的产品文档。

## <a name="know-your-index"></a>了解索引

如前文所述，索引器会将搜索文档传递给搜索引擎进行索引编制。 如同索引器包含用于确定执行行为的属性一样，索引架构包含对字符串索引编制方式（只会分析并标记化字符串）产生深刻影响的属性。 已编制索引的字符串可能与你传入的字符串不同，具体取决于分析器分配。 可以使用[分析文本 (REST)](/rest/api/searchservice/test-analyzer) 来评估分析器的效果。 有关分析器的详细信息，请参阅[用于处理文本的分析器](search-analyzers.md)。

在索引器如何与索引交互方面，索引器只会检查字段名称和类型。 不会执行任何验证步骤来确保传入内容适合索引中对应的搜索字段。 作为验证步骤，可以针对填充的索引运行可返回整个文档或所选字段的查询。 有关查询索引内容的详细信息，请参阅[创建基本查询](search-query-create.md)。

## <a name="next-steps"></a>后续步骤

+ [计划索引器](search-howto-schedule-indexers.md)
+ [定义字段映射](search-indexer-field-mappings.md)
+ [监视索引器状态](search-howto-monitor-indexers.md)
+ [使用托管标识进行连接](search-howto-managed-identities-data-sources.md)