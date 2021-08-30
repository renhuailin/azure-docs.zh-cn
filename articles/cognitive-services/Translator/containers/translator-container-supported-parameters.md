---
title: 容器：翻译器翻译方法
titleSuffix: Azure Cognitive Services
description: 了解 Azure 认知服务翻译器容器翻译方法的参数、标头和正文消息，以便翻译文本。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: b2c13a98c6b689594628a258faf18f8f9c0d8c69
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539549"
---
# <a name="container-translator-translate-method-preview"></a>容器：翻译器翻译方法（预览版）

翻译文本。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
http://localhost:{port}/translate?api-version=3.0
```

示例： http://<span></span>localhost:5000/translate?api-version=3.0

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

### <a name="required-parameters"></a>必需的参数

| 查询参数 | 说明 |
| --- | --- |
| api-version | 必需参数。  <br>客户端所请求的 API 的版本。 值必须是 `3.0`。 |
| from | 必需参数。  <br>指定输入文本的语言。 可以使用 `translation` 范围来查找[支持的语言](../reference/v3-0-languages.md)，了解哪些语言可以翻译。|
| to  | 必需参数。  <br>指定输出文本的语言。 目标语言必须是 `translation` 范围中包含的[支持的语言](../reference/v3-0-languages.md)之一。 例如，若要翻译为德语，请使用 `to=de`。  <br>可以在查询字符串中重复使用此参数，这样就可以同时翻译为多种语言。 例如，若要翻译为德语和意大利语，请使用 `to=de&to=it`。 |

### <a name="optional-parameters"></a>可选参数

| 查询参数 | 说明 |
| --- | --- |
| textType | 可选参数。  <br>定义要翻译的文本是纯文本还是 HTML 文本。 HTML 必须是格式正确的完整元素。 可能的值为 `plain`（默认）`html`。 |
| includeSentenceLength | 可选参数。  <br>指定是否包括输入文本和翻译文本的句子边界。 可能的值为 `true` 或 `false`（默认）。 |

请求标头包括：

| 头文件 | 说明 |
| --- | --- |
| 身份验证标头 | 必需的请求标头。  <br>请参阅[用于身份验证的可用选项](../reference/v3-0-reference.md#authentication)。 |
| Content-Type | 必需的请求标头。  <br>指定有效负载的内容类型。  <br>接受的值为：`application/json; charset=UTF-8`。 |
| Content-Length | 必需的请求标头。  <br>请求正文的长度。 |
| X-ClientTraceId | 可选。  <br>客户端生成的 GUID，用于唯一标识请求。 如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。 |

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个 JSON 对象，具有一个名为 `Text` 的字符串属性，该属性表示要翻译的字符串。

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

以下限制适用：

* 数组最多可具有 100 个元素。
* 包括空格在内，请求中包含的整个文本不能超过 10,000 个字符。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个字符串。 结果对象包括以下属性：

* `translations`：翻译结果的数组。 数组的大小与通过 `to` 查询参数指定的目标语言的数目匹配。 数组中的每个元素包括：

* `to`：一个字符串，表示目标语言的语言代码。

* `text`：一个字符串，提供翻译的文本。

* `sentLen`：一个对象，返回输入和输出文本中的句子边界。

* `srcSentLen`：一个整数数组，表示输入文本中句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。

* `transSentLen`：一个整数数组，表示翻译文本中句子的长度。 数组的长度是句子的数量，而各个值是每个句子的长度。

    只有在请求参数 `includeSentenceLength` 为 `true` 时，才包括句子边界。

  * `sourceText`：一个对象，包含的名为 `text` 的单个字符串属性在源语言的默认脚本中提供输入文本。 `sourceText` 属性存在的前提是，表述输入时采用的脚本不是该语言的通用脚本。 例如，如果输入是采用拉丁语脚本编写的阿拉伯语，则 `sourceText.text` 就是转换为阿拉伯脚本的同一阿拉伯文本。

[示例](#examples)部分提供了 JSON 响应的示例。

## <a name="response-headers"></a>响应标头

| 头文件 | 说明 |
| --- | --- |
| X-RequestId | 服务生成的用于标识请求的值。 它用于故障排除目的。 |
| X-MT-System | 请求翻译时，对于每种“目标”语言，指定用于翻译的系统类型。 此值是以逗号分隔的字符串列表。 每个字符串指示一个类型：  </br></br>&FilledVerySmallSquare; 自定义 - 请求包括一个自定义系统，并且在翻译期间至少使用了一个自定义系统。</br>&FilledVerySmallSquare; 团队 - 所有其他请求 |

## <a name="response-status-codes"></a>响应状态代码

如果发生错误，请求也将返回 JSON 错误响应。 错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 常见错误代码可在 [v3 翻译器参考页](../reference/v3-0-reference.md#errors)上找到。

## <a name="examples"></a>示例

### <a name="translate-a-single-input"></a>翻译单个输入

以下示例演示了如何将单个句子从英文翻译为简体中文。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

响应正文为：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 数组包括一个元素，该元素提供输入中一段文本的翻译。

### <a name="translate-multiple-pieces-of-text"></a>翻译多行文本

一次翻译多个字符串时，只需在请求正文中指定一个字符串数组即可。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

响应包含所有文本片段的翻译，其顺序与请求中的完全相同。
响应正文为：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>翻译为多种语言

以下示例演示如何在一个请求中将同一输入翻译为多种语言。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

响应正文为：

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>翻译带标记的内容并确定翻译的内容

通常需要翻译包含标记的内容，例如 HTML 页中的内容或 XML 文档中的内容。 在翻译包含标记的内容时包括查询参数 `textType=html`。 另外，有时候需从翻译中排除特定的内容。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在以下示例中，第一个 `div` 元素中的内容不会翻译，第二个 `div` 元素中的内容则会翻译。

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

下面是用于演示的示例请求。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

响应为：

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>使用动态词典进行翻译

若已知道要应用于某个单词或短语的翻译，可以在请求中将其作为标记提供。 动态字典仅适用于专有名词，例如个人姓名和产品名称。

要提供的标记使用以下语法。

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

例如，考虑英语句子“The word wordomatic is a dictionary entry.”。 若要在翻译中保留单词 _wordomatic_，请发送请求：

```
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

结果为：

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

不管使用 `textType=text` 还是使用 `textType=html`，此功能都以相同的方式工作。 应尽量少使用此功能。 对翻译进行自定义时，一个合适且要好得多的方法是使用自定义翻译工具。 自定义翻译工具能够充分利用上下文和统计概率。 如果你必须或者有能力创建在上下文中显示你的工作或短语的训练数据，则会得到好得多的结果。 [详细了解自定义翻译工具](../customization.md)。

## <a name="request-limits"></a>请求限制

每个翻译请求在你要翻译到的所有目标语言中都限制为 10,000 个字符。 例如，如果发送 3,000 个字符的翻译请求以翻译成三种不同的语言，则请求大小为 3000x3 = 9,000 个字符，这满足请求限制。 按字符收费，而不是按请求数收费。 建议发送较短的请求。

下表列出了翻译器的“翻译”操作的数组元素和字符限制。

| Operation | 数组元素的最大大小 | 最大数组元素数 | 最大请求大小（字符数） |
|:----|:----|:----|:----|
| translate | 10,000 | 100 | 10,000 |