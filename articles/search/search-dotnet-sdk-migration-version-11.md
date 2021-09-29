---
title: 升级到 .NET SDK 版本 11
titleSuffix: Azure Cognitive Search
description: 将代码从 Azure 认知搜索 .NET SDK 的旧版本迁移到版本 11。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 37c03a5be2f81fff0f4a1cac01153601a39ddd10
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557146"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>升级到 Azure 认知搜索 .NET SDK 版本 11

如果搜索解决方案基于 [Azure SDK for .NET](/dotnet/azure/) 构建而成，则本文将帮助你将代码从早期版本的 [**Microsoft.Azure.Search**](/dotnet/api/overview/azure/search/client10) 迁移到版本 11（新 [Azure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) 客户端库）。 版本 11 是完全重新设计的客户端库，由 Azure SDK 开发团队发布（以前的版本由 Azure 认知搜索开发团队生成）。 

除了[一个例外](#WhatsNew)，版本 10 的所有功能都在版本 11 中实现。 关键不同点包括：

+ 一个程序包 (Azure.Search.Documents)，而不是四个
+ 三个客户端而不是两个客户端：SearchClient、SearchIndexClient、SearchIndexerClient
+ 一系列 API 的命名差异，以及简化了某些任务的小的结构差异

客户端库的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)包含更新的项化列表。

认知搜索产品文档中的所有 C# 代码示例和代码片段已修改为使用新的Azure.Search.Documents 客户端库。

## <a name="why-upgrade"></a>为什么升级？

升级的好处总结如下：

+ 新功能将仅添加到 Azure.Search.Documents。 以前的版本 Microsoft.Azure.Search 现在是旧版客户端。 对旧库的更新仅限于高优先级 bug 修复。

+ 与其他 Azure 客户端库一致。 Azure.Search.Documents 依赖于 [Azure.Core](/dotnet/api/azure.core) 和 [System.Text.Json](/dotnet/api/system.text.json)，并遵循常见任务（如客户端连接和授权）的传统方法。

## <a name="package-comparison"></a>包比较

版本 11 合并并简化了包管理，以便减少要管理的数量。

| 版本 10 及早期版本 | 版本 11 |
|------------------------|------------|
| [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) </br>[Microsoft.Azure.Search.Service](https://www.nuget.org/packages/Microsoft.Azure.Search.Service/) </br>[Microsoft.Azure.Search.Data](https://www.nuget.org/packages/Microsoft.Azure.Search.Data/) </br>[Microsoft.Azure.Search.Common](https://www.nuget.org/packages/Microsoft.Azure.Search.Common/)  | [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/) |

## <a name="client-comparison"></a>客户端比较

下表映射了两个版本的客户端库（适用情况下）。

| 客户端操作 | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| 面向索引的文档集合（查询和数据导入） | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| 面向索引相关的对象（索引、分析器、同义词映射 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 面向索引器相关的对象（索引器、数据源、技能组） | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient（**新增**）](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Caution]
> 请注意，SearchIndexClient 存在于这两个版本中，但针对不同的操作。 在版本 10 中，SearchIndexClient 创建索引和其他对象。 在版本 11 中，SearchIndexClient 适用于现有索引，以具有查询和数据输入 API 的文档集合为目标。 为了避免在更新代码时产生混淆，请注意更新客户端引用时的顺序。 按照[升级步骤](#UpgradeSteps)中的顺序进行操作应当有助于缓解任何字符串替换问题。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>命名和其他 API 差异

除了客户端差异以外（前面已提到，因此在此处省略），还重命名了多个其他 API，在某些情况下对其进行了重新设计。 下面汇总了类名称差异。 此列表并非详尽无遗，但它确实按任务将 API 更改进行了分组，这对于特定代码块的修订很有帮助。 有关 API 更新的详细列表，请参阅 GitHub 上 `Azure.Search.Documents` 的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

### <a name="authentication-and-encryption"></a>身份验证和加密

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| EncryptionKey（API 参考中未记录。 对此 API 的支持已转换为 v10 中的正式版，但仅在[预览 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) 中可用） | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>索引、分析器、同义词映射

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| 字段 | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)（另外，`AnalyzerName` 对应于 `LexicalAnalyzerName`） |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer)（另外，`StandardTokenizerV2` 对应于 `LuceneStandardTokenizerV2`） |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [分词器](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer)（另外，`TokenizerName` 对应于 `LexicalTokenizerName`） |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 无。 删除了对 `Format` 的引用。 |

