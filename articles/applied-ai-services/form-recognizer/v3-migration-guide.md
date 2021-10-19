---
title: 操作：将应用程序从表单识别器 v2.1 迁移到 v3.0。
titleSuffix: Azure Applied AI Services
description: 本操作指南介绍表单识别器 API v2.1 和 v3.0 之间的差异。 还将介绍移动到较新版本的 API 所需的更改。
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.openlocfilehash: 8d20fd0211fb2601ffd0f88e37e78d682d550711
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728905"
---
# <a name="form-recognizer-v30-migration--preview"></a>表单识别器 v3.0 迁移 | 预览版

> [!IMPORTANT]
>
> 表单识别器 REST API v3.0 在 REST API 请求和分析响应 JSON 中引入了重大更改。

表单识别器 v3.0（预览版）引入了几个新特性和功能：

* [表单识别器 REST API](quickstarts/try-v3-rest-api.md) 经过重新设计，提高了可用性。
* [常规文档 (v3.0)](concept-general-document.md) 模型是从表单和文档中提取文本、表、结构、键值对和已命名实体的新 API。
* [回执 (v3.0)](concept-receipt.md) 模型支持单页酒店回执处理。
* [ID 文档 (v3.0)](concept-id-document.md) 模型支持从美国驾照中提取认可、限制和车辆分类信息。
* [自定义模型 API (v3.0)](concept-custom.md) 支持适用于自定义表单的签名检测。

本文介绍表单识别器 v2.1 和 v3.0 之间的差异，以及如何移动到较新版本的 API。

## <a name="changes-to-the-rest-api-endpoints"></a>对 REST API 终结点的更改

 v3.0 REST API 通过向布局分析（预生成布局）和预生成模型分配 `documentModels` 和 `modelId`，将布局分析、预生成模型和自定义模型的分析操作合并为一对操作 。

### <a name="post-request"></a>POST 请求

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>GET 请求

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>分析操作

