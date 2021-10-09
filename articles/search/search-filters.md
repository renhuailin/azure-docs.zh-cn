---
title: 筛选搜索结果
titleSuffix: Azure Cognitive Search
description: 按用户安全标识、语言、地理位置或数字值进行筛选可以减少 Azure 认知搜索（Microsoft Azure 上的托管云搜索服务）中的查询返回的搜索结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f2356235bf70a5fdd3e284c26d421e16ca94fb59
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208488"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure 认知搜索中的筛选器 

“筛选器”提供基于值的条件，用于选择查询中使用的文档。 筛选器可以是单个值，也可以是 OData [筛选表达式](search-query-odata-filter.md)。 与全文搜索不同，筛选器值或表达式仅返回严格匹配。

某些搜索体验（如[分面导航](search-faceted-navigation.md)）依赖于筛选器作为实现的一部分，但你可以随时使用筛选器将查询的范围限定为特定值。 如果你的目标是将查询的范围限定为特定的字段，则可以使用下面所述的替代方法。

## <a name="when-to-use-a-filter"></a>使用筛选器的时机

筛选器在多种搜索体验中不可或缺，包括“附近查找”、分面导航，以及仅显示允许用户查看的文档的安全筛选器。 如果实施其中任何一种体验，必须使用筛选器。 该筛选器会附加到提供地理位置坐标的搜索查询、用户选择的分面类别，或请求者的安全 ID。

常见方案包括下列内容：

+ 基于索引中的内容的切分搜索结果。 如果某个架构具有酒店位置、类别和便利设施，则可以创建一个筛选器，以显式匹配条件（在西雅图，在水上，看风景）。 

+ 实现附带筛选要求的搜索体验：

  + [分面导航](search-faceted-navigation.md)使用筛选器传回用户选择的分面类别。
  + 地理搜索使用筛选器在“附近查找”应用中传递当前位置的坐标。 
  + [安全筛选器](search-security-trimming-for-azure-search.md)将安全标识符作为筛选条件进行传递，在索引中找到的匹配项充当代理，提供对文档的访问权限。

+ 执行“数字搜索”。 数字字段可检索并可显示在搜索结果中，但不可单独搜索（受全文搜索的限制）。 如果需要基于数字数据施加选择条件，请使用筛选器。

### <a name="alternative-methods-for-reducing-scope"></a>缩小范围的替代方法

如果想要缩小搜索结果的范围，筛选器并不是唯一的选择。 根据目标，以下替代方法可能更合适：

+ `searchFields` 查询参数将搜索范围限定为特定的字段。 例如，如果索引分别为英语和西班牙语说明提供了单独的字段，则可以使用 searchFields 来限定要用于全文搜索的字段。 

+ `$select` 参数用于指定要在结果集中包含哪些字段，在将响应发送到调用方应用程序之前能够有效地修剪响应。 此参数不会具体化查询或缩小文档集合，但如果目标是获取更小的响应，则可以考虑使用此参数。 