简化了字段定义：[SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield)、[SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield)、[ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) 是用于创建字段定义的新 API。

### <a name="indexers-datasources-skillsets"></a>索引器、数据源、技能组

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [索引器](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [技能集](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>数据导入

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-requests-and-responses"></a>查询请求和响应

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [DocumentsOperationsExtensions.SearchAsync](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.searchasync) | [SearchClient.SearchAsync](/dotnet/api/azure.search.documents.searchclient.searchasync) |
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) 或 [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)，具体取决于结果是单个文档还是多个文档。 |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |
| [SuggestParameters](/dotnet/api/microsoft.azure.search.models.suggestparameters) |  [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) |
| [SearchParameters.Filter](/dotnet/api/microsoft.azure.search.models.searchparameters.filter) |  [SearchFilter](/dotnet/api/azure.search.documents.searchfilter)（构造 OData 筛选器表达式的新类） |

### <a name="json-serialization"></a>JSON 序列化

默认情况下，Azure SDK 使用 [System.Text.Json](/dotnet/api/system.text.json) 进行 JSON 序列化，依赖这些 API 的功能来处理以前通过原生 [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) 类实现的文本转换，该类在新库中没有对应的类。

若要将属性名称序列化为 camelCase，可以使用 [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute)（类似于[此示例](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)）。

另外，你还可以设置 [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions) 中提供的 [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy)。 下面的 System.Text.Json 代码示例摘自 [Microsoft.Azure.Core.Spatial 自述文件](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents)，它演示了如何使用 camelCase，无需将每个属性特性化：

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

如果使用 Newtonsoft.Json 进行 JSON 序列化，则可以通过使用类似的特性或通过使用 [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm) 上的属性来传入全局命名策略。 有关与以上示例等效的示例，请参阅 Newtonsoft.Json 自述文件中的[反序列化文档示例](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)。

<a name="WhatsNew"></a>

## <a name="inside-v11"></a>v11 内

Azure 认知搜索客户端库的每个版本都面向 REST API 的一个对应版本。 REST API 被视为服务的基础，而各个 SDK 用于包装 REST API 的版本。 作为 .NET 开发人员，查看更详细的 [REST API 文档](/rest/api/searchservice/)可以帮助更深入地了解特定对象或操作。 版本 11 对应于 [2020-06-30 搜索服务](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)。 

版本 11.0 完全支持以下对象和操作：

+ 索引创建和管理
+ 同义词映射创建和管理
+ 索引器创建和管理
+ 索引器数据源创建和管理
+ 技能组创建和管理
+ 所有查询类型和语法

版本 11.1 附加内容（[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1110-2020-08-11)详细信息）：

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder)（在 11.1 中添加）
+ [序列化程序属性](/dotnet/api/azure.search.documents.searchclientoptions.serializer)（在 11.1 中添加），用于支持自定义序列化

版本 11.2 附加内容（[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1120-2021-02-10)详细信息）：

+ [EncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchindexer.encryptionkey) 属性已添加索引器、数据源和技能组
+ [IndexingParameters.IndexingParametersConfiguration](/dotnet/api/azure.search.documents.indexes.models.indexingparametersconfiguration) 属性支持
+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder.build) 本机支持[地理空间类型](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype.geographypoint)。 [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) 可以在没有显式程序集依赖项的情况下编码来自 Microsoft.Spatial 的几何类型。

  还可以继续显式声明对 [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) 的依赖关系。 此方法示例可用于 [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) 和 [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)。

当前不支持任何版本的 Azure.Search.Documents：

+ [知识存储](knowledge-store-concept-intro.md)

## <a name="before-upgrading"></a>升级前

