---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: lajanuar
ms.openlocfilehash: dbbc946b775538b9a39a80d31edbe49ceee7f5e1
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066656"
---
首先，使用 `FromFile()` 静态函数加载关键字模型文件，该函数将返回一个 `KeywordRecognitionModel`。 使用从 Speech Studio 下载的 `.table` 文件的路径。 此外，使用默认麦克风创建一个 `AudioConfig`，然后使用音频配置实例化一个新的 `KeywordRecognizer`。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

接下来，通过传递模型对象调用 `RecognizeOnceAsync()` 来运行关键字识别。 这会启动一个关键字识别会话，此会话将一直持续到关键字被识别为止。 因此，你通常在多线程应用程序中使用此设计模式，或者在可能无限期等待唤醒词的用例中使用此设计模式。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 此处显示的示例使用本地关键字识别，因为它不需要将 `SpeechConfig` 对象用于身份验证上下文，也不会与后端联系。 但是，可以[使用直接后端连接](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)来运行关键字识别和验证。

### <a name="continuous-recognition"></a>连续识别

语音 SDK 中的其他类通过关键字识别支持连续识别（用于语音和意向识别）。 这使你可以使用通常用于连续识别的那些代码，并能够为关键字模型引用 `.table` 文件。

对于语音转文本，请按照[快速入门](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition)中所示的设计模式来设置连续识别。 然后，将对 `recognizer.StartContinuousRecognitionAsync()` 的调用替换为对 `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` 的调用，并传递 `KeywordRecognitionModel` 对象。 若要停止通过关键字识别进行连续识别，请使用 `recognizer.StopKeywordRecognitionAsync()` 而不是 `recognizer.StopContinuousRecognitionAsync()`。

意向识别对 [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) 和 [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) 函数使用相同的模式。
