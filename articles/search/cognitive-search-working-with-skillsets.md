---
title: 技能组概念
titleSuffix: Azure Cognitive Search
description: 技能组是你在 Azure 认知搜索中创作 AI 扩充管道的地方。 了解有关技能组构成的重要概念和详细信息。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: c189fd8d77d33a2397e5a83f73dcdda759247a03
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535232"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure 认知搜索中的技能组概念

本文面向需要更深入了解技能组概念和组合的开发人员，并假设这些开发人员熟悉 [AI 扩充](cognitive-search-concept-intro.md)的基本概念和工作流。

技能组是 Azure 认知搜索中附加到[索引器](search-indexer-overview.md)的可重复使用的资源。 它包含一项或多项技能，这些技能是原子操作，用于调用内置 AI 或从外部数据源检索的文档的外部自定义处理。

从技能组处理的开始到结束，技能对扩充文档进行读取和写入操作。 扩充文档最初是从数据源中提取的原始内容，但随着每项技能的执行，它获得了结构和内容。 最终，扩充文档中的节点会[映射到搜索索引中的字段](cognitive-search-output-field-mapping.md)，或[映射到知识库中的投影](knowledge-store-projection-overview.md)，以便能够适当地路由内容，其中其他应用将会对其进行查询或使用。

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="管道与技能组" border="false":::

## <a name="skillset-definition"></a>技能集定义

技能组是一组表示原子扩充操作的一项或多项技能，例如翻译文本、提取关键短语或从映像文件执行光学字符识别。 技能可以是 Microsoft 的[内置技能](cognitive-search-predefined-skills.md)，也可以是包含所提供的模型或处理逻辑的[自定义技能](cognitive-search-create-custom-skill-example.md)。 它生成在编制索引或投影到知识存储期间使用的扩充文档。

技能具有类型、上下文以及通常链接在一起的输入和输出。 下面的示例演示了两种共同发挥作用的[内置技能](cognitive-search-predefined-skills.md)，并介绍了技能组定义的一些术语。 

+ 技能 #1 是一种[文本拆分技能](cognitive-search-skill-textsplit.md)，它可以接受“reviews_text”源字段的内容作为输入，并将该内容拆分为 5000 个字符的“页”作为输出。 在自然语言处理期间，将大文本拆分为较小的区块可以生成更好的结果。

+ 技能 #2 是一个[情绪检测技能](cognitive-search-skill-sentiment.md)，它接受“页面”作为输入，并生成一个名为“Sentiment”的新字段作为输出，该输出包含情绪分析结果。

```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        }
. . . 
}
```

关于上述示例的要点，需要注意的是，输入和输出是名称/值对，可以将一种技能的输出与下游技能的输入相匹配，并确保所有技能都具有确定在扩充树中进行处理的位置的上下文。

有关如何表述输入和输出的详细信息，请参阅[如何引用注释](cognitive-search-concept-annotations-syntax.md)。

## <a name="enrichment-tree"></a>扩充树

扩充文档是在技能组执行过程中创建的临时类树形数据结构，用于收集通过技能引入的所有更改，并在可寻址节点的层次结构中表示这些更改。 节点还包括从外部数据源逐字传递的任何未扩充字段。 扩充文档在技能组执行期间存在，可以将其缓存或保存到知识存储中。 

