---
title: 如何获取唇音同步的人脸姿态事件
titleSuffix: Azure Cognitive Services
description: 语音 SDK 支持语音合成中的视素事件，该事件表示观察到的语音中的关键姿态，例如，在产生特定音素时嘴唇、下巴和舌头的位置。
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
ms.openlocfilehash: 3601cd6f7580a4d87dda7488826e25ca85b233c9
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654171"
---
# <a name="get-facial-pose-events"></a>获取人脸姿态事件

> [!NOTE]
> 视素事件目前仅适用于 `en-US` 英语（美国）和[神经语音](language-support.md#text-to-speech)。

_视素_ 是口语中音素的视觉描述。
它定义了在说一个字词时人脸和嘴部的位置。
每个视素都描绘与一组特定音素对应的关键人脸姿态。
视素可以用来控制 2D 和 3D 头像模型的运动，使口部运动与合成的语音完美匹配。

视素使头像更易于使用和控制。 使用视素，你可以：

 * 为智能展台创建动画虚拟语音助手，为客户构建多模式集成服务。
 * 构建沉浸式新闻广播并通过自然面部和口部运动改善观众体验。
 * 生成更多交互式游戏头像和可与动态内容交谈的卡通角色。
 * 制作更有效的语言教学视频，帮助语言学员了解每个单词和音素的口部行为。
 * 听障人士还可以从视觉上理解声音，通过“唇读”来理解在动态人脸上显示视素的语音内容。

请查看 viseme 的[简介视频](https://youtu.be/ui9XT47uwxs)。
> [!VIDEO https://www.youtube.com/embed/ui9XT47uwxs]

## <a name="azure-neural-tts-can-produce-visemes-with-speech"></a>Azure 神经 TTS 可以通过语音生成视素

神经语音将输入文本或 SSML（语音合成标记语言）转换为合成语音。 语音音频输出可以附带视素 ID 及其偏移时间戳。 每个视素 ID 均指定观察到的语音中的特定姿势，如产生特定音素时的嘴唇、下巴和舌头的位置。 使用 2D 或 3D 绘制引擎，你可以使用这些视素事件对头像进行动画处理。

以下流程图描述了视素的整体工作流。

![视素的整体工作流](media/text-to-speech/viseme-structure.png)

| 参数 | 说明 |
|-----------|-------------|
| 视素 ID | 整数，用于指定视素。 在英语（美国）中，我们提供 22 个不同的视素来描述一组特定音素的嘴部形状。 在视素和音素之间没有一对一的对应关系。 通常，多个音素对应一个视素，因为多个音素（如 `s` 和 `z`）在生成时在人脸上看起来是相同的。 请参阅[视素 ID 和音素之间的映射表](#map-phonemes-to-visemes)。  |
| 音频偏移 | 每个视素的开始时间，以时钟周期（100 纳秒）为单位。 |

## <a name="get-viseme-events-with-the-speech-sdk"></a>使用语音 SDK 获取视素事件

若要获取视素的合成语音，请在语音 SDK 中订阅 `VisemeReceived` 事件。
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

以下是视素输出的示例。

```text
(Viseme), Viseme ID: 1, Audio offset: 200ms.

(Viseme), Viseme ID: 5, Audio offset: 850ms.

……

(Viseme), Viseme ID: 13, Audio offset: 2350ms.
```

获取视素输出后，可以使用这些事件来驱动角色动画。 可以生成自己的角色并自动对其进行动画处理。

对于 2D 人物，可以设计一个适合你方案的角色，并为每个视素 ID 使用可缩放的矢量图形 (SVG)，以获取基于时间的面部位置。 在视素事件中提供临时标记后，这些设计良好的 SVG 将通过平滑修改进行处理，并提供可靠的动画给用户。 例如，下面的插图显示了为语言学习设计的红唇角色。

![2D 呈现示例](media/text-to-speech/viseme-demo-2D.png)

对于 3D 人物，请将其视为提线木偶。 人偶师把线从一种状态拉到另一种状态，物理定律会让木偶流畅移动。 视素输出充当人偶师，提供操作时间线。 动画引擎定义操作的物理定律。 通过使用缓动算法将帧插值，引擎可以进一步生成高质量的动画。

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
