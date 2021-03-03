---
title: 使用简单的 Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 演示针对 Azure 认知搜索索引的全文搜索、筛选搜索和异地搜索的简单语法的查询示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694030"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>在 Azure 认知搜索中使用“简单”搜索语法

在 Azure 认知搜索中，[简单查询语法](query-simple-syntax.md)调用默认查询分析程序来执行全文搜索。 此分析程序速度快，处理对象是全文搜索、筛选及分面搜索和前缀搜索等常见方案。 本文使用示例来说明[搜索文档 (REST API)](/rest/api/searchservice/search-documents) 请求中的简单语法用法。

> [!NOTE]
> 另一种查询语法是[完整的 Lucene](query-lucene-syntax.md)，它支持模糊搜索和通配符搜索等更复杂的查询结构。 有关详细信息和示例，请参阅[使用完整的 Lucene 语法](search-query-lucene-examples.md)。

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "搜索" 设置为 `*` 一个未指定的查询，等效于 null 或空搜索。 它不是特别有用，但却是你可以执行的最简单的搜索，并且会显示索引中所有可检索的字段以及所有值。

+ 默认情况下，"queryType" 设置为 "simple"，可以省略，但包含它是为了进一步强调，本文中的查询示例是用简单语法表示的。

+ "select" 设置为以逗号分隔的字段列表，可用于搜索结果组合，包括在搜索结果上下文中有用的字段。

+ "计数" 返回与搜索条件匹配的文档数。 在空搜索字符串中，计数将是索引中的所有文档 (50) 的情况。

## <a name="example-1-full-text-search"></a>示例1：全文搜索

全文搜索可以是任意数量的独立字词或引号括起来的短语，其中包含或不包含布尔运算符。 

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

由重要的术语或短语组成的关键字搜索往往最适用。 在索引和查询过程中，字符串字段会经历文本分析，删除不必要的单词，如 "the"、"and"、"it"。 若要查看如何在索引中对查询字符串进行标记，请将 [分析文本](/rest/api/searchservice/test-analyzer) 调用中的字符串传递给索引。

"SearchMode" 参数控制精度和回调。 如果需要更多撤回，请使用默认的 "any" 值，如果查询字符串的任何部分匹配，则将返回结果。 如果优选精度，则字符串的所有部分都必须匹配，将 searchMode 更改为 "all"。 尝试上述两种方法，了解 searchMode 如何更改结果。

"Pool spa + 机场" 查询的响应应类似于下面的示例，为简洁起见进行了修整。

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

请注意响应中的搜索评分。 这是匹配项的相关性分数。 默认情况下，搜索服务会根据此分数返回前50个匹配项。

当没有排名时，将出现 "1.0" 的统一分数，这是因为搜索不是全文搜索，或者未提供任何标准。 例如，在空搜索 (search =) 中 `*` ，行按任意顺序返回。 包括实际条件时，能看到搜索分数变成有意义的值。

## <a name="example-2-look-up-by-id"></a>示例 2：按 ID 查找

在查询中返回搜索结果时，下一个逻辑步骤是提供包含文档中的更多字段的详细信息页。 此示例演示如何通过传入文档 ID，使用 [查找文档](/rest/api/searchservice/lookup-document) 来返回单个文档。

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

所有文档都有一个唯一标识符。 如果使用的是门户，请从 " **索引** " 选项卡中选择索引，然后查看字段定义，以确定哪个字段是键。 使用 REST， [获取索引](/rest/api/searchservice/get-index) 调用返回响应正文中的索引定义。

上述查询的响应包含其键为41的文档。 索引定义中标记为 "可检索" 的任何字段都可以在搜索结果中返回并在应用程序中呈现。

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

## <a name="example-3-filter-on-text"></a>示例3：按文本筛选

[筛选器语法](search-query-odata-filter.md) 是一种 OData 表达式，可以单独使用，也可以使用 "搜索"。 "筛选器" 一起使用时，将首先应用于整个索引，然后在筛选器的结果上执行搜索。 filter 可减少 search 查询需要处理的文档集，因此是一种非常有用的技术，可用于提高查询性能。

可以在索引定义中标记为 "可筛选" 的任何字段上定义筛选器。 对于酒店索引，可筛选字段包括类别、标记、ParkingIncluded、分级和大多数地址字段。

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

上述查询的响应仅限于那些分类为 "报表和 Spa" 的酒店，其中包括术语 "艺术" 或 "教程"。 在这种情况下，只有一个匹配项。

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>示例4：筛选器函数

筛选器表达式可以包含 ["ismatch" 和 "ismatchscoring" 函数](search-query-odata-full-text-search-functions.md)，允许您在筛选器中构建搜索查询。 此筛选器表达式在 *自由* 使用通配符，以选择包括可用 wifi、空闲停车等的可筛选空间。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

