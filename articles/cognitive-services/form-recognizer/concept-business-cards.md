---
title: 名片 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 使用表单识别器 API 了解与名片分析相关的概念 - 使用和限制。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: lajanuar
ms.openlocfilehash: a7fb5eeb90a26d85b3e56706e0c2b32ceadc8d11
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2021
ms.locfileid: "108330883"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>表单识别器预生成的名片模型

Azure 表单识别器可以使用其预生成的名片模型来分析和提取名片中的联系人信息。 它结合了强大的光学字符识别 (OCR) 功能与我们的名片理解模型，可从英文名片中提取重要信息。 它提取个人联系信息、公司名称、职务等。 预生成名片 API 在表单识别器 v2.1 预览版中公开提供。

## <a name="what-does-the-business-card-service-do"></a>名片服务有什么作用？

预生成的名片 API 从名片提取关键字段，并以有组织的 JSON 响应返回它们。

![FOTT + JSON 输出的 Contoso 逐项图像](./media/business-card-example.jpg)

### <a name="fields-extracted"></a>提取的字段：

|名称| 类型 | 说明 | 文本 |
|:-----|:----|:----|:----|
| ContactNames | 对象数组 | 从名片提取的联系人姓名 | [{ "FirstName": "John", "LastName": "Doe" }] |
| FirstName | 字符串 | 联系人的名字 | "John" |
| LastName | 字符串 | 联系人的姓氏 |     "Doe" |
| CompanyNames | 字符串数组 | 从名片提取的公司名称 | [“Contoso”] |
| Departments | 字符串数组 | 联系人的部门或组织 | [“R&D”] |
| JobTitles | 字符串数组 | 列出的联系人职称 | [“Software Engineer”] |
| 电子邮件 | 字符串数组 | 从名片提取的联系人电子邮件 | ["johndoe@contoso.com"] |
| 网站 | 字符串数组 | 从名片提取的网站 | ["https://www.contoso.com"] |
| 地址 | 字符串数组 | 从名片提取的地址 | [“123 Main Street, Redmond, WA 98052”] |
| MobilePhones | 电话号码数组 | 从名片提取的移动电话号码 | [“+19876543210”] |
| 传真 | 电话号码数组 | 从名片提取的传真号码 | [“+19876543211”] |
| WorkPhones | 电话号码数组 | 从名片提取的工作电话号码 | [“+19876543231”] |
| OtherPhones     | 电话号码数组 | 从名片提取的其他电话号码 | [“+19876543233”] |


名片 API 还可以从名片返回所有已识别的文本。 此 OCR 输出包含在 JSON 响应中。

### <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支持的区域设置

预生成的名片 v2.1-preview.3（公共预览版）支持以下区域设置：

* **zh-cn**
* **en-au**
* **en-ca**
* **en-gb**
* **en-in**

## <a name="the-analyze-business-card-operation"></a>Analyze Business Card 操作

[Analyze Business Card](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync) 名片会将名片的图像或 PDF 作为输入，并提取相关值。 该调用返回一个名为 `Operation-Location` 的响应标头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Get Analyze Business Card Result 操作

第二步是调用 [Get Analyze Business Card Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult) 操作。 此操作采用 Analyze Business Card 操作创建的结果 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 3 到 5 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：分析操作尚未启动。<br /><br />running：分析操作正在进行。<br /><br />failed：分析操作失败。<br /><br />succeeded：分析操作成功。|

当“状态”字段的值为“succeeded”时，JSON 响应将包括名片理解结果和可选的文本识别结果（如果请求）。 名片理解结果组织成一个命名字段值字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和相应的词元素。 文本识别结果组织成由行和词组成的层次结构，包含文本、边界框和置信度信息。

![名片输出示例](./media/business-card-results.png)

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get Analyze Business Card Result 操作的响应将是已提取所有信息的名片的结构化表示形式。  在此处查看[示例名片文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg)及其结构化输出[示例名片输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)。

参阅下面的成功 JSON 响应示例：
* `"readResults"` 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。
* `"documentResults"` 节点包含模型发现的特定于名片的值。 在此，你可以找到有用的联系人信息，如名字、姓氏、公司名称等。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines":
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]

            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

按照[快速入门](./QuickStarts/client-library.md)中的步骤使用 Python 和 REST API 来实现名片数据提取。

## <a name="customer-scenarios"></a>客户方案

通过名片 API 提取的数据可用于执行各种任务。 自动提取此联系人信息为面向客户的角色的用户节省时间。 以下是我们的客户通过名片 API 完成的一些示例：

* 从名片提取联系人信息并快速创建电话联系人。
* 与 CRM 集成，使用名片图像自动创建联系人。
* 跟踪潜在销售顾客。
* 从现有名片图像批量提取联系人信息。

此名片 API 还支持 [AI Builder 名片处理功能](/ai-builder/prebuilt-business-card)。

## <a name="next-steps"></a>后续步骤

- 按照[快速入门](./quickstarts/client-library.md)中的步骤开始识别名片。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
