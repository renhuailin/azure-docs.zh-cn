---
title: 翻译工具 v2.0
titleSuffix: Azure Cognitive Services
description: 翻译工具 v2.0 参考文档。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 25dda63c6450040e396de9ee0d3fb0a459416343
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606821"
---
# <a name="translator-v20"></a>翻译工具 v2.0

> [!IMPORTANT]
> 此版翻译工具已弃用。 [查看翻译工具版本 3 的文档](v3-0-reference.md)。

翻译工具版本 2 可以无缝集成到应用、网站、工具或其他解决方案中，提供多语言用户体验。 根据行业标准，你可以在任何硬件平台和任何操作系统中使用它来执行语言翻译以及其他语言相关的任务，例如文本语言检测或文本转语音操作。 有关详细信息，请参阅[翻译工具](../translator-info-overview.md)。

## <a name="getting-started"></a>入门
若要访问转换器，需要 [注册 Microsoft Azure](../translator-how-to-signup.md)。

## <a name="authentication"></a>身份验证 
对翻译工具的所有调用都需要订阅密钥来进行身份验证。 此 API 支持三种身份验证方法：

- 访问令牌。 使用订阅密钥通过向身份验证服务发出 POST 请求来创建访问令牌。 有关详细信息，请参阅令牌服务文档。 通过使用 `Authorization` 标头或 `access_token` 查询参数将访问令牌传递给翻译工具服务。 访问令牌的有效期为 10 分钟。 每 10 分钟获取一次新的访问令牌，并在 10 分钟内对重复请求使用同一访问令牌。
- 直接使用的订阅密钥。 将订阅密钥作为请求中包括的 `Ocp-Apim-Subscription-Key` 标头中的值传递给翻译工具。 直接使用订阅密钥时，不必调用令牌身份验证服务来创建访问令牌。
- [Azure 认知服务多服务订阅](https://azure.microsoft.com/pricing/details/cognitive-services/)。 此方法允许使用一个密钥对多个服务的请求进行身份验证。
在使用多服务密钥时，请求中需要包含两个身份验证标头。 第一个标头传递密钥。 第二个标头指定与订阅关联的区域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

该区域对于多服务文本 API 订阅是必需的。 当你使用多服务订阅密钥时，你所选的区域是唯一可以用于文本翻译的区域。 它需要与你在 Azure 门户上注册多服务订阅时所选的区域相同。

可用区域包括 `australiaeast` 、、 `brazilsouth` 、 `canadacentral` `centralindia` 、、 `centraluseuap` `eastasia` 、、 `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` 、、、、、、、、和。

订阅密钥和访问令牌是不应允许查看的机密。

## <a name="profanity-handling"></a>不雅内容处理
通常，翻译工具服务会保留源中存在的不雅内容。 不雅程度和使词语不雅的语境因文化而异。 因此，在目标语言中的不雅程度可能会增大或降低。

如果你希望在翻译中防止出现不雅内容，即使是在源文本中，可以将不雅内容筛选项用于支持它的方法。 使用该选项可以选择是否要将不雅内容删除或加上相应标记，或者是否允许在目标语言中使用不雅内容。 就 `ProfanityAction` 来说，接受的值为 `NoAction`（默认）、`Marked`、`Deleted`。


|ProfanityAction    |操作 |示例源（日语）  |示例翻译（中文）  |
|:--|:--|:--|:--|
|NoAction   |默认。 与不设置此选项等效。 不雅内容会从源传递到目标。 |彼はジャッカスです。 |他是一个笨蛋。  |
|Marked |不雅词语会括在 XML 标记 \<profanity> 和 \</profanity> 中。 |彼はジャッカスです。   |他是一个\<profanity>笨蛋\</profanity>。  |
|Deleted    |不雅词语会从输出中删除，不进行替换。 |彼はジャッカスです。 |他是一个。   |

    
## <a name="excluding-content-from-translation"></a>将内容从翻译中排除
翻译 HTML (`contentType=text/html`) 之类的带标记的内容时，从翻译中排除特定内容有时会很有用。 可以使用属性 `class=notranslate` 指定应保留不译的内容。 在以下示例中，第一个 `div` 元素中的内容不会翻译，但第二个 `div` 元素中的内容则会翻译。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>实现说明
将一种语言的文本字符串翻译为另一种语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**返回值：** 一个表示已翻译文本的字符串。

如果以前已使用 `AddTranslation` 或 `AddTranslationArray` 为相同的源句输入了一个评分为 5 或更高的翻译，则 `Translate` 只返回适用于系统的首选内容。 “相同的源句”意味着完全相同（100% 匹配），但大小写、空格、标记值以及句尾的标点除外。 如果没有存储 5 分或更高分的评分，则返回的结果会是 Microsoft 翻译工具的自动翻译。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application/xml

### <a name="parameters"></a>参数

|参数|值|描述    |参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)   |必需。 一个字符串，表示要翻译的文本。 文本包含的字符不能超过 10,000 个。|query|字符串|
|from|(empty)   |可选。 一个字符串，表示要翻译的文本的语言代码。 例如，en 表示英语。|query|字符串|
|to|(empty) |必需。 一个字符串，表示文本翻译的目标语言的代码。|query|字符串|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式为 `text/plain`（默认值）和 `text/html`。 任何 HTML 元素都需要是格式标准的完整元素。|query|字符串|
|category|(empty)   |可选。 一个字符串，包含翻译类别（域）。 默认值为 `general`。|query|字符串|
|授权|(empty)  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|


### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

以下是请求正文的格式：

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

这些元素位于 `TranslateArrayRequest` 中：


* `AppId`：必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `AppId` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。
* `From`：可选。 一个字符串，表示要翻译的文本的语言代码。 如果此字段留空，则响应将包括自动语言检测的结果。
* `Options`：可选。 一个包含以下值的 `Options` 对象。 它们都是可选的，并且默认为最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：一个字符串，包含翻译类别（域）。 默认值为 `general`。
    - `ContentType`：要翻译的文本的格式。 支持的格式为 `text/plain`（默认）、`text/xml` 和 `text/html`。 任何 HTML 元素都需要是格式标准的完整元素。
    - `ProfanityAction`：指定如何处理不雅内容（如前所述）。 可接受的值是 `NoAction`（默认值）、`Marked` 和 `Deleted`。
    - `State`：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
    - `Uri`：按此 URI 筛选结果。 默认值：`all`。
    - `User`：按此用户筛选结果。 默认值：`all`。
* `Texts`：必需。 一个数组，包含要翻译的文本。 所有字符串必须采用同一语言。 要翻译的所有文本总共不得超过 10,000 个字符。 最大数组元素数为 2,000。
* `To`：必需。 一个字符串，表示文本翻译的目标语言的代码。

可以省略可选元素。 属于 `TranslateArrayRequest` 的直接子项的元素必须按字母顺序列出。

`TranslateArray` 方法接受 `application/xml` 或 `text/xml` 作为 `Content-Type`。

**返回值：** 一个 `TranslateArrayResponse` 数组。 每个 `TranslateArrayResponse` 都包含以下元素：

* `Error`：如果发生错误，则指示错误。 否则设置为 null。
* `OriginalSentenceLengths`：一个整数数组，指示源文本中每个句子的长度。 数组的长度指示句子数。
* `TranslatedText`：翻译的文本。
* `TranslatedSentenceLengths`：一个整数数组，指示已翻译文本中每个句子的长度。 数组的长度指示句子数。
* `State`：用户状态，可帮助关联请求和响应。 返回与请求相同的内容。

以下是响应正文的格式：

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>响应类（状态 200）
成功的响应包括一系列 `TranslateArrayResponse` 数组，采用的格式如前所述。

字符串

响应内容类型：application/xml

### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码   |原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。 常见错误包括： <ul><li>数组元素不能为空。</li><li>类别无效。</li><li>源语言无效。</li><li>目标语言无效。</li><li>请求包含的元素太多。</li><li>不支持源语言。</li><li>不支持目标语言。</li><li>翻译请求的数据太多。</li><li>HTML 格式有误。</li><li>在翻译请求中传递的字符串过多。</li></ul>|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>实现说明
检索语言的友好名称，该语言已作为 `languageCodes` 参数传入并已本地化为传递的 `locale` 语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

