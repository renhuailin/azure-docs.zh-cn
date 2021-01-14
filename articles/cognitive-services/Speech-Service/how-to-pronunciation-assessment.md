---
title: 如何使用语音 SDK 进行发音评估
titleSuffix: Azure Cognitive Services
description: 语音 SDK 支持 "发音评估"，用于评估语音输入的发音质量、准确性、熟练、完整性等指标。
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
ms.openlocfilehash: 8602d43113f4ce21cdb430e1fa3e83f006c64753
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185557"
---
# <a name="pronunciation-assessment"></a>发音评估

发音评估会评估语音发音，并向演讲者提供有关讲述音频准确性和熟练的反馈。
通过发音评估，语言学习者可以练习、获得即时反馈并改进其发音，因此能够自信地讲话和演示。
教师可以使用此功能来实时评估多个说话人的发音。

在本文中，你将了解如何 `PronunciationAssessmentConfig` 使用 SPEECH SDK 设置和检索 `PronunciationAssessmentResult` 。

> [!NOTE]
> 发音评估功能当前仅在区域 `westus` 、和中可用 `eastasia` ， `centralindia` 并且仅支持语言 `en-US` 。

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>语音 SDK 的发音评估

在下面的示例中，你将创建一个 `PronunciationAssessmentConfig` ，然后将其应用于 `SpeechRecognizer` 。

以下代码片段演示了如何在应用中使用自动语言检测：

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
        var pronunciationScore = pronResult.pronunciationScore;
        var wordLevelResult = pronResult.detailResult.Words;
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

下表列出了用于发音评估的配置参数。

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

## <a name="next-steps"></a>后续步骤

<!-- TODO: update JavaScript sample links after release -->

::: zone pivot="programming-language-csharp"
* 请参阅 GitHub 上的 [示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) 以获得发音评估。
::: zone-end

::: zone pivot="programming-language-cpp"
* 请参阅 GitHub 上的 [示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) 以获得发音评估。
::: zone-end

::: zone pivot="programming-language-java"
* 请参阅 GitHub 上的 [示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) 以获得发音评估。
::: zone-end

::: zone pivot="programming-language-python"
* 请参阅 GitHub 上的 [示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) 以获得发音评估。
::: zone-end

::: zone pivot="programming-language-objectivec"
* 请参阅 GitHub 上的 [示例代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) 以获得发音评估。
::: zone-end

* [语音 SDK 参考文档](speech-sdk.md)
