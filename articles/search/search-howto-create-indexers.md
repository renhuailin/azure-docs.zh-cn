---
title: 创建索引器
titleSuffix: Azure Cognitive Search
description: 设置索引器的属性以确定数据源和目标。 可以设置参数来修改运行时行为。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 0483030312493dde9a50ab9000fbe29f19bfaff4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063821"
---
# <a name="create-a-search-indexer"></a>创建搜索索引器

搜索索引器提供自动工作流，用于将文档和外部数据源的内容传输到搜索服务上的搜索索引。 正如最初设计的，它从 Azure 数据源提取文本和元数据，将文档序列化为 JSON，并将生成的文档传递到用于索引的搜索引擎。 这是因为已将其扩展为支持 [AI 扩充](cognitive-search-concept-intro.md) 进行深层内容处理。 

使用索引器可以显著减少需要编写的代码的数量和复杂性。 本文重点介绍索引器的结构和结构，在探索特定于源的索引器和 [技能集](cognitive-search-working-with-skillsets.md)之前，先构建一个基础。

## <a name="whats-an-indexer-definition"></a>什么是索引器定义？

索引器可用于将文本从源字段提取到索引字段的基于文本的索引，或用于分析无差别文本以进行结构处理的基于 AI 的处理，或分析文本和信息的图像。 以下索引定义是你可能为任一方案创建的内容的典型定义。

### <a name="indexers-for-text-content"></a>文本内容的索引器

索引器的原始用途是通过提供一种机制，用于连接到数据源中的字段和从数据源中的字段读取文本和数字内容，将该内容序列化为 JSON 文档，并将这些文档移交给搜索引擎，以实现索引。 这仍是一个主要用例，对于此操作，你将需要使用此部分中定义的属性创建索引器。

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
**`name`** 需要、 **`dataSourceName`** 和 **`targetIndexName`** 属性，并且根据创建索引器的方式，数据源和索引必须已经存在，然后才能运行索引器。 

**`parameters`** 属性通知运行时行为，如在失败整个作业之前要接受多少个错误。 参数也是指定特定于源的行为的方式。 例如，如果源是 Blob 存储，则可以设置对文件扩展名进行筛选的参数： `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` 。

**`field mappings`** 如果源到目标字段的名称不同，则使用属性显式映射这些字段。  (未显示) 的其他属性，用于指定计划、在禁用状态下创建索引器或为静态数据的补充加密指定加密密钥。

### <a name="indexers-for-ai-indexing"></a>AI 索引编制索引器

由于索引器是搜索服务发出出站请求的机制，因此，对索引器进行了扩展以支持 AI 根据，并添加了此用例所需的步骤和对象。

上述所有属性和参数均适用于执行 ai 扩充的索引器，并添加了三个特定于 ai 扩充的属性： **`skillSets`** 、 **`outputFieldMappings`** 、 **`cache`** (预览和 REST 仅) 。 

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

AI 扩充超出了本文的范围。 有关详细信息，请从 [Azure 中的技能集开始认知搜索](cognitive-search-working-with-skillsets.md) 或 [创建技能组合 (REST) ](/rest/api/searchservice/create-skillset)。

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>选择索引器客户端并创建索引器

准备好在远程搜索服务上创建索引器时，需要使用工具（如 Azure 门户或 Postman）形式的搜索客户端或实例化索引器客户端的代码。 建议将 Azure 门户或 REST Api 用于早期开发和概念证明测试。

### <a name="permissions"></a>权限

与索引器相关的所有操作（包括状态或定义的 GET 请求）在请求时都需要 [管理 api 密钥](search-security-api-keys.md) 。

### <a name="limits"></a>限制

