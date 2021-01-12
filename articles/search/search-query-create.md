---
title: 创建基本查询
titleSuffix: Azure Cognitive Search
description: 了解如何在认知搜索中构造查询请求、用于测试和编码的工具和 Api，以及查询决策从索引设计开始的方式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 9bee391ddb0fa6c270c6d833fb7e81d5f4880497
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118636"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>在 Azure 中创建查询认知搜索

如果是第一次生成查询，本文将介绍所需的工具和 Api、用于创建查询的方法，以及索引结构和内容如何影响查询结果。 有关查询请求的外观介绍，请从 [查询类型和组合](search-query-overview.md)开始。

## <a name="choose-tools-and-apis"></a>选择工具和 Api

需要使用工具或 API 来创建查询。 以下任意建议可用于测试和生产工作负荷。

| 方法 | 说明 |
|-------------|-------------|
| 门户| [搜索资源管理器 (门户) ](search-explorer.md) 是 Azure 门户中的查询接口，用于对基础搜索服务中的索引运行查询。 门户会在后台对 [搜索文档](/rest/api/searchservice/search-documents) 操作进行 REST API 调用，但无法调用自动完成、建议或文档查找。<br/><br/> 您可以选择任何索引和 REST API 版本，包括预览。 查询字符串可以使用简单或完整语法，并支持所有查询参数 (filter、select、searchFields 等) 。 在门户中，打开索引时，可以在并排选项卡中使用 "搜索资源管理器" 和 "索引 JSON 定义" 来轻松访问字段属性。 在测试查询时检查哪些字段可搜索、可排序、可筛选和可查找。 <br/>建议用于早期调查、测试和验证。 [了解详细信息。](search-explorer.md) |
| Web 测试工具| [Postman](search-get-started-rest.md) 或 [Visual Studio Code](search-get-started-vs-code.md) 是用于在 REST 上构建 [搜索文档](/rest/api/searchservice/search-documents) 请求和任何其他请求的强选项。 REST Api 支持 Azure 认知搜索中每个可能的编程操作，并且当你使用 Postman 或 Visual Studio Code 之类的工具时，你可以通过交互方式发出请求，以了解该功能在投入使用之前的工作方式。 如果在 Azure 门户中没有参与者或管理权限，则 web 测试工具是一个不错的选择。 只要有搜索 URL 和查询 API 密钥，就可以使用这些工具对现有索引运行查询。 |
| Azure SDK | 准备好编写代码时，可以在适用于 .NET、Python、JavaScript 或 Java 的 Azure Sdk 中使用 Azure.Search.Docargumentable 客户端库。 每个 SDK 都具有自己的发布计划，但你可以在其中创建和查询索引。 <br/><br/>[SearchClient ( .net) ](/dotnet/api/azure.search.documents.searchclient) 可用于在 c # 中查询搜索索引。  [了解详细信息。](search-howto-dotnet-sdk.md)<br/><br/>可以使用[python) 的 SearchClient (](/dotnet/api/azure.search.documents.searchclient)在 python 中查询搜索索引。 [了解详细信息。](search-get-started-python.md)<br/><br/>[SearchClient (javascript) ](/dotnet/api/azure.search.documents.searchclient) 可用于在 javascript 中查询搜索索引。 [了解详细信息。](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>设置搜索客户端

搜索客户端向搜索服务进行身份验证，发送请求并处理响应。 无论使用哪个工具或 API，搜索客户端都必须具有以下各项：

| 属性 | 说明 |
|------------|-------------|
| 端点 | 搜索服务是按以下格式寻址的 URL： `https://[service-name].search.windows.net` 。 |
| API 访问密钥 (管理或查询)  | 对搜索服务的请求进行身份验证。 |
| 索引名称 | 查询始终定向到单个索引的文档集合。 不能联接索引或者创建自定义或临时数据结构作为查询目标。 |
| API 版本 | REST 调用显式要求 `api-version` 请求中的。 与此相反，Azure SDK 中的客户端库是针对特定的 REST API 版本进行版本控制的。 对于 Sdk， `api-version` 是隐式的。 |

### <a name="in-the-portal"></a>在门户中

搜索资源管理器和其他门户工具具有到服务的内置客户端连接，其中包含来自门户页面的直接访问索引和其他对象。 访问工具、向导和对象需要服务上的 "参与者" 角色的成员身份或更高权限。 

### <a name="using-rest"></a>使用 REST

对于 REST 调用，你可以使用 [Postman 或类似的工具](search-get-started-rest.md) 作为客户端来指定 [搜索文档](/rest/api/searchservice/search-documents) 请求。 每个请求都是独立的，因此你必须提供每个请求的终结点、索引名称和 API 版本。 其他属性、内容类型和 API 密钥在请求标头上传递。 

您可以使用 POST 或 GET 来查询索引。 POST，其中包含请求正文中指定的参数，更易于使用。 如果你使用 POST，请确保包含 `docs/search` 在 URL 中：

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>使用 Azure Sdk

如果使用的是 Azure SDK，请在代码中创建客户端。 所有 Sdk 都提供可保持状态的搜索客户端，从而允许进行连接重用。 对于查询操作，你将实例化 **`SearchClient`** 并为以下属性指定值： Endpoint、Key、Index。 然后，可以调用 **`Search method`** 来传入查询字符串。 