* 请求有效负载和调用模式保持不变。
* 分析操作指定输入文档和特定于内容的配置，它通过响应中的 Operation-Location 标头返回分析结果 URL。
* 通过 GET 请求轮询此分析结果 URL，检查分析操作的状态（请求之间的最小建议间隔为 1 秒）。
* 成功后，状态设置为成功，并在响应正文中返回 [analyzeResult](#changes-to-analyze-result)。 如果遇到错误，状态将设置为失败，并返回错误。

| 型号 | v2.1 | v3.0 |
|:--| :--| :--|
| 请求 URL 前缀| https://{your-form-recognizer-endpoint}/formrecognizer/v2.1  | https://{your-form-recognizer-endpoint}/formrecognizer |
|🆕 常规文档|空值|/documentModels/prebuilt-document:analyze |
| **布局**| /layout/analyze |/documentModels/prebuilt-layout:analyze|
|**自定义**| /custom/{modelId}/analyze    |/documentModels/{modelId}:analyze |
| **发票** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice:analyze |
| **回执** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt:analyze |
| ID 文档 | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument:analyze |
|名片| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard:analyze|

### <a name="analyze-request-body"></a>分析请求正文

通过请求正文提供要分析的内容。 可使用 URL 或 base64 编码数据来构造请求。

  要指定可公开访问的 Web URL，请将 Content-Type 设置为 application/json ****  并发送以下 JSON 正文：

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

表单识别器 v3.0 还支持 Base64 编码：

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>其他参数

继续支持的参数：

* 页
* 区域设置

不再支持的参数： 

* includeTextDetails

新的响应格式更紧凑，始终返回完整输出。

## <a name="changes-to-analyze-result"></a>对分析结果的更改

分析响应已重构为以下顶级结果，以支持多页元素。
* 页
* 表
* keyValuePairs
* 实体
* 样式
* 文档

> [!NOTE]
>
> analyzeResult 响应更改包含许多更改，例如从页面属性向上移动到 analyzeResult 中的顶级属性。

```json

{
// Basic analyze result metadata
"apiVersion": "2021-07-30-preview", // REST API version used
"modelId": "prebuilt-invoice", // ModelId used
"stringIndexType": "textElements", // Character unit used for string offsets and lengths:
// textElements, unicodeCodePoint, utf16CodeUnit // Concatenated content in global reading order across pages.
// Words are generally delimited by space, except CJK (Chinese, Japanese, Korean) characters.
// Lines and selection marks are generally delimited by newline character.
// Selection marks are represented in Markdown emoji syntax (:selected:, :unselected:).
"content": "CONTOSO LTD.\nINVOICE\nContoso Headquarters...", "pages": [ // List of pages analyzed
{
// Basic page metadata
"pageNumber": 1, // 1-indexed page number
"angle": 0, // Orientation of content in clockwise direction (degree)
"width": 0, // Page width
"height": 0, // Page height
"unit": "pixel", // Unit for width, height, and bounding box coordinates
"spans": [ // Parts of top-level content covered by page
{
"offset": 0, // Offset in content
"length": 7 // Length in content
}
], // List of words in page
"words": [
{
"text": "CONTOSO", // Equivalent to $.content.Substring(span.offset, span.length)
"boundingBox": [ ... ], // Position in page
"confidence": 0.99, // Extraction confidence
"span": { ... } // Part of top-level content covered by word
}, ...
], // List of selectionMarks in page
"selectionMarks": [
{
"state": "selected", // Selection state: selected, unselected
"boundingBox": [ ... ], // Position in page
"confidence": 0.95, // Extraction confidence
"span": { ... } // Part of top-level content covered by selection mark
}, ...
], // List of lines in page
"lines": [
{
"content": "CONTOSO LTD.", // Concatenated content of line (may contain both words and selectionMarks)
"boundingBox": [ ... ], // Position in page
"spans": [ ... ], // Parts of top-level content covered by line
}, ...
]
}, ...
], // List of extracted tables
"tables": [
{
"rowCount": 1, // Number of rows in table
"columnCount": 1, // Number of columns in table
"boundingRegions": [ // Bounding boxes potentially across pages covered by table
{
"pageNumber": 1, // 1-indexed page number
"boundingBox": [ ... ], // Bounding box
}
],
"spans": [ ... ], // Parts of top-level content covered by table // List of cells in table
"cells": [
{
"kind": "stub", // Cell kind: content (default), rowHeader, columnHeader, stub, description
"rowIndex": 0, // 0-indexed row position of cell
"columnIndex": 0, // 0-indexed column position of cell
"rowSpan": 1, // Number of rows spanned by cell (default=1)
"columnSpan": 1, // Number of columns spanned by cell (default=1)
"content": "SALESPERSON", // Concatenated content of cell
"boundingRegions": [ ... ], // Bounding regions covered by cell
"spans": [ ... ] // Parts of top-level content covered by cell
}, ...
]
}, ...
], // List of extracted key-value pairs
"keyValuePairs": [
{
"key": { // Extracted key
"content": "INVOICE:", // Key content
"boundingRegions": [ ... ], // Key bounding regions
"spans": [ ... ] // Key spans
},
"value": { // Extracted value corresponding to key, if any
"content": "INV-100", // Value content
"boundingRegions": [ ... ], // Value bounding regions
"spans": [ ... ] // Value spans
},
"confidence": 0.95 // Extraction confidence
}, ...
], // List of extracted entities
"entities": [
{
"category": "DateTime", // Primary entity category
"subCategory": "Date", // Secondary entity category
"content": "11/15/2019", // Entity content
"boundingRegions": [ ... ], // Entity bounding regions
"spans": [ ... ], // Entity spans
"confidence": 0.99 // Extraction confidence
}, ...
], // List of extracted styles
"styles": [
{
"isHandwritten": true, // Is content in this style handwritten?
"spans": [ ... ], // Spans covered by this style
"confidence": 0.95 // Detection confidence
}, ...
], // List of extracted documents
"documents": [
{
"docType": "prebuilt-invoice", // Classified document type (model dependent)
"boundingRegions": [ ... ], // Document bounding regions
"spans": [ ... ], // Document spans
"confidence": 0.99, // Document splitting/classification confidence // List of extracted fields
"fields": {
"VendorName": { // Field name (docType dependent)
"type": "string", // Field value type: string, number, array, object, ...
"valueString": "CONTOSO LTD.",// Normalized field value
"content": "CONTOSO LTD.", // Raw extracted field content
"boundingRegions": [ ... ], // Field bounding regions
"spans": [ ... ], // Field spans
"confidence": 0.99 // Extraction confidence
}, ...
}
}, ...
]
}



```

## <a name="build-or-train-model"></a>生成或训练模型

模型对象在新的 API 中具有两个更新
* ```modelId``` 现在是可在模型上为用户可读的名称设置的属性。
* ```modelName``` 已重名为 ```description```

调用 ```build``` 操作以训练模型。 请求有效负载和调用模式保持不变。 生成操作指定模型和训练数据集，它通过响应中的 Operation-Location 标头返回结果。 通过 GET 请求轮询此模型操作 URL，检查生成操作的状态（请求之间的最小建议间隔为 1 秒）。 与 v2.1 不同，此 URL 不是模型的资源位置。 相反，可从给定的 modelId 构造模型 URL，还可从响应中的 resourceLocation 属性中检索模型 URL。 成功后，状态设置为 ```succeeded```，结果包含自定义模型信息。 如果遇到错误，则状态设置为 ```failed```，并返回错误。

以下代码是使用 SAS 令牌的示例生成请求。 设置前缀或文件夹路径时，请注意尾部斜杠。

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:build?api-version=2021-09-30-preview

{
  "modelId": {modelId},
  "description": "Sample model",
  "azureBlobSource": {
    "containerUrl": "https://{storageAccount}.blob.core.windows.net/{containerName}?{sasToken}",
    "prefix": "{folderName/}"
  }
}
```
## <a name="changes-to-compose-model"></a>对组合模型的更改

模型组合现在仅限于单级嵌套。 组合的模型现在与添加 ```modelId``` 和 ```description``` 属性的自定义模型一致。

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:compose?api-version=2021-09-30-preview
{
  "modelId": "{composedModelId}",
  "description": "{composedModelDescription}",
  "componentModels": [
    { "modelId": "{modelId1}" },
    { "modelId": "{modelId2}" },
  ]
}

```

## <a name="changes-to-copy-model"></a>对复制模型的更改

复制模型的调用模式保持不变：

* 使用调用 ```authorizeCopy``` 的目标资源授权复制操作。 现在是 POST 请求。
* 将授权提交到源资源，以复制调用 ```copy-to``` 的模型
* 轮询返回的操作，以验证操作是否成功完成

对复制模型函数的唯一更改是：

* ```authorizeCopy``` 上的 HTTP 操作现在是 POST 请求。
* 授权有效负载包含提交复制请求所需的全部信息。

授权复制。
```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```
使用授权操作中的响应正文来构造复制请求。

```json
POST https://{sourceHost}/formrecognizer/documentModels/{sourceModelId}:copy-to?api-version=2021-09-30-preview
{
  "targetResourceId": "{targetResourceId}",
  "targetResourceRegion": "{targetResourceRegion}",
  "targetModelId": "{targetModelId}",
  "targetModelLocation": "https://{targetHost}/formrecognizer/documentModels/{targetModelId}",
  "accessToken": "{accessToken}",
  "expirationDateTime": "2021-08-02T03:56:11Z"
}
```

## <a name="changes-to-list-models"></a>对列出模型的更改

列出模型已扩展为现在返回预生成模型和自定义模型。 所有预生成模型名以 ```prebuilt-``` 开始。 但只返回状态为成功的模型。 要列出失败或正在进行中的模型，请参阅[列出操作](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels)。

示例列出模型请求

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>对获取模型的更改

由于获取模型现在包含预生成模型，因此获取操作将返回 ```docTypes``` 字典。 每个文档类型都按其名称、可选说明、字段架构和可选字段置信度进行说明。 字段架构说明可能随文档类型一起返回的字段列表。

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>新的获取信息操作
服务上的 ```info``` 操作返回自定义模型计数和自定义模型限制。

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```
示例响应
```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>后续步骤

此迁移指南已介绍如何升级现有的表单识别器应用程序以使用 v3.0 API。 继续将 2.1 API 用于所有 GA 功能，并将 3.0 API 用于任何预览功能。

* [查看新的 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [什么是表单识别器？](overview.md)
* [表单识别器快速入门](quickstarts/client-library.md)