请求正文包括一个字符串数组，该数组表示要检索其友好名称的 ISO 639-1 语言代码。 下面是一个示例：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**返回值：** 一个字符串数组，其包含的语言名称受翻译工具服务的支持，并已本地化为请求的语言。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个字符串数组，其包含的语言名称受翻译工具服务的支持，并已本地化为请求的语言。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|string|
|区域设置|(empty) |必需。 表示下列各项之一的字符串，用于本地化语言名称： <ul><li>与语言相关联的 ISO 639 双字母小写区域性代码和 ISO 3166 双字母大写子区域性代码的组合。 <li>ISO 639 小写区域性代码本身。|query|字符串|
|授权|(empty)  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>实现说明
获取代表了翻译服务支持的语言的语言代码列表。  `Translate` 和 `TranslateArray` 可以在这些语言的任意两种之间互译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**返回值：** 一个字符串数组，其包含的语言代码受翻译工具服务的支持。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个字符串数组，其包含的语言代码受翻译工具服务的支持。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|授权|(empty)  |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>实现说明
检索可用于语音合成的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**返回值：** 一个字符串数组，其包含的语言代码受翻译工具服务的支持，可以进行语音合成。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个字符串数组，其包含的语言代码受翻译工具服务的支持，可以进行语音合成。

字符串

响应内容类型：application/xml

### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|
 
### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>实现说明
返回以所需语言朗读的传入文本的 WAV 或 MP3 流。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**返回值：** 以所需语言朗读的传入文本的 WAV 或 MP3 流。

### <a name="response-class-status-200"></a>响应类（状态 200）

binary

响应内容类型：application/xml

### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)   |必需。 一个字符串，包含流对应的一个或多个以指定语言朗读的句子。 文本不得超过 2,000 个字符。|query|字符串|
|语言|(empty)   |必需。 一个字符串，表示要用其来朗读文本的语言所支持的语言代码。 该代码必须是方法 `GetLanguagesForSpeak` 返回的代码之一。|query|字符串|
|format|(empty)|可选。 一个指定内容类型 ID 的字符串。 目前，`audio/wav` 和 `audio/mp3` 可用。 默认值为 `audio/wav`。|query|字符串|
|选项|(empty)    |可选。 一个指定合成语音的属性的字符串：<ul><li>`MaxQuality` 和 `MinSize` 指定音频信号的质量。 `MaxQuality` 提供最高的质量。 `MinSize` 提供最小的文件大小。 默认为 `MinSize`。</li><li>`female` 和 `male` 指定语音的所需性别。 默认值为 `female`。 使用垂直条 (<code>\|</code>) 包含多个选项。 例如，`MaxQuality|Male`。</li></li></ul>  |query|字符串|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>实现说明
标识部分文本的语言。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**返回值：** 一个包含文本的双字符语言代码的字符串。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application/xml

### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)|必需。 一个包含要标识其语言的文本的字符串。 文本不得超过 10,000 个字符。|query|  字符串|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key  |(empty)    |如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>实现说明

标识字符串数组中的语言。 独立检测每个单独的数组元素的语言，并为数组的每一行返回一个结果。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

以下是请求正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文本不能超过 10,000 个字符。

**返回值：** 一个字符串数组，其包含的双字符语言代码适用于输入数组中的每一行。

以下是响应正文的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>响应类（状态 200）
`DetectArray` 已成功。 返回一个字符串数组，其包含的双字符语言代码适用于输入数组的每一行。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> 弃用说明：2018 年 1 月 31 日后，此方法不再接受新的句子提交。 你会收到一条错误消息。 请参阅有关协作翻译框架 (CTF) 所做的更改的公告。

