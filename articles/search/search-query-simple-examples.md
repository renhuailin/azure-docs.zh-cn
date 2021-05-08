---
title: 使用简单的 Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 演示对 Azure 认知搜索索引进行全文搜索、筛选搜索和地理搜索的简单语法的查询示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694030"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>在 Azure 认知搜索中使用“简单”搜索语法

在 Azure 认知搜索中，[简单查询语法](query-simple-syntax.md)调用默认查询分析程序来执行全文搜索。 此分析程序速度快，处理对象是全文搜索、筛选及分面搜索和前缀搜索等常见方案。 本文使用示例来说明[搜索文档 (REST API)](/rest/api/searchservice/search-documents) 请求中的简单语法用法。

> [!NOTE]
> 另一种查询语法是[完整的 Lucene](query-lucene-syntax.md)，它支持模糊搜索和通配符搜索等更复杂的查询结构。 有关详细信息和示例，请参阅[使用完整的 Lucene 语法](search-query-lucene-examples.md)。

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ “search”设置为 `*` 时，表示一个未指定的查询，等效于 NULL 或空搜索。 它不是特别有用，但却是你可以执行的最简单的搜索，并且会显示索引中所有可检索的字段以及所有值。

+ 默认情况下，“queryType”设置为“simple”，可以省略，但包含它可以进一步强调本文中的查询示例是采用简单语法来表达的。

+ “select”设置为以逗号分隔的字段列表时，可用于搜索结果组合，使其只包括在搜索结果上下文中有用的字段。

+ “count”返回与搜索条件匹配的文档数。 对于空搜索字符串，计数将是索引中的所有文档数（在 hotels-sample-index 示例中，数量为 50 个）。

## <a name="example-1-full-text-search"></a>示例 1：全文搜索

全文搜索可以是任意数量的独立词语或引号括起来的短语，其中包含或不包含布尔运算符。 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

由重要词语或短语组成的关键字搜索往往最适用。 在索引和查询期间，会对字符串字段进行文本分析，删除不必要的词，例如“the”、“and”和“it”。 若要查看如何在索引中标记化查询字符串，请在[分析文本](/rest/api/searchservice/test-analyzer)调用中将字符串传递给索引。

“SearchMode”参数可控制精准率和召回率。 如果需要更高召回率，请使用默认值“any”，即如果匹配查询字符串的任何部分，则将返回结果。 如果选择精准率，即必须完全匹配字符串，则将 searchMode 更改为“all”。 请尝试上述两种查询方法，了解 searchMode 如何改变结果。

对“pool spa +airport”查询的响应应类似于下面的示例，为简洁起见只截取了一部分。

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

请注意响应中的搜索分数。 这是匹配项的相关性分数。 默认情况下，搜索服务将基于此分数返回前 50 个匹配项。

当因为搜索不是全文搜索或者因为没有提供任何条件而没有排序时，会出现统一的分数“1.0”。 例如，在空搜索（搜索=`*`）中，行会按任意顺序返回。 包括实际条件时，能看到搜索分数变成有意义的值。

## <a name="example-2-look-up-by-id"></a>示例 2：按 ID 查找

在查询中返回搜索结果时，逻辑下一步是提供包含文档中的更多字段的详细信息页。 此示例展示了如何使用[查找文档](/rest/api/searchservice/lookup-document)通过传入文档 ID 来返回单个文档。

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

所有文档都有一个唯一标识符。 如果要使用门户，请从“索引”选项卡选择索引，然后查看字段定义，以确定键字段。 通过使用 REST，[获取索引](/rest/api/searchservice/get-index)调用会在响应正文中返回索引定义。

对上述查询的响应中包含键为 41 的文档。 在索引定义中标记为“retrievable”的任何字段都可以在搜索结果中返回并在应用中呈现。

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>示例 3：按文本筛选

[筛选器语法](search-query-odata-filter.md)是可以单独使用或配合“search”使用的 OData 表达式。 如果一起使用，则会先对整个索引应用“filter”，再对筛选结果执行搜索。 filter 可减少 search 查询需要处理的文档集，因此是一种非常有用的技术，可用于提高查询性能。

可对索引定义中标记为“filterable”的任何字段定义筛选器。 对于 hotels-sample-index，可筛选字段包括类别、标记、ParkingIncluded、分级和大多数地址字段。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

对上述查询的响应仅限于“Report and Spa”类别且包含“art”或“tours”词语的酒店。 在这种情况下，只有一个匹配项。

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>示例 4：筛选器函数

筛选器表达式可以包含[“search.ismatch”和“search.ismatchscoring”函数](search-query-odata-full-text-search-functions.md)，便于在筛选器中构建搜索查询。 此筛选器表达式在 free 上使用通配符，以选择免费 Wi-Fi、免费停车等设施。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

