---
title: 参考：表单识别器 2.0 REST API
description: 使用表单识别器 REST API 创建一个表单处理应用，该应用从自定义文档中提取键/值对和表数据。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: 8bc01edf8c89376fe0757deb0e8813ca53be80cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641728"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> 本指南使用 cURL 执行 REST API 调用。

|[表单识别器 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)|[Azure REST API 参考](/rest/api/azure/)|

## <a name="prerequisites"></a>先决条件

* 已安装 [cURL](https://curl.haxx.se/windows/)。
* [PowerShell 6.0 及以上版本](/powershell/scripting/install/installing-powershell-core-on-windows)，或类似的命令行应用程序。
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据集的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）的“训练”文件夹下的文件。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="创建表单识别器资源"  target="_blank">创建表单识别器资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 回执图像的 URL。 在本快速入门中，可以使用[示例图像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)。
* 名片图像的 URL。 在本快速入门中，可以使用[示例图像](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)。
* 账单图像的 URL。 在本快速入门中，可使用[示例文档](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用表单识别器 REST API 执行以下任务：
<!-- markdownlint-disable MD001 -->

* [分析布局](#analyze-layout)
* [分析回执](#analyze-receipts)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-custom-models)



## <a name="analyze-layout"></a>分析布局

可以使用表单识别器来分析并提取文档中的表格、选定内容标记、文本和结构，而无需训练模型。 有关布局提取的详细信息，请参阅[布局概念指南](../../concept-layout.md)。 运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `\"{your-document-url}` 替换为示例 URL 之一。

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头。 此标头的值包含一个可用于查询异步操作状态和获取结果的操作 ID。 在以下示例中，`analyzeResults/` 后面的字符串就是操作 ID。

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>获取布局结果

调用 [https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) API 后，可调用 [https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult) API 来获取操作状态和已提取的数据 。 运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{resultId}` 替换为上一步中的操作 ID。
<!-- markdownlint-disable MD024 -->

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>检查结果

你将收到包含 JSON 内容的 `200 (success)` 响应。

请查看以下发票图像及其相应的 JSON 输出。

* `"readResults"` 节点包含每一行文本，以及其各自在页面上的边界框位置。
* `"pageResults"` 部分包含提取的表。 对于每个表，将会提取文本、行和列索引、行和列跨距、边界框等。

:::image type="content" source="../../media/contoso-invoice.png" alt-text="包含表的 Contoso 项目声明文档。":::

为了简单起见，已缩短输出。 请参阅 [GitHub 上的完整示例输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

## <a name="analyze-receipts"></a>分析回执

本部分演示如何使用预先训练的收据模型分析和提取美国收据中的常见字段。 有关收据分析的详细信息，请参阅[收据概念指南](../../concept-receipts.md)。 若要开始分析回执，可使用以下 cURL 命令调用[分析收据](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync) API。 运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{your receipt URL}` 替换为回执图像的 URL 地址。
1. 将 `{subscription key>` 替换为从上一步复制的订阅密钥。

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头。 此标头的值包含一个可用于查询异步操作状态和获取结果的操作 ID。 在以下示例中，`operations/` 后面的字符串就是操作 ID。

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>获取回执结果

调用了“分析回执”API 后，可以调用 **[获取分析收据结果](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** API，以获取操作状态和已提取的数据。 运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅密钥中获得的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。
1. 将 `{operationId}` 替换为上一步中的操作 ID。
1. 将 `{subscription key}` 替换为订阅密钥。

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>检查响应

你将收到包含 JSON 输出的 `200 (Success)` 响应。 第一个字段 `"status"` 指示操作的状态。 如果操作未完成，`"status"` 的值将为 `"running"` 或 `"notStarted"`，你应当采用手动方式或通过脚本再次调用该 API。 我们建议两次调用间隔一秒或更长时间。

`"readResults"` 节点包含所有已识别的文本（如果将可选 includeTextDetails 参数设置为 `true`）。 文本按页，然后按行，然后按单个单词进行组织。 `"documentResults"` 节点包含模型发现的特定于回执的值。 你将在这里找到有用的键/值对，如税款、总计、商家地址等。

请查看以下回执图像及其相应的 JSON 输出。

![Contoso 商店提供的回执](../../media/contoso-allinone.jpg)

为便于阅读，已将此输出缩短。 请参阅 [GitHub 上的完整示例输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)。

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="train-a-custom-model"></a>训练自定义模型

若要训练自定义模型，需要 Azure 存储 Blob 中的一组训练数据。 至少需要相同类型/结构的五个已填写表单（PDF 文档和/或图像）。 有关整理训练数据的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。

在没有已标记数据的情况下进行训练是默认操作，并且更简单。 或者，可以预先手动标记部分或全部训练数据。 这是一个更为复杂的过程，但会生成更好的经过训练的模型。

> [!NOTE]
> 你还可以使用图形用户界面（例如[表单识别器示例标记工具](../../label-tool.md)）来训练模型。

### <a name="train-a-model-without-labels"></a>不使用标签训练模型

若要使用 Azure Blob 容器中的文档训练表单识别器模型，请运行下面的 cURL 命令来调用 [训练自定义模型]https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) API。 运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{SAS URL}` 替换为 Azure Blob 存储容器的共享访问签名 (SAS) URL。 

[!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 检索":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

你将收到包含“Location”标头的 `201 (Success)` 响应  。 此标头的值是要训练的新模型的 ID。

### <a name="train-a-model-with-labels"></a>使用标签训练模型

若要使用标签训练，你需要在 blob 存储容器中添加特殊标签信息文件 (`\<filename\>.pdf.labels.json`) 和训练文档。 [表单识别器示例标记工具](../../label-tool.md)提供了可帮助你创建这些标签文件的 UI。 有了这些文件之后，可以在 JSON 正文中将 `"useLabelFile"` 参数设置为 `true`，调用 **[训练自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API。

运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{SAS URL}` 替换为 Azure Blob 存储容器的共享访问签名 (SAS) URL。 [!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 检索":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

你将收到包含“Location”标头的 `201 (Success)` 响应  。 此标头的值是要训练的新模型的 ID。

### <a name="get-training-results"></a>获取训练结果

开始训练操作后，可以使用新操作[获取自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)检查训练状态  。 将模型 ID 传递到此 API 调用以检查训练状态：

1. 将 `{Endpoint}` 替换为从表单识别器订阅密钥中获得的终结点。
1. 将 `{subscription key}` 替换为订阅密钥
1. 将 `{model ID}` 替换为在上一步骤收到的模型 ID

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

你将收到以下格式的 JSON 正文的 `200 (Success)` 响应。 请注意 `"status"` 字段。 完成训练后，将出现值 `"ready"`。 如果模型没有完成训练，则需要重新运行该命令以再次查询该服务。 我们建议两次调用间隔一秒或更长时间。

`"modelId"` 字段包含要训练的模型的 ID。 下一步骤需要用到此字段。

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

接下来，使用新的经过训练的模型分析文档并从中提取键值对和表。 运行以下 cURL 命令来调用[分析表单](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API  。 运行该命令之前，请进行以下更改：

1. 将 `{Endpoint}` 替换为从表单识别器订阅密钥中获取的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `{model ID}` 替换为在上一部分收到的模型 ID。
1. 将 `{SAS URL}` 替换为指向 Azure 存储中文件的 SAS URL。 按照“训练”部分中的步骤操作，但不是获取整个 blob 容器的 SAS URL，而是获取要分析的特定文件的 URL。
1. 将 `{subscription key}` 替换为订阅密钥。

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头  。 此标头的值包括用于跟踪“分析”操作结果的结果 ID。 保存此结果 ID 以供下一步使用。

### <a name="get-the-analyze-results"></a>获取分析结果

调用获取 **[分析表单结果](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeFormResult)** API 可查询分析操作的结果。

1. 将 `{Endpoint}` 替换为从表单识别器订阅密钥中获取的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `{result ID}` 替换为上一部分中收到的 ID。
1. 将 `{subscription key}` 替换为订阅密钥。

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

你将收到以下格式的 JSON 正文的 `200 (Success)` 响应。 为了简单起见，已缩短输出。 请注意底部附近的 `"status"` 字段。 完成“分析”操作时，会出现 `"succeeded"` 值。 如果“分析”操作尚未完成，则需要重新运行该命令以再次查询该服务。 我们建议两次调用间隔一秒或更长时间。

在不使用标签训练的自定义模型中，键/值对关联和表位于 JSON 输出的 `"pageResults"` 节点中。 在使用标签训练的自定义模型中，键/值对关联位于 `"documentResults"` 节点中。 如果还通过 includeTextDetails URL 参数指定了纯文本提取，则 `"readResults"` 节点将显示文档中所有文本的内容和位置  。

为了简单起见，已缩短此示例 JSON 输出。 请参阅 [GitHub 上的完整示例输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)。

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="improve-results"></a>改进结果

[!INCLUDE [improve results](improve-result-unlabeled.md)]

## <a name="manage-custom-models"></a>管理自定义模型

### <a name="get-a-list-of-custom-models"></a>获取自定义模型列表

在以下命令中使用 **[列出自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModels)** API 返回一个列表，其中列出属于你的订阅的所有自定义模型。

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

你将收到一个 `200` 成功响应，其中包含如下所示的 JSON 数据。 `"modelList"` 元素包含所有已创建的模型及其信息。

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>获取特定模型

若要检索有关特定自定义模型的详细信息，请在以下命令中使用 **[获取自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** API。

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{modelId}` 替换为要查找的自定义模型的 ID。

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

你将收到一个 `200` 成功响应，其中包含如下所示的 JSON 数据。

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>从资源帐户中删除模型

还可以通过引用模型的 ID 从帐户中删除该模型。 此命令调用 **[删除自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/DeleteCustomModel)** API，以删除在上一部分中使用的模型。

1. 将 `{Endpoint}` 替换为从表单识别器订阅中获取的终结点。
1. 将 `{subscription key}` 替换为从上一步复制的订阅密钥。
1. 将 `{modelId}` 替换为要查找的自定义模型的 ID。

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

你将收到 `204` 成功响应，指示模型已标记为待删除。 模型项目将在 48 小时内删除。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 REST API 以不同的方式训练模型和分析表单。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
