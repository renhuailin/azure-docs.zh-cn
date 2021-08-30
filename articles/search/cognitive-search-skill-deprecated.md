---
title: 已弃用的认知技能
titleSuffix: Azure Cognitive Search
description: 此页包含一系列被视为已弃用的认知技能，在不久的将来，Azure 认知搜索技能组中将不再支持这些技能。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b44ba49fea555652bdf513e40c3e030ec5862cf5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751866"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Azure 认知搜索中已弃用的认知技能

本文档介绍别视为已弃用的认知技能。 对内容使用以下指南：

* 技能名称：将要弃用的技能的名称；它映射到 @odata.type 属性。
* 最后一个可用的 API 版本：Azure 认知搜索公共 API 的最后一个版本，通过它可以创建/更新技能组，其中包含相应的已弃用技能。 具有这些技能的附加技能组的索引器即使在将来的 API 版本中也会继续运行，直到“终止支持”日期为止，那时它们将开始停用。
* “终止支持”日期：这一天过后，相应的技能会被视为不受支持的技能，并且会停止工作。 以前创建的技能集应该仍然可以继续使用，但建议用户不要使用已弃用的技能。
* 建议：通过前向迁移路径来使用支持的技能。 建议用户按照建议来继续接受支持。

如果你使用的是 [Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill)，则本文将帮助你升级技能组以使用 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，该技能已正式发布并引入了新功能。 

如果你使用的是 [Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill)，则本文将帮助你升级技能组以使用 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)，该技能已正式发布并引入了新功能。 

如果你使用的是 [Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill)，则本文将帮助你升级技能组以使用 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，该技能已正式发布并引入了新功能。

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>最后一个可用的 API 版本

2021-04-30-Preview

### <a name="end-of-support"></a>结束支持

2024 年 8 月 31 日

### <a name="recommendations"></a>建议 

改用 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)。 它以更高的质量提供 EntityRecognitionSkill 的大多数功能。 它还在复杂的输出字段中提供更丰富的信息。

若要迁移到 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，需对技能定义进行下述一项或多项更改。 可以使用[更新技能集 API](/rest/api/searchservice/update-skillset) 更新技能定义。

1. （必需）将 `@odata.type` 从 `"#Microsoft.Skills.Text.EntityRecognitionSkill"` 更改为 `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`。

2. （可选）不再支持 `includeTypelessEntities` 参数，因为新技能只会返回具有已知类型的实体。因此，如果你以前的技能定义引用了此参数，则应该立即将其删除。

3. （可选）如果你使用 `namedEntities` 输出，请注意属性名称有一些细微变化。
    1. `value` 已重命名为 `text`
    2. `confidence` 已重命名为 `confidenceScore`

    如果需要生成完全相同的属性名称，则需要添加一个 [ShaperSkill](cognitive-search-skill-shaper.md)，以使用所需的名称重塑输出。 例如，此 ShaperSkill 将属性重命名为其旧值。

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

4. （可选）如果你使用 `entities` 输出将实体链接到已知实体，则请注意，此功能现在是一个新技能，即 [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md)。 请将实体链接技能添加到你的技能组来生成链接实体。 在 `EntityRecognitionSkill` 与新的 `EntityLinkingSkill` 之间，`entities` 输出的属性名称也有一些细微变化。
    1. `wikipediaId` 已重命名为 `id`
    2. `wikipediaLanguage` 已重命名为 `language`
    3. `wikipediaUrl` 已重命名为 `url`
    4. 不再返回 `type` 和 `subtype` 属性。

    如果需要生成完全相同的属性名称，则需要添加一个 [ShaperSkill](cognitive-search-skill-shaper.md)，以使用所需的名称重塑输出。 例如，此 ShaperSkill 将属性重命名为其旧值。

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

        （之前）EntityRecognition 技能定义

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

        （之后）EntityRecognition 技能 V3 定义

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
    
    * 复杂迁移

        （之前）EntityRecognition 技能定义
    
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
    
        （之后）EntityRecognition 技能 V3 定义
    
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

改用 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)。 它提供了改进的模型，并包含用于添加观点挖掘或基于视角的情绪的选项。 因为该技能明显更复杂，所以输出也有很大差异。

若要迁移到 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)，需要对技能定义执行下述一项或多项更改。 可以使用[更新技能集 API](/rest/api/searchservice/update-skillset) 更新技能定义。

> [!NOTE]
> SentimentSkill V3 的技能输出不兼容基于 SentimentSkill 的索引定义。 你必须对索引定义、技能组（稍后的技能输入和/或知识存储投影）和索引器输出字段映射进行更改，以将情绪技能替换为新版本。

1. （必需）将 `@odata.type` 从 `"#Microsoft.Skills.Text.SentimentSkill"` 更改为 `"#Microsoft.Skills.Text.V3.SentimentSkill"`。

2. （必需）SentimentSkill V3 针对整个文本提供了 `positive`、`neutral` 和 `negative` 分数，针对整个文本中的每个句子提供了相同的分数，而以前的 SentimentSkill 仅针对整个文本提供了范围为 0.0（消极）到 1.0（积极）的单个双精度值。 你需要更新索引定义，以接受三个双精度值来替代单个分数，并确保所有下游技能输入、知识存储投影和输出字段映射与这些命名变化保持一致。

建议完全将旧的 SentimentSkill 替换为 SentimentSkill V3，更新下游技能输入、知识存储投影、索引器输出字段映射和索引定义以匹配新的输出格式，并重置索引器，以使所有文档在以后都具有一致的情绪结果。

> [!NOTE]
> 如果你在更新扩充管道以使用最新版本的情绪技能时需要任何进一步的帮助，或者你不想重设索引器，请创建一个[新的支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，以便我们可以直接与你沟通。

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>最后一个可用的 API 版本

2017-11-11-预览版

### <a name="end-of-support"></a>结束支持

2024 年 8 月 31 日

### <a name="recommendations"></a>建议 

改用 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)。 它提供 NamedEntityRecognitionSkill 的大多数功能，且质量更高。 它还在复杂的输出字段中提供更丰富的信息。

若要迁移到 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)，需对技能定义进行下述一项或多项更改。 可以使用[更新技能集 API](/rest/api/searchservice/update-skillset) 更新技能定义。

1. （必需）将 `@odata.type` 从 `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` 更改为 `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`。

2. （可选）若要使用 `entities` 输出，请改用来自 `EntityRecognitionSkill V3` 的 `namedEntities` 复杂集合输出。 新的 `namedEntities` 复杂输出的属性名称有一些细微变化：
    1. `value` 已重命名为 `text`
    2. `confidence` 已重命名为 `confidenceScore`
    
    如果需要生成完全相同的属性名称，则需要添加一个 [ShaperSkill](cognitive-search-skill-shaper.md)，以使用所需的名称重塑输出。 例如，此 ShaperSkill 将属性重命名为其旧值。

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

        （之后）EntityRecognition 技能 V3 定义

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

        （之后）EntityRecognition 技能 V3 定义

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