---
title: Translator V3.0 参考
titleSuffix: Azure Cognitive Services
description: Translator V3.0 参考文档。 Translator 的版本 3 提供了基于 JSON 的新型 Web API。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: 18a9d2efa3093dd342e05f1c9038fa7f460d97bd
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358914"
---
# <a name="translator-v30"></a>Translator 3.0 版

## <a name="whats-new"></a>新增功能

Translator 的版本 3 提供了基于 JSON 的新型 Web API。 它通过将现有功能合并到更少的操作中来提高可用性和性能，并提供新功能。

 * 音译可将一种语言的文本从一个脚本转换为另一个脚本。
 * 在一个请求中翻译成多种语言。
 * 在一个请求中进行语言检测、翻译和音译。
 * 字典可用于查阅词条的替代翻译，查找反向翻译以及词条在上下文中使用时的示例。
 * 更详细的语言检测结果。

## <a name="base-urls"></a>基 URL

Microsoft Translator 位于多个数据中心位置之外。 目前它们位于 10 个 [Azure 地理区域](https://azure.microsoft.com/global-infrastructure/regions)：

* 美洲：美国东部、美国中南部、美国中西部和美国西部 2 
* 亚太：韩国南部、日本东部、东南亚和澳大利亚东部
* 欧洲：北欧、西欧

在大多数情况下，对 Microsoft Translator 的请求由距离请求来源最近的数据中心处理。 如果数据中心出现故障，请求可能会被路由到地理区域之外。

若要强制特定地理区域处理该请求，请将 API 请求中的全球终结点更改为所需的地理终结点：

|地理位置|基 URL（地理终结点）|
|:--|:--|
|全局（非区域性）|    api.cognitive.microsofttranslator.com|
|美国|    api-nam.cognitive.microsofttranslator.com|
|欧洲|    api-eur.cognitive.microsofttranslator.com|
|亚太区|    api-apc.cognitive.microsofttranslator.com|

<sup>1</sup> 资源位于瑞士北部或瑞士西部的客户可以确保其文本 API 请求在瑞士境内得到处理。 若要确保在瑞士处理请求，请在“资源区域”的“瑞士北部”或“瑞士西部”中创建翻译器资源，然后在 API 请求中使用该资源的自定义终结点。 例如：如果在 Azure 门户中以“资源区域”为“瑞士北部”创建翻译器资源，并且资源名称为“my-ch-n”，则自定义终结点为“https://my-ch-n.cognitiveservices.azure.com”。 要转换的示例请求如下：
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> 自定义翻译器目前在瑞士不可用。

## <a name="authentication"></a>身份验证

订阅 Azure 认知服务中的 Translator 或[认知服务多服务](https://azure.microsoft.com/pricing/details/cognitive-services/)，并使用订阅密钥（在 Azure 门户中提供）进行身份验证。 

有三个标头可用于对你的订阅进行身份验证。 下表介绍了每个标头的使用方式：

|标头|说明|
|:----|:----|
|Ocp-Apim-Subscription-Key|如果要传递密钥，请与认知服务订阅一起使用。<br/>该值是用于 Translator 订阅的 Azure 密钥。|
|授权|如果要传递身份验证令牌，请与认知服务订阅一起使用。<br/>该值是持有者令牌：`Bearer <token>`。|
|Ocp-Apim-Subscription-Region|用于认知服务多服务和区域翻译器资源。<br/>该值是多服务或区域翻译器资源的区域。 当使用全球翻译器资源时，该值是可选的。|

###  <a name="secret-key"></a>密钥
第一个选项是使用 `Ocp-Apim-Subscription-Key` 标头进行身份验证。 将 `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` 标头添加到请求。

#### <a name="authenticating-with-a-global-resource"></a>使用全球资源进行身份验证

当使用[全球翻译器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)时，需要包含一个标头来调用 Translator。

|标头|说明|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 该值是用于 Translator 订阅的 Azure 密钥。|

下面是使用全球翻译器资源调用 Translator 的示例请求

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>使用区域资源进行身份验证

当使用[区域翻译器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)时。
调用 Translator 需要 2 个标头。

|标头|说明|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 该值是用于 Translator 订阅的 Azure 密钥。|
|Ocp-Apim-Subscription-Region| 该值是翻译器资源的区域。 |

下面是使用区域翻译器资源调用 Translator 的示例请求

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>使用多服务资源进行身份验证

在使用认知服务的多服务资源时。 这样便可以使用一个密钥对多个服务的请求进行身份验证。 

在使用多服务密钥时，请求中必须包含两个身份验证标头。 调用 Translator 需要 2 个标头。

|标头|说明|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 该值是用于多服务资源的 Azure 密钥。|
|Ocp-Apim-Subscription-Region| 该值是多服务资源的区域。 |

区域对于多服务文本 API 订阅是必需的。 你选择的区域是在使用多服务订阅密钥时可用于文本翻译的唯一区域，并且必须与你在通过 Azure 门户注册多服务订阅时选择的区域相同。

可用区域包括 `australiaeast`、`brazilsouth`、`canadacentral`、`centralindia`、`centralus`、`centraluseuap`、`eastasia`、`eastus`、`eastus2`、`francecentral`、`japaneast`、`japanwest`、`koreacentral`、`northcentralus`、`northeurope`、`southcentralus`、`southeastasia`、`uksouth`、`westcentralus`、`westeurope`、`westus`、`westus2` 和 `southafricanorth`。

如果使用参数 `Subscription-Key` 传递查询字符串中的密钥，则必须使用查询参数 `Subscription-Region` 指定区域。

### <a name="authenticating-with-an-access-token"></a>使用访问令牌进行身份验证
或者，可以交换访问令牌的密钥。 此令牌作为 `Authorization` 标头包含在每个请求中。 若要获取授权令牌，请向以下 URL 发出 `POST` 请求：

| 资源类型     | 身份验证服务 URL                                |
|-----------------|-----------------------------------------------------------|
| 全球          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| 区域或多服务 | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

以下是根据给定密钥获取令牌的示例请求：

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

成功的请求会在响应正文中将编码的访问令牌作为纯文本返回。 有效的令牌在授权中作为持有者令牌传递给 Translator 服务。

```http
Authorization: Bearer <Base64-access_token>
```

身份验证令牌的有效期为 10 分钟。 在对 Translator 进行多次调用时，应重复使用该令牌。 但是，如果程序向 Translator 发出请求时持续的时间段很长，则该程序必须定期请求新的访问令牌（例如，每隔 8 分钟请求一次）。

## <a name="virtual-network-support"></a>虚拟网络支持

Translator 服务现在随虚拟网络 (VNET) 功能在 Azure 公有云的所有区域中提供。 若要启用虚拟网络，请参阅[配置 Azure 认知服务虚拟网络](../../cognitive-services-virtual-networks.md?tabs=portal)。 

启用此功能后，必须使用自定义终结点来调用 Translator。 你不能使用全球翻译器终结点（“api.cognitive.microsofttranslator.com”），也不能使用访问令牌进行身份验证。

你可以在创建[翻译器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)后找到自定义终结点，并允许从所选网络和专用终结点进行访问。

|头文件|说明|
|:-----|:----|
|Ocp-Apim-Subscription-Key| 该值是用于 Translator 订阅的 Azure 密钥。|
|Ocp-Apim-Subscription-Region| 该值是翻译器资源的区域。 如果资源为 `global`，则此值是可选项|

下面是使用自定义终结点调用 Translator 的示例请求

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>错误

标准错误响应是具有名为 `error` 的名称/值对的 JSON 对象。 该值也是具有以下属性的 JSON 对象：

  * `code`：服务器定义的错误代码。
  * `message`：一个提供人类可读的错误表示形式的字符串。

例如，拥有免费试用版订阅的客户会在免费配额用尽后收到以下错误：

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```

错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 常见错误代码包括：

| 代码 | 说明 |
|:----|:-----|
| 400000| 某个请求输入无效。|
| 400001| “scope”参数无效。|
| 400002| “category”参数无效。|
| 400003| 语言说明符缺失或无效。|
| 400004| 目标脚本说明符（“To script”）缺失或无效。|
| 400005| 输入文本缺失或无效。|
| 400006| 语言和脚本的组合无效。|
| 400018| 源脚本说明符（“From script”）缺失或无效。|
| 400019| 指定的某个语言不受支持。|
| 400020| 输入文本数组中的某个元素无效。|
| 400021| API 版本参数缺失或无效。|
| 400023| 指定的某个语言对无效。|
| 400035| 源语言（“From”字段）无效。|
| 400036| 目标语言（“To”字段）缺失或无效。|
| 400042| 指定的某个选项（“Options”字段）无效。|
| 400043| 客户端跟踪 ID（ClientTraceId 字段或 X-ClientTranceId 标头）缺失或无效。|
| 400050| 输入文本过长。 查看[请求限制](../request-limits.md)。|
| 400064| “translation”参数缺失或无效。|
| 400070| 目标脚本（ToScript 参数）的数目与目标语言（To 参数）的数目不匹配。|
| 400071| TextType 的值无效。|
| 400072| 输入文本的数组包含过多的元素。|
| 400073| 脚本参数无效。|
| 400074| 请求正文是无效的 JSON。|
| 400075| 语言对和类别组合无效。|
| 400077| 超过了最大请求大小。 查看[请求限制](../request-limits.md)。|
| 400079| 请求用于在源语言与目标语言之间进行翻译的自定义系统不存在。|
| 400080| 语言或脚本不支持音译。|
| 401000| 由于凭据缺失或无效，请求未授权。|
| 401015| “提供的凭据适用于语音 API。 此请求需要文本 API 的凭据。 请使用 Translator 的订阅。”|
| 403000| 不允许该操作。|
| 403001| 由于订阅已超过其免费配额，因此不允许该操作。|
| 405000| 请求的资源不支持该请求方法。|
| 408001| 正在准备所请求的翻译系统。 请在几分钟后重试。|
| 408002| 等待传入流时请求超时。 客户端没有在服务器准备等待的时间内生成请求。 客户端可以在以后的任何时间重复该请求，而不做任何修改。|
| 415000| Content-Type 标头缺失或无效。|
| 429000、429001、429002| 由于客户端已超出请求限制，服务器拒绝了请求。|
| 500000| 发生了意外错误。 如果该错误持续出现，请报告发生错误的日期/时间、响应标头 X-RequestId 中的请求标识符，以及请求标头 X-ClientTraceId 中的客户端标识符。|
| 503000| 服务暂时不可用。 Retry。 如果该错误持续出现，请报告发生错误的日期/时间、响应标头 X-RequestId 中的请求标识符，以及请求标头 X-ClientTraceId 中的客户端标识符。|

## <a name="metrics"></a>指标 
利用指标，可以在 Azure 门户中的指标部分下查看翻译器的使用情况和可用性信息，如以下屏幕截图所示。 有关详细信息，请参阅[数据和平台指标](../../../azure-monitor/essentials/data-platform-metrics.md)。

![Translator 指标](../media/translatormetrics.png)

此表列出了可用的指标，以及有关如何使用它们来监视翻译 API 调用的说明。

| 指标 | 说明 |
|:----|:-----|
| TotalCalls| API 调用总数。|
| TotalTokenCalls| 使用身份验证令牌通过令牌服务进行的 API 调用的总数。|
| SuccessfulCalls| 成功调用数。|
| TotalErrors| 产生了错误响应的调用数。|
| BlockedCalls| 超过速率或配额限制的调用数。|
| ServerErrors| 产生了服务器内部错误 (5XX) 的调用数。|
| ClientErrors| 引发客户端错误 (4XX) 的调用数。|
| 延迟| 完成请求的持续时间（毫秒）。|
| CharactersTranslated| 传入的文本请求中的字符总数。|
