---
title: 定义投影
titleSuffix: Azure Cognitive Search
description: 在知识存储中创建表、对象和文件投影，以保存来自索引器和技能组扩充管道的扩充内容。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 6daaf2f3f9521a61307eb8017abce72a47047333
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537924"
---
# <a name="define-projections-in-a-knowledge-store"></a>在知识存储中定义投影

[投影](knowledge-store-projection-overview.md)是[知识存储](knowledge-store-concept-intro.md)中扩充的文档的物理表达形式，采用 Azure 存储中的表、对象或文件的形式。 有效利用扩充的文档需要结构。 本文探讨结构和关系，介绍如何生成投影属性，以及如何关联所创建的不同投影类型的数据。

在你使用[整形程序技能或内联形状](knowledge-store-projection-shape.md)定义扩充和塑造数据后，投影在 [knowledgeStore 定义](knowledge-store-concept-intro.md)中指定。 就上下文而言，本文引用了定义扩充的示例技能组，包括生成适合投影的形状的整形程序技能。

## <a name="enable-caching"></a>启用缓存

开发投影时，请[设置索引器缓存属性](search-howto-incremental-index.md)以确保控制成本。 如果未设置索引器缓存，则编辑投影会导致再次扩充整个文档。 设置缓存并仅更新投影后，对以前已扩充的文档执行技能集不会产生任何新的认知服务费用。

## <a name="example-enrichments"></a>示例扩充

若要了解数据整形与投影之间的交集，请参考以下技能组，它们是扩充内容的基础。 该技能组处理原始图像和文本，生成将在整形和投影中引用的输出。

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>示例整形器技能

整形器技能是一种实用工具，用于处理已扩充的内容，而不是创建内容。 通过向技能组添加整形器，可以创建可投影到表存储的自定义形状。 如果没有自定义形状，投影仅限于引用单个节点（每个输出对应一个投影），这不适用于表。 创建自定义形状可将各个元素聚合成新的逻辑整体，而该整体又可以投影为单个表，或者在一系列表之间进行切片和分布。 

在此示例中，自定义形状合并了 Blob 元数据并标识了实体和关键短语。 自定义形状称为 `projectionShape`，是 `/document` 下的子级。 

整形的目的之一是确保所有扩充节点以适当格式的 JSON 表示，只有这样才能投影到知识存储。 当扩充树包含不是正确格式的 JSON 的节点时（例如，当扩充是字符串等基元的父级时），更是如此。

请注意最后两个节点 `KeyPhrases` 和 `Entities`。 这些节点通过 `sourceContext` 包装成有效的 JSON 对象。 之所以需要这样做，是因为 `keyphrases` 和 `entities` 是针对基元的扩充，只有在转换为有效的 JSON 后才能投影。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

将上述整形器技能添加到技能集。 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>投影到表

投影到 Azure 存储中的表对于使用 Power BI 等工具进行报告和分析非常有用，这些工具可以从表中读取数据，并根据投影过程中生成的键发现关系。 如果你正在尝试生成仪表板，使用相关的表可以简化该任务。

表的架构部分由投影（表名和键）指定，还由提供表形状（列）的源指定。

> [!NOTE] 
> 表预测是 Azure 存储表，受限于 Azure 存储施加的存储限制。 有关详细信息，请参阅[表存储限制](/rest/api/storageservices/understanding-the-table-service-data-model)。 最好是知道实体大小不能超过 1 MB，且单个属性不能大于 64 KB。 这些约束使得表成了用于存储大量小型实体的适当解决方案。

借鉴上面的示例，可以在表投影中引用已知数量的扩充和数据形状。 在下面的表投影中，通过设置 `tableName`、`source` 和 `generatedKeyName` 属性定义了三个表。

| 属性 | 说明 |
|----------|-------------|
| tableName | （必需）确定在 Azure 表存储中创建的新表的名称。 表是使用 partitionKey 和 rowKey 列创建的。 |
| source | 扩充树中节点的路径。 由于表投影很复杂（多个节点填充多个列），路径应解析为包含节点的数据形状。 整形器技能的输出是此属性最常见的值，但你也可以在投影中使用内联整形创建形状。 |
| generatedKeyName | 每一行都由系统生成的值唯一标识。 此属性确定包含这些值的列的名称。 如果省略此属性，将自动创建一个使用表名和“键”作为命名约定的列。 |

