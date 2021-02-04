---
title: 创建索引
titleSuffix: Azure Cognitive Search
description: 介绍 Azure 认知搜索中的索引概念和工具，包括架构定义和物理数据结构。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d0cc7630a3bea67a99c3cb65d2015e934e8ac2da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539088"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>在 Azure 认知搜索中创建搜索索引

认知搜索存储 *搜索索引* 中用于全文和筛选查询的可搜索内容。 索引由架构定义并保存到服务，紧接着的第二步是数据导入。 

索引包含 *搜索文档*。 从概念上讲，文档是索引中的一个可搜索数据单元。 零售商可能有每件产品的文档，新闻机构可能有每篇报道的文档。 将这些概念对应到更为熟悉的数据库等效对象：搜索索引等同于表，文档大致相当于表中的行   。

## <a name="whats-an-index-schema"></a>什么是索引架构？

索引的物理结构由架构确定。 "字段" 集合通常是索引的最大一部分，其中每个字段都命名为，分配有 [数据类型](/rest/api/searchservice/Supported-data-types)，并具有确定如何使用的允许行为。

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

为了简洁起见，还折叠了其他元素，但是下面的链接可以提供详细信息： [建议器](index-add-suggesters.md)、 [计分配置文件](index-add-scoring-profiles.md)、用于根据语言规则或分析器支持的其他特性处理标记的 [分析器](search-analyzers.md) ，以及 [跨源远程脚本 (CORS) ](#corsoptions) 设置。

## <a name="choose-a-client"></a>选择客户端

有几种方法可用于创建搜索索引。 建议将 Azure 门户或 REST Api 用于早期开发和概念证明测试。

在开发过程中，规划频繁的重新生成。 由于物理结构是在服务中创建的，对现有的字段定义进行的许多更改，都必须[删除并重新创建索引](search-howto-reindex.md)。 可以考虑使用一部分数据来加快重新生成的速度。

### <a name="permissions"></a>权限

与搜索索引相关的所有操作（包括 GET 请求其定义）在请求上都需要 [管理 api 密钥](search-security-api-keys.md) 。

### <a name="limits"></a>限制

所有 [服务层限制](search-limits-quotas-capacity.md#index-limits) 了你可以创建的对象数。 如果要试验免费层，在任何给定时间只能有3个索引。

### <a name="use-azure-portal-to-create-a-search-index"></a>使用 Azure 门户创建搜索索引

门户提供了两个用于创建搜索索引的选项： [**导入数据向导**](search-import-data-portal.md) 和添加用于指定索引架构的字段的 **索引** 。 向导还会通过创建索引器、数据源和加载数据，在其他操作中进行打包。 如果这是您所需的内容，则应只使用 **添加索引** 或其他方法。

以下屏幕截图显示了在门户中查找 " **添加索引** " 的位置。 **导入数据** 是右下一门。

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="添加索引命令" border="true":::

> [!Tip]
> 通过门户的索引设计强制实施特定数据类型的要求和架构规则，例如禁用对数值字段的全文搜索功能。 获得可使用的索引后，可以从门户复制 JSON，并将其添加到解决方案中。

### <a name="use-a-rest-client"></a>使用 REST 客户端

使用 Azure 认知搜索) 的扩展 (的 Postman 和 Visual Studio Code 都可以充当搜索索引客户端。 使用任一工具，你都可以连接到搜索服务，并 [ (REST) 请求发送创建索引 ](/rest/api/searchservice/create-index) 。 有许多教程和示例演示了用于创建对象的 REST 客户端。 

请从以下文章之一开始了解每个客户端：

+ [使用 REST 和 Postman 创建搜索索引](search-get-started-rest.md)
+ [Visual Studio Code 和 Azure 认知搜索入门](search-get-started-vs-code.md)

请参阅 [索引操作 (REST) ](/rest/api/searchservice/index-operations) ，以获取有关如何表述索引请求的帮助。

### <a name="use-an-sdk"></a>使用 SDK

对于认知搜索，Azure Sdk 实现了公开提供的功能。 因此，你可以使用任何 Sdk 来创建搜索索引。 所有这些方法都提供一个 **SearchIndexClient** ，其中包含用于创建和更新索引的方法。

| Azure SDK | 客户端 | 示例 |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-dotnet/快速入门/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [索引](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>定义字段

搜索文档由 `fields` 集合定义。 你将需要用于查询和键的字段。 你可能还需要字段来支持筛选器、方面和排序。 你可能还需要用户从不看到的数据字段，例如，你可能需要用于修改搜索排名的利润或营销促销字段。

类型为 Edm 的一个字段必须指定为文档键。 它用于唯一标识每个搜索文档。 可以按文档的键来检索文档，以填充详细信息页。  

如果传入数据在本质上是分层的，则分配[复杂类型](search-howto-complex-data-types.md)数据类型来表示嵌套结构。 内置的示例数据集 Hotels 演示的复杂类型使用一个 Address（包含多个子字段），其中有与每个酒店的一对一关系，并且有一个 Rooms 复杂集合，其中有多个房间与每个酒店相关联。 

在创建索引之前将任何分析器分配给字符串字段。 如果要启用特定字段的自动完成功能，请为建议器执行相同操作。

<a name="index-attributes"></a>

### <a name="attributes"></a>特性

字段属性决定了字段的使用方式，例如，是否用于全文搜索、分面导航和排序等操作中。 

字符串字段通常标记为“searchable”和“retrievable”。 用来缩小搜索结果范围的字段包括“sortable”、“filterable”和“facetable”。

|Attribute|描述|  
|---------------|-----------------|  
|“searchable” |可全文搜索，在编制索引期间遵从语法分析，例如分词。 如果将某个可搜索字段设置为“sunny day”之类的值，在内部它将拆分为单独的标记“sunny”和“day”。 有关详细信息，请参阅[全文搜索工作原理](search-lucene-query-architecture.md)。|  
|“filterable” |在 $filter 查询中引用。 `Edm.String` 或 `Collection(Edm.String)` 类型的可筛选字段不进行分词，因此，比较仅用于查找完全匹配项。 例如，如果将此类字段 f 设置为“sunny day”，则 `$filter=f eq 'sunny'` 将找不到任何匹配项，但 `$filter=f eq 'sunny day'` 可找到。 |  
|“sortable” |默认情况下，系统按分数对结果进行排序，但可以配置基于文档中字段的排序。 `Collection(Edm.String)` 类型的字段不能为“sortable”。 |  
|“facetable” |通常用于包括了按类别（例如特定城市中的宾馆）的命中次数的搜索结果呈现中。 此选项无法与 `Edm.GeographyPoint` 类型的字段一起使用。 `Edm.String` 类型的字段为可筛选，“sortable”或“facetable”字段的长度最多可以是 32 千字节。 有关详细信息，请参阅[创建索引 (REST API)](/rest/api/searchservice/create-index)。|  
|“key” |文档在索引内的唯一标识符。 必须仅选择单个字段作为键字段，并且它必须是 `Edm.String` 类型的。|  
|“retrievable” |决定了是否可以在搜索结果中返回此字段。 当希望将某个字段（例如“利润”）用作筛选器、排序或评分机制，但不希望该字段显示给最终用户时，这很有用。 对于 `key` 字段，此属性必须为 `true`。|  

尽管可以随时添加新字段，但在索引的生存期内现有字段定义将被锁定。 因此，开发人员通常使用门户创建简单索引、测试创意，或者使用门户页面来查找设置。 如果采用基于代码的方式以便可以轻松重新生成索引，那么对索引进行频繁迭代的设计就更为高效。

> [!NOTE]
> 用于生成索引的 API 具有不同的默认行为。 对于 [REST API](/rest/api/searchservice/Create-Index)，大多数属性在默认情况下处于启用状态（例如，“searchable”和“retrievable”对于字符串字段为 true），并且通常只需要在要关闭它们时设置它们。 对于 .NET SDK，情况恰恰相反。 对于未显式设置的任何属性，默认情况下禁用相应的搜索行为，除非你特别启用它。

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>属性和索引大小（存储影响）

索引大小由上传的文档的大小以及索引配置（例如，是否包括建议器，以及如何在各个字段上设置属性）决定。 

以下屏幕截图演示了各种属性组合产生的索引存储模式。 索引基于“房地产示例索引”，你可以使用“导入数据”向导轻松创建此索引。 尽管未显示索引架构，但可以基于索引名称推断属性。 例如，只选择了 realestate-searchable 索引中的“searchable”属性，只选择了 realestate-retrievable 索引中的“retrievable”属性，等等 。

![基于属性选择的索引大小](./media/search-what-is-an-index/realestate-index-size.png "基于属性选择的索引大小")

尽管这些索引变体是人造的，但我们可以参考这些变体来对属性影响存储的方式进行广泛比较。 设置“retrievable”是否会增大索引大小？ 否。 将字段添加到 suggester 是否会增大索引大小？ 可以。 

使字段可筛选或可排序还会增加存储消耗量，因为筛选和排序字段不会进行标记，因此字符序列可以逐字匹配。

也未在上表中反映出 [分析器](search-analyzers.md)的影响。 如果使用 edgeNgram 标记器来存储逐字字符序列 (a、ab、abc、abcd) ，则索引大小将大于使用标准分析器时的大小。

> [!Note]
> 存储体系结构被视为 Azure 认知搜索的实现细节，随时可能在不另行通知的情况下进行更改。 不保证将来仍会保持当前的行为。

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>关于 `corsOptions`

索引架构包含用于设置的部分 `corsOptions` 。 默认情况下，客户端 JavaScript 无法调用任何 API，因为浏览器将阻止所有跨域请求。 若要允许对索引进行跨域查询，请通过设置 **corsOptions** 来启用 CORS（跨域资源共享）。 出于安全原因，只有查询 API 才支持 CORS。 

可为 CORS 设置以下选项：

+ **allowedOrigins**（必需）：这是会被授予索引访问权限的来源的列表。 这意味着，将允许从这些来源提供的任何 JavaScript 代码查询索引（假设它提供正确的 api-key）。 每个来源通常采用 `protocol://<fully-qualified-domain-name>:<port>` 格式，不过往往会省略 `<port>`。 有关更多详细信息，请参阅 [跨域资源共享 (维基百科) ](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 。

  若要允许访问所有来源，请将 `*` 作为单个项目包含在 **allowedOrigins** 数组中。 不建议对生产搜索服务采用这种做法，但它在开发和调试中却很有用。 

+ **maxAgeInSeconds**（可选）：浏览器使用此值确定缓存 CORS 预检响应的持续时间（以秒为单位）。 此值必须是非负整数。 此值越大，性能越好，但 CORS 策略更改生效所需的时间也越长。 如果未设置此值，将使用 5 分钟的默认持续时间。

## <a name="next-steps"></a>后续步骤

您可以使用几乎任何示例或演练认知搜索来获取创建索引的实践体验。 对于初学者，你可以从目录中选择任意快速入门。

但您还希望熟悉用于加载带有数据的索引的方法。 索引定义和数据导入策略在一起定义。 以下文章提供有关加载索引的详细信息。

+ [数据导入概述](search-what-is-data-import.md)

+ [ (REST) 添加、更新或删除文档 ](/rest/api/searchservice/addupdate-or-delete-documents) 