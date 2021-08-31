---
title: 翻译器 Transliterate 方法
titleSuffix: Azure Cognitive Services
description: 使用翻译器 Transliterate 方法将一种语言的文本从一个脚本转换为另一个脚本。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 11574542116fe90629f84fc572f404a1b42b078b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730196"
---
# <a name="translator-30-transliterate"></a>翻译器 3.0：Transliterate

将一种语言的文本从一个脚本转换为另一个脚本。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

| 查询参数 | 说明 |
| --- | --- |
| api-version | 必需参数。<br/>客户端所请求的 API 的版本。 值必须是 `3.0`。 |
| 语言 | 必需参数。<br/>指定要从一个脚本转换为另一个脚本的文本的语言。 通过查询服务的[支持的语言](./v3-0-languages.md)获得的 `transliteration` 范围中列出了可能的语言。 |
| fromScript | 必需参数。<br/>指定输入文本所使用的脚本。 使用 `transliteration` 范围查找[支持的语言](./v3-0-languages.md)，以找到可用于选定语言的输入脚本。 |
| toScript | 必需参数。<br/>指定输出脚本。 使用 `transliteration` 范围查找[支持的语言](./v3-0-languages.md)，以找到可用于选定输入语言和输入脚本组合的输出脚本。 |

请求标头包括：

| 头文件 | 说明 |
| --- | --- |
| 身份验证标头 | 必需的请求标头。<br/>请参阅[用于身份验证的可用选项](./v3-0-reference.md#authentication)。 |
| Content-Type | 必需的请求标头。<br/>指定有效负载的内容类型。 可能的值有：`application/json` |
| Content-Length | 必需的请求标头。<br/>请求正文的长度。 |
| X-ClientTraceId | 可选。<br/>客户端生成的 GUID，用于唯一标识请求。 请注意，如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。 |

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个 JSON 对象，具有一个名为 `Text` 的字符串属性，该属性表示要转换的字符串。

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

以下限制适用：

* 该数组最多可具有 10 个元素。
* 数组元素的文本值不能超过 1,000 个字符（包括空格）。
* 包括空格在内，请求中包含的整个文本不能超过 5,000 个字符。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个元素。 结果对象包括以下属性：

  * `text`：一个字符串，它是将输入字符串转换为输出脚本的结果。
  
  * `script`：一个字符串，指定输出中使用的脚本。

示例 JSON 响应如下：

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

如果发生错误，请求也会返回 JSON 错误响应。 错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 常见错误代码可在 [v3 翻译器参考页](./v3-0-reference.md#errors)上找到。 

## <a name="examples"></a>示例

以下示例显示如何将两个日语字符串转换为用罗马字母拼写的日语。

此示例中请求的 JSON 有效负载：

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

若要在不支持 Unicode 字符的命令行窗口中使用 cURL，请获取以下 JSON 有效负载，并将它保存到 `request.txt` 文件中。 请务必使用 `UTF-8` 编码保存文件。

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
