---
title: 如何使用语音 SDK 进行发音评估
titleSuffix: Azure Cognitive Services
description: 语音 SDK 支持发音评估，这样便可以使用准确性、流畅性、完整性等指标评估语音输入的发音质量。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2335b1e85290cdea805c3a77754a5843da6cbbda
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672355"
---
# <a name="pronunciation-assessment"></a>发音评估

发音评估可以评估语音发音，并为说话人提供有关讲话音频准确度和流利度的反馈。
通过发音评估，语言学习者可以练习、获得即时反馈并改进其发音，因此能够自信地讲话和演示。
教师可以使用此功能来实时评估多个说话人的发音。

本文介绍如何使用语音 SDK 设置 `PronunciationAssessmentConfig` 并检索 `PronunciationAssessmentResult`。

> [!NOTE]
> 发音评估功能目前支持 `en-US` 语言（可以在所有[语音转文本区域](regions.md#speech-to-text-text-to-speech-and-translation)使用）。 对 `en-GB` 和 `zh-CN` 语言的支持现为预览版。

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>通过语音 SDK 进行发音评估

在下面的示例中，你将创建 `PronunciationAssessmentConfig`，然后将其应用于 `SpeechRecognizer`。

以下代码片段说明如何在应用中使用语言识别：

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>发音评估配置参数

下表列出了发音评估的配置参数。

| 参数 | 说明 | 必需？ |
|-----------|-------------|---------------------|
| ReferenceText | 用来对发音进行评估的文本。 | 必须 |
| GradingSystem | 用于分数校准的分数系统。 `FivePoint` 系统给出 0-5 的浮点分数，而 `HundredMark` 系统给出 0-100 的浮点分数。 默认值：`FivePoint`。 | 可选 |
| 粒度 | 评估粒度。 接受的值为 `Phoneme`（显示全文、单词和音素级别的分数）、`Word`（显示全文和单词级别的分数）、`FullText`（只显示全文级别的分数）。 默认值：`Phoneme`。 | 可选 |
| EnableMiscue | 启用误读计算。 启用此功能后，系统会将发音单词与参考文本进行比较，并会根据比较结果将其标记为省略/插入。 接受的值为 `False` 和 `True`。 默认值：`False`。 | 可选 |
| ScenarioId | 一个 GUID，表示自定义分数系统。 | 可选 |

### <a name="pronunciation-assessment-result-parameters"></a>发音评估结果参数

下表列出了发音评估的结果参数。

| 参数 | 说明 |
|-----------|-------------|
| `AccuracyScore` | 语音的发音准确度。 准确度表示音素与母语人士发音的接近程度。 单词级别和全文级别的准确度分数由音素级别的准确度分数聚合而来。 |
| `FluencyScore` | 给定语音的流畅度。 流畅度表示语音与母语人士在单词之间使用无声停顿的接近程度。 |
| `CompletenessScore` | 语音的完整性，通过计算发音单词与参考文本输入的比率来确定。 |
| `PronunciationScore` | 总分，表示给定语音的发音质量。 此分数按权重从 `AccuracyScore`、`FluencyScore` 和 `CompletenessScore` 聚合而成。 |
| `ErrorType` | 此值指示与 `ReferenceText` 相比，是省略、插入还是错误读出字词。 可能的值为 `None`（表示此词没有错误）、`Omission`、`Insertion` 和 `Mispronunciation`。 |

### <a name="sample-responses"></a>示例响应

以 JSON 格式显示的典型发音评估结果：

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
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤

<!-- TODO: update JavaScript sample links after release -->

* 观看有关发音评估的[视频简介](https://www.youtube.com/watch?v=cBE8CUHOFHQ)和[视频教程](https://www.youtube.com/watch?v=zFlwm7N4Awc)

* 试用[发音评估演示](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)

::: zone pivot="programming-language-csharp"
* 有关发音评估，请参阅 GitHub 上的[示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949)。
::: zone-end

::: zone pivot="programming-language-cpp"
* 有关发音评估，请参阅 GitHub 上的[示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633)。
::: zone-end

::: zone pivot="programming-language-java"
* 有关发音评估，请参阅 GitHub 上的[示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697)。
::: zone-end

::: zone pivot="programming-language-python"
* 有关发音评估，请参阅 GitHub 上的[示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576)。
::: zone-end

::: zone pivot="programming-language-objectivec"
* 有关发音评估，请参阅 GitHub 上的[示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642)。
::: zone-end

* [语音 SDK 参考文档](speech-sdk.md)

* [创建免费 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)