对上述查询的响应匹配了 19 家提供免费设施的酒店。 请注意，所有结果的搜索分数都是“1.0”。 这是因为搜索表达式为 NULL 或为空，导致逐字筛选器匹配，但未进行全文搜索。 仅全文搜索时返回相关性分数。 如果要使用无“search”的筛选器，请确保具有足够的可排序字段，以便控制搜索排序。

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>示例 5：范围筛选器

通过任何数据类型的筛选器表达式支持范围筛选。 下面的示例演示数值和字符串范围。 数据类型在范围筛选器中很重要，当数字数据位于数字字段且字符串数据位于字符串字段中时效果最佳。 由于数字字符串不可比较，因此字符串字段中的数字数据不适用于范围。

以下查询是一个数字范围。 在 hotels-sample-index 中，唯一可筛选的数字字段为“分级”。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

对此查询的响应应类似于下面的示例，为简洁起见只截取了一部分。

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

接下来的查询是基于字符串字段 (Address/StateProvince) 的范围筛选器：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

对此查询的响应应类似于下面的示例，为简洁起见只截取了一部分。 在此示例中，不能按 StateProvince 排序，因为此字段在索引定义中的属性并非“可排序的”。

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>示例 6：地理搜索

hotels-sample index 包含带有纬度和经度坐标的 geo_location 字段。 此示例使用 [geo.distance 函数](search-query-odata-geo-spatial-functions.md#examples)来筛选从起点开始，直到所提供的任意距离（以公里为单位）圆周范围内的文档。 可以调整查询 (10) 中的最后一个值，以缩小或放大查询的表面积。

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

对此查询的响应会返回距所提供坐标 10 公里内的所有酒店：

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>示例 7：使用 searchMode 的布尔值

简单语法支持字符形式 (`+, -, |`) 的布尔运算符，从而支持 AND、OR 和 NOT 查询逻辑。 布尔型搜索的行为符合预期，但有几个值得注意的例外情况。 

在前面的示例中，引入了“searchMode”参数作为影响精准率和召回率的机制，使用“searchMode = any”选择召回率（满足任一条件的文档即视为匹配项），“searchMode = all”选择精准率（文档必须匹配所有条件）。 

在布尔型搜索的上下文中，如果使用多个运算符堆叠查询并获得更宽泛而非更精准的结果，则默认的“searchMode = any”可能会造成混淆。 在使用 NOT 时尤为如此，该运算符导致结果包括所有“不含”特定字词或短语的文档。

下面的示例进行了这方面的演示。 使用 searchMode (any) 运行以下查询后，返回 42 个文档，即包含词语“restaurant”的文档以及不含短语“air conditioning”的所有文档。 

请注意，布尔运算符 (`-`) 和短语“air conditioning”之间没有空格。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

更改为“searchMode = all”会对条件强制实施累积效果，并返回一个较小的结果集（7 个匹配项），该集由包含词语“restaurant”的文档构成，排除包含短语“air conditioning”的文档。

现在，对此查询的响应应类似于下面的示例，为简洁起见只截取了一部分。

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>示例 8：分页结果

在前面的示例中，你了解了影响搜索结果组合的参数，包括用于确定结果中包含的字段、排序顺序以及如何包含所有匹配项计数的“select”。 本示例继续介绍用于搜索结果组合的分页参数，通过该参数，你可以确定任何给定页面中显示的批结果数。 

默认情况下，搜索服务会返回前 50 个匹配项。 若要控制每个页面中的匹配数，请使用“置顶”来定义批的大小，然后使用“跳过”选取后续批。

下面的示例对“分级”字段（“分级”既可筛选又可排序）使用筛选器和排序顺序，因为这样可以更轻松地看到分页对排序结果的影响。 在常规的完全搜索查询中，最匹配的项按“@search.score”排序和分页。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

此查询会查找到 21 个匹配的文档，但由于你指定了“置顶”，响应将只返回前五个匹配项，其分级从 4.9 开始，到 4.7“Lady of the Lake B & B”结束。 

要获取后续 5 个文档，请跳过第一批：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

对第二批的响应将跳过前五个匹配项，并从“Pull'r Inn Motel”开始，返回后五个匹配项。 若要继续获取其他批，请将“置顶”保留选择 5，然后针对每个新请求将“跳过”递增 5（skip = 5，skip = 10，skip = 15，依此类推）。

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>后续步骤

现在，你已经使用了基本查询语法，可以尝试在代码中指定查询。 以下链接介绍了如何使用 Azure SDK 设置搜索查询。

+ [使用 .NET SDK 查询索引](search-get-started-dotnet.md)
+ [使用 Python SDK 查询索引](search-get-started-python.md)
+ [使用 JavaScript SDK 查询索引](search-get-started-javascript.md)

可在以下链接找到其他语法参考、查询体系结构和示例：

+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单的查询语法](query-simple-syntax.md)
+ [完整 Lucene 查询语法](query-lucene-syntax.md)
+ [筛选器语法](search-query-odata-filter.md)