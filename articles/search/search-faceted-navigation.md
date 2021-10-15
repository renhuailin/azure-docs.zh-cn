---
title: 添加分面导航类别层次结构
titleSuffix: Azure Cognitive Search
description: 在与 Azure 认知搜索集成的应用程序中为自定向筛选添加分面导航。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ebd61d57e79864c1af8a583de349eb5a6706fce8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273925"
---
# <a name="add-faceted-navigation-to-a-search-app"></a>将分面导航添加到搜索应用

分面导航用于在搜索应用中自定向向下钻取筛选查询结果，其中应用程序提供窗体控件以将搜索范围限定到文档组（例如，类别或品牌），Azure 认知搜索则提供数据结构和筛选器以支持体验。 

本文介绍在 Azure 认知搜索中创建分面导航结构的基本步骤。

> [!div class="checklist"]
> * 在索引中设置字段属性
> * 构建请求和响应
> * 在表示层中添加导航控件和筛选器

表示层中的代码在分面导航体验中执行繁重的工作。 本文末尾列出的演示和示例提供了工作代码，用于演示整个工作过程。

## <a name="faceted-navigation-in-a-search-page"></a>搜索页面中的分面导航

分面为动态并在查询中返回。 搜索响应包含用于在结果中导航文档的所有分面类别。 首先执行查询，然后从当前结果拉取分面，并组合成分面导航结构。

在认知搜索中，分面深度为一层，不能是分层的。 如果不熟悉结构化分面导航，以下示例左侧显示的就是一个分面导航。 计数指示每个分面的匹配项数。 同一文档可在多个分面中表示。

:::image source="media/tutorial-csharp-create-first-app/azure-search-facet-nav.png" alt-text="分面搜索结果":::

分面可帮助你查找所需的内容，同时确保获取相关结果。 作为开发人员，分面允许公开用于导航搜索索引的最有用的搜索条件。

## <a name="enable-facets-in-the-index"></a>在索引中启用分面

当“facetable”属性设置为 true 时，将在索引定义中逐个按字段启用分面。

尽管这不是严格必需的，但仍应设置“filterable”属性，以便生成必要的筛选器，用于支持搜索应用程序中的分面导航体验。

以下“hotels”示例索引显示了包含单个值或短语的低基数字段“Category”、“Tags”和“Rating”的“facetable”和“filterable”属性。

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "Description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "HotelName", "type": "Edm.String", "facetable": false },
    { "name": "Category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint" }
  ]
}
```

### <a name="choosing-fields"></a>选择字段

可通过单个值字段和集合计算分面。 最适用于分面导航的字段具有以下特征：

* 低基数（在搜索语料库的整个文档中重复的少量不同值）

* 简短描述值（一到两个单词），可在导航树中较好地呈现

字段内容（而不是字段本身）在分面导航结构中生成分面。 如果分面是字符串字段“Color”，则分面将是“blue”、“green”和该字段的其他任何值。

最佳做法是，检查字段是否存在空值、拼写错误或大小写差异以及同一单词的单复数形式。 筛选器和分面不进行词法分析或[拼写检查](speller-how-to-add.md)，这意味着“facetable”字段的所有值都可能生成分面，即使这些单词只相差一个字符。

### <a name="defaults-in-rest-and-azure-sdks"></a>REST 和 Azure SDK 中的默认值

如果使用 Azure SDK 之一，则代码必须指定所有字段属性。 不同的是，REST API 根据[数据类型](/rest/api/searchservice/supported-data-types)来指定字段属性的默认值。 默认情况下，以下数据类型具有“filterable”和“facetable”属性：

* `Edm.String`
* `Edm.DateTimeOffset`
* `Edm.Boolean`
* `Edm.Int32`, `Edm.Int64`, `Edm.Double`
* 上述任何类型的集合，例如 `Collection(Edm.String)` 或 `Collection(Edm.Double)`

无法在分面导航中的使用 `Edm.GeographyPoint` 或 `Collection(Edm.GeographyPoint)` 字段。 分面最适合用于基数较小的字段。 由于地理坐标的精度，给定数据集中两组坐标完全相同的情况很少见。 因此，地理坐标不支持分面。 需要城市或区域字段才可实现按位置进行分面。

> [!Tip]
> 为实现最佳性能和存储优化，请针对绝不应用作分面的字段关闭分面功能。 具体而言，应将唯一值的字符串字段（例如 ID 或产品名称）设置为 `"facetable": false`，以避免在分面导航中意外（和无效）使用它们。 对于默认启用筛选器和分面的 REST API，这一点尤其如此。

## <a name="facet-request-and-response"></a>分面请求和响应

分面在查询上指定，分面导航结构在响应顶部返回。 请求和响应的结构相当简单。 事实上，分面导航背后的实际工作位于表示层，后面的部分将对此进行介绍。 

以下 REST 示例是一个非限定查询 (`"search": "*"`)，它的查询范围为整个索引（请参阅[内置 hotels 示例](search-get-started-portal.md)）。 分面通常是字段列表，但为了下面的响应更具可读性，此查询只显示一个字段。

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "queryType": "simple",
    "select": "",
    "searchFields": "",
    "filter": "",
    "facets": [ "Category"], 
    "orderby": "",
    "count": true
}
```

