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
ms.date: 11/19/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: e7f017c1f3dc189af2b0fc053912decca3459478
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952754"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>如何：使用健康状况文本分析（预览）

> [!IMPORTANT] 
> 健康状况文本分析是一项预览功能，其“按原样”提供并在“不保证没有缺点”情况下提供。 因此，不应在任何生产用途中实施或部署健康状况文本分析（预览版）。 健康状况文本分析不应用于或不可供用于医疗设备、临床支持、诊断工具或者其他旨在用于诊断、治愈、缓解、治疗或预防疾病或其他健康问题的技术，Microsoft 不授予将此功能用于此类目的的任何许可或权利。 此功能不旨在代替专业人员医疗建议或保健意见、诊断、治疗或医疗保健专业人员临床判断而实施或部署，并且不应用作此用途。 客户独自负责健康状况文本分析的任何使用。 Microsoft 不保证健康状况文本分析或提供的与该功能相关的任何材料足够充分用于任何医疗目的，或者满足任何人的健康或医疗要求。 


用于运行状况的文本分析是文本分析 API 服务的一项功能，它可从非结构化文本（如医生的说明、解雇汇总、临床文档和电子健康记录）中提取和标记相关的医疗信息。  使用此服务有两种方法： 

* 基于 web 的 API (异步)  
*  (同步的 Docker 容器)    

## <a name="features"></a>功能

用于运行状况文本分析 (NER) 、关系提取、实体求反和实体链接，以在非结构化临床和生物医学文本中发现见解。

### <a name="named-entity-recognition"></a>[命名实体识别](#tab/ner)

命名实体识别检测非结构化文本中提及的可与一个或多个语义类型关联的字词和短语，如诊断、药物名称、症状/体征或年龄。

> [!div class="mx-imgBorder"]
> ![健康状况 NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[关系提取](#tab/relation-extraction)

关系提取标识文本中提及的概念之间有意义的联系。 例如，通过将疾病名称与时间关联来查找“疾病时间”关系。 

> [!div class="mx-imgBorder"]
> ![健康状况 RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[实体链接](#tab/entity-linking)

实体链接将文本中提及的命名实体与预定义概念数据库中找到的概念相关联来消除不同实体的歧义。 例如，统一医学语言系统 (UMLS)。

> [!div class="mx-imgBorder"]
> ![健康状况 EL](../media/ta-for-health/health-entity-linking.png)

健康状况文本分析支持链接到统一医学语言系统 ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) 元词表知识源中的健康状况和生物医学词汇。

### <a name="negation-detection"></a>[否定检测](#tab/negation-detection) 

医学内容的意义受到修饰语（如否定）的高度影响，这在误诊时可能有重大影响。 健康状况文本分析支持对文本中提到的不同实体进行否定检测。 

> [!div class="mx-imgBorder"]
> ![健康状况 NEG](../media/ta-for-health/health-negation.png)

---

请参阅运行状况文本分析返回的[实体类别](../named-entity-types.md?tabs=health)，获取支持的实体的完整列表。

### <a name="supported-languages-and-regions"></a>支持的语言和区域

健康状况文本分析仅支持英语文档。 

运行状况托管 web API 的文本分析目前仅在以下区域提供：美国西部2、美国东部2、美国中部、北欧和西欧。

## <a name="request-access-to-the-public-preview"></a>请求访问公共预览版

填写并提交 [认知服务请求表单](https://aka.ms/csgate) ，请求访问运行状况公共预览版文本分析。 不会向你收取文本分析的健康状况。 

通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交该表单后，Azure 认知服务团队将会对其进行评审，并通过一种决定向你发送电子邮件。

> [!IMPORTANT]
> * 在此表单上，必须使用与 Azure 订阅 ID 关联的电子邮件地址。
> * 你使用的 Azure 资源必须使用已批准的 Azure 订阅 ID 创建。 
> * 请检查你的电子邮件（“收件箱”和“垃圾邮件”文件夹）以获取来自 Microsoft 的应用程序状态更新。

## <a name="using-the-docker-container"></a>使用 Docker 容器 

若要在自己的环境中运行运行状况容器的文本分析，请按照以下 [说明下载并安装该容器](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)。

## <a name="using-the-client-library"></a>使用客户端库

文本分析客户端库的最新预发行版使你能够使用客户端对象调用运行状况文本分析。 请参阅参考文档，并查看 GitHub 上的示例：
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>发送 REST API 请求 

### <a name="preparation"></a>准备工作

当你为其提供较小的要处理的文本量时，文本分析 for health 会生成更高质量的结果。 这与一些其他文本分析功能（如关键短语提取）相反，在较大的文本块上执行效果更佳。 若要从这些操作获得最佳结果，请考虑相应地重构输入。

必须拥有以下格式的 JSON 文档：ID、文本和语言 

每个文档的大小必须少于 5,120 个字符， 有关集合中允许的最大文档数，请参阅概念下的 [数据限制](../concepts/data-limits.md?tabs=version-3) 一文。 集合在请求正文中提交。

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>为托管的异步 web API 构造 API 请求

对于容器和托管 web API，必须创建 POST 请求。 你可以 [使用 Postman](text-analytics-how-to-call-api.md)中的 [文本分析 "运行状况托管 API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health)" 中的、一种卷命令或 **API 测试控制台** 来快速构建 POST 请求并将其发送到所需区域中的托管 web API。 

下面是附加到运行状况 API 请求的 POST 正文的文本分析的 JSON 文件的示例：

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

### <a name="hosted-asynchronous-web-api-response"></a>托管的异步 web API 响应 

由于此 POST 请求用于提交异步操作的作业，因此响应对象中没有任何文本。  但是，您需要响应标头中的操作位置键的值，以发出 GET 请求来检查作业的状态和输出。  下面是 POST 请求的响应标头中操作位置键的值的示例：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

若要检查作业状态，请在 POST 响应的操作位置键标头的值中向 URL 发出 GET 请求。  以下状态用于反映作业的状态： `NotStarted` 、 `running` 、、、、 `succeeded` `failed` `rejected` `cancelling` 和 `cancelled` 。  

你可以使用或状态取消与 `NotStarted` `running` GET 请求相同的 URL 的删除 HTTP 调用来取消作业。  有关删除调用的详细信息，请 [参阅运行状况托管 API 参考文本分析](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)。

下面是 GET 请求响应的示例。  请注意，输出可用于检索，直到 `expirationDateTime` 从创建作业开始 (24 小时后) 传递输出后的时间。

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
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
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>为容器构造 API 请求

你可以 [使用以下 Postman](text-analytics-how-to-call-api.md) 或示例卷请求将查询提交到部署的容器，并将该变量替换为 `serverURL` 适当的值。  请注意，容器的 URL 中的 API 版本不同于托管 API。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

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

下面的 JSON 是容器化同步调用中的运行状况 API 响应正文的文本分析示例：

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>否定检测输出

在某些情况下，使用否定检测时，单个否定词语一次可以处理多个词语。 已识别的实体在 JSON 输出中用标记的布尔值表示 `isNegated` ，例如：

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>关系提取输出

关系提取输出包含对关系的源的 URI 引用及其目标 。 将具有 `ENTITY` 关系角色的实体分配给 `target` 字段。 将具有 `ATTRIBUTE` 关系角色的实体分配给 `source` 字段。 缩写关系包含双向 `source` 和 `target` 字段，并且 `bidirectional` 将设置为 `true`。 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [“命名实体”类别](../named-entity-types.md)
* [新增功能](../whats-new.md)
