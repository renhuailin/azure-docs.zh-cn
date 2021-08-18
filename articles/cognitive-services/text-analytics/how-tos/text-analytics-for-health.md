---
title: 如何使用健康状况文本分析
titleSuffix: Azure Cognitive Services
description: 了解如何健康状况文本分析从非结构化临床文本中提取和标记医疗信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: aahi
ms.openlocfilehash: 5b1883b06ae234ed8a4f9adf949cf26919f7b877
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550149"
---
# <a name="how-to-use-text-analytics-for-health"></a>如何：使用健康状况文本分析

> [!IMPORTANT] 
> 健康状况文本分析是一项“按原样”提供的功能，“不保证没有故障”。 健康状况文本分析不应用于或不可供用于医疗设备、临床支持、诊断工具或者其他旨在用于诊断、治愈、缓解、治疗或预防疾病或其他健康问题的技术，Microsoft 不授予将此功能用于此类目的的任何许可或权利。 此功能不旨在代替专业人员医疗建议或保健意见、诊断、治疗或医疗保健专业人员临床判断而实施或部署，并且不应用作此用途。 客户独自负责健康状况文本分析的任何使用。 客户必须根据 [UMLS 元词表许可协议附录](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html)或任何将来的等效链接规定的条款，单独许可其打算使用的任何及所有源词汇。 客户负责确保遵守这些许可条款，包括任何地理限制或其他适用限制。


健康状况文本分析是文本分析 API 服务的一项功能，它可以从非结构化文本（例如医生的备注、出院摘要、临床文档和电子健康记录）中提取和标记相关医疗信息。  可以使用两种方法来利用这项服务： 

