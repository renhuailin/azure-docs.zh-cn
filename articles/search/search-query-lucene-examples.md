---
title: 使用完整的 Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 演示了用于模糊搜索、邻近搜索、字词提升、正则表达式搜索和通配符认知搜索搜索的查询示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693554"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>使用“完整的”Lucene 搜索语法（Azure 认知搜索中的高级查询）

构造 Azure 认知搜索的查询时，可以将默认的 [简单查询分析器](query-simple-syntax.md) 替换为功能更强大的 [Lucene 查询分析器](query-lucene-syntax.md) ，以表述专用查询表达式和高级查询表达式。

Lucene 分析器支持复杂的查询格式，如字段范围查询、模糊搜索、中缀和后缀通配符搜索、邻近搜索、术语提升和正则表达式搜索。 额外的功能需遵守额外的处理要求，因此执行时间应该会更长一些。 在本文中，你可以逐步了解一些示例，这些示例演示了基于完整语法的查询操作。

> [!Note]
> 通过完整的 Lucene 查询语法实现的专用查询构造很多都不是[按文本分析的](search-lucene-query-architecture.md#stage-2-lexical-analysis)，所以并不涉及词干分解和词形还原，这一点有些出人意料。 只会对完整字词（字词查询或短语查询）进行词法分析。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对部分查询字词执行的唯一转换操作是转换为小写。 
>

## <a name="hotels-sample-index"></a>酒店样品索引

以下查询基于宾馆示例索引，你可以按照本 [快速入门](search-get-started-portal.md)中的说明进行创建。

示例查询使用 REST API 和 POST 请求进行表述。 您可以在 [Postman](search-get-started-rest.md) 或 [具有认知搜索扩展名的 Visual Studio Code](search-get-started-vs-code.md)中粘贴并运行它们。

请求头必须具有以下值：

| 密钥 | 值 |
|-----|-------|
| Content-Type | application/json|
| api-key  | `<your-search-service-api-key>`，查询或管理密钥 |

URI 参数必须包括具有索引名称、文档集合、搜索命令和 API 版本的搜索服务终结点，类似于以下示例：

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

+ "搜索" 设置为 `*` 一个未指定的查询，等效于 null 或空搜索。 它不是特别有用，但却是你可以执行的最简单的搜索，并且会显示索引中所有可检索的字段以及所有值。

+ 如果将 "queryType" 设置为 "full"，则会调用完整的 Lucene 查询分析器，此语法需要此方法。

+ "select" 设置为以逗号分隔的字段列表，可用于搜索结果组合，包括在搜索结果上下文中有用的字段。

+ "计数" 返回与搜索条件匹配的文档数。 在空搜索字符串中，计数将是索引中的所有文档 (50) 的情况。

## <a name="example-1-fielded-search"></a>示例1：现场搜索

现场搜索范围查找特定字段的个人嵌入搜索表达式。 此示例将在其中搜索带有 "酒店" 一词的酒店名称，而不是 "motel"。 可以使用 AND 指定多个字段。 

使用此查询语法时，当要查询的字段位于搜索表达式本身时，可以省略 "searchFields" 参数。 如果将 "searchFields" 与现场搜索一起使用，则 `fieldName:searchExpression` 始终优先于 "searchFields"。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

此查询的响应应类似于以下示例，筛选依据 "滑雪场 and Spa"，返回名称中包含 "宾馆" 或 "motel" 的酒店。

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

搜索表达式可以是单个字词或短语，也可以是括号中更复杂的表达式，可以选择使用布尔运算符。 下面是部分示例：

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

如果希望将这两个字符串作为单个实体进行计算，请确保将该短语放入引号中，如在 Address/StateProvince 字段中搜索两个不同的位置。 根据客户端，可能需要将 (`\`) 转义。

中指定的字段 `fieldName:searchExpression` 必须是可搜索字段。 有关如何特性化字段定义的详细信息，请参阅 [创建索引 (REST API) ](/rest/api/searchservice/create-index) 。

## <a name="example-2-fuzzy-search"></a>示例2：模糊搜索

模糊搜索可匹配类似的术语，包括拼写错误的单词。 若要执行模糊搜索，请在单个字词的末尾追加“`~`”波形符，后跟指定编辑距离的可选参数（介于 0 到 2 之间的值）。 例如，`blue~` 或 `blue~1` 会返回 blue、blues 和 glue。

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

此查询的响应解析为匹配文档中的 "接待"，为简洁起见进行了修整：

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

不直接支持短语，但你可以基于多部件短语的每个字词指定一个模糊匹配，例如 `search=Tags:landy~ AND sevic~`。  此查询表达式在 "细目服务" 中找到了15个匹配项。

> [!Note]
> 不会对模糊查询进行[分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对部分查询字词执行的唯一转换是较低的大小写。
>

## <a name="example-3-proximity-search"></a>示例3：邻近搜索

邻近搜索查找文档中彼此相邻的字词。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。

此查询在文档中搜索每个术语的5个字以内的术语 "酒店" 和 "机场"。 引号被转义 (`\"`) 以保留短语：

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

此查询的响应应类似于以下示例：

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

## <a name="example-4-term-boosting"></a>示例4：字词提升

术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 若要提升字词，请使用插入符号 `^`，并且所搜索字词末尾还要附加提升系数（数字）。 提升因子默认值为1，但它必须是正数，例如 0.2) ，它可以小于 1 (。 术语提升不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。

在此 "之前" 查询中，搜索 "浮水访问"，请注意，有七个文档与一个或两个词匹配。

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

事实上，仅有一个文档在 "访问" 中匹配，因为它是唯一匹配项，所以它的位置 (第二个位置) ，即使该文档缺少术语 "海滩" 也是如此。

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

在 "之后" 查询中，重复搜索，这一次是在 "访问" 一词上提升术语 "海滩" 的结果。 查询的人工可读版本为 `search=Description:beach^2 access`。 根据你的客户端，可能需要将 express `^2` 视为 `%5E2` 。

在提升 "海滩" 一词后，旧 Carrabelle 宾馆上的匹配将向下移动到第六个位置。

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>示例5： Regex

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

此查询的响应应类似于以下示例：

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
> 不会对正则表达式查询进行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 对部分查询字词执行的唯一转换是较低的大小写。
>

## <a name="example-6-wildcard-search"></a>示例6：通配符搜索

可将通常可识别的语法用于多个 (`*`) 或单个 (`?`) 字符通配符搜索。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

在此查询中，搜索包含前缀 "sc" 的酒店名称。 不能将 `*` 或 `?` 符号用作搜索的第一个字符。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

此查询的响应应类似于以下示例：

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
> 不会对通配符查询进行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 对部分查询字词执行的唯一转换是较低的大小写。
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