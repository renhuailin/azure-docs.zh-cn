---
title: 已弃用的认知技能
titleSuffix: Azure Cognitive Search
description: 此页包含一系列被视为已弃用的认知技能，在不久的将来，Azure 认知搜索技能组中将不再支持这些技能。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: 884d5cd8d2a1318162a09a39a7ef03bb8b8f612e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631735"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Azure 认知搜索中已弃用的认知技能

本文档介绍别视为已弃用的认知技能。 对内容使用以下指南：

* 技能名称：将要弃用的技能的名称，映射到 @odata.type 属性。
* 最后一个可用的 API 版本：Azure 认知搜索公共 API 的最后一个版本，通过它可以创建/更新技能组，其中包含相应的已弃用技能。 具有含这些技能的附加技能集的索引器将在未来的 API 版本中继续运行，直到“支持结束”日期，届时这些索引器将开始失效。
* 支持结束：提供支持的最后一天，这一天过后相应的技能将视为不受支持并停用。 以前创建的技能集应该仍然可以继续使用，但建议用户不要使用已弃用的技能。
* 建议：通过前向迁移路径来使用支持的技能。 建议用户按照建议来继续接受支持。

如果使用的是 [Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill)，可借助本文升级技能组，以使用正式版的 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，同时可借助本文了解新功能。 

如果使用的是 [Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill)，可借助本文升级技能组，以使用正式版的 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)，同时可借助本文了解新功能。 

如果使用的是 [Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill)，可借助本文升级技能组，以使用正式版的 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，同时可借助本文了解新功能。

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>最后一个可用的 API 版本

2021-04-30-Preview

### <a name="end-of-support"></a>结束支持

2024 年 8 月 31 日

### <a name="recommendations"></a>建议 

改用 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)。 它提供 EntityRecognitionSkill 的大多数功能，且质量更高。 它还在复杂的输出字段中提供更丰富的信息。

若要迁移到 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，需对技能定义进行下述一个或多个更改。 可以使用[更新技能集 API](/rest/api/searchservice/update-skillset) 更新技能定义。

1. （必需）将 `@odata.type` 从 `"#Microsoft.Skills.Text.EntityRecognitionSkill"` 更改为 `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`。

2. （可选）不再支持 `includeTypelessEntities` 参数，因为新技能将只返回已知类型的实体，因此如果先前的技能定义引用了该参数，现应将其删除。

