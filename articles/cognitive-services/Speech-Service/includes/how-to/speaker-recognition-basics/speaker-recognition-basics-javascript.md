---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: a651fe0ddb25fb44de518c794264133f16879982
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378169"
---
本快速入门介绍使用语音 SDK 进行说话人识别的基本设计模式，其中包括：

* 依赖于文本和独立于文本的验证
* 用于识别一组语音中的语音样本的说话人识别
* 删除语音配置文件

若要深入了解语音识别概念，请参阅[概述](../../../speaker-recognition-overview.md)一文。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [JavaScript 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 目前仅 `westus` 区域中创建的 Azure 语音资源支持说话人识别。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript 语音 SDK</a>，然后才能执行操作。 根据你的平台，使用以下说明：

- [Node.js](../../../speech-sdk.md?tabs=nodejs#get-the-speech-sdk)
- [Web 浏览器](../../../speech-sdk.md?tabs=browser#get-the-speech-sdk)

另外，请根据目标环境使用以下项之一：

# <a name="script"></a>[script](#tab/script)

下载并提取 <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript 语音 SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 文件，将其置于可供 HTML 文件访问的文件夹中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果以 Web 浏览器为目标并使用 `<script>` 标记，则不需 `sdk` 前缀。 `sdk` 前缀是一个别名，用于为 `require` 模块命名。

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

有关 `import` 的详细信息，请参阅 <a href="https://javascript.info/import-export" target="_blank">export 和 import </a>。

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

有关 `require` 的详细信息，请参阅<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什么是 require？</a>。

---

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在 .js 文件的最前面添加以下语句。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

这些语句将导入所需的库，并从环境变量中获取语音服务订阅密钥和区域。 它们还指定了将在以下任务中使用的音频文件的路径。

## <a name="create-helper-function"></a>创建 helper 函数

添加以下 helper 函数，以将音频文件读入流以供语音服务使用。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

在此函数中，使用 [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) 和 [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) 方法创建 [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) 对象。 此 `AudioConfig` 对象表示音频流。 在以下任务中，你将使用其中几个 `AudioConfig` 对象。

## <a name="text-dependent-verification"></a>依赖于文本的验证

说话人验证是确认说话人与已知或已注册的语音匹配的操作。 第一步是注册语音配置文件，以便该服务有可与将来的语音样本进行比较的内容。 在此示例中，使用依赖于文本的策略注册配置文件，该策略需要用于注册和验证的特定密码。 有关支持的密码的列表，请参阅[参考文档](/rest/api/speakerrecognition/)。

### <a name="textdependentverification-function"></a>TextDependentVerification 函数

请先创建 `TextDependentVerification` 函数。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

此函数使用 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 方法创建 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 对象。 请注意，有三种[类型](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype)的 `VoiceProfile`：

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

在本例中，你将 `VoiceProfileType.TextDependentVerification` 传递到 `VoiceProfileClient.createProfileAsync`。

然后调用接下来要定义的两个 helper 函数 `AddEnrollmentsToTextDependentProfile` 和 `SpeakerVerify`。 最后，调用 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) 以删除配置文件。

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 函数

定义以下函数以注册语音配置文件。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

在此函数中，调用之前定义的 `GetAudioConfigFromFile` 函数，以从音频示例创建 `AudioConfig` 对象。 这些音频示例包含一个密码，例如“我的语音是我的通行证，请验证我。” 然后，使用 [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) 方法注册这些音频示例。

### <a name="speakerverify-function"></a>SpeakerVerify 函数

按如下所示定义 `SpeakerVerify`。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

在此函数中，使用 [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-) 方法创建 [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) 对象，从而传入之前创建的 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 对象。

接下来，调用 [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) 方法，验证包含与之前注册的音频示例相同的密码的音频样本。 `SpeechRecognizer.recognizeOnceAsync` 返回一个 [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) 对象，其 `score` 属性包含介于 0.0-1.0 之间的相似性分数。 `SpeakerRecognitionResult` 对象还包含类型 [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason) 的 `reason` 属性。 如果验证成功，则 `reason` 属性的值应为 `RecognizedSpeaker`。

## <a name="text-independent-verification"></a>独立于文本的验证

与依赖于文本的验证不同，独立于文本的验证 ：

* 不需要说出特定的密码，可以说任何内容
* 不需要三个音频样本，但需要总共 20 秒的音频

### <a name="textindependentverification-function"></a>TextIndependentVerification 函数

请先创建 `TextIndependentVerification` 函数。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

与 `TextDependentVerification` 函数一样，此函数使用 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 方法创建 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 对象。

在本例中，你将 `VoiceProfileType.TextIndependentVerification` 传递到 `createProfileAsync`。

然后，调用两个 helper 函数：`AddEnrollmentsToTextIndependentProfile`（将在下一步定义）和 `SpeakerVerify`（已定义）。 最后，调用 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) 以删除配置文件。

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

定义以下函数以注册语音配置文件。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

在此函数中，调用之前定义的 `GetAudioConfigFromFile` 函数，以从音频示例创建 `AudioConfig` 对象。 然后，使用 [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) 方法注册这些音频示例。

## <a name="speaker-identification"></a>说话人识别

说话人识别用于确定谁正在一组给定的注册语音中说话。 此过程与独立于文本的验证相似，主要区别在于前者能够一次针对多个语音配置文件进行验证，而不是针对单个配置文件进行验证。

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 函数

请先创建 `TextIndependentIdentification` 函数。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

与 `TextDependentVerification` 和 `TextIndependentVerification` 函数一样，此函数使用 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 方法创建 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 对象。

在本例中，你将 `VoiceProfileType.TextIndependentIdentification` 传递到 `VoiceProfileClient.createProfileAsync`。

然后，调用两个 helper 函数：`AddEnrollmentsToTextIndependentProfile`（已定义）和 `SpeakerIdentify`（将在下一步定义）。 最后，调用 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) 以删除配置文件。

### <a name="speakeridentify-function"></a>SpeakerIdentify 函数

按如下所示定义 `SpeakerIdentify` 函数。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

在此函数中，使用 [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---) 方法创建 [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) 对象，从而传入之前创建的 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 对象。

接下来，调用 [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) 方法并传入音频示例。
`SpeechRecognizer.recognizeOnceAsync` 尝试根据用于创建 `SpeakerIdentificationModel` 的 `VoiceProfile` 对象来标识此音频示例的语音。 它返回一个 [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) 对象，该对象的 `profileId` 属性标识匹配的 `VoiceProfile`（如果有），而 `score` 属性包含介于 0.0-1.0 之间的相似性分数。

## <a name="main-function"></a>main 函数

最后，按如下所示定义 `main` 函数。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

此函数创建一个 [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient) 对象，该对象用于创建、注册和删除语音配置文件。 然后，它调用你之前定义的函数。