* [基于 Web 的 API（异步）](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Docker 容器（同步）](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>功能

健康状况文本分析对英语文本执行命名实体识别 (NER)、关系提取、实体否定和实体链接，以发现非结构化临床和生物医学文本中的见解。

### <a name="named-entity-recognition"></a>[命名实体识别](#tab/ner)

命名实体识别检测非结构化文本中提及的可与一个或多个语义类型关联的字词和短语，如诊断、药物名称、症状/体征或年龄。

> [!div class="mx-imgBorder"]
> ![健康状况 NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[关系提取](#tab/relation-extraction)

关系提取标识文本中提及的概念之间有意义的联系。 例如，通过将条件名称与时间或缩写与完整描述相关联，可找到“条件的时间”关系。  

> [!div class="mx-imgBorder"]
> ![健康状况 RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[实体链接](#tab/entity-linking)

实体链接将文本中提及的命名实体与预定义概念数据库（包括统一医学语言系统 (UMLS)）中找到的概念相关联来消除不同实体的歧义。 医学概念也被指定为首选命名，作为一种规范化的附加形式。

> [!div class="mx-imgBorder"]
> ![健康状况 EL](../media/ta-for-health/health-entity-linking.png)

健康状况文本分析支持链接到统一医学语言系统 ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) 元词表知识源中的健康状况和生物医学词汇。

### <a name="assertion-detection"></a>[断言检测](#tab/assertion-detection) 

医学内容的含义受到修饰词的高度影响，例如负面或有条件的断言，如果被歪曲，这些断言可能会产生至关重要的影响。 健康状况文本分析支持对文本中的实体进行三类断言检测： 

* 确定性
* conditional
* 关联

> [!div class="mx-imgBorder"]
> ![健康状况 NEG](../media/ta-for-health/assertions.png)

---

请参阅运行状况文本分析返回的[实体类别](../named-entity-types.md?tabs=health)，获取支持的实体的完整列表。 有关置信度分数的信息，请参阅[文本分析透明度备注](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)。 

### <a name="supported-languages"></a>支持的语言

健康状况文本分析仅支持英语文档。 

## <a name="using-the-docker-container"></a>使用 Docker 容器 

若要在自己的环境中运行健康状况文本分析，请[按照说明下载并安装该容器](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)。

## <a name="using-the-client-library"></a>使用客户端库

文本分析客户端库的最新预发行版本让你能够使用客户端对象调用健康状况文本分析。 请参阅参考文档，并查看 GitHub 上的示例：
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>发送 REST API 请求 

### <a name="preparation"></a>准备工作

必须拥有以下格式的 JSON 文档：ID、文本和语言 

每个文档的大小必须少于 5,120 个字符， 对于集合中允许的最大文档数，请参阅“概念”下的[数据限制](../concepts/data-limits.md?tabs=version-3)一文。 集合在请求正文中提交。 如果文本超出此限制，请考虑将文本拆分为单独的请求。 为了获得最佳结果，请将文本拆分为句子。

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>为托管的异步 Web API 构造 API 请求

对于容器和托管 Web API，必须创建 POST 请求。 可以[使用 Postman](text-analytics-how-to-call-api.md)、cURL 命令或[健康状况文本分析托管 API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Health)中的 API 测试控制台快速构造 POST 请求，并将其发送到所需区域中的托管 Web API。 在 API v3.1 终结点中，`loggingOptOut` 布尔查询参数可用于启用日志记录以进行故障排除。  如果未在请求查询中指定，则默认值为 TRUE。

将 POST 请求发送到 `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs`。下面是附加到健康状况文本分析 API 请求 POST 正文的 JSON 文件示例：

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>托管的异步 Web API 响应 

由于此 POST 请求用于提交异步操作的作业，因此响应对象中没有任何文本。  但是，需要响应标头中操作位置键的值，才能发出 GET 请求来检查作业和输出的状态。  下面是 POST 请求的响应标头中操作位置键的值的示例：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs/<jobID>`

若要检查作业状态，请在 POST 响应的操作位置键标头的值中向 URL 发出 GET 请求。  以下状态用于反映作业的状态：`NotStarted`、`running``succeeded``failed``rejected``cancelling` 和 `cancelled`。  

你可以通过对与 GET 请求相同的 URL 的 DELETE HTTP 调用来取消状态为 `NotStarted` 或 `running` 的作业。  有关 DELETE 调用的详细信息，请参阅[健康状况文本分析托管 API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/CancelHealthJob)。

以下是 GET 请求的响应示例。  在 `expirationDateTime`（创建作业 24 小时后）已过之前，输出可供检索；在此时间之后，输出将被清除。

```json
{
    "jobId": "69081148-055b-4f92-977d-115df343de69",
    "lastUpdateDateTime": "2021-07-06T19:06:03Z",
    "createdDateTime": "2021-07-06T19:05:41Z",
    "expirationDateTime": "2021-07-07T19:05:41Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 0.99
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.98
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-05-15"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>为容器构造 API 请求

可以[使用 Postman](text-analytics-how-to-call-api.md) 或下面的 cURL 请求示例向部署的容器提交查询，以适当的值替换 `serverURL` 变量。  请注意，容器的 URL 中的 API 版本不同于托管 API。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

以下 JSON 是附加到健康状况文本分析 API 请求 POST 正文的 JSON 文件示例：

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>容器响应正文

以下 JSON 是来自容器化同步调用的健康状况文本分析 API 响应正文的示例：

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>断言输出

健康状况文本分析返回断言修饰符，这些修饰符是分配给医学概念的信息属性，能够提供对文本中概念上下文更深入的理解。 这些修饰符分为三个类别，每个类别侧重于不同的方面，并包含一组互斥的值。 每个类别仅为每个实体指定一个值。 每个类别最常见的值是默认值。 服务的输出响应仅包含不同于默认值的断言修饰符。

**CERTAINTY** - 提供有关概念存在的信息（存在与不存在），以及文本与其存在（明确与可能）有关的信息。
*   **Positive** [默认值]：概念存在或已发生。
* **Negative**：概念目前尚不存在或者从未发生过。
* **Positive_Possible**：概念可能存在，但存在一些不确定性。
* **Negative_Possible**：概念可能不存在，但存在一些不确定性。
* **Neutral_Possible**：概念可能存在，也可能不存在，没有偏向任何一方的倾向。

**CONDITIONALITY** - 提供有关概念的存在是否依赖于特定条件的信息。 
*   **None** [默认值]：概念是事实，而不是假设，并且不依赖于特定情况。
*   **Hypothetica**：概念可能正在形成，或者会在将来发生。
*   **Conditional**：概念存在或仅在某些条件下出现。

**ASSOCIATION** - 描述概念是否与文本的主体或其他人相关联。
*   **Subject** [默认值]：概念与文本的主体（通常为患者）相关联。
*   **Someone_Else**：概念与不是文本主体的人员关联。


断言检测将否定的实体表示为确定性类别的负值，例如：

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty": "negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id": "0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>关系提取输出

健康状况文本分析可识别不同概念之间的关系，包括属性和实体之间的关系（例如正文结构的方向、药物的剂量）和实体之间的关系（例如缩写检测）。

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**

> [!NOTE]
> * 引用 CONDITION 的关系可以指 DIAGNOSIS 实体类型，也可以指 SYMPTOM_OR_SIGN 实体类型。
> * 引用 MEDICATION 的关系可以指 MEDICATION_NAME 实体类型，也可以指 MEDICATION_CLASS 实体类型。
> * 引用 TIME 的关系可以指 TIME 实体类型，也可以指 DATE 实体类型。

关系提取输出包含关系类型的实体的 URI 引用和分配的角色。 例如：

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [“命名实体”类别](../named-entity-types.md)
* [新增功能](../whats-new.md)
