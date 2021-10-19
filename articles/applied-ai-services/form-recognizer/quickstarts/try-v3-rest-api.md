---
title: 快速入门：表单识别器 REST API | 预览版
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 REST API v3.0（预览版）进行表单和文档处理、数据提取和分析
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: fc23ef0b4311a01e5d7bae1904f9a04ef77a0478
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716658"
---
# <a name="get-started-form-recognizer-rest-api---preview"></a>入门：表单识别器 REST API | 预览版

>[!NOTE]
> 表单识别器 v3.0 当前为公共预览版。 某些功能可能不受支持或者受限。 

| [表单识别器 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) | [Azure REST API 参考](/rest/api/azure/) |

Azure 认知服务表单识别器是一种云服务，它使用机器学习从文档中提取和分析表单字段、文本和表。 可通过将客户端库 SDK 集成到工作流和应用程序，轻松调用表单识别器模型。

### <a name="form-recognizer-models"></a>表单识别器模型

 该 REST API 支持以下模型和功能：

* 🆕常规文档 - 分析和提取文本、表、结构、键值对和命名实体。|
* 布局 - 分析和提取表、行、字和选择标记（如表单文档中的单选按钮和复选框）而无需训练模型。
* 自定义 - 通过使用自己的表单类型训练的模型，分析和提取自定义表单中的表单字段和其他内容。
* 发票 - 使用预先训练的发票模型分析和提取发票中的常见字段。
* 收据 - 使用预先训练的收据模型分析和提取收据中的常见字段。
* ID 文档 - 使用预先训练的 ID 文档模型分析和提取 ID 文档（如护照或驾驶执照）中的常见字段。
* 名片 - 使用预先训练的名片模型分析和提取名片中的常见字段。

在本快速入门中，你将使用以下功能来分析和提取表单和文档中的数据和值：

