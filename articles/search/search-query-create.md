---
title: 创建查询
titleSuffix: Azure Cognitive Search
description: 了解如何在认知搜索中构造查询请求，使用哪些工具和 API 进行测试和编码，以及查询决策如何从索引设计开始。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 53428a0185b003e22fd0ad68001b2b1588f994b1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750742"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>在 Azure 认知搜索中创建查询

如果你是第一次生成查询，本文介绍了用于设置查询的方法。 还介绍了查询请求，并说明了字段属性和语言分析器如何影响查询结果。

## <a name="whats-a-query-request"></a>什么是查询请求？

查询是针对单个搜索索引的文档集合的只读请求。 它指定“search”参数，其中包含由字词、括在引号中的短语和运算符组成的查询表达式。

请求的其他参数提供对查询和响应的更多定义。 例如，“searchFields”将查询执行限制为特定字段，“select”指定在结果中返回的字段，“count”返回在索引中找到的匹配项数。

以下示例通过显示一些可用参数，使你全面了解查询请求。 有关查询组合的详细信息，请参阅[查询类型和组合](search-query-overview.md)和[搜索文档 (REST)](/rest/api/searchservice/search-documents)。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>选择客户端

你将需要使用 Azure 门户或 Postman 等工具，或者使用 API 实例化查询客户端的代码。 建议使用 Azure 门户或 REST API 进行早期开发和概念证明测试。

### <a name="permissions"></a>权限