向翻译记忆库添加翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application: xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型   |
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|originalText|(empty)|必需。 一个包含要翻译文本的字符串。 该字符串的最大长度为 1,000 个字符。|query|字符串|
|translatedText|(empty) |必需。 一个字符串，包含翻译成目标语言的文本。 该字符串的最大长度为 2,000 个字符。|query|字符串|
|from|(empty)   |必需。 一个字符串，表示文本原始语言的语言代码。 例如，en 表示英语，而 de 表示德语。|query|字符串|
|to|(empty)|必需。 一个字符串，表示文本翻译的目标语言的语言代码。|query|字符串|
|rating|(empty) |可选。 一个整数，表示此字符串的质量评分。 该值介于 -10 和 10 之间。 默认值为 1。|query|integer|
|contentType|(empty)    |可选。 要翻译的文本的格式。 支持的格式是 `text/plain` 和 `text/html`。 任何 HTML 元素都需要是格式标准的完整元素。    |query|字符串|
|category|(empty)|可选。 一个字符串，包含翻译类别（域）。 默认值为 `general`。|query|字符串|
|user|(empty)|必需。 一个字符串，用于跟踪提交发起方。|query|字符串|
|uri|(empty)|可选。 一个包含翻译内容位置的字符串。|query|字符串|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。  |标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410|`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>实现说明

> [!IMPORTANT]
> 弃用说明：2018 年 1 月 31 日后，此方法不再接受新的句子提交。 你会收到一条错误消息。 请参阅有关协作翻译框架 (CTF) 所做的更改的公告。

将翻译数组添加到翻译记忆库中。 此方法是数组版本的 `AddTranslation`。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

以下是请求正文的格式：

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

这些元素位于 `AddtranslationsRequest` 中：

* `AppId`：必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `AppId` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。
* `From`：必需。 一个字符串，包含源语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `To`：必需。 一个字符串，包含目标语言的语言代码。 必须是 `GetLanguagesForTranslate` 方法返回的语言之一。
* `Translations`：必需。 要添加到翻译记忆库的一个翻译数组。 每个翻译必须包含 `OriginalText`、`TranslatedText` 和 `Rating`。 每个 `OriginalText` 和 `TranslatedText` 的最大大小为 1,000 个字符。 所有 `OriginalText` 和 `TranslatedText` 元素的总大小不能超过 10,000 个字符。 最大数组元素数为 100。
* `Options`：必需。 一组选项，包括 `Category`、`ContentType`、`Uri` 和 `User`。 需要 `User`。 `Category`、`ContentType` 和 `Uri` 是可选的。 指定的元素必须按字母顺序列出。

### <a name="response-class-status-200"></a>响应类（状态 200）
`AddTranslationArray` 方法已成功。 

2018 年 1 月 31 日后，不再接受句子提交。 此服务会以错误代码 410 进行响应。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|410    |`AddTranslation` 不再受支持。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>实现说明
将部分文本拆分成多个句子，并返回一个数组，其中包含每个句子的长度。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**返回值：** 一个整数数组，表示句子的长度。 数组的长度表示句子数。 值表示每个句子的长度。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个整数数组，表示句子的长度。 数组的长度表示句子数。 值表示每个句子的长度。

integer

响应内容类型：application/xml

### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)  |必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query| 字符串|
|text|(empty)   |必需。 一个字符串，表示要拆分成句子的文本。 文本的最大大小为 10,000 个字符。|query|字符串|
|语言   |(empty)    |必需。 一个字符串，表示输入文本的语言代码。|query|字符串|
|授权|(empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。   |标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)|如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|请求错误。 请查看输入参数和详细的错误响应。|
|401|凭据无效。|
|500|服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>实现说明
从存储和 MT 引擎中检索给定语言对的翻译数组。 `GetTranslations` 与 `Translate` 的不同之处在于，它返回所有可用的翻译。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

请求的正文包括可选的 `TranslationOptions` 对象，该对象的格式如下：

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions` 对象包含以下列表中的值。 它们都是可选的，并且默认为最常用的设置。 指定的元素必须按字母顺序列出。

