---
title: 使用完整的 Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索服务中进行模糊搜索、邻近搜索、术语提升、正则表达式搜索和通配符搜索的 Lucene 查询语法。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 406233fd93ca76a683cf9f9a9e857de9099705ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368539"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>使用“完整的”Lucene 搜索语法（Azure 认知搜索中的高级查询）

构造 Azure 认知搜索的查询时，可以将默认的 [简单查询分析器](query-simple-syntax.md) 替换为 azure 认知搜索中功能更强大的 [Lucene 查询分析器](query-lucene-syntax.md) ，以表述专用的和高级的查询定义。 

Lucene 分析器支持复杂的查询构造，比如字段范围查询、模糊搜索、中缀和后缀通配符搜索、邻近搜索、术语提升以及正则表达式搜索。 额外的功能需遵守额外的处理要求，因此执行时间应该会更长一些。 在本文中，可以逐步完成演示基于完整语法的查询操作的示例。

> [!Note]
> 通过完整的 Lucene 查询语法实现的专用查询构造很多都不是[按文本分析的](search-lucene-query-architecture.md#stage-2-lexical-analysis)，所以并不涉及词干分解和词形还原，这一点有些出人意料。 只会对完整字词（字词查询或短语查询）进行词法分析。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对部分查询字词执行的唯一转换操作是转换为小写。 
>

## <a name="nyc-jobs-examples"></a>NYC 作业示例

下面的示例使用 [NYC 作业搜索索引](https://azjobsdemo.azurewebsites.net/)  ，其中包含基于 [纽约 OpenData 计划的城市](https://nycopendata.socrata.com/)提供的数据集的可用作业。 此数据不应认为是最新或完整数据。 该索引位于 Microsoft 提供的一项沙盒服务上，也就是说无需 Azure 订阅或 Azure 认知搜索即可试用这些查询。

您所需要的是 Postman 或用于在 GET 或 POST 上发出 HTTP 请求的等效工具。 如果你不熟悉这些工具，请参阅 [快速入门：浏览 Azure 认知搜索 REST API](search-get-started-rest.md)。

## <a name="set-up-the-request"></a>设置请求

1. 请求标头必须具有以下值：

   | Key | 值 |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br>  (这是沙盒搜索服务的实际查询 API 密钥，它托管 NYC 作业索引)  |

1. 将谓词设置为 **`GET`** 。

1. 将 URL 设置为 **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + 索引上的文档集合包含所有可搜索的内容。 请求标头中提供的查询 API 密钥仅适用于针对文档集合的读取操作。

   + **`$count=true`** 返回与搜索条件匹配的文档的计数。 在空搜索字符串中，计数将是索引中的所有文档 (大约 2558) NYC 作业的情况。

   + **`search=*`** 是一个未指定的查询，等效于 null 或空搜索。 这并不是特别有用，但它是您可以执行的最简单的搜索，它显示索引中所有可检索的字段，所有值均为。

   + **`queryType=full`** 调用完整的 Lucene 分析器。

1. 进行验证，将以下请求粘贴至 GET 并单击“发送”  。 结果以详细的 JSON 文档形式返回。

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>如何调用完整 Lucene 分析

添加 **`queryType=full`** 以调用完整查询语法，并覆盖默认的简单查询语法。 本文中的所有示例都指定了 **`queryType=full`** 搜索参数，该参数指示由 Lucene 查询分析器处理完整语法。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>示例 1：将查询范围限定为字段列表

第一个示例并未特定于分析器，但我们将先使用它来介绍第一个基本查询概念，即“包含”。 此示例将查询执行和响应限制为仅有几个特定字段。 当你的工具是 Postman 或搜索资源管理器时，了解如何构建可读的 JSON 响应非常重要。 

此查询仅针对中的 *business_title* **`searchFields`** ，并通过 **`select`** 参数指定响应中的同一字段。

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查询的响应应与以下屏幕截图类似。

  ![带有分数的 Postman 示例响应](media/search-query-lucene-examples/postman-sample-results.png)

你可能已经注意到响应中的搜索分数。 如果未进行全文搜索，或者未提供任何条件，则在没有级别的情况下，将出现均匀分数 **1** 。 对于空搜索，行将按任意顺序返回。 包括实际条件时，能看到搜索分数变成有意义的值。

## <a name="example-2-fielded-search"></a>示例 2：字段化搜索

完整 Lucene 语法支持将单个搜索表达式的范围限定为特定的字段。 此示例搜索其中带有字词 senior 而非 junior 的 business title（职务）。 您可以使用和指定多个字段。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

此查询的响应应类似于以下屏幕截图 (posting_type 不) 显示。

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Postman 示例响应搜索表达式" border="false":::

搜索表达式可以是单个词或短语，也可以是括号中更复杂的表达式，可以选择使用布尔运算符。 下面是部分示例：

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

如果想要两个字符串评估为单个实体，请务必将多个字符串放置在引号内，正如这个在 `state` 字段中搜索两个不同位置的情况一样。 根据工具的不同，您可能需要将 (`\`) 转义。 

在 **fieldName:searchExpression** 中指定的字段必须是可搜索的字段。 有关如何在字段定义中使用索引属性的详细信息，请参阅[创建索引（Azure 认知搜索 REST API）](/rest/api/searchservice/create-index)。

> [!NOTE]
> 在上面的示例中， **`searchFields`** 参数被省略，因为查询的每个部分都显式指定了字段名称。 但是， **`searchFields`** 如果查询包含多个部分 (使用和语句) ，则仍可使用。 例如，查询 `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` 只将 `senior NOT junior` 匹配到 `business_title` 字段，而它则会将“external”与 `posting_type` 字段匹配。 中提供的字段名称 `fieldName:searchExpression` 始终优先于 **`searchFields`** ，这就是在此示例中，我们可以省略 `business_title` 的原因 **`searchFields`** 。

## <a name="example-3-fuzzy-search"></a>示例 3：模糊搜索

完整 Lucene 语法还支持模糊搜索，能对构造相似的术语进行匹配。 若要执行模糊搜索，请在单个字词的末尾追加“`~`”波形符，后跟指定编辑距离的可选参数（介于 0 到 2 之间的值）。 例如，`blue~` 或 `blue~1` 会返回 blue、blues 和 glue。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

不直接支持短语，但可以对多部分短语的每个术语指定模糊匹配，例如 `search=business_title:asosiate~ AND comm~` 。  在下面的屏幕截图中，响应包括 *团体关联* 的匹配项。

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="模糊搜索响应" border="false":::

> [!Note]
> 不会对模糊查询进行[分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对部分查询字词执行的唯一转换操作是转换为小写。
>

## <a name="example-4-proximity-search"></a>示例 4：邻近搜索

邻近搜索用于搜索文档中彼此邻近的术语。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。 例如“酒店机场”~5 将查找文档中彼此之间 5 个字以内的术语“酒店”和“机场”。

此查询将搜索术语 "高级" 和 "分析师"，其中每个术语由不超过一个单词进行分隔，并且引号转义 (`\"`) 以保留短语：

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查询的响应应类似于以下屏幕截图 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="邻近查询" border="false":::

再试一次，消除 `~0` 术语 "高级分析人员" 之间 () 的距离。 请注意，此查询返回了 8 个文档，而前面的查询中返回了 10 个文档。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>示例 5：术语提升

术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 若要提高某个术语，请使用插入符号， `^` 并使用提升因子 (一个数字) 在要搜索的字词的末尾处。

在“before”查询中，搜索包含术语“computer analyst”的作业时，你会发现没有同时包含“computer”和“analyst”的结果，但“computer”作业排在结果顶部     。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

在“after”查询中，请重试该搜索，如果两个词都不存在，此时会提升包含术语“analyst”而非“computer”的结果   。 用户可读的查询版本是 `search=business_title:computer analyst^2` 。 对于 Postman 中的可行查询， `^2` 将编码为 `%5E2` 。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="...后提升术语" border="false":::

术语提升不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。 以下示例有助于解释这些差异。

请考虑在某个字段中提升匹配项的计分配置文件，例如 musicstoreindex 示例中的“流派”  。 术语提升可用于进一步提升高于其他术语的某些搜索词。 例如“rock^2 electronic”将提升在“流派”  字段（高于搜索中的其他搜索字段）中包含搜索词的文档。 另外，由于术语提升值 (2) 的原因，包含搜索词“rock”的文档的排名要比包含搜索词“electronic”的要高。

在设置因素级别时，提升系数越高，术语相对于其他搜索词的相关性也越大。 默认情况下，提升系数是 1。 虽然提升系数必须是整数，但可以小于 1（例如 0.2）。

## <a name="example-6-regex"></a>示例 6：正则表达式

正则表达式搜索基于正斜杠“/”之间的内容查找匹配项，如在 [RegExp 类](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所记录的那样。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="正则表达式查询" border="false":::

> [!Note]
> 不会对正则表达式查询进行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 对部分查询字词执行的唯一转换操作是转换为小写。
>

## <a name="example-7-wildcard-search"></a>示例 7：通配符搜索

可将通常可识别的语法用于多个 (\*) 或单个 (?) 字符通配符搜索。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

在此查询中，搜索包含前缀“prog”的作业，这会包含带有术语“编程”和“程序员”的职位。 不能使用 `*` 或 `?` 符号作为搜索的第一个字符。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查询的响应应与以下屏幕截图类似。

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="通配符查询" border="false":::

> [!Note]
> 不会对通配符查询进行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 对部分查询字词执行的唯一转换操作是转换为小写。
>

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