所有 [服务层限制](search-limits-quotas-capacity.md#indexer-limits) 了你可以创建的对象数。 如果要试验免费层，则每种类型只能有3个对象，而索引器处理的2分钟 (不包括技能组合处理) 。

### <a name="use-azure-portal-to-create-an-indexer"></a>使用 Azure 门户创建索引器

门户提供了两个用于创建索引器的选项： [**导入数据**](search-import-data-portal.md) 和 **新索引器** ，用于提供用于指定索引器定义的字段。 向导是唯一的，因为它会创建所有必需的元素。 其他方法要求预定义数据源和索引。

以下屏幕截图显示了在门户中可以找到这些功能的位置。 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="hotels 索引器" border="true":::

### <a name="use-a-rest-client"></a>使用 REST 客户端

带有 Azure 认知搜索) 的扩展的 Postman 和 Visual Studio Code (可充当索引器客户端。 使用任意一种工具，都可以连接到搜索服务并发送创建索引器和其他对象的请求。 有许多教程和示例演示了用于创建对象的 REST 客户端。 

请从以下文章之一开始了解每个客户端：

+ [使用 REST 和 Postman 创建搜索索引](search-get-started-rest.md)
+ [Visual Studio Code 和 Azure 认知搜索入门](search-get-started-vs-code.md)

请参阅 [索引器操作 (REST) ](/rest/api/searchservice/Indexer-operations) ，以获取有关如何表述索引器请求的帮助。

### <a name="use-an-sdk"></a>使用 SDK

对于认知搜索，Azure Sdk 实现了公开提供的功能。 因此，你可以使用任何 Sdk 来创建与索引器相关的对象。 它们都实现了一个 **SearchIndexerClient** ，该对象提供创建索引器和相关对象的方法，包括技能集。

| Azure SDK | 客户端 | 示例 |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>运行索引器

当您在服务上创建索引器时，索引器将自动运行。 这就是您会发现数据源连接错误、字段映射问题或技能组合问题的情况。 [Create 索引](/rest/api/searchservice/create-indexer)器或[更新索引器](/rest/api/searchservice/update-indexer)的交互式 HTTP 请求将运行索引器。 运行调用 SearchIndexerClient 方法的程序也将运行一个索引器。

若要避免在创建时立即运行索引器，请 **`disabled=true`** 在索引器定义中包含。

索引器存在后，可以使用 [运行索引器 (REST) ](/rest/api/searchservice/run-indexer) 或等效的 SDK 方法，按需运行该索引器。 或者，将索引器置于 [计划中](search-howto-schedule-indexers.md) ，以便定期调用处理。 

计划的处理通常与对更改内容进行增量索引的需要相符。 更改检测逻辑是一种内置于源平台的功能。 索引器自动检测 blob 容器中的更改。 有关在其他数据源中利用更改检测的指南，请参阅特定数据源的索引器文档：

+ [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure 表存储](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>了解你的数据

索引器应为表格行集，其中每行都成为索引中的完整或部分搜索文档。 通常，在行与生成的搜索文档之间有一个完全一一对应的关系，所有字段都排在那里。 但是，您可以使用索引器仅生成文档的一部分，例如，如果使用多个索引器或方法来生成索引。 

若要将关系数据平展到行集，您可能需要创建一个 SQL 视图，或者生成一个查询，该查询返回同一行中的父记录和子记录。 例如，内置的酒店示例数据集是一个 SQL 数据库，该数据库具有50记录 (每个旅馆) ，链接到相关表中的教室记录。 将集体数据平展到行集的查询会将所有房间信息嵌入到每个酒店记录的 JSON 文档中。 嵌入的空间信息是一个使用 **FOR JSON AUTO** 子句的查询生成的。 有关此技术的详细信息，请参阅 [定义返回嵌入 JSON 的查询](index-sql-relational-data.md#define-a-query-that-returns-embedded-json)。 这只是一个示例;您可以找到将产生相同效果的其他方法。

除平展数据外，还必须仅提取可搜索的数据。 可搜索数据为字母数字。 认知搜索无法以任何格式搜索二进制数据，但它可以提取并推断图像文件的文本说明 (请参阅 [AI 扩充](cognitive-search-concept-intro.md)) 创建可搜索的内容。 同样，使用 AI 扩充，自然语言模型可以分析大文本，以查找结构或相关信息，并生成可添加到搜索文档中的新内容。

假设索引器不能解决数据问题，可能需要其他形式的数据清理或操作。 有关详细信息，请参阅 [Azure 数据库产品](/azure/?product=databases)的产品文档。

## <a name="know-your-index"></a>了解索引

请记住，索引器会将搜索文档传递给搜索引擎，以便进行索引。 就像索引器具有确定执行行为的属性一样，索引架构具有程度对字符串进行索引的方式的属性， (仅分析字符串并将其标记) 。 根据分析器分配，索引字符串可能不同于传入的字符串。 可以使用 " [分析文本" (REST) ](/rest/api/searchservice/test-analyzer)来评估分析器的效果。 有关分析器的详细信息，请参阅 [文本处理分析器](search-analyzers.md)。

索引器仅检查字段名称和类型。 没有验证步骤可确保传入内容对于索引中对应的搜索字段是正确的。 作为验证步骤，你可以对已填充的索引运行查询，以返回整篇文档或所选字段。 有关查询索引内容的详细信息，请参阅 [创建基本查询](search-query-create.md)。

## <a name="next-steps"></a>后续步骤

+ [计划索引器](search-howto-schedule-indexers.md)
+ [定义字段映射](search-indexer-field-mappings.md)
+ [监视器索引器状态](search-howto-monitor-indexers.md)
+ [使用托管标识进行连接](search-howto-managed-identities-data-sources.md)