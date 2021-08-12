---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: f4f5d64dc68644fa29d687992017180050ed4c07
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113279942"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [C++ 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，使用以下说明：

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)。 此类包含有关订阅的信息，例如密钥和关联的位置/区域、终结点、主机或授权令牌。 通过使用密钥和区域创建 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

可以通过以下其他几种方法初始化 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)：

* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

## <a name="recognize-from-microphone"></a>从麦克风识别

若要使用设备麦克风识别语音，需使用 `FromDefaultMicrophoneInput()` 创建 `AudioConfig`。 然后初始化 [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer)，传递 `audioConfig` 和 `config`。

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

如果你想使用特定的音频输入设备，则需要在 `AudioConfig` 中指定设备 ID。 [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>从文件识别

如果要从音频文件（而不是使用麦克风）识别语音，则仍需要创建 `AudioConfig`。 但创建 [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) 时，需要调用 `FromWavFileInput()`（而不是调用 `FromDefaultMicrophoneInput()`）并传递文件路径。

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>识别语音

用于 C++ 的语音 SDK 的[识别器类](/cpp/cognitive-services/speech/speechrecognizer)公开了一些可用于语音识别的方法。

### <a name="single-shot-recognition"></a>单步识别

单步识别可异步识别单个言语。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。 下面是使用 [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) 进行异步单步识别的示例：

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

需要编写一些代码来处理结果。 此示例计算 [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason)：

* 输出识别结果：`ResultReason::RecognizedSpeech`
* 如果没有识别匹配项，请通知用户：`ResultReason::NoMatch`
* 如果遇到错误，则输出错误消息：`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the speech key and location/region info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>连续识别

连续识别涉及的方面比单步识别多一点。 它要求你订阅 `Recognizing`、`Recognized` 和 `Canceled` 事件以获取识别结果。 若要停止识别，必须调用 [StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)。 下面是有关如何对音频输入文件执行连续识别的示例。

首先，我们将定义输入并初始化一个 [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer)：

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

接下来，让我们创建一个变量来管理语音识别的状态。 首先，我们将声明 `promise<void>`，因为在开始识别时，我们可以放心地假定该操作尚未完成。

```cpp
promise<void> recognitionEnd;
```

我们将订阅从 [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) 发送的事件。

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing)：事件信号，包含中间识别结果。
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized)：事件信号，包含最终识别结果（指示成功的识别尝试）。
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped)：事件信号，指示识别会话的结束（操作）。
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled)：事件信号，包含已取消的识别结果（指示因直接取消请求或者传输或协议失败导致的识别尝试取消）。

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the speech key and location/region info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

完成所有设置后，可以调用 [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)。

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>听写模式

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

若要启用听写模式，请在 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) 上使用 [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation) 方法。

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>更改源语言

语音识别的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为德语。 在代码中找到 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)，并直接在其下方添加此行。

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) 是采用字符串作为实参的形参。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中的任何值。

## <a name="improve-recognition-accuracy"></a>提高识别准确度

短语列表用于标识音频数据中的已知短语，如人的姓名或特定位置。 通过提供短语列表，可以提高语音识别的准确性。

例如，如果命令为“Move to”，可能的目标为可以说出来的“Ward”，则可添加条目“Move to Ward”。 添加短语会增加将音频中的“Move toward”（移向）识别为“Move to Ward”（移到病房）的概率

可以将单个词或完整短语添加到短语列表。 在识别过程中，会使用短语列表中的某个条目进一步识别列表中的单词和短语，即使条目出现在言语的中间位置。 

> [!IMPORTANT]
> “短语列表”功能适用于以下语言：en-US、de-DE、en-AU、en-CA、en-GB、en-IN、es-ES、fr-FR、it-IT、ja-JP、pt-BR、zh-CN
>
> 应在短语数量只有几百的情况下使用短语列表功能。 如果列表较大或语言当前不受支持，则[训练自定义模型](../../../custom-speech-overview.md)可能是提高准确性的更好选择。
>
> 不要对自定义终结点使用“短语列表”功能。 而是训练包含短语的自定义模型。

若要使用短语列表，请首先创建一个 [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) 对象，然后使用 [`AddPhrase`](/cpp/cognitive-services/speech/phraselistgrammar#addphrase) 添加特定的单词和短语。

对 [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) 所做的任何更改都将在下一次识别或重新连接到语音服务之后生效。

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

如果需要清除短语列表： 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>提高识别精确度的其他方式

短语列表只是提高识别准确度的一种方式。 也可执行以下操作： 

* [使用自定义语音识别提高准确性](../../../custom-speech-overview.md)
* [使用租户模型提高准确性](../../../tutorial-tenant-model.md)
