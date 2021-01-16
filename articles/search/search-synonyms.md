---
title: 搜索索引上查询扩展的同义词
titleSuffix: Azure Cognitive Search
description: 创建一个同义词映射，用于扩展 Azure 认知搜索索引上搜索查询的范围。 扩宽了范围，使其包括你在列表中提供的同义术语。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251618"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure 认知搜索中的同义词

利用同义词映射，可以关联等效的术语，扩展查询的作用域，而无需用户实际提供术语。 例如，假设 "狗"、"canine" 和 "puppy" 是同义词，则对 "canine" 的查询将在包含 "dog" 的文档上匹配。

## <a name="create-synonyms"></a>创建同义词

同义词映射是一种可创建一次并且可供多个索引使用的资产。 [服务层](search-limits-quotas-capacity.md#synonym-limits)确定可以创建的同义词映射数量，范围从三个同义词映射（对于免费层和基本层，最高20个，适用于标准层）。 

如果您的内容包含技术或模糊术语，则可以为不同的语言（如英语和法语版本）或词典创建多个同义词映射。 尽管可以在搜索服务中创建多个同义词映射，但字段只能使用其中之一。

同义词映射由名称、格式和充当同义词映射条目的规则组成。 唯一受支持的格式为 `solr` ， `solr` 格式确定规则构造。

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

若要创建同义词映射，请使用 [创建同义词映射 (REST API) ](/rest/api/searchservice/create-synonym-map) 或 Azure SDK。 对于 c # 开发人员，我们建议从 [使用 c # 的 Azure 认知搜索中开始添加同义词](search-synonyms-tutorial-sdk.md)。

## <a name="define-rules"></a>定义规则

映射规则遵循 Apache Solr 的开源同义词筛选器规范，如本文档中所述： [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)。 `solr` 格式支持两种规则：

+ 等效 (其中，术语在查询中是相同的) 

+ 显式映射 (其中，术语在查询之前映射到一个显式字词) 

每个规则必须由换行符分隔 (`\n`) 。 在免费服务中，最多可以为每个同义词映射定义5000个规则，在其他层中为每个地图定义20000个规则。 每个规则最多可以有20个扩展 (或规则) 中的项。 有关详细信息，请参阅 [同义词限制](search-limits-quotas-capacity.md#synonym-limits)。

查询分析器将会降低任何大写或混合大小写术语的大小写，但如果想要保留字符串中的特殊字符（如逗号或短划线），请在创建同义词映射时添加适当的转义符。

### <a name="equivalency-rules"></a>等效规则

等效术语的规则在同一规则内以逗号分隔。 在第一个示例中，对的查询 `USA` 将展开为 `USA` 或 `"United States"` 或 `"United States of America"` 。 请注意，如果想要匹配某个短语，则查询本身必须是带引号的短语查询。

在等效情况下，的查询 `dog` 将展开查询，同时包含 `puppy` 和 `canine` 。

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>显式映射

显式映射的规则由箭头表示 `=>` 。 当指定时，与左侧匹配的搜索查询的字词序列 `=>` 将替换为查询时右侧的替代项。

在显式情况下，或的查询 `Washington` `Wash.` `WA` 将被重写为 `WA` ，并且查询引擎将仅查找字词的匹配项 `WA` 。 显式映射仅适用于指定的方向， `WA` `Washington` 在这种情况下，不会将查询重写为。

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>转义特殊字符

在查询处理过程中对同义词进行分析。 如果需要定义包含逗号或其他特殊字符的同义词，可以使用反斜杠对其进行转义，如本示例所示：

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

由于反斜杠本身是其他语言（如 JSON 和 c #）中的特殊字符，因此你可能需要对其进行双重转义。 例如，发送到上述同义词映射的 REST API 的 JSON 如下所示：

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>上传和管理同义词映射

如前文所述，可以创建或更新同义词映射，而不会中断查询和索引工作负荷。 同义词映射是) 的索引或数据源等独立对象 (，只要没有字段使用它，更新将不会导致索引或查询失败。 但是，一旦您将同义词映射添加到字段定义，则在您删除同义词映射后，包含相关字段的任何查询都将失败并出现404错误。

创建、更新和删除同义词映射始终是一个全文档操作，这意味着您不能增量更新或删除同义词映射的各个部分。 即使只更新一条规则，也需要重载。

## <a name="assign-synonyms-to-fields"></a>向字段分配同义词

上传同义词映射后，可以针对类型为的字段 `Edm.String` 或包含的字段启用同义词 `Collection(Edm.String)` `"searchable":true` 。 如上所述，字段定义只能使用一个同义词映射。

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>对等效或映射字段进行查询

添加同义词不会对查询构造施加新的要求。 您可以发出术语和短语查询，就像在添加同义词之前所做的那样。 唯一的区别是，如果同义词映射中存在查询词，则查询引擎将根据规则展开或重写该字词或短语。

## <a name="how-synonyms-are-used-during-query-execution"></a>在查询执行过程中如何使用同义词

同义词是一种查询扩展技术，它对具有等效术语的索引的内容进行补充，但仅适用于具有同义词赋值的字段。 如果字段范围内的查询不 *包括* 已启用同义词的字段，则不会看到同义词映射中的匹配项。

对于启用了同义词的字段，同义词与关联的字段具有相同的文本分析。 例如，如果使用标准 Lucene 分析器分析了某个字段，则在查询时，同义词术语也将服从标准 Lucene 分析器。 如果要保留标点符号（如句点或短划线），请在 "同义词" 项中对该字段应用内容保留分析器。

在内部，同义词功能通过 OR 运算符将原始查询重写为同义词。 出于这个原因，突出显示和计分配置文件会将原始术语和同义词视为等效项。

同义词仅适用于自由格式的文本查询，筛选器、方面、自动完成或建议不支持同义词。 自动完成和建议仅基于原始术语;同义词匹配不会出现在响应中。

同义词扩展不适用于通配符搜索术语；也不会扩展前缀、模糊和正则表达式术语。

如果需要执行应用同义词扩展和通配符、正则表达式或模糊搜索的单个查询，则可以使用 OR 语法组合查询。 例如，若要将同义词与通配符组合用于简单查询语法，则术语将为 `<query> | <query>*`。

如果开发（非生产）环境中具有现有索引，请使用一个小字典进行试验，了解添加同义词如何更改搜索体验，包括对计分配置文件、突出显示和建议造成的影响。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [ (REST API 创建同义词地图) ](/rest/api/searchservice/create-synonym-map)