| 语言 | 客户端 | 示例 |
|----------|--------|---------|
| C # 和 .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [在 C 中发送第一个搜索查询#](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [在 Python 中发送第一个搜索查询](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [在 Java 中发送第一个搜索查询](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [在 JavaScript 中发送第一个搜索查询](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>选择一个分析器：简单 | 完整

如果查询是全文搜索，则将使用分析器来处理搜索参数的内容。 Azure 认知搜索提供两个查询分析。 简单分析器理解 [简单查询语法](query-simple-syntax.md)。 此分析器被选为默认值，以便其在自由格式文本查询中的速度和有效性。 语法支持 (AND、OR、NOT) 用于术语和短语搜索，并为 `*` 西雅图和 Seaside (的 "海平面 *") 搜索) 提供 (前缀。 一般的建议是先尝试简单分析器，然后在应用程序要求调用更强大的查询时继续使用完整分析器。

在添加到请求时启用的 [完整 Lucene 查询语法](query-Lucene-syntax.md#bkmk_syntax) `queryType=full` 基于 [Apache Lucene 分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

完全语法和简单语法与支持相同的前缀和布尔运算的范围重叠，但完整语法提供了更多运算符。 完全地而言，有更多运算符用于布尔表达式，还有更多运算符用于查找模糊搜索、通配符搜索、邻近搜索和正则表达式等高级查询。

## <a name="choose-query-methods"></a>选择查询方法

搜索本质上是用户驱动的练习，其中的词或短语是从搜索框中收集的，或者是从某个页面上的单击事件中收集的。 下表总结了可用于收集用户输入的机制以及预期的搜索体验。

| 输入 | 体验 |
|-------|---------|
| [搜索方法](/rest/api/searchservice/search-documents) | 用户在搜索框中键入字词或短语（带有或不带运算符），然后单击 "搜索" 以发送请求。 搜索可与筛选器一起用于相同请求，但不能与自动完成或建议一起使用。 |
| [自动完成方法](/rest/api/searchservice/autocomplete) | 用户键入几个字符，并在键入每个新字符后启动查询。 响应是索引中的已完成字符串。 如果提供的字符串有效，用户可单击 "搜索" 将该查询发送到服务。 |
| [建议方法](/rest/api/searchservice/suggestions) | 与 "自动完成" 一样，用户键入几个字符，而增量查询会生成。 响应是匹配文档的下拉列表，通常由几个唯一或描述性字段表示。 如果选择有效，则用户单击其中一个，然后返回匹配的文档。 |
| [多面导航](/rest/api/searchservice/search-documents#query-parameters) | 页面将显示可点击的导航链接或可缩小搜索范围的痕迹导航。 分面导航结构根据初始查询动态组合。 例如， `search=*` 要填充分面导航树，其中包含每个可能的类别。 分面导航结构由查询响应创建，但它也是用于表示下一查询的机制。 n REST API 引用， `facets` 记录为搜索文档操作的查询参数，但它可以在没有参数的情况下使用 `search` 。|
| [筛选器方法](/rest/api/searchservice/search-documents#query-parameters) | 筛选器与方面一起用于缩小结果范围。 您还可以实现页面后的筛选器，例如，使用特定于语言的字段初始化页面。 在 REST API 引用中， `$filter` 记录为搜索文档操作的查询参数，但它可以在没有参数的情况下使用 `search` 。|

## <a name="know-your-field-attributes"></a>了解字段特性

如果你以前查看过 [查询请求的基础知识](search-query-overview.md)，你可能会注意到，查询请求上的参数取决于字段在索引中的特性。 例如，若要在查询、筛选或排序顺序中使用，字段必须是可 *搜索*、可 *筛选* 和可 *排序* 的字段。 同样，只有标记为可 *检索* 的字段才能出现在结果中。 当你开始 `search` `filter` 在请求中指定、和 `orderby` 参数时，请确保在执行时检查属性，以避免意外结果。

在门户的 " [酒店示例" 索引](search-get-started-portal.md)下面的屏幕截图中，只可在唯一的子句中使用最后两个字段 "LastRenovationDate" 和 "分级" `"$orderby"` 。

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

有关字段属性的说明，请参阅 [创建索引 (REST API) ](/rest/api/searchservice/create-index)。

## <a name="know-your-tokens"></a>了解令牌

在编制索引期间，查询引擎使用分析器来对字符串执行文本分析，从而最大程度地提高查询时的匹配可能性。 字符串至少采用小写，但可能还会经历词形还原并停止单词删除。 较大的字符串或组合词通常由空格、连字符或短划线分割，并索引为单独的标记。 

接下来，需要注意的是，您的索引包含的内容以及其中的实际内容可能不同。 如果查询未返回预期的结果，则可以通过 " [分析文本" (REST API) ](/rest/api/searchservice/test-analyzer)检查分析器创建的令牌。 有关标记化和对查询的影响的详细信息，请参阅 [包含特殊字符的部分术语搜索和模式](search-query-partial-matching.md)。

## <a name="next-steps"></a>后续步骤

既然您已经更好地了解了查询请求的构造方式，接下来请尝试以下快速入门教程。

+ [搜索资源管理器](search-explorer.md)
+ [如何在 REST 中进行查询](search-get-started-rest.md)
+ [如何在 .NET 中进行查询](search-get-started-dotnet.md)
+ [如何在 Python 中查询](search-get-started-python.md)
+ [如何在 JavaScript 中查询](search-get-started-javascript.md)