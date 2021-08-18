---
title: 文本转语音 API 参考 (REST) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用文本转语音 REST API。 本文介绍授权选项、查询选项，以及如何构建请求和接收响应。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.custom: references_regions
ms.openlocfilehash: 31d03ee9cc230412ef6fdbdcc351b7a6b5e296a6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066282"
---
# <a name="text-to-speech-rest-api"></a>文本转语音 REST API

语音服务可让你使用一组 REST API [将文本转换为合成语音](#convert-text-to-speech)，并[获取某个区域支持的语音列表](#get-a-list-of-voices)。 每个可用终结点都与一个区域相关联。 需要计划使用的终结点/区域的订阅密钥。

文本转语音 REST API 支持神经和标准文本转语音，每种语音支持区域设置标识的特定语言和方言。

* 有关语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。
* 有关区域可用性的信息，请参阅[区域](regions.md#text-to-speech)。

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的费用各不相同。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

使用此 API 之前，请了解：

* 文本转语音 REST API 需要授权标头。 这意味着，需要完成令牌交换才能访问该服务。 有关详细信息，请参阅[身份验证](#authentication)。

> [!TIP]
> 请参阅[此文](sovereign-clouds.md)，以了解 Azure 政府和 Azure 中国终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>获取语音列表

`voices/list` 终结点允许你获取特定区域/终结点的完整语音列表。

### <a name="regions-and-endpoints"></a>区域和终结点

| 区域 | 端点 |
|--------|----------|
| 澳大利亚东部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 巴西南部 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 加拿大中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 东亚 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国东部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国东部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 法国中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 印度中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 韩国中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 北欧 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 南非北部 | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 东南亚 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 英国南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国中西部 | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 西欧 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美国西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [预览版语音](language-support.md#neural-voices-in-preview)只在以下 3 个区域提供：美国东部、西欧和东南亚。

### <a name="request-headers"></a>请求标头

下表列出了文本转语音请求的必需和可选标头。

| 标头 | 说明 | 必需/可选 |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 语音服务订阅密钥。 | 此标头或 `Authorization` 是必需的。 |
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 此标头或 `Ocp-Apim-Subscription-Key` 是必需的。 |



### <a name="request-body"></a>请求正文

对此终结点的 `GET` 请求不需要正文。

### <a name="sample-request"></a>示例请求

此请求仅需要授权标头。

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>示例响应

为说明响应的结构，已将此响应截断。

> [!NOTE]
> 语音可用性因区域/终结点而异。

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

    ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, JennyMultilingualNeural)",
    "ShortName": "en-US-JennyMultilingualNeural",
    "DisplayName": "Jenny Multilingual",
    "LocalName": "Jenny Multilingual",
    "Gender": "Female",
    "Locale": "en-US",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "SecondaryLocaleList": [
        "de-DE",
        "en-AU",
        "en-CA",
        "en-GB",
        "es-ES",
        "es-MX",
        "fr-CA",
        "fr-FR",
        "it-IT",
        "ja-JP",
        "ko-KR",
        "pt-BR",
        "zh-CN"
      ],
    "Status": "Preview"
    },
    
  ...
    
    {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | 说明 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | OK | 请求已成功。 |
| 400 | 错误的请求 | 必需参数缺失、为空或为 null。 或者，传递给必需参数或可选参数的值无效。 常见问题是标头太长。 |
| 401 | 未授权 | 请求未经授权。 确保订阅密钥或令牌有效并在正确的区域中。 |
| 429 | 请求过多 | 已经超过了订阅允许的配额或请求速率。 |
| 502 | 错误的网关    | 网络或服务器端问题。 也可能表示标头无效。 |


## <a name="convert-text-to-speech"></a>将文本转换到语音

`v1` 终结点允许你使用[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 将文本转换为语音。

### <a name="regions-and-endpoints"></a>区域和终结点

使用 REST API 的文本转语音支持以下区域。 请务必选择与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>请求标头

下表列出了文本转语音请求的必需和可选标头。

| 标头 | 说明 | 必需/可选 |
|--------|-------------|---------------------|
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 必需 |
| `Content-Type` | 指定所提供的文本的内容类型。 接受的值：`application/ssml+xml`。 | 必需 |
| `X-Microsoft-OutputFormat` | 指定音频输出格式。 有关接受值的完整列表，请参阅[音频输出](#audio-outputs)。 | 必需 |
| `User-Agent` | 应用程序名称。 提供的值必须少于 255 个字符。 | 必需 |

### <a name="audio-outputs"></a>音频输出

这是在每个请求中作为 `X-Microsoft-OutputFormat` 标头发送的受支持音频格式的列表。 每种格式合并了比特率和编码类型。 语音服务支持 24 kHz、16 kHz 和 8 kHz 音频输出。

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> 如果所选语音和输出格式具有不同的比特率，则根据需要对音频重新采样。
> ogg-24khz-16bit-mono-opus 可以使用 [opus 编解码器](https://opus-codec.org/downloads/)进行解码

### <a name="request-body"></a>请求正文

每个 `POST` 请求的正文作为[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 发送。 SSML 允许选择文本到语音转换服务返回的合成语音的语音和语言。 有关受支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

> [!NOTE]
> 如果使用自定义语音，请求正文可以作为纯文本（ASCII 或 UTF-8）发送。

### <a name="sample-request"></a>示例请求

此 HTTP 请求使用 SSML 指定语音和语言。 如果正文长度较长，并且生成的音频超过 10 分钟，则将其截断为 10 分钟。 换句话说，音频长度不能超过 10 分钟。

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | 说明 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | OK | 请求成功；响应正文是一个音频文件。 |
| 400 | 错误的请求 | 必需参数缺失、为空或为 null。 或者，传递给必需参数或可选参数的值无效。 常见问题是标头太长。 |
| 401 | 未授权 | 请求未经授权。 确保订阅密钥或令牌有效并在正确的区域中。 |
| 415 | 不支持的媒体类型 | 可能是提供了错误的 `Content-Type`。 `Content-Type` 应设置为 `application/ssml+xml`。 |
| 429 | 请求过多 | 已经超过了订阅允许的配额或请求速率。 |
| 502 | 错误的网关    | 网络或服务器端问题。 也可能表示标头无效。 |

如果 HTTP 状态为 `200 OK`，则响应正文包含采用所请求格式的音频文件。 可以一边传输一边播放此文件，或者将其保存到缓冲区或文件中。

## <a name="next-steps"></a>后续步骤

- [创建免费 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)
- [用于长格式音频的异步合成](./long-audio-api.md)
- [自定义神经语音入门](how-to-custom-voice.md)
