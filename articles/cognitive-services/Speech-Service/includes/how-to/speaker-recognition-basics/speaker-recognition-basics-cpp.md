---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 655c5d80f1fb4e047a3cd9cc751a853815a70a68
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114671168"
---
本快速入门介绍使用语音 SDK 进行说话人识别的基本设计模式，其中包括：

* 依赖于文本和独立于文本的验证
* 用于识别一组语音中的语音样本的说话人识别
* 删除语音配置文件

若要深入了解语音识别概念，请参阅[概述](../../../speaker-recognition-overview.md)一文。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [C++ 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 目前仅 `westus` 区域中创建的 Azure 语音资源支持说话人识别。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，使用以下说明：

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在 .cpp 文件的最前面添加以下语句。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)。 此类包含有关你的订阅的信息，例如你的密钥和关联的区域、终结点、主机或授权令牌。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>依赖于文本的验证

说话人验证是确认说话人与已知或已注册的语音匹配的操作。 第一步是注册语音配置文件，以便该服务有可与将来的语音样本进行比较的内容。 在此示例中，使用依赖于文本的策略注册配置文件，该策略需要用于注册和验证的特定密码。 有关支持的密码的列表，请参阅[参考文档](/rest/api/speakerrecognition/)。

### <a name="textdependentverification-function"></a>TextDependentVerification 函数

请先创建 `TextDependentVerification` 函数。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

此函数使用 [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) 方法创建 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 对象。 请注意，有三种[类型](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype)的 `VoiceProfile`：

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

在本例中，你将 `VoiceProfileType::TextDependentVerification` 传递到 `CreateProfileAsync`。

然后调用接下来要定义的两个 helper 函数 `AddEnrollmentsToTextDependentProfile` 和 `SpeakerVerify`。 最后，调用 [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync)，以清理该配置文件。

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 函数

定义以下函数以注册语音配置文件。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

在此函数中，注册 `while` 循环中的音频示例，该循环跟踪注册所必需的剩余示例数。 在每次迭代中，[EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync) 都会提示你对着麦克风说出密码，并将该示例添加到语音配置文件。

### <a name="speakerverify-function"></a>SpeakerVerify 函数

按如下所示定义 `SpeakerVerify`。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

在此函数中，使用 [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speaker-speakerverificationmodel#fromprofile) 方法创建 [SpeakerVerificationModel](/cpp/cognitive-services/speech/speaker-speakerverificationmodel) 对象，从而传入之前创建的 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 对象。

接下来，[SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) 提示你再次说出密码，但这次将针对语音配置文件进行验证，并返回介于 0.0-1.0 之间的相似性分数。 [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speaker-speakerrecognitionresult) 对象还会根据密码是否匹配返回 `Accept` 或 `Reject`。

## <a name="text-independent-verification"></a>独立于文本的验证

与依赖于文本的验证不同，独立于文本的验证 ：

* 不需要说出特定的密码，可以说任何内容
* 不需要三个音频样本，但需要总共 20 秒的音频

### <a name="textindependentverification-function"></a>TextIndependentVerification 函数

请先创建 `TextIndependentVerification` 函数。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

与 `TextDependentVerification` 函数一样，此函数使用 [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) 方法创建 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 对象。

在本例中，你将 `VoiceProfileType::TextIndependentVerification` 传递到 `CreateProfileAsync`。

然后，调用两个 helper 函数：`AddEnrollmentsToTextIndependentProfile`（将在下一步定义）和 `SpeakerVerify`（已定义）。 最后，调用 [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync)，以清理该配置文件。

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

定义以下函数以注册语音配置文件。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

在此函数中，注册 `while` 循环中的音频示例，该循环跟踪注册所必需的剩余音频秒数。 在每次迭代中，[EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync) 都会提示你对着麦克风讲话，并将该示例添加到语音配置文件。

## <a name="speaker-identification"></a>说话人识别

说话人识别用于确定谁正在一组给定的注册语音中说话。 此过程与独立于文本的验证非常相似，主要区别在于前者能够一次针对多个语音配置文件进行验证，而不是针对单个配置文件进行验证。

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 函数

请先创建 `TextIndependentIdentification` 函数。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

与 `TextDependentVerification` 和 `TextIndependentVerification` 函数一样，此函数使用 [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) 方法创建 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 对象。

在本例中，你将 `VoiceProfileType::TextIndependentIdentification` 传递到 `CreateProfileAsync`。

然后，调用两个 helper 函数：`AddEnrollmentsToTextIndependentProfile`（已定义）和 `SpeakerIdentify`（将在下一步定义）。 最后，调用 [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync)，以清理该配置文件。

### <a name="speakeridentify-function"></a>SpeakerIdentify 函数

按如下所示定义 `SpeakerIdentify` 函数。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

在此函数中，使用 [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel#fromprofiles) 方法创建 [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel) 对象。 `SpeakerIdentificationModel::FromProfiles` 接受 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 对象的列表。 在本例中，只需传入前面创建的 `VoiceProfile` 对象即可。 但是，如果需要，可以传入多个 `VoiceProfile` 对象，每个对象都注册了来自不同语音的音频示例。

接下来，[SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) 将提示你再次讲话。 这次，它会将你的语音与已注册的语音配置文件进行比较，并返回最相似的语音配置文件。

## <a name="main-function"></a>main 函数

最后，按如下所示定义 `main` 函数。

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

此函数只调用你之前定义的函数。 但首先，它会创建一个 [VoiceProfileClient](/cpp/cognitive-services/speech/speaker-voiceprofileclient) 对象和一个 [SpeakerRecognizer](/cpp/cognitive-services/speech/speaker-speakerrecognizer) 对象。

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient` 用于创建、注册和删除语音配置文件。 `SpeakerRecognizer` 用于针对一个或多个已注册的语音配置文件验证语音样本。

## <a name="changing-audio-input-type"></a>更改音频输入类型

本文中的示例使用默认设备麦克风作为音频示例的输入。 但是，在需要使用音频文件而不是麦克风输入的情况下，只需更改以下行：

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

to:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

或者将 `audio_config` 的任何用途替换为 [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput)。 还可以具有混合输入，例如，使用麦克风进行注册，使用文件进行验证。
