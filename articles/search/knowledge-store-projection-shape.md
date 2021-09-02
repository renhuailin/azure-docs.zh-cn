---
title: 为知识存储整理数据
titleSuffix: Azure Cognitive Search
description: 通过创建数据形状并将其传给投影，来定义知识存储中的数据结构。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 05ba404217fcab9171fad011cedd64cc06557279
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779739"
---
# <a name="shaping-data-for-projection-into-a-knowledge-store"></a>整理投影到知识存储中的数据

在 Azure 认知搜索中，“整理数据”描述了[知识存储工作流](knowledge-store-concept-intro.md)中的一个步骤，该步骤可用于创建要投影到 Azure 存储中表、对象及文件的内容的数据表示形式。

在执行技能时，输出将被写入节点层次结构中的扩充树，此时你可能想完整地查看并使用该扩充树，而更有可能的情况是，你将需要更精细的粒度，为不同方案创建节点子集，如在特定表中放置与已翻译文本或已提取实体相关的节点。

就其本身而言，扩充树不包含对其内容在知识存储中的表示形式予以通知的逻辑。 数据形状将通过提供每个表、对象及文件投影中的内容的架构，来填补这一空白。 你可以将数据形状视为扩充数据的自定义定义或视图。 你可以根据需要创建任意数量的形状，然后将其分配到知识存储定义中的投影。 

## <a name="approaches-for-creating-shapes"></a>创建形状的方法

可以通过以下两种方式创建扩充内容的形状，以便其可以投影到知识存储中：

+ 使用[整形程序技能](cognitive-search-skill-shaper.md)在扩充树中创建专门用于投影的节点。 大多数技能都会创建新内容。 而整形程序技能却相反，它通常会使用现有节点，将多个节点合并为单个复杂对象。 当你想要多个节点的输出以物理形式表现为表中的列时，这一点非常实用。 

+ 使用投影定义中的内联形状。

使用整形程序技能将形状外部化，以便其可以被多个投影甚至其他技能使用。 它还可以确保扩充树的所有突变都包含在该项技能中，且输出也是可重用的对象。 相反，内联整形可让你创建所需的形状，但它是一个匿名对象，仅适用于定义它时所针对的投影。

这些方法可以结合使用，也可以单独使用。 本文将介绍这两种方法：用于表投影的整形程序技能，以及带关键短语表投影的内联整形。

## <a name="use-a-shaper-skill"></a>使用整形程序技能

整形程序技能通常位于技能组的末尾，可用于创建要传给投影的数据视图。 本示例将创建一个名为“tableprojection”的形状，其中包含以下节点：“reviews_text”、“reviews_title”、“AzureSearch_DocumentKey”以及分页审阅中的情绪评分和关键短语。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
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

### <a name="sourcecontext-property"></a>SourceContext 属性

整形程序技能中的输入可以拥有一项 `sourceContext` 元素。 此相同属性还可用于投影中的内联形状。 

`sourceContext` 用于在扩充管道中构造多层嵌套对象。 如果输入所在的上下文与技能上下文不同，则使用 *sourceContext*。 *sourceContext* 要求使用寻址为源的特定元素定义嵌套的输入。 

在上面的示例中，情绪分析与关键短语的提取均在拆分为数页的文本上进行，以便进行更高效的分析。 假设你想将分数和短语投影到表中，那么现在就需要将上下文设置为提供分数及短语的嵌套输入。

### <a name="projecting-a-shape-into-multiple-tables"></a>将形状投影到多个表中

通过上述 `outputs` 部分中定义的节点 `tableprojection`，你可以将 `tableprojection` 节点的各个部分切分为独立的相关表：

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

## <a name="inline-shaping-projections"></a>内联整形投影

内联整形是在投影定义本身内创建新形状的功能。 内联整形具备以下特征：

+ 创建的形状只能由包含该形状的投影使用。
+ 创建的形状可与整形程序技能生成的形状相同。

内联形状使用 `sourceContext` 和 `inputs` 进行创建。

| 属性 | 说明 |
|----------|-------------|
| sourceContext | 设置投影的根。  |
| inputs | 每项输入都是表中的一列。 “名称”为列名称。 “源”为提供数值的扩充节点。 |

若要投影与上一个示例相同的数据，可使用如下所示的内联投影选项：

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
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
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
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
]
```
  
使用这两种方法后，可以观察如何使用“sourceContext”来投影“Keyphrases”的值。 包含字符串集合的“Keyphrases”节点本身是页文本的子级。 但是，由于投影需要 JSON 对象并且页是基元（字符串），因此使用了“sourceContext”将关键短语包装成具有命名属性的对象。 使用此技术可以单独投影平整基元。

## <a name="next-steps"></a>后续步骤

本文将介绍投影形状的概念和原则。 下一步，请参阅如何在表、对象和文件投影模式下应用这些内容。

> [!div class="nextstepaction"]
> [在知识存储中定义投影](knowledge-store-projections-examples.md)