使用打开的查询初始化搜索页以完全填充分面导航结构，这一方法非常有用。 在请求中传递查询词后，分面导航结构的范围将仅限定为结果中的匹配项，而不是整个索引。

上述示例的响应包括顶部的分面导航结构。 该结构由“Category”值和每个值的 hotel 计数组成。 然后是其余搜索结果，为简洁起见，此处已进行剪裁。 此示例运行效果良好有多种原因。 此字段的分面数未超过限制（默认值为 10），因此所有分面都得以显示，并且 50 个 hotel 索引中的每个 hotel 都只以其中一个类别表示。

```json
{
    "@odata.context": "https://demo-search-svc.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "@odata.count": 50,
    "@search.facets": {
        "Category": [
            {
                "count": 13,
                "value": "Budget"
            },
            {
                "count": 12,
                "value": "Resort and Spa"
            },
            {
                "count": 9,
                "value": "Luxury"
            },
            {
                "count": 7,
                "value": "Boutique"
            },
            {
                "count": 5,
                "value": "Suite"
            },
            {
                "count": 4,
                "value": "Extended-Stay"
            }
        ]
    },
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Category": "Boutique",
            "Tags": [
                "pool",
                "air conditioning",
                "concierge"
            ],
            "ParkingIncluded": false,
  . . .
```

## <a name="facets-syntax"></a>分面语法

