---
title: 文本转语音快速入门 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 将文本转换为语音。 本快速入门介绍有关语音合成的对象构造和设计模式、支持的音频输出格式、语音 CLI 以及自定义配置选项。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/17/2021
ms.author: nitinme
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: 文本转语音
ms.openlocfilehash: be6080e90fedb28d5c579fbec8abb19164236ac1
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114709786"
---
# <a name="get-started-with-text-to-speech"></a>开始使用文本转语音

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>获取位置信息

你的项目可能需要知道何时会通过文本转语音说出某个字词，以便能够根据该时间执行特定操作。
例如，如果需要字词在被说出时突出显示，就会需要知道要突出显示什么内容、何时突出显示这些内容，以及突出显示多长时间。

可以使用 `SpeechSynthesizer` 中提供的 `WordBoundary` 事件来实现此目的。
此事件在每个新说出的字词的开头引发，并且将会在说出的流中提供时间偏移，另外还会在输入提示中提供文本偏移。

* `AudioOffset` 会报告输出音频在合成的开始处和下一个字词的开头之间经历的时间。 此时间以百纳秒单位 (HNS) 进行测量，10,000 HNS 相当于 1 毫秒。
* `WordOffset` 报告输入字符串（原始文本或 [SSML](speech-synthesis-markup.md)）中紧靠在要说出的字词之前的字符位置。

> [!NOTE]
> `WordBoundary` 事件在输出音频数据变为可用时引发，这样将会比播放到输出设备更快。 将流计时相应地同步到“实时”必须由调用方来完成。

在 GitHub 上的[文本转语音示例](https://aka.ms/csspeech/samples)中可以找到使用 `WordBoundary` 的示例。

## <a name="next-steps"></a>后续步骤

* [自定义语音入门](how-to-custom-voice.md)
* [通过 SSML 改进合成](speech-synthesis-markup.md)
* 了解如何使用[长音频 API](long-audio-api.md) 来获取大型文本示例，例如书籍和新闻文章
* 请参阅 GitHub 上的[快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)