有关上述任一参数的详细信息，请参阅[搜索文档 > 请求 > 查询参数](/rest/api/searchservice/search-documents#query-parameters)。

## <a name="how-filters-are-executed"></a>如何执行筛选器

在查询时，筛选器分析器会接受作为输入提供的条件、将表达式转换为树状表示的原子布尔表达式，然后基于索引中的可筛选字段评估筛选器树。

筛选是配合搜索发生的，限定要将哪些文档包含在下游处理流程中进行文档检索和相关性评分。 与搜索字符串搭配使用时，筛选器能够有效减小后续搜索操作的重调集。 如果单独使用（例如，在 `search=*` 中查询字符串为空时），筛选条件是唯一的输入。 

## <a name="defining-filters"></a>定义筛选器

筛选器是 OData 表达式，如认知搜索支持的[筛选语法](search-query-odata-filter.md)中所述。

可为每个 **搜索** 操作指定一个筛选器，但筛选器本身可以包含多个字段和多个条件，如果使用 **ismatch** 函数，则还可以包含多个全文搜索表达式。 在多部分筛选表达式中，可按任意顺序指定谓词（受运算符优先顺序规则的约束）。 如果尝试按特定的顺序重新排列谓词，性能不会有明显的提升。

筛选表达式的限制之一是请求的最大大小限制。 整个 POST 请求（包括筛选器）最大可为 16 MB；对于 GET 请求，最大可为 8 KB。 筛选表达式中的子句数也有限制。 根据经验，如果有数百个子句，则就存在达到限制的风险。 我们建议正确设计应用程序，使之不会生成大小不受限制的筛选器。

以下示例显示了多个 API 中的原型筛选器定义。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-patterns"></a>筛选模式

以下示例演示了筛选器方案的多种使用模式。 有关更多思路，请参阅 [OData 表达式语法 > 示例](./search-query-odata-filter.md#examples)。

+ 单独使用 **$filter** 而不使用查询字符串：如果筛选表达式能够完全限定所需的文档，则此模式很有效。 不使用查询字符串也就不会执行词法或语言分析、评分和排名。 请注意，搜索字符串只是一个星号，表示“匹配所有文档”。

  ```json
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ 将查询字符串与 **$filter** 结合使用：让筛选器创建子集，让查询字符串提供字词输入，用于对筛选的子集进行全文搜索。 添加搜索词（“步行距离内的影院”）可以在结果中引入搜索分数，与搜索词最匹配的文档的排名较高。 将筛选器与查询字符串结合使用是最常见的使用模式。

  ```json
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```http
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  还可以使用 `and`（而不是 `or`）通过包含筛选器的 `search.ismatchscoring` 来合并全文搜索，但此功能相当于在搜索请求中使用 `search` 和 `$filter` 参数。 例如，以下两个查询生成相同的结果：

  ```http
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

## <a name="field-requirements-for-filtering"></a>与筛选相关的字段要求

在 REST API 中，默认为简单字段启用了可筛选性。  可筛选字段会增大索引大小；对于不打算真正在筛选器中使用的字段，请务必设置 `"filterable": false`。 有关字段定义设置的详细信息，请参阅[创建索引](/rest/api/searchservice/create-index)。

在 .NET SDK 中，可筛选性默认为“关”。  可以通过将相应 [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) 对象的 [IsFilterable 属性](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)设置为 `true`，使某个字段可筛选。 在以下示例中，该特性已在一个映射到索引定义的模型类的 `BaseRate` 属性中设置。

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>使现有字段可筛选

无法通过修改现有字段来使其可筛选。 为此需要添加新字段或重建索引。 有关重建索引或重新填充字段的详细信息，请参阅[如何重建 Azure 认知搜索索引](search-howto-reindex.md)。

## <a name="text-filter-fundamentals"></a>文本筛选器基础知识

文本筛选器根据筛选器中提供的文本字符串匹配字符串字段：`$filter=Category eq 'Resort and Spa'`

与全文搜索不同，对于文本筛选器，不会执行词法分析或分词，因此，比较操作仅用于精确匹配。 例如，假设字段 *f* 包含“sunny day”，则 `$filter=f eq 'Sunny'` 与条件不匹配，但 `$filter=f eq 'sunny day'` 匹配。 

文本字符串区分大小写。 大写的单词不会转换成小写：`$filter=f eq 'Sunny day'` 不会查找“sunny day”。

### <a name="approaches-for-filtering-on-text"></a>基于文本进行筛选的方法

| 方法 | 说明 | 何时使用 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 根据字符串分隔列表匹配字段的函数。 | 建议用于[安全筛选器](search-security-trimming-for-azure-search.md)，以及其中的许多原始文本值需要与某个字符串字段匹配的任何筛选器。 **search.in** 函数旨在提高速度，相比于显式使用 `eq` 和 `or` 将字段与每个字符串进行比较，其速度要快得多。 | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 用于在同一个筛选表达式中将全文搜索操作与严格的布尔筛选操作混合使用的函数。 | 想要在一个请求中使用多种搜索-筛选组合时，请使用 **search.ismatch**（或其等效的评分函数 **search.ismatchscoring**）。 还可以使用该函数来构建 *contains* 筛选器，以根据较大字符串中的部分字符串进行筛选。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 由字段、运算符和值组成的用户定义的表达式。 | 想要在字符串字段与字符串值之间查找完全匹配项时，请使用此函数。 |

## <a name="numeric-filter-fundamentals"></a>数字筛选器基础知识

在全文搜索的上下文中，数字字段不可搜索 (not `searchable`)。 只有字符串支持全文搜索。 例如，如果输入 99.99 作为搜索词，不会返回价格为 $99.99 的项， 而只会看到在文档字符串字段中包含数字 99 的项。 因此，如果使用了数字数据，则假设条件是要在范围、分面、组等筛选器中使用这些数据。 

如果数字字段（价格、大小、SKU、ID）已标记为 `retrievable`，则包含这些字段的文档会在搜索结果中提供这些值。 此处的要点是，全文搜索本身不适用于数字字段类型。

## <a name="next-steps"></a>后续步骤

首先，尝试在门户中使用“搜索浏览器”来提交包含 **$filter** 参数的查询。 将 [real-estate-sample 索引](search-get-started-portal.md)粘贴到搜索栏后，该索引会针对以下筛选的查询提供有趣的结果：

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

若要学习更多示例，请参阅 [OData 筛选表达式语法 > 示例](./search-query-odata-filter.md#examples)。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [搜索文档 REST API](/rest/api/searchservice/search-documents)
+ [简单的查询语法](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 查询语法](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [支持的数据类型](/rest/api/searchservice/supported-data-types)