查询请求需要通过标头中传递的 API 密钥授予的读取权限。 任何操作（包括查询请求）都将在[管理 API 密钥](search-security-api-keys.md)下执行，但查询请求可以选择使用[查询 API 密钥](search-security-api-keys.md#create-query-keys)。 强烈建议使用查询 API 密钥。 最多可为每个服务创建 50 个，并为不同的应用程序分配不同的密钥。

在 Azure 门户中，访问工具、向导和对象需要具有服务的参与者角色或更高级别的成员身份。 

### <a name="use-azure-portal-to-query-an-index"></a>使用 Azure 门户查询索引

[搜索资源管理器（门户）](search-explorer.md)是 Azure 门户中的一个查询界面，用于针对基础搜索服务上的索引运行查询。 在内部，门户会发出[搜索文档](/rest/api/searchservice/search-documents)请求，但无法调用自动完成、建议或文档查找功能。 

你可以选择任何索引和 REST API 版本（包括预览版）。 查询字符串可以使用简单语法或完整语法，并支持所有查询参数（filter、select 和 searchFields 等）。 在门户中，打开索引时，可以使用搜索资源管理器以及并排选项卡中的索引 JSON 定义，以便轻松访问字段属性。 在测试查询时，检查哪些字段可搜索、可排序、可筛选和可分面。

### <a name="use-a-rest-client"></a>使用 REST 客户端

Postman 和 Visual Studio Code（带有 Azure 认知搜索的扩展）都可以充当查询客户端。 使用任一工具都可以连接到搜索服务，并发送[搜索文档 (REST)](/rest/api/searchservice/search-documents) 请求。 许多教程和示例演示了用于查询索引的 REST 客户端。 

请从以下任一文章开始阅读，了解每个客户端（均包括查询说明）：

+ [使用 REST 和 Postman 创建搜索索引](search-get-started-rest.md)
+ [Visual Studio Code 和 Azure 认知搜索入门](search-get-started-vs-code.md)

每个请求都是独立的，因此必须在每个请求中提供终结点、索引名称和 API 版本。 其他属性（内容类型和 API 密钥）通过请求头传递。 有关详细信息，请参阅[搜索文档 (REST)](/rest/api/searchservice/search-documents) 以获取有关表述查询请求的帮助。

### <a name="use-an-sdk"></a>使用 SDK

对于认知搜索，Azure SDK 实现了正式发布的功能。 因此，你可以使用任何 SDK 来查询索引。 所有 SDK 都提供了 SearchClient，其中包含与索引进行交互的方法（从使用搜索文档加载索引到表述查询请求）。

| Azure SDK | 客户端 | 示例 |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| Javascript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | Pending。 |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>选择查询类型：简单 | 完整

如果查询是全文搜索，则查询分析器将用于处理作为搜索词和短语传递的任何文本。Azure 认知搜索提供两个查询分析器。 

+ 简单分析程序理解[简单查询语法](query-simple-syntax.md)。 选择此分析程序作为默认分析程序是因为它在自由格式文本查询中的速度和有效性。 该语法支持将常见搜索运算符（AND、OR、NOT）用于术语和短语搜索，以及前缀 (`*`) 搜索（例如，使用“sea*”搜索 Seattle 和 Seaside）。 一般建议首先尝试使用简单版分析程序，如果应用程序需要更强大的查询，则可以改用完整版分析程序。

+ 向请求添加 `queryType=full` 时启用的[完整 Lucene 查询语法](query-Lucene-syntax.md#bkmk_syntax)基于 [Apache Lucene 分析程序](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

虽然完整语法和简单语法存在重叠，都支持相同的前缀和布尔操作，但完整语法提供了更多的操作符。 在完整语法中，布尔表达式有了更多运算符，高级查询（如模糊搜索、通配符搜索、邻近搜索和正则表达式）也有了更多运算符。

## <a name="choose-query-methods"></a>选择查询方法

搜索本质上是一个用户驱动的活动，该活动从搜索框或页面上的单击事件中收集术语或短语。 下表总结了收集用户输入的机制以及预期的搜索体验。

| 输入 | 体验 |
|-------|---------|
| [搜索方法](/rest/api/searchservice/search-documents) | 用户在搜索框中键入术语或短语（带或不带运算符），然后单击“搜索”发送请求。 搜索可与筛选器一起用于同一请求，但不能与自动完成或建议一起使用。 |
| [“自动完成”方法](/rest/api/searchservice/autocomplete) | 用户键入几个字符，然后在键入每个新字符后启动查询。 响应是索引中的已完成字符串。 如果提供的字符串有效，则用户单击“搜索”将该查询发送到服务。 |
| [“建议”方法](/rest/api/searchservice/suggestions) | 与自动完成一样，用户键入几个字符并生成增量查询。 响应是匹配文档的下拉列表，通常由几个唯一或描述性字段表示。 如果任何一个选择有效，用户单击其中一个，就会返回匹配的文档。 |
| [多面导航](/rest/api/searchservice/search-documents#query-parameters) | 页面显示可单击的导航链接或导航痕迹，缩小了搜索范围。 分面导航结构基于初始查询动态组合。 例如，`search=*` 可用于填充由每个可能的类别组成的分面导航树。 分面导航结构是从查询响应创建的，但它也是表示下一个查询的机制。 在 REST API 引用中，`facets` 作为“搜索文档”操作的查询参数记录，但是它可以在没有 `search` 参数的情况下使用。|
| [筛选器方法](/rest/api/searchservice/search-documents#query-parameters) | 筛选器与分面一起使用，以缩小结果范围。 你还可以在页面后面实现一个筛选器，例如用特定于语言的字段初始化页面。 在 REST API 引用中，`$filter` 作为“搜索文档”操作的查询参数记录，但是它可以在没有 `search` 参数的情况下使用。|

## <a name="know-your-field-attributes"></a>了解字段属性

如果之前回顾过[查询类型和组合](search-query-overview.md)，则可能还记得查询请求上的参数取决于字段在索引中的属性。 例如，要在查询、筛选或排序顺序中使用，字段必须可搜索、可筛选和可排序  。 同样，只有标记为“可检索”的字段才能出现在结果中。 开始在请求中指定 `search`、`filter` 和 `orderby` 参数时，请确保检查属性以避免出现意外的结果。

在下面的[酒店示例索引](search-get-started-portal.md)门户屏幕截图中，只有最后两个字段“LastRenovationDate”和“Rating”可用于 `"$orderby"` only 子句。

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

有关字段属性的描述，请参阅[创建索引 (REST API)](/rest/api/searchservice/create-index)。

## <a name="know-your-tokens"></a>了解标记

在编制索引期间，搜索引擎使用分析器对字符串执行文本分析，最大限度地提高查询时匹配的可能性。 字符串至少是小写的，但也可能需要进行词形还原和停用词删除。 较大的字符串或复合词通常由空格、连字符或短划线分隔，并作为单独的标记编制索引。 

这里要注意的一点是，你认为索引包含的内容和索引实际包含的内容可能会有所不同。 如果查询没有返回预期的结果，则可以通过[分析文本 (REST API)](/rest/api/searchservice/test-analyzer) 检查分析器创建的标记。 有关词汇切分及其对查询的影响的详细信息，请参阅[具有特殊字符的部分术语搜索和模式](search-query-partial-matching.md)。

## <a name="next-steps"></a>后续步骤

现在你已经更好地了解了查询请求的工作原理，可以尝试以下快速入门以获得实际操作经验。

+ [搜索资源管理器](search-explorer.md)
+ [如何在 REST 中进行查询](search-get-started-rest.md)
+ [如何在 .NET 中进行查询](search-get-started-dotnet.md)
+ [如何用 Python 查询](search-get-started-python.md)
+ [如何用 JavaScript 查询](search-get-started-javascript.md)