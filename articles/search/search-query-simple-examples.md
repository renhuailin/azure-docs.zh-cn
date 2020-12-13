---
title: 使用简单的 Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 通过示例了解如何运行基于简单语法的查询，以便对 Azure 认知搜索索引进行全文搜索、筛选搜索、地理搜索、分面搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: 51d36211c7ffa0507a186c9a1e1f2b52d478fe4e
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369084"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>使用 Azure 中的 "简单" 搜索语法认知搜索

在 Azure 认知搜索中， [简单查询语法](query-simple-syntax.md) 调用默认查询分析器进行全文搜索。 分析器非常快捷，并处理常见方案，包括全文搜索、筛选和分面搜索，以及前缀搜索。 本文使用示例来阐释搜索文档中的简单语法用法 [ (REST API) ](/rest/api/searchservice/search-documents) 请求。

> [!NOTE]
> 可选的查询语法是 [完整的 Lucene](query-lucene-syntax.md)，支持更复杂的查询结构，如模糊和通配符搜索。 有关详细信息和示例，请参阅 [使用完整的 Lucene 语法](search-query-lucene-examples.md)。

## <a name="nyc-jobs-examples"></a>NYC 作业示例

下面的示例使用 [NYC 作业搜索索引](https://azjobsdemo.azurewebsites.net/) ，其中包含基于 [纽约 OpenData 计划的城市](https://nycopendata.socrata.com/)提供的数据集的可用作业。 此数据不应认为是最新或完整数据。 该索引位于 Microsoft 提供的一项沙盒服务上，也就是说无需 Azure 订阅或 Azure 认知搜索即可试用这些查询。

您所需要的是 Postman 或用于在 GET 或 POST 上发出 HTTP 请求的等效工具。 如果你不熟悉这些工具，请参阅 [快速入门：浏览 Azure 认知搜索 REST API](search-get-started-rest.md)。

## <a name="set-up-the-request"></a>设置请求

1. 请求标头必须具有以下值：

   | Key | 值 |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br>  (这是沙盒搜索服务的实际查询 API 密钥，它托管 NYC 作业索引)  |

1. 将谓词设置为 **`GET`** 。

1. 将 URL 设置为 **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** 。 

   + 索引上的文档集合包含所有可搜索的内容。 请求标头中提供的查询 api 密钥仅适用于针对文档集合的读取操作。

   + **`$count=true`** 返回与搜索条件匹配的文档的计数。 在空搜索字符串中，计数将是索引中的所有文档 (大约 2558) NYC 作业的情况。

   + **`search=*`** 是一个未指定的查询，等效于 null 或空搜索。 这并不是特别有用，但它是您可以执行的最简单的搜索，它显示索引中所有可检索的字段，所有值均为。

1. 进行验证，将以下请求粘贴至 GET 并单击“发送”  。 结果以详细的 JSON 文档形式返回。

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>如何调用简单查询分析

如果是交互式查询，无需指定任何内容：默认为简单查询。 在代码中，如果之前调用 **`queryType=full`** 了，则可以通过重置默认值 **`queryType=simple`** 。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>示例1：针对特定字段的全文搜索

第一个示例并未特定于分析器，但我们将先使用它来介绍第一个基本查询概念，即“包含”。 此示例将查询执行和响应限制为仅有几个特定字段。 当你的工具是 Postman 或搜索资源管理器时，了解如何构建可读的 JSON 响应非常重要。 

此查询仅针对中的 *business_title* **`searchFields`** ，并通过 **`select`** 参数指定响应中的同一字段。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman 示例响应" border="false":::

你可能已经注意到响应中的搜索分数。 如果未进行全文搜索，或者未提供任何条件，则在没有级别的情况下，将出现均匀分数 **1** 。 对于空搜索，行将按任意顺序返回。 包括实际条件时，能看到搜索分数变成有意义的值。

## <a name="example-2-look-up-by-id"></a>示例 2：按 ID 查找

在查询中返回搜索结果时，逻辑后续步骤是提供包含文档中的更多字段的详细信息页。 此示例演示如何使用 [查找操作](/rest/api/searchservice/lookup-document) 来返回单个文档，以传入文档 ID。

所有文档都有一个唯一标识符。 要在查找查询中试用此语法，请先返回一个文档 ID 列表，以便找到要使用的文档。 对于纽约工作岗位，标识符存储在 `id` 字段中。

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

接下来，根据上一次 `id` 响应中首先出现的 "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" 检索集合中的文档。 下面的查询将返回整个文档的所有可检索字段。

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>示例 3：筛选器查询

[筛选器语法](./search-query-odata-filter.md) 是一种可单独使用或与一起使用的 OData 表达式 **`search`** 。 如果筛选表达式能够完全限定所需的文档，则不带 search 参数的单独筛选器很有用。 不使用查询字符串也就不会执行词法或语言分析、评分（所有评分为 1）和排名。 请注意，搜索字符串为空。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

如果一起使用，则会先对整个索引应用 filter，再对筛选结果执行 search。 filter 可减少 search 查询需要处理的文档集，因此是一种非常有用的技术，可用于提高查询性能。

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="筛选器查询响应" border="false":::

另一种合并筛选器和搜索的有效方法是通过筛选表达式中的 **`search.ismatch*()`** ，在其中可以使用筛选器中的搜索查询。 此筛选表达式使用计划中的通配符来选择包含字词 plan、planner、planning 等的 business_title。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

有关该函数的详细信息，请参阅[“筛选器示例”中的 search.ismatch](./search-query-odata-full-text-search-functions.md#examples)。

## <a name="example-4-range-filters"></a>示例 4：范围筛选器

通过任何数据类型的 **`$filter`** 表达式支持范围筛选。 以下示例搜索数字和字符串字段。 

数据类型在范围筛选器中很重要，当数字数据位于数字字段且字符串数据位于字符串字段中时效果最佳。 由于 Azure 认知搜索中的数字字符串不可比较，因此字符串字段中的数字数据不适用于范围。

下面的查询是一个数字范围：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="数字范围的范围筛选器" border="false":::

在此查询中，范围位于字符串字段 (business_title) ：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="文本范围的范围筛选器" border="false":::

> [!NOTE]
> 基于值的范围分面是常见的搜索应用程序要求。 有关详细信息和示例，请参阅 [如何构建分面筛选器](search-filters-facets.md)。

## <a name="example-5-geo-search"></a>示例 5：地理搜索

示例索引包含带有纬度和经度坐标的 geo_location 字段。 此示例使用 [geo.distance 函数](search-query-odata-geo-spatial-functions.md#examples)来筛选从起点开始，直到所提供的任意距离（以公里为单位）圆周范围内的文档。 可以调整查询 (4) 中的最后一个值，以缩小或放大查询的表面积。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

为了获得更具可读性的结果，搜索结果会被剪裁为包含职务和工作位置。 起始坐标是从索引中的随机文档（在本例中，为斯塔顿岛上的某个工位）获取的。

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Staten 岛地图" border="false":::

## <a name="example-6-search-precision"></a>示例 6：搜索精度

字词查询是独立评估的单个字词（可能很多是这样）。 短语查询用引号括起来，作为逐字字符串进行评估。 匹配精度由运算符和 searchMode 控制。

示例1： `search=fire`  在140结果中匹配，其中所有匹配项都包含文档中某个位置的单词。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

示例 2：`search=fire department` 返回 2002 个结果  。 针对此文档返回了包含“fire”或“department”的匹配项。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

示例3： `search="fire department"` 返回77结果。 用引号将字符串括起来会创建由这两个词组成的短语搜索，并在索引中包含组合字词的标记化字词上找到匹配项。 这就解释了为何诸如 `search=+fire +department` 之类的搜索是不等效的  。 需具备两个字词，但独立扫描它们。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> 由于短语查询是通过引号指定的，因此，此示例将 () 中添加一个转义字符 `\` 以保留语法。

## <a name="example-7-booleans-with-searchmode"></a>示例 7：使用 searchMode 的布尔值

简单语法支持字符形式的布尔运算符 (`+, -, |`) 。 SearchMode 参数会将精度与撤回之间的权衡进行判断，使用 **`searchMode=any`** favoring 回调 (匹配) 的结果集的文档，并且 **`searchMode=all`** favoring 精度 (必须) 匹配所有条件。 

默认情况下 **`searchMode=any`** ，如果使用多个运算符堆叠查询并获得更广泛的结果，则可能会造成混淆。 在使用 NOT 时尤为如此，该运算符导致结果包括所有“不含”特定字词的文档。

如果使用默认的 searchMode (任何) ，2800文档将返回：其中包含短语 "灭火器部门"，以及不含短语 "Metrotech Center" 的所有文档。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="搜索模式 - any" border="false":::

更改以对 **`searchMode=all`** 条件强制执行累积影响，并返回一个较小的结果集-21 个文档-由包含整个短语 "灭火器部门" 的文档组成，减去 Metrotech 中心地址的那些作业。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="搜索模式 - all" border="false":::

## <a name="example-8-structuring-results"></a>示例 8：构建结果

有多个参数控制着搜索结果中包括哪些字段、每批返回多少文档以及排列顺序。 此示例 resurfaces 了几个前面的示例，使用语句和逐字搜索条件将结果限制为特定字段 **`$select`** ，并返回82匹配项。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

将此改动追加到上一示例，即可按职位排序。 这种排序是可行的，因为在该索引中 civil_service_title 是可排序的  。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

使用参数实现分页结果 **`$top`** ，在本例中，返回前5个文档：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

要获取后续 5 个文档，请跳过第一批：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>后续步骤

尝试在代码中指定查询。 下面的链接说明如何使用 Azure Sdk 设置搜索查询。

+ [使用 .NET SDK 查询索引](search-get-started-dotnet.md)
+ [使用 Python SDK 查询索引](search-get-started-python.md)
+ [使用 JavaScript SDK 查询索引](search-get-started-javascript.md)

可在以下链接找到其他语法参考、查询体系结构和示例：

+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单的查询语法](query-simple-syntax.md)
+ [完整 Lucene 查询语法](query-lucene-syntax.md)
+ [筛选器语法](search-query-odata-filter.md)