+ 已更新[快速入门](search-get-started-dotnet.md)、教程和 [C# 示例](samples-dotnet.md)，以使用 Azure.Search.Documents 包。 建议在开始迁移练习之前查看现有示例和演练，以了解新 API。

+ [如何使用 Azure.Search.Documents](search-howto-dotnet-sdk.md) 介绍了最常使用的 API。 即使是认知搜索的知识丰富的用户，也可能希望在迁移之前查看此新库简介。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤

以下步骤通过执行第一组必需任务（特别是与客户端引用相关的任务）来开始进行代码迁移。

1. 在 Visual Studio 中，右键单击你的项目引用并选择“管理 NuGet 包...”，以安装 [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)。

1. 将用于 Microsoft.Azure.Search 的 using 指令替换为以下项：

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 对于需要 JSON 序列化的类，请将 `using Newtonsoft.Json` 替换为 `using System.Text.Json.Serialization`。

1. 修订客户端身份验证代码。 在以前的版本中，你会使用客户端对象上的属性（例如，[SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) 属性）设置 API 密钥。 在当前版本中，请使用 [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) 类将密钥作为凭据传递，以便在需要时可以更新 API 密钥，而无需创建新的客户端对象。

   客户端属性已精简，仅剩 `Endpoint`、`ServiceName` 和 `IndexName`（如果适用）。 下面的示例使用系统 [Uri](/dotnet/api/system.uri) 类提供终结点，并使用 [Environment](/dotnet/api/system.environment) 类来读取密钥值：

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. 为索引器相关对象添加新的客户端引用。 如果使用的是索引器、数据源或技能组，请将客户端引用更改为 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)。 此客户端是版本 11 中的新客户端，之前没有。

1. 请修改集合和列表。 在新的 SDK 中，所有列表都是只读的，这是为了避免在列表刚好包含 NULL 值时出现下游问题。 代码更改是为了向列表中添加项。 例如，可以按如下所示添加字符串，而不是为 Select 属性指定字符串：

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Select、Facets、SearchFields、SourceFields、ScoringParameters 和 OrderBy 都是现在需要重新构造的列表。

1. 为查询和数据导入更新客户端引用。 应将 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) 的实例更改为 [SearchClient](/dotnet/api/azure.search.documents.searchclient)。 为了避免名称混乱，请确保在继续下一步之前捕获所有实例。

1. 为索引、同义词映射和分析器对象更新客户端引用。 应将 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) 的实例更改为 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient)。 

1. 对于你的代码的剩余部分，请更新类、方法和属性以使用新库的 API。 可以从[命名差异](#naming-differences)部分来开始了解，但也可以查看[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

   如果在查找等效 API 时遇到困难，建议你在 [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) 上记录问题，以便我们可以改进文档或调查问题。

1. 重新生成解决方案。 在修复了任何生成错误或警告后，可以对应用程序进行其他更改，以利用[新功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>中断性变更

考虑到对库和 API 的全面更改，升级到版本 11 是一项重大升级，你的代码将不再后向兼容版本 10 及更低版本。从这个意义上说，这是一项中断性变更。 若要全面了解差异，请参阅 `Azure.Search.Documents` 的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

就服务版本更新而言，版本 11 中的代码更改与现有功能（而不仅仅是 API 重构）相关，你会发现以下行为更改：

+ [BM25 排名算法](index-ranking-similarity.md)将以前的排名算法替换为更新的技术。 新服务将自动使用此算法。 对于现有服务，必须将参数设置为使用新算法。

+ 在此版本中，NULL 值的[排序结果](search-query-odata-orderby.md)发生了变化，如果排序是 `asc`，则 NULL 值首先出现；如果排序是 `desc`，则 NULL 值最后出现。 如果你编写了代码来处理如何对 NULL 值排序，则应查看该代码并可在不再需要时将其删除。

## <a name="next-steps"></a>后续步骤

+ [如何在 C# .NET 应用程序中使用 Azure.Search.Documents](search-howto-dotnet-sdk.md)
+ [教程：向 Web 应用添加搜索](tutorial-csharp-overview.md)
+ [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 上的示例](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API 参考](/dotnet/api/overview/azure/search.documents-readme)