分面查询参数设置为以逗号分隔的“facetable”字段列表，根据数据类型，可以进一步参数化以设置计数、排序顺序和范围：`count:<integer>`、`sort:<>`、`interval:<integer>` 和 `values:<list>`。 有关分面参数的更多详细信息，请参阅 [REST API 中的“查询参数”](/rest/api/searchservice/search-documents#query-parameters)。

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "facets": [ "Category", "Tags,count:5", "Rating,values:1|2|3|4|5"],
    "count": true
}
```

对于每个分面导航树，默认限制为 10 个分面。 此默认值对导航结构有意义，因为它可使值列表保持在合理的大小。 可通过向“count”分配某个值来替代默认值。 例如，`"Tags,count:5"` 可将“Tags”部分下的 tag 数减少到前五位。

仅对于 Numeric 和 DateTime 值，可以在分面字段上显式设置值（例如，`facet=Rating,values:1|2|3|4|5`），从而将结果分隔为连续范围（根据数值或时间段确定范围）。 或者，也可以添加“interval:”,如 `facet=Rating,interval:1`。 

通过以下方式生成每个范围：使用 0 作为起点、使用列表中的某个值作为终结点，并剪裁上一个范围以创建离散间隔。

### <a name="discrepancies-in-facet-counts"></a>分面计数中的差异

在某些情况下，可能发现分面计数与结果集不匹配（请参阅 [Azure 认知搜索中的分面导航（Microsoft 问答页面）](/answers/topics/azure-cognitive-search.html)）。

由于分片体系结构，分面计数可能不准确。 每个搜索索引具有多个分片，每个分片报告按记录计数排序的前 N 个分面，并合并到单个结果中。 如果某些分片具有大量匹配值，而其他分片的值很少，你可能会发现某些分面值丢失或未计入结果中。

尽管此行为可能随时更改，但如果现在遇到此行为，可通过以下方式解决它：人为地将 count:\<number> 扩大到较大的值，以强制从每个分片进行完整报告。 如果 count: 的值大于或等于字段中唯一值的数目，可保证获得准确结果。 但是，如果记录计数较高，性能可能会受到负面影响，因此请谨慎使用此选项。

## <a name="presentation-layer"></a>表示层

在应用程序代码中，模式旨在使用分面查询参数返回分面导航结构以及分面结果，还可使用 $filter 表达式。  筛选器表达式处理单击事件，并基于分面选择进一步缩小搜索结果范围。

### <a name="facet-and-filter-combination"></a>分面和筛选器组合

以下代码片段源自 NYCJobs 演示中的 `JobsSearch.cs` 文件，如果从 Business Title 分面中选择一个值，该代码片段会将选定的 Business Title 添加到筛选器。

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

下面是 hotels 演示的另一个示例。 如果用户从 category 分面中选择一个值，以下代码片段会将 categorFacet 添加到筛选器。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

### <a name="html-for-faceted-navigation"></a>用于分面导航的 HTML

以下示例源自示例应用程序 NYCJobs 的 `index.cshtml` 文件，显示了用于在搜索结果页中显示分面导航的静态 HTML 结构。 提交搜索词或者选择或清除分面时，系统会动态生成或重新生成分面列表。

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

### <a name="build-html-dynamically"></a>动态生成 HTML

以下源自 `index.cshtml`（同样来自 NYCJobs 演示）的代码片段会动态生成 HTML 来显示第一个分面，即 Business Title。 类似的函数可为其他分面动态生成 HTML。 每个方面具有标签和计数，用于显示为该分面结果找到的项数。

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

## <a name="tips-for-working-with-facets"></a>使用分面的技巧

本部分汇集了一系列可能有用的技巧和解决方法。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>以异步方式保留筛选结果的分面导航结构

在 Azure 认知搜索中使用分面导航所面临的一个难题是，分面仅针对当前结果存在。 在实践中，通常会保留一组静态分面，便于用户按相反顺序进行导航，回顾步骤以通过搜索内容了解可选路径。 

虽然这是常见的用例，但并不是分面导航结构当前提供的现成内容。 通过发布 2 个筛选查询（一个将范围限定到结果，另一个用于创建针对导航的静态分面列表），需要静态分面的开发人员通常便可解决限制问题。

### <a name="clear-facets"></a>清除分面

设计搜索结果页面时，请记住添加用于清除分面的机制。 如果使用复选框，可以轻松了解如何清除筛选器。 对于其他布局，可能需要痕迹导航模式或其他创新方法。 在 hotels C# 示例中，可以发送空搜索以重置页面。 不同的是，NYCJobs 示例应用程序在选定分面之后提供了一个可单击的 `[X]` 用于清除分面，这对用户来说是一个更强的视觉对象队列。

### <a name="trim-facet-results-with-more-filters"></a>使用更多筛选器修剪分面结果

分面结果是在匹配分面词语的搜索结果中找到的记录。 在以下示例中，在 *云计算* 的搜索结果中，254 个项还具有 *内部规范* 作为内容类型。 项不一定互相排斥。 如果某个项满足这两个筛选条件，它将分别计入每一个。 针对通常用于实现记录标记的 `Collection(Edm.String)` 字段进行分面时，可能会出现这种重复。

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

一般情况下，如果发现分面结果持续很大，我们建议添加更多筛选器，以便为用户提供更多缩小搜索范围的选项。

### <a name="a-facet-only-search-experience"></a>仅限分面的搜索体验

如果应用程序以独占方式（即无搜索框）使用分面导航，可将字段标记为 `searchable=false`、`filterable=true` 或 `facetable=true` 以生成更简洁的索引。 索引将不包含倒排索引，并且不会进行文本分析或词汇切分。 筛选器在字符级别上进行完全匹配。

### <a name="validate-inputs-at-query-time"></a>在 query-time 验证输入

如果根据不受信任的用户输入动态生成分面列表，应验证分面字段的名称是否有效。 或者，在使用 .NET 中的 `Uri.EscapeDataString()` 或所选平台的等效项生成 URL 时会名称转义。

## <a name="demos-and-samples"></a>演示和示例

几个示例包括分面导航。 本部分提供了指向示例的链接，还说明了每个示例使用哪种客户端库和语言。

### <a name="create-your-first-app-in-c-razor"></a>使用 C# (Razor) 创建第一个应用

本 C# 教程和示例系列包含[重点介绍分面导航的课程](tutorial-csharp-facets.md)。 解决方案是 ASP.NET MVC 应用，表示层使用 Razor 客户端库。

### <a name="add-search-to-web-apps-react"></a>向 Web 应用 (React) 添加搜索

[C#](tutorial-csharp-overview.md)、[Python](tutorial-python-overview.md) 和 [JavaScript](tutorial-javascript-overview.md) 的教程和示例包括分面导航以及筛选器、建议和自动完成。 这些示例将 React 用于表示层。

### <a name="nycjobs-sample-code-and-demo-ajax"></a>NYCJobs 示例代码和演示 (Ajax)

NYCJobs 示例是一个 ASP.NET MVC 应用程序，在表示层中使用 Ajax。 该示例作为[现场演示应用程序](https://aka.ms/azjobsdemo)使用，并在 [GitHub 上的 Azure-Samples 存储库](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)中提供源代码。

### <a name="video-demonstration"></a>视频演示

在[深入探讨 Azure 认知搜索](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410)视频的 45:25 处，有一个关于如何实现分面的演示。
