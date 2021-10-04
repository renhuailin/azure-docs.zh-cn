---
title: 用于筛选、分面、排序的文本规范化
titleSuffix: Azure Cognitive Search
description: 对索引中的文本字段指定规范化器，以自定义匹配筛选、分面和排序行为的严格关键字。
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: c050023c9cb859f3100f7e2165ba3a069ff4e372
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784540"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>用于不区分大小写的筛选、分面和排序的文本规范化

> [!IMPORTANT] 
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，此功能以公共预览版提供。 [预览版 REST API](/rest/api/searchservice/index-preview) 支持此功能。

从 Azure 认知搜索索引搜索和检索文档需要将查询与文档内容进行匹配。 可对内容进行分析，以生成标记用于匹配使用 `search` 参数时的情况，或者与 `$filter`、`facets` 和 `$orderby` 一样，直接用于严格关键字匹配。 这种孤注一掷的方法涵盖大多数情况，但在不经历整个分析链而需要进行大小写调整、删除重音、asciifolding 等简单的预处理时，就不符合标准。

请开考虑以下示例：

+ `$filter=City eq 'Las Vegas'` 将仅返回包含确切文本“Las Vegas”的文档，排除包含“LAS VEGAS”和“las vegas”的文档，当用例需要不区分大小写的所有文档时，这是不充分的。

+ `search=*&facet=City,count:5` 将以不同的值返回“Las Vegas”、“LAS VEGAS”和“las vegas”，尽管这是同一个城市。

+ `search=usa&$orderby=City` 将按字典顺序返回城市：“Las Vegas”、“Seattle”和“las vegas”，即使意图是不论大小写将相同的城市排列在一起。 

## <a name="normalizers"></a>规范化器

“规范化器”是搜索引擎的一个组件，负责预处理关键字匹配文本。 规范化器类似于分析器，只不过它们不标记化查询。 可以使用规范化器实现的一些转换包括：

+ 转换为小写或大写。
+ 将 ö 或 ê 等重音和音调符号规范化为 ASCII 等效字符“o”和“e”。
+ 将 `-` 和空格等字符映射到用户指定的字符。

可以在索引中的文本字段上指定规范化器，并在编制索引和执行查询时应用。

## <a name="predefined-and-custom-normalizers"></a>预定义的和自定义规范化器 

Azure 认知搜索支持预定义的规范化器用于常见用例，以及支持根据需要进行自定义的能力。

| 类别 | 说明 |
|----------|-------------|
| [预定义的规范化器](#predefined-normalizers) | 开箱即用，无需任何配置即可使用。 |
|[自定义规范化器](#add-custom-normalizers) | 用于高级方案。 要求对现有元素组合进行用户定义的配置，包括字符和标记筛选器。<sup>1</sup>|

<sup>(1)</sup> 自定义规范化器不指定 tokenizer，因为规范化器始终生成单个标记。

## <a name="how-to-specify-normalizers"></a>如何指定规范化器

可以为至少将 `filterable`、`sortable` 或 `facetable` 属性中的一个属性设置为 true 的文本字段（`Edm.String` 和 `Collection(Edm.String)`）上的每个字段指定规范化器。 设置规范化器为可选，默认情况下为 `null`。 建议先评估预定义的规范化器，然后再配置自定义规范化器，以方便使用。 如果结果不符合预期，请尝试使用其他规范化器。

只能在向索引添加新字段时指定规范化器。 在例行删除和重新创建索引时，建议提前评估规范化需求，并在初始开发阶段分配规范化器。 不能对已创建的字段指定规范化器。

1. 在[索引](/rest/api/searchservice/create-index)中创建字段定义时，将“normalizer”属性设置为以下之一：[预定义的规范化器](#predefined-normalizers)（例如 `lowercase`），或自定义规范化器（在同一索引架构中定义）。  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. 自定义规范化器必须先在索引的“[normalizers]”部分中定义，然后分配给字段定义，如前一步所示。 有关详细信息，请参阅[创建索引](/rest/api/searchservice/create-index)和[添加自定义规范化器](#add-custom-normalizers)。


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> 若要更改现有字段的规范化器，必须重新生成整个索引（不能重新生成单个字段）。

生成索引（其中重新生成索引成本高昂）的一种好办法是，创建一个与旧字段相同的新字段，但使用新的规范化器，用它代替旧字段。 使用 [Update Index](/rest/api/searchservice/update-index) 合并新字段，使用 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) 填充该字段。 之后在计划索引服务中，可清除索引以删除过时字段。

## <a name="add-custom-normalizers"></a>添加自定义规范化器

自定义规范化器在索引架构中定义，可以使用字段属性指定。 自定义规范化器的定义包括名称、类型、一个或多个字符筛选器和标记筛选器。 字符筛选器和标记筛选器是自定义规范化器的构建块，负责处理文本。这些筛选器从左到右应用。

 `token_filter_name_1` 是标记筛选器的名称，`char_filter_name_1` 和 `char_filter_name_2` 是字符筛选器的名称（请参阅[支持的标记筛选器](#supported-token-filters)和下面的字符筛选器表查看有效值）。

规范化器定义是较大索引的一部分。 有关索引其余部分的信息，请参阅[创建索引 API](/rest/api/searchservice/create-index)。

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

自定义规范化器可以在创建索引时添加，也可以在以后通过更新添加。 若要将自定义规范化器添加到现有索引中，需要在[更新索引](/rest/api/searchservice/update-index)中指定“allowIndexDowntime”标志，这将导致索引在几秒钟内不可用。

## <a name="normalizers-reference"></a>规范化器引用

### <a name="predefined-normalizers"></a>预定义的规范化器
|**名称**|**说明和选项**|  
|-|-|  
|standard| 先确定文本的大小写，再进行 asciifolding。|  
|小写| 将字符转换为小写。|
|大写| 将字符转换为大写。|
|asciifolding| 将不在基本拉丁字母 Unicode 构建块中的字符转换为其 ASCII 等效字符（如果存在）。 例如，将 à 改为 a。|
|elision| 从标记开头删除省略。|

### <a name="supported-char-filters"></a>支持的字符筛选器
有关字符筛选器的更多详细信息，请参阅[字符筛选器引用](index-add-custom-analyzers.md#CharFilter)。
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>支持的标记筛选器
以下列表显示了规范化器支持的标记筛选器，它是词法分析中涉及的总体标记筛选器的子集。 有关这些筛选器的更多详细信息，请参阅[标记筛选器引用](index-add-custom-analyzers.md#TokenFilters)。

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>自定义规范化器示例

下面的示例演示了具有相应字符筛选器和标记筛选器的自定义规范化器定义。 字符筛选器和标记筛选器的自定义选项分别指定为命名构造，然后在规范化器定义中引用，如下所示。

* 自定义规范化器“my_custom_normalizer”在索引定义的 `normalizers` 部分中定义。
* 规范化器由两个字符筛选器和三个标记筛选器组成：省略、小写和自定义的 asciifolding 筛选器“my_asciifolding”。
* 第一个字符筛选器“map_dash”使用下划线替换所有破折号，而第二个“remove_whitespace”删除所有空格。

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>另请参阅
+ [用于语言和文本处理的分析器](search-analyzers.md)

+ [搜索文档 REST API](/rest/api/searchservice/search-documents) 
