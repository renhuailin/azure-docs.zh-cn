---
title: 名片 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 API 了解与名片分析相关的概念 - 使用和限制。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 779c65487fd34a94426c2f9dc8a6ffaad36d2313
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676516"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>表单识别器预生成的名片模型

Azure 表单识别器可以使用其预生成的名片模型来分析和提取名片中的联系人信息。 它结合了强大的光学字符识别 (OCR) 功能与我们的名片理解模型，可从英文名片中提取重要信息。 它提取个人联系信息、公司名称、职务等。 预生成名片 API 在表单识别器 v2.1 中公开提供。

## <a name="customer-scenarios"></a>客户场景

通过名片 API 提取的数据可用于执行各种任务。 自动提取此联系人信息为面向客户的角色的用户节省时间。 以下是我们的客户通过名片 API 完成的一些示例：

* 从名片提取联系人信息并快速创建电话联系人。
* 与 CRM 集成，使用名片图像自动创建联系人。
* 跟踪潜在销售顾客。
* 从现有名片图像批量提取联系人信息。

此名片 API 还支持 [AI Builder 名片处理功能](/ai-builder/prebuilt-business-card)。

## <a name="try-it"></a>试用

若要试用表单识别器收据服务，请转到联机 UI 工具示例：

> [!div class="nextstepaction"]
> [试用名片模型](https://aka.ms/fott-2.1-ga "首先使用预生成模型从名片中提取数据")

## <a name="what-does-the-business-card-service-do"></a>名片服务有什么作用？

预生成的名片 API 从名片提取关键字段，并以有组织的 JSON 响应返回它们。

![示例标记工具 + JSON 输出的 Contoso 逐项图像](./media/business-card-example.jpg)

### <a name="fields-extracted"></a>提取的字段：

|名称| 类型 | 说明 | 文本 | 值（标准化输出） |
|:-----|:----|:----|:----|:----|
| ContactNames | 对象数组 | 从名片提取的联系人姓名 | [{ "FirstName": "Chris", "LastName": "Smith" }] | |
| FirstName | 字符串 | 联系人的名字 | "Chris" | "Chris" |
| LastName | 字符串 | 联系人的姓氏 |     "Smith" | "Smith" |
| CompanyNames | 字符串数组 | 从名片提取的公司名称 | ["CONTOSO"] | CONTOSO |
| Departments | 字符串数组 | 联系人的部门或组织 | ["Cloud & Al Department"] | Cloud & Al Department |
| JobTitles | 字符串数组 | 列出的联系人职称 | ["Senior Researcher"] | 高级研究员 |
| 电子邮件 | 字符串数组 | 从名片提取的联系人电子邮件 | ["chris.smith@contoso.com"] | chris.smith@contoso.com |
| 网站 | 字符串数组 | 从名片提取的网站 | ["https://www.contoso.com"] | https://www.contoso.com |
| 地址 | 字符串数组 | 从名片提取的地址 | ["4001 1st Ave NE Redmond, WA 98052"] | 4001 1st Ave NE Redmond, WA 98052 |
| MobilePhones | 电话号码数组 | 从名片提取的移动电话号码 | ["+1 (987) 123-4567"] | +19871234567 |
| 传真 | 电话号码数组 | 从名片提取的传真号码 | ["+1 (987) 312-6745"] | +19873126745 |
| WorkPhones | 电话号码数组 | 从名片提取的工作电话号码 | ["+1 (987) 213-5674"] | +19872135674 | 
| OtherPhones     | 电话号码数组 | 从名片提取的其他电话号码 | ["+1 (987) 213-5673"] | +19872135673 |

名片 API 还可以从名片返回所有已识别的文本。 此 OCR 输出包含在 JSON 响应中。

### <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支持的区域设置

预生成名片 v2.1 支持以下区域设置：en-us、en-au、en-ca、en-gb、en-in     

## <a name="analyze-business-card"></a>分析名片

[Analyze Business Card](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync) 名片会将名片的图像或 PDF 作为输入，并提取相关值。 该调用返回一个名为 `Operation-Location` 的响应标头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync` |

## <a name="get-analyze-business-card-result"></a>获取分析名片结果

第二步是调用 [Get Analyze Business Card Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult) 操作。 此操作采用 Analyze Business Card 操作创建的结果 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 3 到 5 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：分析操作尚未启动。<br /><br />running：分析操作正在进行。<br /><br />failed：分析操作失败。<br /><br />succeeded：分析操作成功。|

当“状态”字段的值为“succeeded”时，JSON 响应将包括名片理解结果和可选的文本识别结果（如果请求）。 名片理解结果组织成一个命名字段值字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和相应的词元素。 文本识别结果组织成由行和词组成的层次结构，包含文本、边界框和置信度信息。

![名片输出示例](./media/business-card-results.png)

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get Analyze Business Card Result 操作的响应将是已提取所有信息的名片的结构化表示形式。  在此处查看[示例名片文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg)及其结构化输出[示例名片输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)。

请参阅以下成功的 JSON 响应示例（为简单起见，已缩短了输出）：
* `"readResults"` 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。
* `"documentResults"` 节点包含模型发现的特定于名片的值。 在此，你可以找到有用的联系人信息，如名字、姓氏、公司名称等。

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-05-27T02:18:35Z",
    "lastUpdatedDateTime": "2021-05-27T02:18:37Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0.0255,
                "width": 2592,
                "height": 4608,
                "unit": "pixel",
                "lines": [
                    {
                        "text": "CONTOSO",
                        "boundingBox": [
                            533,
                            1570,
                            1334,
                            1570,
                            1333,
                            1721,
                            533,
                            1720
                        ],
                        "words": [
                            {
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "confidence": 0.994
                            }
                        ],
                        "appearance": {
                            "style": {
                                "name": "other",
                                "confidence": 0.878
                            }
                        }
                    },
                    ...
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
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "4001 1st Ave NE Redmond, WA 98052",
                                "text": "4001 1st Ave NE Redmond, WA 98052",
                                "boundingBox": [
                                    400,
                                    2789,
                                    1514,
                                    2789,
                                    1514,
                                    2857,
                                    400,
                                    2857
                                ],
                                "page": 1,
                                "confidence": 0.986,
                                "elements": [
                                    "#/readResults/0/lines/9/words/0",
                                    "#/readResults/0/lines/9/words/1",
                                    "#/readResults/0/lines/9/words/2",
                                    "#/readResults/0/lines/9/words/3",
                                    "#/readResults/0/lines/9/words/4",
                                    "#/readResults/0/lines/9/words/5",
                                    "#/readResults/0/lines/9/words/6"
                                ]
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "CONTOSO",
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "page": 1,
                                "confidence": 0.985,
                                "elements": [
                                    "#/readResults/0/lines/0/words/0"
                                ]
                            }
                        ]
                    },
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Chris",
                                        "text": "Chris",
                                        "boundingBox": [
                                            1556,
                                            2018,
                                            1915,
                                            2021,
                                            1915,
                                            2156,
                                            1558,
                                            2154
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/0"
                                        ]
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1944,
                                            2021,
                                            2368,
                                            2016,
                                            2364,
                                            2156,
                                            1944,
                                            2156
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/1"
                                        ]
                                    }
                                },
                                "text": "Chris Smith",
                                "boundingBox": [
                                    1556.1,
                                    2010.3,
                                    2368,
                                    2016,
                                    2367,
                                    2159.6,
                                    1555.1,
                                    2154
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/1/words/0",
                                    "#/readResults/0/lines/1/words/1"
                                ]
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
                                    1578,
                                    2288.8,
                                    2277,
                                    2295.1,
                                    2276.3,
                                    2367.8,
                                    1577.3,
                                    2361.5
                                ],
                                "page": 1,
                                "confidence": 0.989,
                                "elements": [
                                    "#/readResults/0/lines/3/words/0",
                                    "#/readResults/0/lines/3/words/1",
                                    "#/readResults/0/lines/3/words/2",
                                    "#/readResults/0/lines/3/words/3"
                                ]
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "chris.smith@contoso.com",
                                "text": "chris.smith@contoso.com",
                                "boundingBox": [
                                    1583,
                                    2381,
                                    2309,
                                    2382,
                                    2308,
                                    2445,
                                    1584,
                                    2447
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/4/words/0"
                                ]
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19873126745",
                                "text": "+1 (987) 312-6745",
                                "boundingBox": [
                                    740,
                                    2703.8,
                                    1273,
                                    2702.1,
                                    1273.2,
                                    2774.1,
                                    740.2,
                                    2775.8
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/8/words/1",
                                    "#/readResults/0/lines/8/words/2",
                                    "#/readResults/0/lines/8/words/3"
                                ]
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
                                    1578,
                                    2206,
                                    2117,
                                    2207.6,
                                    2116.8,
                                    2272.6,
                                    1577.8,
                                    2271
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/2/words/0",
                                    "#/readResults/0/lines/2/words/1"
                                ]
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19871234567",
                                "text": "+1 (987) 123-4567",
                                "boundingBox": [
                                    744,
                                    2529,
                                    1281,
                                    2529,
                                    1281,
                                    2603,
                                    744,
                                    2603
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/5/words/1",
                                    "#/readResults/0/lines/5/words/2",
                                    "#/readResults/0/lines/5/words/3"
                                ]
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
                                    1576,
                                    2462,
                                    2383,
                                    2462,
                                    2380,
                                    2535,
                                    1576,
                                    2535
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/6/words/0"
                                ]
                            }
                        ]
                    },
                    "WorkPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19872135674",
                                "text": "+1 (987) 213-5674",
                                "boundingBox": [
                                    736,
                                    2617.6,
                                    1267.1,
                                    2618.5,
                                    1267,
                                    2687.5,
                                    735.9,
                                    2686.6
                                ],
                                "page": 1,
                                "confidence": 0.984,
                                "elements": [
                                    "#/readResults/0/lines/7/words/1",
                                    "#/readResults/0/lines/7/words/2",
                                    "#/readResults/0/lines/7/words/3"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>后续步骤

* 在[表单识别器示例 UI](https://fott-preview.azurewebsites.net/) 中尝试你自己的商务名片和示例。
* 完成[表单识别器快速入门](quickstarts/client-library.md)，开始使用表单识别器以你选择的开发语言编写名片处理应用。