所有这三个表都将通过生成的键和共享父级 `/document/projectionShape` 相关联。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

可以执行以下步骤来处理工作：

1. 将知识存储的 `storageConnectionString` 属性设置为有效的 V2 常规用途存储帐户连接字符串。  

1. 通过发出 PUT 请求来更新技能集。

1. 更新技能集后，运行索引器。 

现已获得一个包含三个表的正常运行的投影。 [将这些表导入 Power BI](knowledge-store-connect-power-bi.md) 后，Power BI 应会自动发现关系。

在继续学习下一个示例之前，让我们回顾一下表投影的各个方面，以理解进行数据切片和关联的机制。

### <a name="slicing-a-table-into-multiple-child-tables"></a>将表切分为多个子表

切片是将合并的整个形状细分为组成部分的技术。 结果包括独立但相关的、可单独使用的表。

在此示例中，`projectionShape` 是合并的形状（或扩充节点）。 在投影定义中，`projectionShape` 切片为附加的表，这样你就可以提取形状的组成部分：`keyPhrases` 和 `Entities`。 此技术在 Power BI 中非常有用，因为多个实体和关键短语与每个文档相关联，如果可以看到分类数据形式的实体和关键短语，则会获得更多的见解。

切片在父表与子表之间隐式生成关系，使用父表中的 `generatedKeyName` 在子表中创建同名的列。 

### <a name="naming-relationships"></a>命名关系

`generatedKeyName` 和 `referenceKeyName` 属性用于关联表之间的数据，甚至可以关联投影类型之间的数据。 子表中的每一行都有一个指向回父级的属性。 子级中的列或属性的名称是来自父级的 `referenceKeyName`。 如果未提供 `referenceKeyName`，服务默认使用来自父级中 `generatedKeyName` 的名称。 

Power BI 依赖于这些生成的键来发现表中的关系。 如果需要以不同的方式命名子表中的列，请在父表中设置 `referenceKeyName` 属性。 例如，将 `generatedKeyName` 设置为 tblDocument 表的 ID，并将 `referenceKeyName` 设置为 DocumentID。 这会导致包含文档 ID 的 tblEntities 和 tblKeyPhrases 表中的列被命名为 DocumentID。

## <a name="projecting-to-objects"></a>投影到对象

对象投影更易于定义并且在投影整个文档时使用。 对象投影限制为容器中的单个投影，并且无法切片。

为了定义对象投影，将在 `projections` 中使用 `objects` 数组。 可以使用整形器技能或使用对象投影的内联整形来生成新形状。 表示例演示的是创建形状和切片的方法，而本示例演示的是内联整形的用法。 

内联整形是指在投影输入的定义中创建新形状的功能。 内联整形创建一个匿名对象，该对象与整形器技能生成的对象相同（在本例中为 `projectionShape`）。 定义一个你不打算重复使用的形状时，内联整形非常有用。

投影属性是一个数组。 此示例将一个新的投影实例添加到数组，其中的 knowledgeStore 定义包含内联投影。 使用内联投影时，可以省略整形器技能。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [ ],
            "objects": [
                {
                    "storageContainer": "sampleobject",
                    "source": null,
                    "generatedKeyName": "myobject",
                    "sourceContext": "/document",
                    "inputs": [
                        {
                            "name": "metadata_storage_name",
                            "source": "/document/metadata_storage_name"
                        },
                        {
                            "name": "metadata_storage_path",
                            "source": "/document/metadata_storage_path"
                        },
                        {
                            "name": "content",
                            "source": "/document/content"
                        },
                        {
                            "name": "keyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        },
                        {
                            "name": "entities",
                            "source": "/document/merged_content/entities/*/name"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        },
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]

                }
            ],
            "files": []
        }
    ]
}
```

## <a name="projecting-to-file"></a>投影到文件

文件投影始终是从源文档提取的图像，或者是可从扩充过程中投影出来的扩充的输出。 类似于对象投影，文件投影实现为 Azure 存储中的 Blob，并包含图像。 

为了生成文件投影，将在投影对象中使用 `files` 数组。 此示例将从文档中提取的所有图像投影到名为 `myImages` 的容器。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [ ],
            "objects": [ ],
            "files": [
                {
                    "storageContainer": "myImages",
                    "source": "/document/normalized_images/*"
                }
            ]
        }
    ]
}
```

