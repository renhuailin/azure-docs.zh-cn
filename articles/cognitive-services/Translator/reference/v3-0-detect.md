---
title: 翻译器 Detect 方法
titleSuffix: Azure Cognitive Services
description: 使用 Azure 认知服务翻译器 Detect 方法识别一段文本的语言。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 36691229ebe59a6b3b5dd59776b6af41e326a3c2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749998"
---
# <a name="translator-30-detect"></a>翻译器 3.0：Detect

标识一段文本的语言。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

| 查询参数 | 说明 |
| --- | --- |
| api-version | 必需参数。<br/>客户端所请求的 API 的版本。 值必须是 `3.0`。 |

请求标头包括：

| 头文件 | 说明 |
| --- | --- |
| 身份验证标头 | 必需的请求标头。<br/>请参阅[用于身份验证的可用选项](./v3-0-reference.md#authentication)</a>。 |
| Content-Type | 必需的请求标头。<br/>指定有效负载的内容类型。 可能的值为：`application/json` |
| Content-Length | 必需的请求标头。<br/>请求正文的长度。 |
| X-ClientTraceId | 可选。<br/>客户端生成的 GUID，用于唯一标识请求。 请注意，如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。 |

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个包含字符串属性名称为 `Text` 的 JSON 对象。 语言检测应用于 `Text` 属性的值。 语言自动检测对于较长的输入文本更有效。 示例响应正文如下所示：

```json
[
    { "Text": "Ich würde wirklich gerne Ihr Auto ein paar Mal um den Block fahren." }
]
```

以下限制适用：

* 数组最多可具有 100 个元素。
* 请求中包含的整个文本不能超过 50,000 个字符（包括空格）。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个字符串。 结果对象包括以下属性：

  * `language`：已检测语言的代码。

  * `score`：一个浮点值，表示结果的置信度。 分数介于 0 和 1 之间，较低的分数表示较低的置信度。

  * `isTranslationSupported`：一个布尔值，如果检测到的语言是文本翻译支持的语言之一，则为 true。

  * `isTransliterationSupported`：一个布尔值，如果检测到的语言是文本音译支持的语言之一，则为 true。
  
  * `alternatives`：其他可能语言的阵列。 数组中的每个元素是上述所列相同属性的另一个对象：`language`、`score`、`isTranslationSupported` 和 `isTransliterationSupported`。

示例 JSON 响应如下：

```json
[

    {

        "language": "de",

        "score": 1.0,

        "isTranslationSupported": true,

        "isTransliterationSupported": false

    }

]
```

## <a name="response-headers"></a>响应标头

| 头文件 | 说明 |
| --- | --- |
| X-RequestId | 服务生成的用于标识请求的值。 它用于故障排除目的。 |

## <a name="response-status-codes"></a>响应状态代码

下面是请求可能返回的 HTTP 状态代码。 

| 状态代码 | 说明 |
| --- | --- |
| 200 | 成功。 |
| 400 | 查询参数之一缺失或无效。 请更正请求参数，然后重试。 |
| 401 | 无法对请求进行身份验证。 请确保凭据已指定且有效。 |
| 403 | 请求未经授权。 请检查详细错误消息。 这通常表示试用订阅提供的所有可用翻译已用完。 |
| 429 | 由于客户端已超出请求限制，服务器拒绝了请求。 |
| 500 | 发生了意外错误。 如果错误持续存在，请报告相关信息：发生故障的日期和时间、响应标头 `X-RequestId` 中的请求标识符、请求标头 `X-ClientTraceId` 中的客户端标识符。 |
| 503 | 服务器暂不可用。 重试请求。 如果错误持续存在，请报告相关信息：发生故障的日期和时间、响应标头 `X-RequestId` 中的请求标识符、请求标头 `X-ClientTraceId` 中的客户端标识符。 |

如果发生错误，请求也将返回 JSON 错误响应。 错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 常见错误代码可在 [v3 翻译器参考页](./v3-0-reference.md#errors)上找到。 

## <a name="examples"></a>示例

以下示例演示如何检索文本翻译支持的语言。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