最初，扩充文档只是[文档破解](search-indexer-overview.md#document-cracking)期间从数据源提取的内容，其中文本和图像从源提取，并可用于语言或图像分析。 

初始内容是“根节点”。 它通常是一个完整的文档或规范化的映像。 它在扩充树中的表达方式因数据源类型而异。 下表显示了进入多个受支持数据源的扩展管道的文档的状态：

|数据源\分析模式|默认|JSON、JSON 行 和 CSV|
|---|---|---|
|Blob 存储|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|Azure SQL|/document/{column1}<br>/document/{column2}<br>…|空值 |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|空值|

随着技能的执行，输出将作为新节点添加到扩充树中。 然后，这些节点可用作下游技能的输入，最终将被投影到知识存储中或映射到索引字段。 创建内容的技能（例如翻译后的字符串）会将其输出写入扩充文档。 同样，使用上游技能输出的技能将从扩充文档中进行读取，以获取所需的输入。 

:::image type="content" source="media/cognitive-search-working-with-skillsets/skillset-def-enrichment-tree.png" alt-text="从扩充树中进行读取和写入的技能" border="false":::

扩充树包含提取的内容和从源中提取的元数据，以及由技能创建的任何新节点，例如来自[文本翻译技能](cognitive-search-skill-text-translation.md)的 `translated_text`、来自[实体识别技能](cognitive-search-skill-entity-recognition-v3.md)的 `locations` 或来自[关键短语提取技能](cognitive-search-skill-keyphrases.md)的 `keyPhrases`。 尽管可以通过可视化编辑器来[可视化和使用扩充树](cognitive-search-debug-session.md)，但它大部分是由内部结构组成的。 

扩充是不可变的：创建节点后无法对其进行编辑。 随着技能集变得越来越复杂，扩充树也会更加复杂，但是，并非扩充树中的所有节点都需要将扩充保存到索引或知识存储中。 可以有选择地只保留部分扩充输出，以便只保留要使用的内容。

由于技能的输入和输出是从扩充树读取并写入的，因此，在技能组设计过程中要完成的任务之一就是创建[输出字段映射](cognitive-search-output-field-mapping.md)，用于将内容移出扩充树并移入搜索索引中的字段。 同样，如果要创建知识存储，可以将输出映射到分配给投影的[形状](knowledge-store-projection-shape.md)。

> [!NOTE]
> 扩充树格式甚至能够使扩充管道将元数据附加到基元数据类型。 元数据将不是有效的 JSON 对象，但它可以在知识存储的投影定义中投影为有效的 JSON 格式。 有关详细信息，请参阅[整形程序技能](cognitive-search-skill-shaper.md)。

## <a name="context"></a>上下文

每项技能都有上下文，该上下文可以是整个文档 (`/document`) 或树 (`/document/countries/`) 下的节点。 上下文确定：

+ 技能对单个值执行的次数（每个字段、每个文档一次），或针对集合类型的上下文值执行的次数，其中添加 `/*` 会导致对集合中的每个实例调用一次技能。 

+ 输出声明，或在扩充树中添加技能输出的位置。 输出始终作为上下文节点的子级添加到树中。

+ 输入的形状。 对于多级别集合，将上下文设置为父集合会影响技能输入的形状。 例如，如果某个扩充树包含国家/地区列表，其中的每个国家/地区已使用包含邮政编码列表的省/自治区/直辖市列表进行扩充，则设置上下文的方式将决定输出的表现形式。

|上下文|输入|输入的形状|技能调用|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |国家/地区中所有邮政编码的列表 |对每个国家/地区调用一次 |
|`/document/countries/*/states/*` |`/document/countries/*/states/*/zipcodes/*` |省/自治区/直辖市中邮政编码的列表 | 对每个国家/地区和州/省的组合调用一次|

## <a name="enrichment-example"></a>扩充示例

本示例使用[酒店评价技能组](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)作为参考点，说明如何使用概念图通过技能执行来发展[扩充树](cognitive-search-working-with-skillsets.md#enrichment-tree)。

本示例还演示了如何执行以下操作：

+ 技能的上下文和输入如何共同确定技能的执行次数
+ 哪种输入形状基于上下文

在本示例中，CSV 文件中的源字段包括客户对酒店的评价（“reviews_text”）和评级（“reviews_rating”）。 索引器将从 Blob 存储中添加元数据字段，并且技能将添加翻译文本、情绪评分和关键短语检测。

在酒店评价示例中，扩充流程中的“文档”表示单条酒店评价。

> [!TIP]
> 可以在[Azure 门户](knowledge-store-create-portal.md)中或通过[Postman 和 REST API](knowledge-store-create-rest.md)为此数据创建搜索索引和知识存储。 还可以使用[调试会话](cognitive-search-debug-session.md)来深入了解扩充树的技能组组合、依赖关系和效果。 本文中的映像是从调试会话中提取的。

从概念上讲，初始的扩充树如下所示：

![完成文档破解后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "完成文档破解之后、执行技能之前的扩充树")

所有扩充的根节点是 `"/document"`。 使用 Blob 索引器时，`"/document"` 节点包含 `"/document/content"` 和 `"/document/normalized_images"` 的子节点。 使用 CSV 数据时（如本示例所示），列名称将映射到 `"/document"` 下的节点。

### <a name="skill-1-split-skill"></a>技能 #1：拆分技能

当源内容由大块文本组成时，将其分解成较小的部分会很有帮助，可以提高语言、情绪和关键短语检测的准确度。 有两个可用的粒度：页面和句子。 一个页面包含大约 5000 个字符。

文本拆分技能通常是技能组中的第一个技能。

```json
"@odata.type": "#Microsoft.Skills.Text.SplitSkill",
"name": "#1",
"description": null,
"context": "/document/reviews_text",
"defaultLanguageCode": "en",
"textSplitMode": "pages",
"maximumPageLength": 5000,
"inputs": [
{
    "name": "text",
    "source": "/document/reviews_text"
}
],
"outputs": [
{
    "name": "textItems",
    "targetName": "pages"
}
```

此拆分技能使用 `"/document/reviews_text"` 的技能上下文对 `reviews_text` 执行一次。 技能输出是一个列表，其中的 `reviews_text` 分块成包含 5000 个字符的段。 拆分技能的输出名为 `pages`，将添加到扩充树中。 使用 `targetName` 功能可以在将技能输出添加到扩充树之前对其重命名。

现在，扩充树的技能上下文下包含一个新节点。 此节点可用于任何技能、投影或输出字段映射。 
 
![执行技能 #1 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "执行技能 #1 后的扩充树")

若要访问由技能添加到节点的任何扩充，需要使用扩充的完整路径。 例如，若要使用 ```pages``` 节点中的文本作为另一技能的输入，需将该节点指定为 ```"/document/reviews_text/pages/*"```。 有关路径的详细信息，请参阅[引用注释](cognitive-search-concept-annotations-syntax.md)。

### <a name="skill-2-language-detection"></a>技能 #2：语言检测

酒店评论文档包含以多种语言表达的客户反馈。 语言检测技能确定使用的是哪种语言。 然后，考虑到语言，在检测情绪和短语时，结果将传递给关键短语提取和情绪检测（未显示）。

尽管语言检测技能是技能集中定义的第三个技能（技能 #3），但它是下一个要执行的技能。 由于它不会受到阻止（无需输入），因此它将与前一个技能同时执行。 与前面的拆分技能一样，语言检测技能也只对每个文档调用一次。 扩充树现在包含新的语言节点。

 ![执行技能 #2 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "执行技能 #2 之后的扩充树")

### <a name="skills-3-and-4-sentiment-analysis-and-key-phrase-detection"></a>技能 #3 和 #4（情绪分析和关键短语检测）

客户反馈反映了一系列正面和负面的体验。 情绪分析技能将分析反馈，并在一个从负到正的连续区间上打分，如果情绪不确定，则指定为中性。 在进行情绪分析的同时，关键短语检测识别并提取看似重要的单词和短短语。

根据上下文 `pages`，系统将对 `/document/reviews_text/pages/*` 集合中的每个项目调用一次情绪分析和关键短语技能。 该技能的输出是关联的页元素下的一个节点。 

现在，你应该可以查看技能集中的其他技能，并直观地了解扩充树如何随着每个技能的执行而不断扩大。 某些技能（例如合并技能和整形程序技能）也会创建新的节点，但只使用现有节点中的数据，而不会创建全新的扩充。

![执行所有技能后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "执行所有技能后的扩充树")

上述树中的连接器颜色指示扩充由不同的技能创建，节点需要单独寻址，并且在选择父节点时不会成为返回的对象部分。

### <a name="skill-5-shaper-skill"></a>技能 #5 整形程序技能

如果输出中包含[知识存储](knowledge-store-concept-intro.md)，则添加一个[整形程序技能](cognitive-search-skill-shaper.md)作为最后一步。 整形程序技能使用扩充树中的节点创建数据形状。 例如，你可能想要将多个节点合并为一个形状。 然后，便可以将此形状投影为一个表（节点成为表中的列），并按名称将形状传递到表投影。

可以轻松地使用整形程序技能，因为它侧重于在一种技能下进行塑造。 或者，你可以选择在单个投影内进行内联成型。 整形程序技能不会增加或减少扩充树，因此没有对它进行可视化。 相反，可以将整形程序技能视为重新表述已有扩充树的方式。 从概念上讲，这类似于在数据库中使用表创建视图。

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "name": "#5",
  "description": null,
  "context": "/document",
  "inputs": [
    {
      "name": "name",
      "source": "/document/name"
    },
    {
      "name": "reviews_date",
      "source": "/document/reviews_date"
    },
    {
      "name": "reviews_rating",
      "source": "/document/reviews_rating"
    },
    {
      "name": "reviews_text",
      "source": "/document/reviews_text"
    },
    {
      "name": "reviews_title",
      "source": "/document/reviews_title"
    },
    {
      "name": "AzureSearch_DocumentKey",
      "source": "/document/AzureSearch_DocumentKey"
    },
    {
      "name": "pages",
      "sourceContext": "/document/reviews_text/pages/*",
      "inputs": [
        {
          "name": "Sentiment",
          "source": "/document/reviews_text/pages/*/Sentiment"
        },
        {
          "name": "LanguageCode",
          "source": "/document/Language"
        },
        {
          "name": "Page",
          "source": "/document/reviews_text/pages/*"
        },
        {
          "name": "keyphrase",
          "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
          "inputs": [
            {
              "name": "Keyphrases",
              "source": "/document/reviews_text/pages/*/Keyphrases/*"
            }
          ]
        }
      ]
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "tableprojection"
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

借助介绍和示例，尝试使用[内置技能](cognitive-search-predefined-skills.md)创建第一个技能组。

> [!div class="nextstepaction"]
> [创建第一个技能组](cognitive-search-defining-skillset.md)