## <a name="projecting-to-multiple-types"></a>投影到多个类型

更复杂的方案可能要求跨投影类型投影内容。 例如，将关键短语和实体投影到表，将文本的 OCR 结果和布局文本保存为对象，然后将图像投影为文件。 

多个投影类型的步骤：

1. 为每个文档创建包含一行的表。
1. 创建与文档表相关的表，并将每个关键短语标识为此表中的一行。
1. 创建与文档表相关的表，并将每个实体标识为此表中的一行。
1. 创建一个对象投影，其中包含每个图像的布局文本。
1. 创建一个文件投影，用于投影提取的每个图像。
1. 创建一个交叉引用表，其中包含对文档表、带有布局文本的对象投影以及文件投影的引用。

### <a name="shape-data-for-cross-projection"></a>为交叉投影整形数据

若要获取这些投影所需的形状，请首先添加一个新的整形器技能来创建名为 `crossProjection` 的形状对象。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>定义表、对象和文件投影

在合并的 crossProjection 对象中，将对象切片成多个表，将 OCR 输出捕获为 Blob，然后将图像另存为文件（也在 Blob 存储中）。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

对象投影需要为每个投影指定一个容器名称。 对象投影和文件投影不能共享容器。 

### <a name="relationships-among-table-object-and-file-projections"></a>表投影、对象投影与文件投影之间的关系

此示例还重点演示了投影的另一项功能。 通过在同一投影对象内定义多种类型的投影，可以在不同类型（表、对象、文件）之内和之间表达一种关系。 这样你就可以从文档的表行开始，在对象投影中查找该文档中的图像的所有 OCR 文本。 

如果你不希望关联数据，请在不同的投影组中定义投影。 例如，以下代码片段会导致关联表，但不会在表与对象（OCR 文本）投影之间建立关系。 

根据不同的需求投影不同形状中的相同数据时，投影组非常有用。 例如，Power BI 仪表板的投影组，以及用于捕获数据（这些数据用于训练自定义技能中包装的机器学习模型）的另一个投影组。

生成不同类型的投影时，首先会生成文件和对象投影，并将路径添加到表中。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": []
        }
    ]
}
```

## <a name="common-issues"></a>常见问题

定义投影时，有几个常见问题可能会导致意外的结果。 如果知识存储中的输出不符合预期，请检查是否存在这些问题。

+ 无法将字符串扩充整形为有效的 JSON。 扩充字符串后（例如，使用关键短语扩充 `merged_content`），扩充的属性将表示为扩充树中 `merged_content` 的子级。 默认的表示形式不是适当格式的 JSON。 因此在投影时，请确保将扩充转换为包含名称和值的有效 JSON 对象。

+ 省略源路径末尾的 ```/*```。 如果投影的源是 `/document/projectionShape/keyPhrases`，则关键字短语数组将投影为单个对象/行。 请改为将源路径设置为 `/document/projectionShape/keyPhrases/*`，以便为每个关键短语生成单个行或对象。

+ 路径语法错误。 路径选择器区分大小写，如果不对选择器使用确切的大小写，可能会导致出现“缺少输入”警告。

## <a name="next-steps"></a>后续步骤

本文中的示例演示了有关如何创建投影的常用模式。 充分理解概念后，接下来可以运用更丰富的知识来为特定的方案生成投影。

在探索新功能时，请考虑将增量扩充作为下一步。 增量扩充基于缓存，可让你重复使用任何不受技能集修改所影响的扩充。 增量扩充对于包含 OCR 和图像分析的管道特别有用。

> [!div class="nextstepaction"]
> [为增量扩充配置缓存](search-howto-incremental-index.md)
