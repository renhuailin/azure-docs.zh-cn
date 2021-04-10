---
title: ID - 表单识别器
titleSuffix: Azure Cognitive Services
description: 通过表单识别器预生成 ID API 了解与标识文档中的数据提取相关的概念。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467934"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>表单识别器预生成标识卡 (ID) 模型

Azure 表单识别器可以使用其预生成 ID 模型分析和提取政府标识卡 (ID) 中的信息。 它结合了强大的[光学字符识别 (OCR)](../computer-vision/concept-recognizing-text.md) 功能与 ID 识别功能，可从全球护照和美国驾照（所有 50 个州和华盛顿特区）中提取重要信息。 ID API 从这些标识文档中提取关键信息，如名字、姓氏、出生日期、文档编号等。 此 API 在表单识别器 v2.1 预览版中作为云服务和本地容器提供。

## <a name="what-does-the-id-service-do"></a>ID 服务有什么作用？ 

预生成 ID 服务从全球护照和美国驾照中提取关键值，并以结构化的 JSON 响应返回这些值。 

![驾照示例](./media/id-example-drivers-license.JPG)

![护照示例](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>提取的字段

|名称| 类型 | 说明 | 值 | 
|:-----|:----|:----|:----|
|  国家/地区 | country | 符合 ISO 3166 标准的国家/地区代码 | “USA” | 
|  DateOfBirth | date | YYYY-MM-DD 格式的 DOB | “1980-01-01” | 
|  DateOfExpiration | date | YYYY-MM-DD 格式的到期日期 | “2019-05-05” |  
|  DocumentNumber | 字符串 | 相关护照号、驾照编号等。 | “340020013” |  
|  FirstName | 字符串 | 提取的名字和中间名（如适用） | “JENNIFER” | 
|  LastName | 字符串 | 提取的姓氏 | “BROOKS” |   
|  国家/地区 | country | 符合 ISO 3166 标准的国家/地区代码 | “USA” |
|  Sex | gender | 可能的提取值包括“M”、“F”和“X” | “F” | 
|  MachineReadableZone | 对象 (object) | 提取的护照 MRZ 包括两行，每行 44 个字符 | “P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816” |
|  DocumentType | 字符串 | 文档类型，例如护照、驾照 | “passport” |  
|  地址 | 字符串 | 提取的地址（仅限驾照） | “123 STREET ADDRESS YOUR CITY WA 99999-1234”|
|  区域 | 字符串 | 提取的区域、州、省/直辖市/自治区等。（仅限驾照） | “Washington” | 

### <a name="additional-features"></a>其他功能

ID API 还会返回以下信息：

* 字段可信度（每个字段都返回关联的置信度值）
* OCR 原始文本（整个收据的 OCR 提取文本输出）
* 美国驾照中每个提取字段的边界框
* 护照上计算机可读区域 (MRZ) 的边界框

  > [!NOTE]
  > 预生成 ID 不检测 ID 真实性
  >
  > 表单识别器预生成 ID 从 ID 数据中提取关键数据。 但是，它不检测原始标识文档的有效性或真实性。 

## <a name="try-it-out"></a>试试看

若要试用表单识别器 ID 服务，请转到联机 UI 工具示例：

> [!div class="nextstepaction"]
> [试用预生成模型](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>支持的 ID 类型  

* 预生成 ID v2.1-preview.3 从全球护照和美国驾照中提取关键值。 

  > [!NOTE]
  > ID 类型支持 
  >
  > 目前支持的 ID 类型包括全球护照和美国关键值。 我们正努力将我们的 ID 支持扩展到世界各地的其他标识文档中。

## <a name="post-analyze-id-document"></a>POST 分析 ID 文档

[Analyze ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) 操作将 ID 的图像或 PDF 作为输入，并提取相关值。 该调用返回一个名为 `Operation-Location` 的响应标头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>GET Analyze Id Document Result

<!---
Need to update this with updated APIM links when available
-->

第二步是调用 [Get Analyze idDocument Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) 操作。 此操作采用 Analyze ID 操作创建的结果 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 3 到 5 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：分析操作尚未启动。 |
| |  | running：分析操作正在进行。 |
| |  | failed：分析操作失败。 |
| |  | succeeded：分析操作成功。 |

当“状态”字段的值为“succeeded”时，JSON 响应将包括收据理解和文本识别结果。 ID 结果组织成一个命名字段值字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和相应的词元素。 文本识别结果组织成由行和词组成的层次结构，包含文本、边界框和置信度信息。

![示例收据结果](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>示例 JSON 输出

请参阅以下成功 JSON 响应示例：`readResults` 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。 `documentResults` 节点包含模型发现的 ID 值。 在此节点中，还可以找到有用的键/值对，如名字、姓氏、文档编号等。

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>后续步骤

- 在[表单识别器示例 UI](https://fott-preview.azurewebsites.net/) 中尝试你自己的 ID 和示例。
- 完成[表单识别器快速入门](quickstarts/client-library.md)，开始使用表单识别器以你选择的开发语言编写 ID 处理应用。

## <a name="see-also"></a>另请参阅

* [**什么是表单识别器？**](./overview.md)
* [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