* `Category`：一个字符串，包含翻译类别（域）。 默认值为 `general`。
* `ContentType`：`text/plain` 是唯一支持的选项，也是默认选项。
* `IncludeMultipleMTAlternatives`：一个布尔标志，用于指定是否应从 MT 引擎返回多个备选项。 有效值为 `true` 和 `false`（区分大小写）。 默认值是 `false`，它只返回一个备选项。 将标志设置为 `true` 可以创建与协作翻译框架 (CTF) 完全集成的人工备选项。 此功能允许为在 CTF 中没有翻译的句子返回备选项，其方法是从解码器的“n 个最佳”列表中添加人工备选项。
    - 评级。 评级的适用范围如下： 
         - 最佳自动翻译评级为 5。
       - CTF 中的备选项反映了审阅者的权威性。 其评分范围为 -10 到 +10。
       - 自动生成的（n 个最佳）翻译备选项的评级为 0，匹配度为 100。
    - 替代项数。 返回的备选项的数目可以与 `maxTranslations` 中指定的值一样多，但也可以更少。
    - 语言对。 此功能不适用于简体中文和繁体中文之间任意向的翻译。 它可用于 Microsoft 翻译工具支持的所有其他语言对。
* `State`：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为 `all`。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为 `all`。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 以下是响应的格式：

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

此响应包含一个 `GetTranslationsResponse` 元素，该元素包含以下值：

* `Translations`：找到的匹配项的数组，存储在 `TranslationMatch` 对象中（在下一部分中介绍）。 翻译可能包括原始文本的略变体（模糊匹配）。 将对翻译进行排序：首先是 100% 匹配，接下来是模糊匹配。
* `From`：如果该方法没有指定 `From` 语言，则该值来自自动语言检测。 否则，它将是指定的 `From` 语言。
* `State`：用户状态，可帮助关联请求和响应。 包含 `TranslateOptions` 参数中提供的值。

`TranslationMatch` 对象由以下值组成：

* `Error`：如果特定输入字符串发生错误，则返回错误代码。 否则，此字段为空。
* `MatchDegree`：表示输入文本与存储中发现的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值为 0 到 100，0 表示不相似，100 表示完全匹配（区分大小写）。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本与输入文本不同时，才会返回此值。 它用于返回模糊匹配的源文本。 对于 Microsoft 翻译工具结果，不返回该值。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果的评分为 5。 匿名提供的翻译的评分通常为 1 到 4。 权威提供的翻译的评分通常为 6 到 10。
* `Count`：此评分的这个翻译被选中的次数。 对于自动翻译的响应，此值为 0。
* `TranslatedText`：翻译的文本。

### <a name="response-class-status-200"></a>响应类（状态 200）
一个采用前面所述的格式的 `GetTranslationsResponse` 对象。

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|appid|(empty)|必需。 如果使用 `Authorization` 或 `Ocp-Apim-Subscription-Key` 标头，请将 `appid` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。|query|字符串|
|text|(empty)|必需。 一个字符串，表示要翻译的文本。 文本的最大大小为 10,000 个字符。|query|字符串|
|from|(empty)|必需。 一个字符串，表示要翻译的文本的语言代码。|query|字符串|
|to |(empty)    |必需。 一个字符串，表示文本翻译的目标语言的语言代码。|query|字符串|
|maxTranslations|(empty)|必需。 一个整数，表示要返回的最大翻译数。|query|integer|
|授权| (empty)|如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。 授权令牌：`"Bearer" + " " + "access_token"`。|字符串|  标头的值开始缓存响应|
|Ocp-Apim-Subscription-Key|(empty)  |如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503|服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>实现说明
检索多个源文本的多个翻译候选项。

请求 URI 为 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

