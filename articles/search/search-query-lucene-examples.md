---
title: 使用完整的 Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 演示在 Azure 认知搜索服务中进行模糊搜索、邻近搜索、术语提升、正则表达式搜索和通配符搜索的 Lucene 查询语法的查询示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693554"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>使用“完整的”Lucene 搜索语法（Azure 认知搜索中的高级查询）

在构造 Azure 认知搜索的查询时，可以将默认的[简单查询分析程序](query-simple-syntax.md)替换为功能更强大的 [Lucene 查询分析程序](query-lucene-syntax.md)，以便构建专用的高级查询表达式。

Lucene 分析程序支持复杂的查询格式，比如字段范围查询、模糊搜索、中缀和后缀通配符搜索、邻近搜索、术语提升以及正则表达式搜索。 额外的功能需遵守额外的处理要求，因此执行时间应该会更长一些。 在本文中，你可以逐步了解一些示例，这些示例演示了基于完整语法的查询操作。

> [!Note]
> 通过完整的 Lucene 查询语法实现的专用查询构造很多都不是[按文本分析的](search-lucene-query-architecture.md#stage-2-lexical-analysis)，所以并不涉及词干分解和词形还原，这一点有些出人意料。 只会对完整字词（字词查询或短语查询）进行词法分析。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对部分查询字词执行的唯一转换操作是转换为小写。 
>

## <a name="hotels-sample-index"></a>酒店示例索引

以下查询基于 hotels-sample-index，你可以按照此[快速入门](search-get-started-portal.md)中的说明进行创建。

示例查询使用 REST API 和 POST 请求来表达。 可以在 [Postman](search-get-started-rest.md) 或 [带认知搜索扩展的 Visual Studio Code](search-get-started-vs-code.md) 中粘贴并运行它们。

请求头必须具有以下值：

| 密钥 | 值 |
|-----|-------|
| Content-Type | application/json|
| api-key  | `<your-search-service-api-key>`，查询或管理密钥 |

URI 参数必须包括具有索引名、文档集合、搜索命令和 API 版本的搜索服务终结点，类似于以下示例：

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

请求正文的格式应为有效的 JSON：

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ “search”设置为 `*` 时，表示一个未指定的查询，等效于 NULL 或空搜索。 它不是特别有用，但却是你可以执行的最简单的搜索，并且会显示索引中所有可检索的字段以及所有值。

+ “queryType”设置为“完整”时，会调用完整的 Lucene 查询分析程序，它是此语法的必需参数。

+ “select”设置为以逗号分隔的字段列表时，可用于搜索结果组合，使其只包括在搜索结果上下文中有用的字段。

+ “count”返回与搜索条件匹配的文档数。 对于空搜索字符串，计数将是索引中的所有文档数（在 hotels-sample-index 示例中，数量为 50 个）。

## <a name="example-1-fielded-search"></a>示例 1：字段化搜索

字段化搜索将单个嵌入式搜索表达式的范围限定为特定字段。 此示例搜索包含字词“hotel”的酒店名称，而不是“motel”。 可以使用 AND 指定多个字段。 

使用此查询语法时，当要查询的字段包含于搜索表达式本身时，可以省略“searchFields”参数。 如果在字段化搜索中使用“searchFields”，则 `fieldName:searchExpression` 会始终优先于“searchFields”。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

对此查询的响应应类似于以下示例，根据“Resort and Spa”进行筛选，会返回名称中包含“hotel”或“motel”的酒店。

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

搜索表达式可以是单个字词或短语，也可以是用括号括起来的更复杂的表达式，其中可以选择使用布尔运算符。 下面是部分示例：

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

如果想要两个字符串评估为单个实体，请务必将短语放置在引号内，正如这个在 Address/StateProvince 字段中搜索两个不同位置的情况一样。 你可能需要对引号进行转义 (`\`)，具体取决于客户端。

在 `fieldName:searchExpression` 中指定的字段必须是可搜索的字段。 有关如何特性化字段定义的详细信息，请参阅[创建索引 (REST API)](/rest/api/searchservice/create-index)。

## <a name="example-2-fuzzy-search"></a>示例 2：模糊搜索

模糊搜索可匹配类似的字词，包括拼写错误的字词。 若要执行模糊搜索，请在单个字词的末尾追加“`~`”波形符，后跟指定编辑距离的可选参数（介于 0 到 2 之间的值）。 例如，`blue~` 或 `blue~1` 会返回 blue、blues 和 glue。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

对此查询的响应会解析为匹配文档中的“concierge”，为简洁起见只截取了一部分：

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

不直接支持短语，但你可以基于多部件短语的每个字词指定一个模糊匹配，例如 `search=Tags:landy~ AND sevic~`。  此查询表达式根据“laundry service”查找到 15 个匹配项。

> [!Note]
> 不会对模糊查询进行[分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对部分查询字词执行的唯一转换操作是转换为小写。
>

## <a name="example-3-proximity-search"></a>示例 3：邻近搜索

邻近搜索会查找在文档中相互靠近的字词。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。

此查询将搜索文档中彼此相距 5 个单词以内的字词“hotel”和“airport”。 引号经过转义 (`\"`)，以保留短语：

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

此查询的响应应与以下示例类似：

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>示例 4：术语提升

术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 若要提升字词，请使用插入符号 `^`，并且所搜索字词末尾还要附加提升系数（数字）。 提升系数默认为 1，虽然它必须是正数，但可以小于 1（例如 0.2）。 术语提升不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。

在“before”查询中，搜索“beach access”，你会注意到有七个文档匹配一个或两个字词。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

事实上，只有一个文档匹配“access”，因为它是唯一匹配项，所以即使该文档缺少字词“beach”，它的位置依然靠前（第二位）。

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

在“after”查询中，重试该搜索，此时会提升包含字词“beach”而非“access”的结果。 查询的人工可读版本为 `search=Description:beach^2 access`。 根据你的客户端，可能需要将 `^2` 表达为 `%5E2`。

提升字词“beach”后，“Old Carrabelle Hotel”匹配项向下移动到第六位。

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>示例 5：正则表达式

正则表达式搜索基于正斜杠“/”之间的内容查找匹配项，如在 [RegExp 类](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所记录的那样。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

此查询的响应应与以下示例类似：

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> 不会对正则表达式查询进行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 对部分查询字词执行的唯一转换操作是转换为小写。
>

## <a name="example-6-wildcard-search"></a>示例 6：通配符搜索

可将通常可识别的语法用于多个 (`*`) 或单个 (`?`) 字符通配符搜索。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

在此查询中，搜索包含前缀“sc”的酒店名称。 不能将 `*` 或 `?` 符号用作搜索的第一个字符。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

此查询的响应应与以下示例类似：

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> 不会对通配符查询进行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 对部分查询字词执行的唯一转换操作是转换为小写。
>

## <a name="next-steps"></a>后续步骤

尝试在代码中指定查询。 以下链接介绍了如何使用 Azure SDK 设置搜索查询。

+ [使用 .NET SDK 查询索引](search-get-started-dotnet.md)
+ [使用 Python SDK 查询索引](search-get-started-python.md)
+ [使用 JavaScript SDK 查询索引](search-get-started-javascript.md)

可在以下链接找到其他语法参考、查询体系结构和示例：

+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单的查询语法](query-simple-syntax.md)
+ [完整 Lucene 查询语法](query-lucene-syntax.md)
+ [筛选器语法](search-query-odata-filter.md)