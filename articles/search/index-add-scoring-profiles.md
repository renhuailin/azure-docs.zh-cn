---
title: 添加计分概要文件以提高搜索分数
titleSuffix: Azure Cognitive Search
description: 通过将计分概要文件添加到搜索索引，提高 Azure 认知搜索结果的搜索相关度分数。
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 986bc72d135cbbfceeb4b7e60556673a8dd8edbc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275841"
---
# <a name="add-scoring-profiles-to-a-search-index"></a>将计分概要文件添加到搜索索引

对于全文搜索查询，搜索引擎会计算每个匹配文档的搜索分数，从而将结果按分数从高到低排名。 Azure 认知搜索使用默认计分算法计算初始分数，但可以通过“计分概要文件”自定义计算。

计分概要文件嵌入索引定义中，包含用于提升匹配项分数的属性，其中概要文件中的附加条件提供了提升逻辑。 例如，你可能想要根据创收能力提升匹配项、提升新项或提升库存时间太长的项。  

不熟悉相关概念？ 下面的视频片段快进到计分概要文件在 Azure 认知搜索中的工作原理，但视频还介绍了基本概念。 你可能还需要查看[相似性排名和计分](index-similarity-and-scoring.md)以了解更多背景知识。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="what-is-a-scoring-profile"></a>什么是计分概要文件？

计分概要文件属于索引定义的一部分，由加权字段、函数和参数组成。 计分概要文件的用途是根据你所提供的条件提升或放大匹配文档。 

以下定义展示了名为“geo”的简单概要文件。 此文件用于提升在“hotelName”字段中具有搜索词的结果。 它还使用 `distance` 函数优先提升在当前位置十公里范围内的结果。 如果有人搜索“inn”一词，而“inn”恰好是酒店名称的一部分，包含当前位置 10 公里范围内带有“inn”的酒店的文档会在搜索结果中的较高位置出现。  

