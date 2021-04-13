---
title: 如何获取唇音同步的人脸姿态事件
titleSuffix: Azure Cognitive Services
description: 语音 SDK 支持语音合成中的视素事件，该事件用于表示观察到的语音中的关键姿态，例如，在产生特定音素时嘴唇、下巴和舌头的位置。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 15fa1dd230b7f07846653278533805fa66ed2195
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870377"
---
# <a name="get-facial-pose-events"></a>获取人脸姿态事件

> [!NOTE]
> 视素目前暂时只适用于美国西部 (`westus`) 区域的 `en-US-AriaNeural` 语音，在 2021 年 4 月底之后，将会适用于所有 `en-US` 语音。

视素是口语中音素的视觉描述。
它定义了在说一个字词时人脸和嘴部的位置。
每个视素都描绘与一组特定音素对应的关键人脸姿态。
在视素和音素之间没有一对一的对应关系。
通常，多个音素对应一个视素，因为多个音素（如 `s` 和 `z`）在生成时在人脸上看起来是相同的。
请参阅[视素和音素之间的映射表](#map-phonemes-to-visemes)。

使用视素，可以创建更多自然且智能的新闻广播助手、更具交互性的游戏和卡通人物，以及更直观的语言教学视频。 听障人士还可以直观地拾取声音，通过“唇读”来理解在动态人脸上显示视素的语音内容。

## <a name="get-viseme-events-with-the-speech-sdk"></a>使用语音 SDK 获取视素事件

为了创建视素事件，我们将输入文本转换为一组音素序列以及与它们相对应的视素序列。 我们会预估语音音频中每个视素的开始时间。 视素事件包含一系列视素 ID，每个 ID 都带有在该视素出现的音频中的偏移量。 这些事件可以驱动对朗读输入文本的人员进行模拟的嘴部动态。

| 参数 | 说明 |
|-----------|-------------|
| 视素 ID | 整数，用于指定视素。 在英语（美国）中，我们提供 22 个不同的视素来描述一组特定音素的嘴部形状。 请参阅[视素 ID 和音素之间的映射表](#map-phonemes-to-visemes)。  |
| 音频偏移 | 每个视素的开始时间，以时钟周期（100 纳秒）为单位。 |

若要获取视素事件，请在语音 SDK 中订阅 `VisemeReceived` 事件。
以下代码片段演示了如何订阅视素事件。

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>将音素映射到视素

视素因语言而异。 每种语言都有一组与该语言的特定音素相对应的视素。 下表显示了国际拼音字母 (IPA) 音素和英语（美国）的视素 ID 之间的对应关系。

| IPA | 示例 | 视素 ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard, f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|d  |   **d** ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|m  |   **m** at、s **m** ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|s  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id, g **l** ad     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)
