---
title: 创建基本查询
titleSuffix: Azure Cognitive Search
description: 了解如何在认知搜索中构造查询请求、用于测试和编码的工具和 Api，以及查询决策从索引设计开始的方式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371097"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>在 Azure 中创建基本查询认知搜索

本文逐步介绍查询构造。 示例在 REST 中，以便你可以将字符串复制到门户中的 **搜索资源管理器** ，或者使用 Postman 或 Visual Studio code 以交互方式生成查询。 对于本文中的示例，你可以使用任何层或认知搜索版本。

## <a name="choose-a-tool-or-api"></a>选择工具或 API

从以下工具和 Api 中进行选择，以创建用于测试或生产工作负荷的查询。

| 方法 | 说明 |
|-------------|-------------|
| 门户| [搜索资源管理器 (门户) ](search-explorer.md) 提供搜索栏和用于选择索引和 api 版本的选项。 结果会以 JSON 文档的形式返回。 建议用于早期调查、测试和验证。 <br/>[了解详细信息。](search-explorer.md) |
| Web 测试工具| [Postman 或 Visual Studio Code](search-get-started-rest.md) 是用于表述 [搜索文档](/rest/api/searchservice/search-documents) REST 调用的强大选项。 REST API 支持 Azure 认知搜索中的每个编程操作，因此你可以通过交互方式发出请求，以了解在投入使用代码之前，如何工作。  |
| Azure SDK | [SearchClient ( .net) ](/dotnet/api/azure.search.documents.searchclient) 可用于在 c # 中查询搜索索引。  [了解详细信息。](search-howto-dotnet-sdk.md) <br/><br/>可以使用[python) 的 SearchClient (](/dotnet/api/azure.search.documents.searchclient)在 python 中查询搜索索引。 [了解详细信息。](search-get-started-python.md) <br/><br/> [SearchClient (javascript) ](/dotnet/api/azure.search.documents.searchclient) 可用于在 javascript 中查询搜索索引。 [了解详细信息。](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>设置搜索客户端

搜索客户端向搜索服务进行身份验证，发送请求并处理响应。 查询始终定向到单个索引的文档集合。 不能联接索引或者创建自定义或临时数据结构作为查询目标。

### <a name="in-the-portal"></a>在门户中

搜索资源管理器和其他门户工具具有到服务的内置客户端连接，其中包含来自门户页面的直接访问索引和其他对象。 访问 "工具"、"向导" 和 "对象" 时，会假设您拥有服务的管理权限。 借助搜索资源管理器，您可以集中指定搜索字符串和其他参数。 

### <a name="using-rest"></a>使用 REST

对于 REST 调用，你可以使用 [Postman 或类似的工具](search-get-started-rest.md) 作为客户端来指定 [搜索文档](/rest/api/searchservice/search-documents) 请求。 每个请求都是独立的，因此，必须提供终结点 (URL 到服务) ，以及访问管理员或查询 API 密钥。 根据请求，URL 可能还包括索引名称、文档集合和其他属性。 在请求标头上传递几个属性，如 Content 类型和 API 密钥。 可以在 URL 或请求正文中传递其他参数。 所有 REST 调用都需要用于身份验证的 API 密钥和 api 版本。

### <a name="using-azure-sdks"></a>使用 Azure Sdk

Azure Sdk 提供可以保持状态的搜索客户端，从而允许进行连接重用。 对于查询操作，你将实例化一个 SearchClient 并为以下属性提供值： Endpoint、Key、Index。 然后，你可以调用搜索方法来提供查询字符串。 

| 语言 | 客户端 | 示例 |
|----------|--------|---------|
| C # 和 .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [在 C 中发送第一个搜索查询#](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [在 Python 中发送第一个搜索查询](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [在 Java 中发送第一个搜索查询](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| Javascript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [在 JavaScript 中发送第一个搜索查询](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>选择一个分析器：简单 | 完整

Azure 认知搜索提供了两个查询分析器之间的选择，用于处理典型查询和专用查询。 使用简单分析器的请求通常是全文搜索查询，使用 [简单的查询语法](query-simple-syntax.md)进行了表述，并将其选择为默认值，以便其在自由格式文本查询中的速度和有效性。 此语法支持多种常用的搜索运算符，包括 AND、OR、NOT、短语、后缀和优先运算符。

在将 `queryType=full` 添加到请求时所启用的[完整 Lucene 查询语法](query-Lucene-syntax.md#bkmk_syntax)公开作为 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 的一部分开发的、已被广泛采用的且富有表达能力的查询语言。 完整语法扩展了简单语法。 为简单语法编写的任何查询在完整 Lucene 分析器下运行。 

下面的示例说明了 point：相同的查询，但具有不同的 **`queryType`** 设置，这会产生不同的结果。 在第一个查询中，`historic` 后面的 `^3` 被视为搜索字词的一部分。 此查询的排名靠前的结果是 "Marquis Plaza & 套件"，其说明中有 *海洋* 。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

使用完整 Lucene 分析器的同一查询会将 `^3` 解释为字段内字词提升器。 切换分析器会更改排名，并将包含 *historic* 一词的结果移到最前面。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>在索引中启用查询行为

索引设计和查询设计在 Azure 认知搜索中紧密耦合。 具有每个字段的属性的 *索引架构* 确定了您可以生成的查询类型。

字段中的索引属性设置允许的操作 - 字段在索引中是否可搜索、在结果中是否可检索、是否可排序、是否可筛选，等等。 在示例查询中， `"$orderby": "Rating desc"` 只是因为 "分级" 字段在索引架构中标记为可 *排序* 。

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

以上屏幕截图是 [酒店示例索引](search-get-started-portal.md)的索引属性的部分列表。 您可以在门户中创建和查看整个索引架构。 有关索引属性的详细信息，请参阅 [创建索引 (REST API) ](/rest/api/searchservice/create-index)。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何构造请求，请尝试使用简单语法和完整语法的示例。

+ [简单查询示例](search-query-simple-examples.md)
+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)