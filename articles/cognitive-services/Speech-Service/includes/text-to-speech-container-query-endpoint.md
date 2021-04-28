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
ms.openlocfilehash: 4571acfc46d9b336be23eaa3287dce5696c707b2
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930203"
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