* [常规文档](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [预生成的发票]#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)

* 已安装 [cURL](https://curl.haxx.se/windows/)。

* [PowerShell 6.0 及以上版本](/powershell/scripting/install/installing-powershell-core-on-windows)，或类似的命令行应用程序。

* 认知服务或表单识别器资源。 拥有 Azure 订阅后，在 Azure 门户中创建[单一服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)或[多服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)表单识别器资源，以获取密钥和终结点。 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

> [!TIP]
> 如果计划通过一个终结点/密钥访问多个认知服务，请创建认知服务资源。 若要仅访问表单识别器，请创建表单识别器资源。 请注意，若要使用 [Azure Active Directory 身份验证](/azure/active-directory/authentication/overview-authentication)，则需要单一服务资源。

* 部署资源后，单击“转到资源”。 需从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中：

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

## <a name="try-it-general-document-model"></a>**试用**：常规文档模型

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个表单文档文件。 在本快速入门中，可使用[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。 运行该命令之前，请进行以下更改：

1. 将 `{endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `\"{your-document-url}` 替换为示例表单文档 URL。

#### <a name="request"></a>请求

```bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头。 此头的值包含一个可用于查询异步操作状态和获取结果的结果 ID：

https:\//{host}/formrecognizer/documentModels/{modelId}/analyzeResults/{resultId}?api-version=2021-07-30-preview

### <a name="get-general-document-results"></a>获取常规文档结果

调用[分析文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) API 后，可调用[获取分析结果](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetAnalyzeDocumentResult) API，以获取操作状态和已提取的数据 。 运行该命令之前，请进行以下更改：

1. 将 `{endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{resultId}` 替换为上一步中的结果 ID。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>请求

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>检查响应

你将收到包含 JSON 输出的 `200 (Success)` 响应。 第一个字段 `"status"` 指示操作的状态。 如果操作未完成，`"status"` 的值将为 `"running"` 或 `"notStarted"`，你应当采用手动方式或通过脚本再次调用该 API。 我们建议两次调用间隔一秒或更长时间。

`"analyzeResults"` 节点包含所有已识别的文本。 文本按页、行、表、键值对和实体进行组织。

#### <a name="sample-response"></a>示例响应

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-09-28T16:52:51Z",
    "lastUpdatedDateTime": "2021-09-28T16:53:08Z",
    "analyzeResult": {
        "apiVersion": "2021-09-30-preview",
        "modelId": "prebuilt-document",
        "stringIndexType": "textElements",
        "content": "content extracted",
        "pages": [
            {
                "pageNumber": 1,
                "angle": 0,
                "width": 8.4722,
                "height": 11,
                "unit": "inch",
                "words": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            1.7328,
                            0.2244,
                            1.7328,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "confidence": 1,
                        "span": {
                            "offset": 0,
                            "length": 4
                        }
                    }

                ],
                "lines": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            3.2879,
                            0.2244,
                            3.2879,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "spans": [
                            {
                                "offset": 0,
                                "length": 22
                            }
                        ]
                    }
                ]
            }
        ],
        "tables": [
            {
                "rowCount": 8,
                "columnCount": 3,
                "cells": [
                    {
                        "kind": "columnHeader",
                        "rowIndex": 0,
                        "columnIndex": 0,
                        "rowSpan": 1,
                        "columnSpan": 1,
                        "content": "Applicant's Name:",
                        "boundingRegions": [
                            {
                                "pageNumber": 1,
                                "boundingBox": [
                                    1.9198,
                                    4.277,
                                    3.3621,
                                    4.2715,
                                    3.3621,
                                    4.5034,
                                    1.9198,
                                    4.5089
                                ]
                            }
                        ],
                        "spans": [
                            {
                                "offset": 578,
                                "length": 17
                            }
                        ]
                    }
                ],
                "spans": [
                    {
                        "offset": 578,
                        "length": 300
                    },
                    {
                        "offset": 1358,
                        "length": 10
                    }
                ]
            }
        ],
        "keyValuePairs": [
            {
                "key": {
                    "content": "Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.3578,
                                0.2244,
                                1.7328,
                                0.2244,
                                1.7328,
                                0.3502,
                                1.3578,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 0,
                            "length": 4
                        }
                    ]
                },
                "value": {
                    "content": "A Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.8026,
                                0.2276,
                                3.2879,
                                0.2276,
                                3.2879,
                                0.3502,
                                1.8026,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 5,
                            "length": 17
                        }
                    ]
                },
                "confidence": 0.867
            }
        ],
        "entities": [
            {
                "category": "Person",
                "content": "Jim Smith",
                "boundingRegions": [
                    {
                        "pageNumber": 1,
                        "boundingBox": [
                            3.4672,
                            4.3255,
                            5.7118,
                            4.3255,
                            5.7118,
                            4.4783,
                            3.4672,
                            4.4783
                        ]
                    }
                ],
                "confidence": 0.93,
                "spans": [
                    {
                        "offset": 596,
                        "length": 21
                    }
                ]
            }
        ],
        "styles": [
            {
                "isHandwritten": true,
                "confidence": 0.95,
                "spans": [
                    {
                        "offset": 565,
                        "length": 12
                    },
                    {
                        "offset": 3493,
                        "length": 1
                    }
                ]
            }
        ]
    }
}

```

## <a name="try-it-layout-model"></a>**试用**：布局模型

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个表单文档文件。 在本快速入门中，可使用[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。

 运行该命令之前，请进行以下更改：

1. 将 `{endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `\"{your-document-url}` 替换为示例 URL 之一。

#### <a name="request"></a>请求

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头。 此头的值包含一个可用于查询异步操作状态和获取结果的结果 ID：

`https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview`

### <a name="get-general-document-results"></a>获取常规文档结果

调用[分析文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) API 后，可调用[获取分析结果](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetAnalyzeDocumentResult) API，以获取操作状态和已提取的数据 。 运行该命令之前，请进行以下更改：

1. 将 `{endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{resultId}` 替换为上一步中的结果 ID。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>请求

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>检查响应

你将收到包含 JSON 输出的 `200 (Success)` 响应。 第一个字段 `"status"` 指示操作的状态。 如果操作未完成，`"status"` 的值将为 `"running"` 或 `"notStarted"`，你应当采用手动方式或通过脚本再次调用该 API。 我们建议两次调用间隔一秒或更长时间。

## <a name="try-it-prebuilt-invoice-model"></a>**试用**：预生成的发票模型

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个发票文档文件。 在本快速入门中，可使用[示例发票文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)。

### <a name="choose-the-invoice-prebuilt-model-id"></a>选择预生成的发票模型 ID

不止发票，还有几个预生成模型可供选择，每个模型都有自己的一组受支持的字段。 用于分析操作的模型取决于要分析的文档类型。 下面是表单识别器服务目前支持的预生成模型的模型 ID：

* **prebuilt-invoice**：从发票中提取文本、选择标记、表、键值对和密钥信息。
* **prebuilt-businessCard**：从名片中提取文本和密钥信息。
* **prebuilt-idDocument**：从驾驶执照和国际护照中提取文本和密钥信息。
* **prebuilt-receipt**：从收据中提取文本和密钥信息。

运行该命令之前，请进行以下更改：

1. 将 `{endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `\"{your-document-url}` 替换为示例 URL 之一。

#### <a name="request"></a>请求

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头。 此头的值包含一个可用于查询异步操作状态和获取结果的结果 ID：

https:\//{host}/formrecognizer/documentModels/{modelId}/analyzeResults/{resultId}?api-version=2021-07-30-preview

### <a name="get-general-document-results"></a>获取常规文档结果

调用[分析文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) API 后，可调用[获取分析结果](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetAnalyzeDocumentResult) API，以获取操作状态和已提取的数据 。 运行该命令之前，请进行以下更改：

1. 将 `{endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{resultId}` 替换为上一步中的结果 ID。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>请求

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>检查响应

你将收到包含 JSON 输出的 `200 (Success)` 响应。 第一个字段 `"status"` 指示操作的状态。 如果操作未完成，`"status"` 的值将为 `"running"` 或 `"notStarted"`，你应当采用手动方式或通过脚本再次调用该 API。 我们建议两次调用间隔一秒或更长时间。

### <a name="improve-results"></a>改进结果

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>管理自定义模型

### <a name="get-a-list-of-models"></a>获取模型列表

除了自定义模型外，预览版 v3.0   [列表模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels)请求还会返回预生成模型的分页列表。 仅包含状态为成功的模型。 正在进行或已失败的模型可通过[列出操作](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations)请求进行枚举。 使用 nextLink 属性访问下一页模型（如果有）。 若要获取有关每个返回的模型的详细信息（包括支持的文档和其字段的列表），请将 modelId 传递到 [获取模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations)请求。 

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels?api-version=2021-07-30-preview"
```

### <a name="get-a-specific-model"></a>获取特定模型

预览版 v3.0 [获取模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModel)可检索状态为成功的特定模型的有关信息。 对于失败和正在进行的模型，使用[获取操作](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperation)来跟踪模型创建操作的状态和任何产生的错误。

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview" 
```

### <a name="delete-a-model"></a>删除模型

预览版 v3.0 [删除模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/DeleteModel)请求会删除自定义模型，之后的操作将无法再访问 modelId。  可使用同一 modelId 创建新模型，这不会发生冲突。

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用表单识别器 REST API 预览版 (v3.0) 以不同的方式分析了表单。 接下来，请浏览参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 预览版 (v3.0) 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