上述查询的响应与提供免费的空间的19家酒店匹配。 请注意，搜索分数是整个结果中的 "1.0"。 这是因为搜索表达式为 null 或为空，导致原义筛选器匹配，但没有全文搜索。 相关性分数仅在全文搜索时返回。 如果使用筛选器时未使用 "搜索"，请确保有足够的可排序字段，以便可以控制搜索排名。

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

## <a name="example-5-range-filters"></a>示例5：范围筛选器

范围筛选受任何数据类型的筛选器表达式支持。 下面的示例演示数值和字符串范围。 数据类型在范围筛选器中很重要，当数字数据位于数字字段且字符串数据位于字符串字段中时效果最佳。 字符串字段中的数字数据不适用于范围，因为数值字符串不可比较。

下面的查询是一个数值范围。 在旅馆索引中，唯一可筛选的数字字段为 "分级"。

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

此查询的响应应类似于下面的示例，为简洁起见进行了修整。

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

下一个查询是 (Address/StateProvince) 的字符串字段的范围筛选器：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

此查询的响应应类似于下面的示例，为简洁起见进行了修整。 在此示例中，不能按 StateProvince 排序，因为字段在索引定义中的属性不是 "可排序的"。

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

## <a name="example-6-geo-search"></a>示例6：地域搜索

宾馆样品索引包括一个包含纬度和经度坐标的 geo_location 字段。 此示例使用 [geo.distance 函数](search-query-odata-geo-spatial-functions.md#examples)来筛选从起点开始，直到所提供的任意距离（以公里为单位）圆周范围内的文档。 您可以调整查询 (10) 中的最后一个值，以缩小或放大查询的表面区域。

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

此查询的响应将返回所提供坐标的 10 kilometer 距离内的所有酒店：

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

简单语法支持 () 字符形式的布尔运算符， `+, -, |` 以支持 and、OR 和 NOT 查询逻辑。 布尔值搜索的行为可能会很多，但有几个值得注意的例外情况。 

在前面的示例中，"searchMode" 参数被引入为用于影响精度和召回的机制，使用 "searchMode = any" favoring 回忆 (满足任一条件的文档被视为匹配) ，"searchMode = all" favoring precision (所有条件都必须在文档) 中匹配。 

在布尔搜索的上下文中，如果使用多个运算符堆叠查询并获得更广泛的结果，则默认的 "searchMode = any" 可能会造成混淆。 这种情况特别适用于 NOT，其中的结果包含特定词或短语的所有文档 "不包含"。

下面的示例进行了这方面的演示。 使用 searchMode 运行以下查询 (任何) ，42文档将返回：其中包含术语 "餐馆"，以及不含短语 "空调" 的所有文档。 

请注意，布尔运算符 (`-`) 和短语 "空调" 之间没有空格。

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

更改为 "searchMode = all" 将对条件强制执行累积影响，并返回一个较小的结果集， (7) 匹配包含术语 "餐馆" 的文档，减去包含短语 "空调" 的文档。

此查询的响应现在类似于下面的示例，为简洁起见进行了修整。

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

## <a name="example-8-paging-results"></a>示例8：分页结果

在前面的示例中，你了解了影响搜索结果组合的参数，包括用于确定结果、排序顺序以及如何包含所有匹配项的计数的 "select"。 此示例是使用分页参数形式的搜索结果组合的继续符，使用此参数可以对在任何给定页面中显示的结果数进行批处理。 

默认情况下，搜索服务返回前50个匹配项。 若要控制每个页面中的匹配数，请使用 "top" 来定义批的大小，然后使用 "跳过" 选取后续批次。

下面的示例对 "分级" 字段使用筛选器和排序顺序， (分级既可以筛选又可排序) ，因为这样可以更轻松地查看分页结果的分页效果。 在常规的完全搜索查询中，最匹配项按 "" 进行排序和分页 @search.score 。

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

查询会找到21个匹配的文档，但由于您指定了 "top"，响应将只返回前五个匹配项，其级别从4.9 开始，以 "雄伟 of Lake B & B" 4.7 结束。 

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

第二个批处理的响应将跳过前五个匹配项，并从 "Pull'r Inn Motel" 开始返回后5个匹配项。 若要继续执行其他批处理，请将 "top" 保留为5，然后在每个新请求上递增 "skip" (skip = 5，skip = 10，skip = 15，依此类推) 。

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

现在，您已经使用了基本查询语法，可以尝试在代码中指定查询。 以下链接介绍了如何使用 Azure SDK 设置搜索查询。

+ [使用 .NET SDK 查询索引](search-get-started-dotnet.md)
+ [使用 Python SDK 查询索引](search-get-started-python.md)
+ [使用 JavaScript SDK 查询索引](search-get-started-javascript.md)

可在以下链接找到其他语法参考、查询体系结构和示例：

+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单的查询语法](query-simple-syntax.md)
+ [完整 Lucene 查询语法](query-lucene-syntax.md)
+ [筛选器语法](search-query-odata-filter.md)