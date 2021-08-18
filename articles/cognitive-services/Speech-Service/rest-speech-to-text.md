---
title: 语音转文本 API 参考 (REST) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音转文本 REST API。 本文介绍授权选项、查询选项，以及如何构建请求和接收响应。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c97213b9163d6371bcee875d34dfc95cab3badc
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067078"
---
# <a name="speech-to-text-rest-api"></a>语音转文本 REST API

语音转文本有两个不同的 REST API。 每个 API 都有其特殊用途，并使用不同的终结点集。

语音转文本 REST API 包括：
- [语音转文本 REST API v3.0](#speech-to-text-rest-api-v30)，用于[批量听录](batch-transcription.md)和[自定义语音识别](custom-speech-overview.md)。 v3.0 是 [v2.0 的后继版本](./migrate-v2-to-v3.md)。
- [适用于短音频的语音转文本 REST API](#speech-to-text-rest-api-for-short-audio)，用于联机听录，作为[语音 SDK](speech-sdk.md) 的替代方法。 使用此 API 的请求每个最多只能传输 60 秒的音频。 

## <a name="speech-to-text-rest-api-v30"></a>语音转文本 REST API v3.0

语音转文本 REST API v3.0 用于[批量听录](batch-transcription.md)和[自定义语音识别](custom-speech-overview.md)。 如果需要通过 REST 与联机听录进行通信，请使用[适用于短音频的语音转文本 REST API](#speech-to-text-rest-api-for-short-audio)。

使用 REST API v3.0 执行以下操作：
- 在希望同事有权访问你生成的模型或希望将模型部署到多个区域的情况下，将模型复制到其他订阅
- 从容器转录数据（批量听录），并提供多个音频文件 URL
- 使用 SAS URI 从 Azure 存储帐户上传数据
- 如果已请求某终结点的日志，则按终结点获取日志
- 请求创建的模型的清单，以便设置本地容器

REST API v3.0 包含以下功能：
- 通知 Webhook - 服务的所有正在运行的进程现在都支持 Webhook 通知。 REST API v3.0 提供了调用，使你能够在发送通知的位置注册 Webhook
- 更新终结点后面的模型 
- 多数据集模型适应 - 使用声音、语言和发音数据的多个数据集组合调整模型
- 自带存储 - 针对日志、听录文件和其他数据使用自己的存储帐户

请参阅[本文](batch-transcription.md)中有关使用 REST API v3.0 批量听录的示例。

如果使用的是语音转文本 REST API v2.0，请参阅[本指南](./migrate-v2-to-v3.md)中迁移到 v3.0 的方式。

请参阅[此处](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)完整的语音转文本 REST API v3.0 参考。

## <a name="speech-to-text-rest-api-for-short-audio"></a>适用于短音频的语音转文本 REST API

作为[语音 SDK](speech-sdk.md) 的一种替代方法，语音服务允许使用 REST API 转换语音转文本。
适用于短音频的 REST API 的使用场景非常有限，只应在[语音 SDK](speech-sdk.md) 不能使用的情况下使用。

使用适用于短音频的语音转文本 REST API 之前，请注意以下几点：

* 使用适用于短音频的 REST API 并直接传输音频的请求最多只能包含 60 秒的音频。
* 适用于短音频的语音转文本 REST API 仅返回最终结果。 不提供部分结果。

如果必须为应用程序发送更长的音频，请考虑使用[语音 SDK](speech-sdk.md) 或[语音转文本 REST API v3.0](#speech-to-text-rest-api-v30)。

> [!TIP]
> 请参阅[此文](sovereign-clouds.md)，以了解 Azure 政府和 Azure 中国终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>区域和终结点

适用于短音频的 REST API 的终结点具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必须将语言参数追加到 URL 以避免收到 4xx HTTP 错误。 例如，使用“美国西部”终结点设置为美国英语的语言为：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="query-parameters"></a>查询参数

可将以下参数包含在 REST 请求的查询字符串中。

| 参数 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `language` | 标识所要识别的口语。 请参阅[支持的语言](language-support.md#speech-to-text)。 | 必需 |
| `format` | 指定结果格式。 接受的值为 `simple` 和 `detailed`。 简单结果包括 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 Detailed 响应包括显示文本的四种不同的表示形式。 默认设置为 `simple`。 | 可选 |
| `profanity` | 指定如何处理识别结果中的不雅内容。 接受的值为 `masked`（将亵渎内容替换为星号）、`removed`（删除结果中的所有亵渎内容）或 `raw`（包含结果中的亵渎内容）。 默认设置为 `masked`。 | 可选 |
| `cid` | 使用[自定义语音门户](./custom-speech-overview.md)创建自定义模型时，可以通过在“部署”页上找到的其终结点 ID 使用自定义模型。 使用终结点 ID 作为 `cid` 查询字符串形式参数的实际参数。 | 可选 |

### <a name="request-headers"></a>请求标头

该表列出了语音转文本请求的必需和可选标头。

|标头| 说明 | 必需/可选 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 语音服务订阅密钥。 | 此标头或 `Authorization` 是必需的。 |
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 此标头或 `Ocp-Apim-Subscription-Key` 是必需的。 |
| `Pronunciation-Assessment` | 指定用于在识别结果中显示发音分数的参数，这些参数评估语音输入的发音质量，并具有准确性、流畅性、完整性等指标。此参数是 Base64 编码的 json，其中包含多个详细的参数。 若要了解如何生成此标头，请参阅[发音评估参数](#pronunciation-assessment-parameters)。 | 可选 |
| `Content-type` | 描述所提供音频数据的格式和编解码器。 接受的值为 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必需 |
| `Transfer-Encoding` | 指定要发送分块的音频数据，而不是单个文件。 仅当要对音频数据进行分块时才使用此标头。 | 可选 |
| `Expect` | 如果使用分块传输，则发送 `Expect: 100-continue`。 语音服务将确认初始请求并等待附加的数据。| 如果发送分块的音频数据，则是必需的。 |
| `Accept` | 如果提供此标头，则值必须是 `application/json`。 语音服务以 JSON 格式提供结果。 某些请求框架提供不兼容的默认值。 最好始终包含 `Accept`。 | 可选，但建议提供。 |

### <a name="audio-formats"></a>音频格式

在 HTTP `POST` 请求的正文中发送音频。 它必须采用下表中的格式之一：

| 格式 | 编解码器 | 比特率 | 采样率  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz，单声道 |
| OGG    | OPUS  | 256 kpbs | 16 kHz，单声道 |

>[!NOTE]
>语音服务中适用于短音频的 REST API 和 WebSocket 支持上述格式。 [语音 SDK](speech-sdk.md) 当前支持使用 PCM 编解码器的 WAV 格式以及[其他格式](how-to-use-codec-compressed-audio-input-streams.md)。

### <a name="pronunciation-assessment-parameters"></a>发音评估参数

下表列出了发音评估的必需参数和可选参数。

| 参数 | 说明 | 必需？ |
|-----------|-------------|---------------------|
| ReferenceText | 用来对发音进行评估的文本。 | 必须 |
| GradingSystem | 用于分数校准的分数系统。 `FivePoint` 系统给出 0-5 的浮点分数，而 `HundredMark` 系统给出 0-100 的浮点分数。 默认值：`FivePoint`。 | 可选 |
| 粒度 | 评估粒度。 接受的值为 `Phoneme`（显示全文、单词和音素级别的分数）、`Word`（显示全文和单词级别的分数）、`FullText`（只显示全文级别的分数）。 默认设置为 `Phoneme`。 | 可选 |
| 维度 | 定义输出条件。 接受的值为 `Basic`（只显示准确度分数）、`Comprehensive`（显示更多维度的分数，例如，全文级别的流畅度分数和完整性分数、单词级别的错误类型）。 检查[响应参数](#response-parameters)以了解不同分数维度和单词错误类型的定义。 默认设置为 `Basic`。 | 可选 |
| EnableMiscue | 启用误读计算。 启用此功能后，系统会将发音单词与参考文本进行比较，并会根据比较结果将其标记为省略/插入。 接受的值为 `False` 和 `True`。 默认设置为 `False`。 | 可选 |
| ScenarioId | 一个 GUID，表示自定义分数系统。 | 可选 |

下面是一个示例 JSON，其中包含发音评估参数：

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

下面的示例代码演示如何将发音评估参数构建到 `Pronunciation-Assessment` 标头中：

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

我们强烈建议在发布音频数据时进行流式（分块）上传，这可以显著减少延迟。 请参阅[不同编程语言的示例代码](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment)以了解如何启用流式处理。

>[!NOTE]
> 发音评估功能当前支持 `en-US` 语言，可用于所有[语音转文本区域](regions.md#speech-to-text)。 对 `en-GB` 和 `zh-CN` 语言的支持现为预览版，可用于 `westus`、`eastasia` 和 `centralindia` 区域。

### <a name="sample-request"></a>示例请求

以下示例包括主机名和必需的标头。 必须注意，服务同时预期提供音频数据，但此示例未包括这些数据。 如前所述，建议进行分块，但不是非要这样做。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

若要启用发音评估，可以添加以下标头。 若要了解如何生成此标头，请参阅[发音评估参数](#pronunciation-assessment-parameters)。

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | 说明 | 可能的原因 |
|------------------|-------------|-----------------|
| `100` | 继续 | 已接受初始请求。 继续发送剩余的数据。 （与分块传输配合使用） |
| `200` | OK | 请求成功；响应正文是一个 JSON 对象。 |
| `400` | 错误的请求 | 语言代码未提供、不是支持的语言、音频文件无效等。 |
| `401` | 未授权 | 指定区域中的订阅密钥或授权令牌无效，或终结点无效。 |
| `403` | 禁止 | 缺少订阅密钥或授权令牌。 |

### <a name="chunked-transfer"></a>分块传输

分块传输 (`Transfer-Encoding: chunked`) 有助于降低识别延迟。 它允许语音服务在传输音频文件时开始处理该文件。 适用于短音频的 REST API 不提供部分结果或临时结果。

此代码示例演示如何以块的形式发送音频。 只有第一个区块应该包含音频文件的标头。 `request` 是连接到相应 REST 终结点的 `HttpWebRequest` 对象。 `audioFile` 是音频文件在磁盘上的路径。

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>响应参数

结果以 JSON 格式提供。 `simple` 格式包含以下顶级字段。

| 参数 | 说明  |
|-----------|--------------|
|`RecognitionStatus`|状态，例如 `Success` 表示成功识别。 请参阅下表。|
|`DisplayText`|经过大小写转换、添加标点、执行反向文本规范化（将口头文本转换为短形式，例如，200 表示“two hundred”，或“Dr.Smith”表示“doctor smith”）和屏蔽亵渎内容之后的识别文本。 仅在成功时提供。|
|`Offset`|在音频流中开始识别语音的时间（以 100 纳秒为单位）。|
|`Duration`|在音频流中识别语音的持续时间（以 100 纳秒为单位）。|

`RecognitionStatus` 字段可包含以下值：

| 状态 | 说明 |
|--------|-------------|
| `Success` | 识别成功并且存在 `DisplayText` 字段。 |
| `NoMatch` | 在音频流中检测到语音，但没有匹配目标语言的字词。 通常表示识别语言不同于讲话用户所用的语言。 |
| `InitialSilenceTimeout` | 音频流的开始仅包含静音，并且服务在等待语音时超时。 |
| `BabbleTimeout` | 音频流的开始仅包含噪音，并且服务在等待语音时超时。 |
| `Error` | 识别服务遇到内部错误，无法继续。 如果可能，请重试。 |

> [!NOTE]
> 如果音频仅包含亵渎内容，并且 `profanity` 查询参数设置为 `remove`，则服务不会返回语音结果。

`detailed` 格式包括其他形式的已识别结果。
使用 `detailed` 格式时，将以 `Display` 形式为 `NBest` 列表中的每条结果提供 `DisplayText`。

`NBest` 列表中的对象可以包括：

| 参数 | 说明 |
|-----------|-------------|
| `Confidence` | 条目的置信度评分，从 0.0（完全不可信）到 1.0（完全可信） |
| `Lexical` | 已识别文本的词法形式：识别的实际单词。 |
| `ITN` | 已识别文本的反向文本规范化（“规范”）形式，已应用电话号码、数字、缩写（“doctor smith”缩写为“dr smith”）和其他转换。 |
| `MaskedITN` | 可根据请求提供应用了亵渎内容屏蔽的 ITN 形式。 |
| `Display` | 已识别文本的显示形式，其中添加了标点符号和大小写形式。 此参数与将格式设置为 `simple` 时提供的 `DisplayText` 相同。 |
| `AccuracyScore` | 语音的发音准确度。 准确度表示音素与母语人士发音的接近程度。 单词级别和全文级别的准确度分数由音素级别的准确度分数聚合而来。 |
| `FluencyScore` | 给定语音的流畅度。 流畅度表示语音与母语人士在单词之间使用无声停顿的接近程度。 |
| `CompletenessScore` | 语音的完整性，通过计算发音单词与参考文本输入的比率来确定。 |
| `PronScore` | 总分，表示给定语音的发音质量。 此分数按权重从 `AccuracyScore`、`FluencyScore` 和 `CompletenessScore` 聚合而成。 |
| `ErrorType` | 此值指示与 `ReferenceText` 相比，是省略、插入还是错误读出字词。 可能的值为 `None`（表示此词没有错误）、`Omission`、`Insertion` 和 `Mispronunciation`。 |

### <a name="sample-responses"></a>示例响应

`simple` 识别的典型响应：

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` 识别的典型响应：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
    {
      "Confidence": 0.9052885,
      "Display": "What's the weather like?",
      "ITN": "what's the weather like",
      "Lexical": "what's the weather like",
      "MaskedITN": "what's the weather like"
    },
    {
      "Confidence": 0.92459863,
      "Display": "what is the weather like",
      "ITN": "what is the weather like",
      "Lexical": "what is the weather like",
      "MaskedITN": "what is the weather like"
    }
  ]
}
```

使用发音评估进行识别的典型响应：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤

- [创建免费 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)
- [自定义声学模型](./how-to-custom-speech-train-model.md)
- [自定义语言模型](./how-to-custom-speech-train-model.md)
- [熟悉批量听录](batch-transcription.md)

