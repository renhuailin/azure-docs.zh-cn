---
title: 查询文本转语音容器终结点
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622342"
---
该容器提供了[基于 REST 的终结点 API](../rest-text-to-speech.md)。 有许多适用于平台、框架和语言变体的[示例源代码项目](https://github.com/Azure-Samples/Cognitive-Speech-TTS)。

对于标准或神经文本转语音容器，应依赖于所下载图像标记的区域设置和语音。 例如，如果下载了 `latest` 标记，则默认区域设置为 `en-US` 和 `AriaNeural` 语音。 然后，`{VOICE_NAME}` 参数将为 [`en-US-AriaNeural`](../language-support.md#neural-voices)。 请参阅以下 SSML 示例：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

但是，对于自定义文本语音转换，需要从[自定义语音门户](https://aka.ms/custom-voice-portal)获取“语音/模型”。 自定义模型名称与语音名称同义。 导航到“训练”页，并复制“语音/模型”作为 `{VOICE_NAME}` 参数。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自定义语音模型 - 语音名称":::

请参阅以下 SSML 示例：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

让我们构造一个 HTTP POST 请求，提供一些标头和数据负载。 将 `{VOICE_NAME}` 占位符值替换为你自己的值。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


此命令：

* 为 `speech/synthesize/cognitiveservices/v1` 终结点构造 HTTP POST 请求。
* 指定 `audio/*` 的 `Accept` 标头
* 指定 `application/ssml+xml` 的 `Content-Type` 标头，有关详细信息，请参阅[请求正文](../rest-text-to-speech.md#request-body)。
* 指定 `riff-16khz-16bit-mono-pcm` 的 `X-Microsoft-OutputFormat` 标头，有关更多选项，请参阅[音频输出](../rest-text-to-speech.md#audio-outputs)。
* 将给定了 `{VOICE_NAME}` 的[语音合成标记语言 (SSML)](../speech-synthesis-markup.md) 请求发送到终结点。
