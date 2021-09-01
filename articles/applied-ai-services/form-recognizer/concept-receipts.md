---
title: 收据 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 API 了解与收据分析相关的概念 - 使用和限制。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 1efe839a1344f28d2f3033d67de2dd7035320f08
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326341"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>表单识别器预生成收据模型

许多企业和个人仍然依赖于从销售收据中手动提取的数据。 自动从这些收据提取数据可能会很复杂。 收据可能皱褶、难于辨认、有手写部分，还包含低质量的手机图像。 另外，收据模板和字段可能因市场、地区和商家而有很大的差异。 这些数据提取和字段检测难题使收据处理成为一个独特的问题。

Azure 表单识别器可以使用其预生成的收据模型分析和提取销售收据中的信息。 它结合了强大的[光学字符识别 (OCR)](../../cognitive-services/computer-vision/overview-ocr.md) 功能与深度学习模型，可以从用英语撰写的收据中提取关键信息，如商家名称、商家电话号码、交易日期、交易总额等。

## <a name="customer-scenarios"></a>客户场景

### <a name="business-expense-reporting"></a>业务零用金报销单

通常，归档业务支出需要花时间手动输入收据图像中的数据。 使用收据 API，可以使用提取的字段部分自动执行此过程，并快速分析收据。

收据 API 是一个简单的 JSON 输出，允许你以多种方式使用提取的字段值。 与内部费用申请集成，以预填充零用金报销单。 有关此场景的详细信息，请阅读 Acumatica 如何利用收据 API 来[简化零用金报销的过程](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)。

### <a name="auditing-and-accounting"></a>审核和记帐

还可以使用收据 API 输出在开支报告和报销过程的不同时间点对大量支出进行分析。 可以处理收据，对其进行分类，以便手动审核或快速批准。

收据输出对于企业或个人的一般帐簿也很有用。 使用收据 API，可以将任何原始收据图像/PDF 数据转换为可操作的数字输出。

### <a name="consumer-behavior"></a>消费者行为

收据包含有用的数据，可用于分析消费者行为和购物趋势。

收据 API 还支持 [AI Builder 收据处理功能](/ai-builder/prebuilt-receipt-processing)。

## <a name="try-it"></a>试用

若要试用表单识别器收据服务，请转到联机 UI 工具示例：

> [!div class="nextstepaction"]
> [试用收据模型](https://aka.ms/fott-2.1-ga "首先使用预生成模型从收据中提取数据。")

## <a name="what-does-the-receipt-service-do"></a>收据服务有什么作用？

预生成的收据服务提取销售收据的内容 &mdash; 通常在餐馆、零售商或杂货店收到的收据类型。

![收据示例](./media/receipts-example.jpg)

### <a name="fields-extracted"></a>提取的字段

|名称| 类型 | 说明 | 文本 | 值（标准化输出） |
|:-----|:----|:----|:----| :----|
| ReceiptType | 字符串 | 销售收据类型 |  | Itemized |
| MerchantName | 字符串 | 开具收据的商家的名称 | Contoso |  |
| MerchantPhoneNumber | phoneNumber | 列出的商户电话号码 | 987-654-3210 | +19876543210 |
| MerchantAddress | 字符串 | 列出的商家地址 | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | 开具收据的日期 | 2019 年 6 月 6 日 | 2019-06-26  |
| TransactionTime | time | 开具收据的时间 | 下午 4:49 | 16:49:00  |
| 总计 | 数字 | 全部交易收据总额 | 14.34 美元 | 14.34 |
| 小计 | 数字 | 收据小计，通常在纳税前 | 12.34 美元 | 12.34 |
| 税款 | 数字 | 收据上的税金，通常为销售税或等价税款 | $2.00 | 2.00 |
| 提示 | 数字 | 买家提供的小费 | 1\.00 美元 | 1.00 |
| 项 | 对象数组 | 提取的行项，其中包含名称、数量、单价和提取的总价格 | |
| 名称 | 字符串 | 项名称 | Surface Pro 6 | |
| 数量 | 数字 | 每个项的数量 | 1 | 1 |
| 价格 | 数字 | 每个项单位的单独价格 | 999.00 美元 | 999.00 |
| 总价 | 数字 | 行项总价 | 999.00 美元 | 999.00 |

### <a name="additional-features"></a>其他功能

收据 API 还会返回以下信息：

* 字段可信度（每个字段都返回关联的置信度值）
* OCR 原始文本（整个收据的 OCR 提取文本输出）
* 每个值、行和字的边界框

## <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支持的区域设置

* 预生成的 receipt v2.1 支持 en-au、en-ca、en-gb、en-in 和 en-us 英语区域设置的销售收据     

  > [!NOTE]
  > 语言输入
  >
  > 预生成的 Receipt v2.1 提供了一个可选的请求参数，用于指定来自其他英语市场的收据区域设置。 对于来自澳大利亚 (en-au)、加拿大 (en-ca)、英国 (en-gb) 和印度 (en-in) 的英语销售收据，可以指定区域设置以获得改进的结果。 如果在 v2.1 中未指定任何区域设置，则模型会自动检测区域设置。

## <a name="analyze-receipt"></a>分析回执

[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync) 采用收据的图像或 PDF 作为输入，并提取相关值和文本。 该调用返回一个名为 `Operation-Location` 的响应标头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="get-analyze-receipt-result"></a>获取分析收据结果

第二步是调用 [Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeReceiptResult) 操作。 此操作采用 Analyze Receipt 操作创建的结果 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 3 到 5 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：操作尚未启动。 |
| |  | running：分析操作正在进行。 |
| |  | failed：分析操作失败。 |
| |  | succeeded：分析操作成功。 |

当“状态”字段的值为“succeeded”时，JSON 响应将包括收据理解和文本识别结果。 收据理解结果组织为一个命名字段值字典。 每个值都包含提取的文本、规范化值、边界框、置信度和相应的词元素。 文本识别结果组织成由行和词组成的层次结构，包含文本、边界框和置信度信息。

![示例收据结果](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get Analyze Receipt Result 操作的响应将是已提取所有信息的收据的结构化表示形式。  有关[示例收据文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg)及其结构化输出[示例收据输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)，请参阅此处。

请参阅以下成功的 JSON 响应示例（为简单起见，已缩短了输出）：
* `"readResults"` 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。
* `"documentResults"` 节点包含模型发现的特定于名片的值。 在此，你可以找到有用的键/值对，如名字、姓氏、公司名等。

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.0.0",
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

## <a name="next-steps"></a>后续步骤

* 在[表单识别器示例 UI](https://fott-preview.azurewebsites.net/) 中尝试你自己的收据和示例。
* 完成[表单识别器快速入门](quickstarts/client-library.md)，开始使用表单识别器以你选择的开发语言编写收据处理应用。