以下是请求正文的格式：

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` 包括以下元素：

* `AppId`：必需。 如果使用 `Authorization` 标头，请将 `AppId` 字段留空。 否则，请包含带有 `"Bearer" + " " + "access_token"` 的字符串。
* `From`：必需。 一个字符串，表示要翻译的文本的语言代码。
* `MaxTranslations`：必需。 一个整数，表示要返回的最大翻译数。
* `Options`：可选。 一个包含以下值的 `Options` 对象。 它们都是可选的，并且默认为最常用的设置。 指定的元素必须按字母顺序列出。
    - `Category`：一个字符串，包含翻译类别（域）。 默认值为 `general`。
    - `ContentType`：`text/plain` 是唯一支持的选项，也是默认选项。
    - `IncludeMultipleMTAlternatives`：一个布尔标志，用于指定是否应从 MT 引擎返回多个备选项。 有效值为 `true` 和 `false`（区分大小写）。 默认值是 `false`，它只返回一个备选项。 将标志设置为 `true` 可以在翻译中生成与协作翻译框架 (CTF) 完全集成的人工备选项。 此功能允许为在 CTF 中没有备选项的句子返回备选项，其方法是从解码器的“n 个最佳”列表中添加人工备选项。
        - 评级。评级的适用范围如下：
          - 最佳自动翻译评级为 5。
          - CTF 中的备选项反映了审阅者的权威性。 其评分范围为 -10 到 +10。
          - 自动生成的（n 个最佳）翻译备选项的评级为 0，匹配度为 100。
        - 替代项数。 返回的备选项的数目可以与 `maxTranslations` 中指定的值一样多，但也可以更少。
        - 语言对。 此功能不适用于简体中文和繁体中文之间任意向的翻译。 它可用于 Microsoft 翻译工具支持的所有其他语言对。
* `State`：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
* `Uri`：按此 URI 筛选结果。 如果未设置任何值，则默认值为 `all`。
* `User`：按此用户筛选结果。 如果未设置任何值，则默认值为 `all`。
* `Texts`：必需。 一个数组，包含要翻译的文本。 所有字符串必须采用同一语言。 要翻译的所有文本总共不得超过 10,000 个字符。 最大数组元素数为 10。
* `To`：必需。 一个字符串，表示文本翻译的目标语言的语言代码。

可以省略可选元素。 属于 `GetTranslationsArrayRequest` 的直接子项的元素必须按字母顺序列出。

请求 `Content-Type` 应为 `text/xml`。

**返回值：** 以下是响应的格式：

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

每个 `GetTranslationsResponse` 元素包含以下值：

* `Translations`：找到的匹配项的数组，存储在 `TranslationMatch` 对象中（在下一部分中介绍）。 翻译可能包括原始文本的略变体（模糊匹配）。 将对翻译进行排序：首先是 100% 匹配，接下来是模糊匹配。
* `From`：如果该方法没有指定 `From` 语言，则该值来自自动语言检测。 否则，它将是指定的 `From` 语言。
* `State`：用户状态，可帮助关联请求和响应。 包含 `TranslateOptions` 参数中提供的值。

`TranslationMatch` 对象包含以下值：
* `Error`：如果特定输入字符串发生错误，则返回错误代码。 否则，此字段为空。
* `MatchDegree`：表示输入文本与存储中发现的原始文本的匹配程度。 系统将输入句子与存储内容进行匹配，包括不精确匹配。 返回的值为 0 到 100，0 表示不相似，100 表示完全匹配（区分大小写）。
* `MatchedOriginalText`：此结果的匹配的原始文本。 仅当匹配的原始文本与输入文本不同时，才会返回此值。 它用于返回模糊匹配的源文本。 对于 Microsoft 翻译工具结果，不返回该值。
* `Rating`：表示进行质量决策的人员的权威性。 机器翻译结果的评分为 5。 匿名提供的翻译的评分通常为 1 到 4。 权威提供的翻译的评分通常为 6 到 10。
* `Count`：此评分的这个翻译被选中的次数。 对于自动翻译的响应，此值为 0。
* `TranslatedText`：翻译的文本。


### <a name="response-class-status-200"></a>响应类（状态 200）

字符串

响应内容类型：application/xml
 
### <a name="parameters"></a>参数

|参数|值|描述|参数类型|数据类型|
|:--|:--|:--|:--|:--|
|授权  |(empty)    |如果 `appid` 字段和 `Ocp-Apim-Subscription-Key` 标头都留空，则它为必填项。  授权令牌：`"Bearer" + " " + "access_token"`。|标头的值开始缓存响应|字符串|
|Ocp-Apim-Subscription-Key|(empty)  |如果 `appid` 字段和 `Authorization` 标头都留空，则它为必填项。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400    |请求错误。 请查看输入参数和详细的错误响应。|
|401    |凭据无效。|
|500    |服务器错误。 如果此错误仍然存在，请告知我们。 请向我们提供请求的大致日期和时间，以及包括在响应标头 `X-MS-Trans-Info` 中的请求 ID。|
|503    |服务暂时不可用。 请重试。如果此错误仍然存在，请告知我们。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移到翻译工具 v3](../migrate-to-v3.md)