```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  

若要使用此计分概要文件，请制定查询，以在请求中指定 scoringProfile 参数。

```http
POST /indexes/hotels/docs&api-version=2020-06-30
{
    "search": "inn",
    "scoringProfile": "geo",
    "scoringParameter": currentLocation--122.123,44.77233
}
```  

此查询对“inn”一词进行搜索，并在当前位置中传递。 请注意，此查询包含其他参数（例如 scoringParameter）。 [搜索文档 (REST API)](/rest/api/searchservice/Search-Documents)中对查询参数进行了介绍。  

请参阅[扩展示例](#bkmk_ex)以查看计分概要文件的更详细示例。  

<a name=what-is-default-scoring></a>

## <a name="how-scores-are-computed"></a>如何计算评分

为全文搜索查询计算分数，目的是对最相关的匹配项进行排名，并将其返回到响应顶部。 每个文档的总分是每个字段的各个分数的聚合，其中每个字段的各个分数是基于该字段中搜索词的字词频率和文档频率计算的（称为 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) 或字词频率-逆向文档频率）。 

> [!Tip]
> 可以使用 [featuresMode](index-similarity-and-scoring.md#featuresmode-parameter-preview) 参数请求包含搜索结果的其他计分详细信息（包括字段级分数）。

## <a name="when-to-add-scoring-logic"></a>何时添加评分逻辑

当默认排名行为不足以满足业务目标时，应创建一个或更多计分概要文件。 例如，可能会决定搜索相关性应更倾向于新添加的项。 同样，可能有一个包含利润率的字段，或其他某些指示营收潜力的字段。 提升对用户或业务更有意义的结果通常是采用计分概要文件的决定性因素。

搜索页中基于相关性的排序也通过计分概要文件实现。 请考虑过去使用的可以按价格、日期、评分或相关性排序的搜索结果页。 在 Azure 认知搜索中，计分概要文件可用于驱动“相关性”选项。 相关性定义由你控制，基于业务目标和希望提供的搜索体验类型断定。  

<a name="bkmk_ex"></a>

## <a name="extended-example"></a>扩展示例

下面的示例演示具有两个计分概要文件（`boostGenre`、`newAndHighlyRated`）的索引架构。 针对此索引的任何查询（包含任一概要文件作为查询参数）将使用此概要文件对结果集进行计分。 

`boostGenre` 概要文件使用加权文本字段，提升在“albumTitle”、“genre”和“artistName”字段中找到的匹配项。 这些字段分别提升了 1.5、5 和 2。 genre 为何比其他字段提升更高？ 如果对某种程度上为同类的数据执行搜索（正如 musicstoreindex 中的“genre”一样），可能需要在相对权重中具有更大的差异。 例如，在 musicstoreindex 中，“rock”既作为流派显示，又显示在采用相同组句方式的流派说明中。 如果希望流派的权重在流派说明之上，genre 字段将需要更高的相对权重。

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="steps-for-adding-a-scoring-profile"></a>添加计分概要文件的步骤

要实现自定义计分行为，请将计分概要文件添加到定义该索引的架构。 在一个索引中可以具有最多 100 个计分概要文件（请参阅[服务限制](search-limits-quotas-capacity.md)），但在任何给定查询中，一次只能指定一个概要文件。

1. 从索引定义开始。 可以在现有索引上添加和更新计分概要文件，而无需重新生成它。 使用[更新索引](/rest/api/searchservice/update-index)请求发布修订版本。

1. 粘贴本主题中提供的[模板](#bkmk_template)。  

1. 提供一个名称。 计分概要文件为可选项，但如果要添加一个概要文件，必须提供名称。 请务必遵循字段的认知搜索[命名约定](/rest/api/searchservice/naming-rules)（以字母开头，避免使用特殊字符和保留字）。  

1. 指定提升条件。 单个概要文件可以包含[加权字段](#weighted-fields)、[函数](#functions)或者两者都包含。 

应该以迭代的方式工作，使用有助于证明或反驳给定概要文件有效性的数据集。

可以在 Azure 门户中定义计分概要文件（如以下屏幕截图所示），也可以通过 [REST API](/rest/api/searchservice/update-index) 或 Azure SDK（例如用于 .NET 的 Azure SDK 中的 [ScoringProfile](/dotnet/api/azure.search.documents.indexes.models.scoringprofile) 类）以编程方式定义计分概要文件。

   :::image type="content" source="media/scoring-profiles/portal-add-scoring-profile-small.png" alt-text="“添加计分概要文件”页面" lightbox="media/scoring-profiles/portal-add-scoring-profile.png" border="true":::

<a name="weighted-fields"></a>

### <a name="using-weighted-fields"></a>使用加权字段

字段上下文很重要并且查询是全文搜索（也称为自由形式文本搜索）时，请使用加权字段。 例如，如果查询包含字词“airport”，则你可能希望“Description”字段中的“airport”具有比 HotelName 中更多的权重。 

加权字段由可搜索字段和用作乘数的正数组成。 如果 HotelName 的原始字段分数为 3，该字段的提升分数将变为 6，从而提升父文档本身的总体分数。

```json
"scoringProfiles": [  
{  
  "name": "boostKeywords",  
  "text": {  
    "weights": {  
      "HotelName": 2,  
      "Description": 5 
    }  
  }  
}
```

<a name="functions"></a>

### <a name="using-functions"></a>使用函数

当简单的相对权重不足或不适用时，请使用函数，例如距离和新鲜度的情况（即对数值数据的计算）。 可以为每个计分概要文件指定多个函数。

| 函数 | 说明 |
|-|-|
| “freshness” | 按照日期/时间字段中的值 (Edm.DataTimeOffset) 进行提升。 此函数具有 `boostingDuration` 属性，因此可以指定一个值，该值表示发生提升的时间跨度。 | 
| “magnitude” | 根据数值的高低程度进行提升。 调用此函数的方案包括按照利润率、最高价格、最低价格或下载次数提升。 此函数只能与 Edm.Double 和 Edm.Int 字段一起使用。 对于 magnitude 函数，如果想要反转模式（例如，将价格较低项提升至价格较高项之上），可以将范围反转为从高到低。 假设价格范围从 $100 到 $1，则将“boostingRangeStart”设置为 100，并将“boostingRangeEnd”设置为 1 以提升价格较低的项。 | 
| “distance”  | 按照邻近程度或地理位置进行提升。 此函数只能与 Edm.GeographyPoint 字段一起使用。 | 
| “tag”  | 按照搜索文档和查询字符串通用的标记进行提升。 标记在 `tagsParameter` 中提供。 此函数只能与 Edm.String 和 Collection(Edm.String) 字段一起使用。 | 

### <a name="rules-for-using-functions"></a>使用函数的规则

+ 函数只能应用于属性为可筛选的字段。
+ 函数类型（“freshness”、“magnitude”、“distance”、“tag”）必须为小写。
+ 函数不能包含 null 或空值。

<a name="bkmk_template"></a>

## <a name="template"></a>模板

 本部分演示计分概要文件的语法和模板。 有关计分概要文件的属性，请参阅下一部分中的[属性参考](#bkmk_indexref)。  

```json
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "(...)",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...", 
```  

<a name="bkmk_indexref"></a> 

## <a name="property-reference"></a>属性参考

|属性|说明|  
|---------------|-----------------|  
| name | 必需。 这是计分概要文件的名称。 它遵循与字段相同的命名约定。 它必须以字母开头，不能包含点、冒号或 @ 符号，并且不能以短语“azureSearch”（区分大小写）开头。|  
| text | 包含 weights 属性。|  
| weights | 可选。 指定可搜索字段和正整数或浮点数的名称/值对，用于提升字段的分数。 正整数或数字成为排名算法生成的原始字段分数的乘数。 例如，如果字段分数为 2，权重值为 3，则该字段的提升分数将为 6。 然后，汇总单个字段的分数来创建文档字段分数，并用该分数对结果集内的文档进行排名。 |  
| functions | 可选。 计分函数仅可应用于可筛选的字段。|  
| functions > type | 计分函数的必需项。 指示要使用的函数类型。 有效值包括 magnitude、freshness、distance 和 tag。 可以在每个计分概要文件中包含多个函数。 函数名称必须小写。|  
| functions > boost | 计分函数的必需项。 用作原始分数乘数的正数。 不得等于 1。|  
| functions > fieldname | 计分函数的必需项。 计分函数仅可应用于作为索引字段集合一部分且可筛选的字段。 此外，每个函数类型都引入了其他限制（freshness 与 datetime 字段结合使用、magnitude 与 integer 或 double 字段结合使用、distance 与 location 字段结合使用。）。 仅可按函数定义指定单个字段。 例如，若要在同一概要文件中使用两次 magnitude，则需要包含两个定义 magnitude，每个字段一个。|  
| functions > interpolation | 计分函数的必需项。 定义从范围起始至范围结束的分数提升增量的斜率。 有效值包括 Linear（默认值）、Constant、Quadratic 和 Logarithmic。 请参阅[设置插值](#bkmk_interpolation)获取详细信息。|  
| functions > magnitude | magnitude 计分函数用于改变基于数值字段的值范围的排名。 一些最常见的用法示例如下： </br></br>“星级评分”：改变基于“Star Rating”字段内的值的计分。 如果两个项相关，具有较高评分的项先显示。 </br>“利润”：当两个文档相关时，零售商可能希望先提升具有较高利润的文档。 </br>“点击次数”：对于跟踪产品或页面点击行为的应用程序，可使用 magnitude 提升容易获得最多流量的项。 </br>“下载次数”：对于跟踪下载量的应用程序，magnitude 函数可提升下载次数最多的项。|  
| functions > magnitude > boostingRangeStart | 设置对其进行量值计分的范围的起始值。 该值必须是整数或浮点数。 对于星级评分 1 到 4，这里应为 1。 对于超过 50% 的利润率，这里应为 50。|  
| functions > magnitude > boostingRangeEnd | 设置对其进行量值计分的范围的结束值。 该值必须是整数或浮点数。 对于星级评分 1 到 4，这里应为 4。|  
| functions > magnitude > constantBoostBeyondRange | 有效值为 true 或 false（默认）。 设置为 true 时，完整的提升将继续应用到有一个目标字段值高于范围上限的文档。 如果为 false，此函数的提升不会应用到有一个目标字段值超出范围的文档。|  
| functions > freshness | freshness 计分函数用于改变基于 DateTimeOffset 字段值的项的排名分数。 例如，具有较新日期的项可以排在日期较旧的项之上。 </br></br>也可以排列日历事件等具有未来日期的项，以便接近当前日期的项可以排在距离当前日期较远的将来的项之上。 </br></br>在当前服务版本中，范围的一端将固定为当前时间。 另一端是基于 boostingDuration 的过去的时间。 要提升将来的时间范围，请使用负的 boostingDuration。 </br></br>提升从最大范围和最小范围改变的比率由应用到计分概要文件的内插确定（请见下图）。 若要反转应用的提升系数，请选择不超过 1 的提升系数。|  
| functions > freshness > boostingDuration | 设置一个有效期，超过这个有效期之后，针对特定文档的 Boosting 将停止。 请参阅以下语法和示例部分中的[设置 boostingDuration](#bkmk_boostdur)。|  
| functions > distance | 距离计分函数用于影响基于其相对的参考地理位置远近程度的文档分数。 参考位置在参数中指定为查询的一部分（使用 scoringParameter 查询参数），作为经纬度实参。|  
|functions > distance > referencePointParameter | 要在查询中传递以用作参考位置的参数（使用 scoringParameter 查询参数）。 请参阅[搜索文档 (REST API)](/rest/api/searchservice/Search-Documents) 获取查询参数的说明。|  
| functions > distance > boostingDistance | 以公里为单位指示与参考位置（提升范围结束）之间距离的数字。|  
| functions > tag | tag 计分函数用于影响基于文档和搜索查询中标记的文档的分数。 将提升与搜索查询有共同标记的文档。 搜索查询的标记作为每个搜索请求中的计分参数提供（使用 scoringParameter 查询参数）。 |  
| functions > tag > tagsParameter | 要在查询中传递以指定特定请求的标记的参数（使用 scoringParameter 查询参数）。 请参阅[搜索文档 (REST API)](/rest/api/searchservice/Search-Documents) 获取查询参数的说明。|  
| functions > functionAggregation | 可选。 仅在函数已指定时适用。 有效值包括：sum（默认值）、average、minimum、maximum和 firstMatching。 搜索分数是从多个变量（包括多个函数）中计算的单个值。 此属性指示所有函数的提升如何组合到随后应用到基本文档分数的单个聚合提升中。 基本分数基于从文档和搜索查询计算得出的 [tf-idf](http://www.tfidf.com/) 值。|  
| defaultScoringProfile | 在执行搜索请求时，如果未指定任何计分概要文件，则使用默认计分（仅限 [tf-idf](http://www.tfidf.com/)）。 </br></br>可以替代内置默认值，将自定义概要文件替换为在搜索请求中未提供特定配置文件时将使用的配置文件。|  

<a name="bkmk_interpolation"></a> 

## <a name="set-interpolations"></a>设置内插

通过内插，可设置用于计分的坡度形状。 由于评分从高到低，坡度总是在下降，但内插决定了下坡的曲线。 可以使用以下内插：  

| 内插 | 说明 |  
|-|-|  
|`linear`|对于在最大和最小范围内的项目，将按递减的方式进行提升。 Linear 是计分概要文件的默认内插。|  
|`constant`|对于起始和结束范围内的项，将对排名结果应用恒定提升。|  
|`quadratic`|与具有不断减小的提升的线性内插相比，二次方最初以较小的速度递减，然后在接近结束范围时，以更高的间隔递减。 tag 计分函数中不允许使用此内插选项。|  
|`logarithmic`|与具有恒定递减提升的 Linear 内插相比，Logarithmic 最初以较大的速度递减，然后在接近结束范围时，以明显更小的间隔递减。 tag 计分函数中不允许使用此内插选项。|  

 ![关系图上的常数、线性、二次、log10 线](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

<a name="bkmk_boostdur"></a> 

## <a name="set-boostingduration"></a>设置 boostingDuration

`boostingDuration` 是 `freshness` 函数的属性。 使用它设置一个有效期，超过这个有效期之后，针对特定文档的提升将停止。 例如，要在 10 天促销期内提升某个产品系列或品牌，应针对这些文档将 10 天期限指定为 "P10D"。  

`boostingDuration` 必须设置为 XSD "dayTimeDuration" 值（ISO 8601 持续时间值的受限子集）的格式。 它的模式为：“P[nD][T[nH][nM][nS]]”。  

下表提供几个示例。  

|持续时间|boostingDuration|  
|--------------|----------------------|  
|1 天|"P1D"|  
|2 天 12 小时|"P2DT12H"|  
|15 分钟|"PT15M"|  
|30 天 5 小时 10 分钟 6.334 秒|"P30DT5H10M6.334S"|  

有关更多示例，请参阅 [XML 架构：数据类型（W3.org 网站）](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)。  

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中的相似性排名和评分](index-similarity-and-scoring.md)
+ [REST API 参考](/rest/api/searchservice/)
+ [创建索引 API](/rest/api/searchservice/create-index)
+ [Azure 认知搜索 .NET SDK](/dotnet/api/overview/azure/search?)
+ [是计分概要文件？](https://social.technet.microsoft.com/wiki/contents/articles/26706.azure-search-what-are-scoring-profiles.aspx)