3. （可选）如果使用 `namedEntities` 输出，属性名称会发生一些小的更改。
    1. `value` 已重命名为 `text`
    2. `confidence` 已重命名为 `confidenceScore`

    如果需生成完全相同的属性名称，则需添加 [ShaperSkill](cognitive-search-skill-shaper.md)，在输出中使用所需的名称。 例如，此 ShaperSkill 将属性重命名为其旧值。

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntitiesV3/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntitiesV3/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntitiesV3/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntitiesV3/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "namedEntities"
            }
        ]
    }
    ```

4. （可选）如果使用 `entities` 输出将实体链接到常见实体，则相关功能现在是一项新技能，即 [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md)。 将实体链接技能添加到技能组以生成链接实体。 `EntityRecognitionSkill` 和新的 `EntityLinkingSkill` 之间的 `entities` 输出的属性名称也会发生一些小的更改。
    1. `wikipediaId` 已重命名为 `id`
    2. `wikipediaLanguage` 已重命名为 `language`
    3. `wikipediaUrl` 已重命名为 `url`
    4. 不再返回 `type` 和 `subtype` 属性。

    如果需生成完全相同的属性名称，则需添加 [ShaperSkill](cognitive-search-skill-shaper.md)，在输出中使用所需的名称。 例如，此 ShaperSkill 将属性重命名为其旧值。

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "LinkedEntitiesShaper",
        "description": "LinkedEntitiesShaper",
        "context": "/document/linkedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/linkedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "name",
                        "source": "/document/linkedEntitiesV3/*/name"
                    },
                    {
                        "name": "wikipediaId",
                        "source": "/document/linkedEntitiesV3/*/id"
                    },
                    {
                        "name": "wikipediaLanguage",
                        "source": "/document/linkedEntitiesV3/*/language"
                    },
                    {
                        "name": "wikipediaUrl",
                        "source": "/document/linkedEntitiesV3/*/url"
                    },
                    {
                        "name": "bingId",
                        "source": "/document/linkedEntitiesV3/*/bingId"
                    },
                    {
                        "name": "matches",
                        "source": "/document/linkedEntitiesV3/*/matches"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

5. （可选）如果不显式指定 `categories`，则除了那些受 `EntityRecognitionSkill` 支持的类别，`EntityRecognitionSkill V3` 可能还会返回不同类型的类别。 如果此行为不合适，请确保将 `categories` 参数显式设置为 `["Person", "Location", "Organization", "Quantity", "Datetime", "URL", "Email"]`。

    示例迁移定义

    * 简单迁移

        _（之前）EntityRecognition 技能定义_

        ```json
        {   
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _（之后）EntityRecognition 技能 V3 定义_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * 复杂的迁移

        _（之前）EntityRecognition 技能定义_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "includeTypelessEntities": true,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntities"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        }
        ```
    
        _（之后）EntityRecognition 技能 V3 定义_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntitiesV3"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntitiesV3/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntitiesV3/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntitiesV3/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntitiesV3/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "linkedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "LinkedEntitiesShaper",
            "description": "LinkedEntitiesShaper",
            "context": "/document/linkedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/linkedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "name",
                            "source": "/document/linkedEntitiesV3/*/name"
                        },
                        {
                            "name": "wikipediaId",
                            "source": "/document/linkedEntitiesV3/*/id"
                        },
                        {
                            "name": "wikipediaLanguage",
                            "source": "/document/linkedEntitiesV3/*/language"
                        },
                        {
                            "name": "wikipediaUrl",
                            "source": "/document/linkedEntitiesV3/*/url"
                        },
                        {
                            "name": "bingId",
                            "source": "/document/linkedEntitiesV3/*/bingId"
                        },
                        {
                            "name": "matches",
                            "source": "/document/linkedEntitiesV3/*/matches"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="microsoftskillstextsentimentskill"></a>Microsoft.Skills.Text.SentimentSkill

### <a name="last-available-api-version"></a>最后一个可用的 API 版本

2021-04-30-Preview

### <a name="end-of-support"></a>结束支持

2024 年 8 月 31 日

### <a name="recommendations"></a>建议 

改用 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)。 它提供了一个优化的模型，并包括添加观点挖掘或添加基于方面的情绪的选项。 由于该技能复杂了许多，因此输出也非常不同。

若要迁移到 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)，需对技能定义进行下述一个或多个更改。 可以使用[更新技能集 API](/rest/api/searchservice/update-skillset) 更新技能定义。

> [!NOTE]
> 情绪技能 V3 的技能输出与基于 SentimentSkill 的索引定义不兼容。 必须对索引定义、技能集（后续的技能输入和/或知识存储预测）和索引器输出字段映射进行更改，将情绪技能替换为新版本。

1. （必需）将 `@odata.type` 从 `"#Microsoft.Skills.Text.SentimentSkill"` 更改为 `"#Microsoft.Skills.Text.V3.SentimentSkill"`。

2. （必填）情绪技能 V3 对整体文本提供 `positive`、`neutral`、`negative` 评分，并对整体文本中的每个语句都提供相同的评分，而先前的 SentimentSkill 仅对整体文本提供一个双精度值，范围从 0.0（负）到 1.0（正）。 需更新索引定义以接受三个双精度值，而不是单个分数，并确保所有下游技能输入、知识存储预测和输出字段映射与命名更改一致。

建议将旧的 SentimentSkill 完全替换为 SentimentSkill V3，更新下游技能输入、知识库预测、索引器输出字段映射和索引定义以匹配新的输出格式，并重置索引器，确保从目前开始所有文档都具有一致的情绪结果。

> [!NOTE]
> 如果需要任何其他帮助来更新扩充管道以使用最新版的情绪技能，或者如果不想要重置索引器，请提交[新的支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我们可以通过该请求直接与你沟通。

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>最后一个可用的 API 版本

2017-11-11-预览版

### <a name="end-of-support"></a>结束支持

2024 年 8 月 31 日

### <a name="recommendations"></a>建议 

改用 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)。 它提供 NamedEntityRecognitionSkill 的大多数功能，且质量更高。 它还在复杂的输出字段中提供更丰富的信息。

若要迁移到 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，需对技能定义进行下述一个或多个更改。 可以使用[更新技能集 API](/rest/api/searchservice/update-skillset) 更新技能定义。

1. （必需）将 `@odata.type` 从 `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` 更改为 `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`。

2. （可选）若要使用 `entities` 输出，请改用来自 `EntityRecognitionSkill V3` 的 `namedEntities` 复杂集合输出。 新的 `namedEntities` 复杂输出的属性名称发生了一些细微的变化：
    1. `value` 已重命名为 `text`
    2. `confidence` 已重命名为 `confidenceScore`
    
    如果需生成完全相同的属性名称，则需添加 [ShaperSkill](cognitive-search-skill-shaper.md)，在输出中使用所需的名称。 例如，此 ShaperSkill 将属性重命名为其旧值。

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntities",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntities/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntities/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntities/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntities/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntities/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

3. （可选）如果不显式指定 `categories`，则除了那些受 `NamedEntityRecognitionSkill` 支持的类别，`EntityRecognitionSkill V3` 可能还会返回不同类型的类别。 如果此行为不合适，请确保将 `categories` 参数显式设置为 `["Person", "Location", "Organization"]`。

    示例迁移定义

    * 简单迁移

        （之前）NamedEntityRecognition 技能定义

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _（之后）EntityRecognition 技能 V3 定义_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * 稍微复杂的迁移

        （之前）NamedEntityRecognition 技能定义

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "entities"
                }
            ]
        }
        ```

        _（之后）EntityRecognition 技能 V3 定义_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntities",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntities/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntities/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntities/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntities/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntities/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能 (V3)](cognitive-search-skill-entity-recognition-v3.md)
+ [情绪技能 (V3)](cognitive-search-skill-sentiment-v3.md)