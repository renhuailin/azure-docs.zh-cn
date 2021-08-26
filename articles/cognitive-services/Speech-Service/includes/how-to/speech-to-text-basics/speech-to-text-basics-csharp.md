---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d8d39b0e023d2ffa7bf186d9cc106b9c2152e56
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071151"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [C# 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

如果只想开始使用包名称，请在 NuGet 控制台中运行 `Install-Package Microsoft.CognitiveServices.Speech`。

有关特定于平台的安装说明，请参阅以下链接：

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)。 此类包含有关订阅的信息，例如密钥和关联的位置/区域、终结点、主机或授权令牌。 使用密钥和位置/区域创建 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    }
}
```

可以通过以下其他几种方法初始化 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)：

* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域/位置。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

## <a name="recognize-from-microphone"></a>从麦克风识别

若要使用设备麦克风识别语音，需使用 `FromDefaultMicrophoneInput()` 创建 `AudioConfig`。 然后初始化 [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer)，传递 `audioConfig` 和 `speechConfig`。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
        await FromMic(speechConfig);
    }
}
```

如果你想使用特定的音频输入设备，则需要在 `AudioConfig` 中指定设备 ID。 [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>从文件识别

如果要从音频文件（而不是麦克风）识别语音，则仍需要创建 `AudioConfig`。 但创建 [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) 时，需要调用 `FromWavFileInput()`（而不是调用 `FromDefaultMicrophoneInput()`）并传递文件路径。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>从内存中流识别

对于许多用例，你的音频数据可能来自 Blob 存储，或者已经作为 `byte[]` 或类似的原始数据结构存在于内存中。 以下示例使用 [`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream) 来识别语音，语音本质上是抽象的内存流。 该示例代码执行下列操作：

* 使用接受 `byte[]` 的 `Write()` 函数将原始音频数据 (PCM) 写入 `PushAudioInputStream`。
* 为了演示目的，请使用 `FileReader` 读取 `.wav` 文件，但如果你已经在 `byte[]` 中拥有音频数据，则可以直接跳过此步骤，将内容写入输入流。
* 默认格式是 16 位 16khz 单声道 PCM。 若要自定义格式，可以使用静态函数 `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)` 将 [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat) 对象传递给 `CreatePushStream()`。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
        await FromStream(speechConfig);
    }
}
```

使用推送流作为输入假定音频数据是原始 PCM，例如，跳过任何标头。
如果未跳过标头，API 在某些情况下仍可正常运行，但为获得最佳结果，请考虑实现读取标头的逻辑，确保 `byte[]` 以 <start of the audio data> 开头。

## <a name="error-handling"></a>错误处理。

前面的示例只从 `result.text` 获取已识别的文本，但要处理错误和其他响应，需要编写一些代码来处理结果。 以下代码评估 [`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason) 属性并：

* 输出识别结果：`ResultReason.RecognizedSpeech`
* 如果没有识别匹配项，请通知用户：`ResultReason.NoMatch`
* 如果遇到错误，则输出错误消息：`ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the speech key and location/region info?");
        }
        break;
}
```

## <a name="continuous-recognition"></a>连续识别

前面的示例使用单步识别，可识别单个言语。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。

与此相反，当你想控制何时停止识别时，需要使用连续识别。 它要求你订阅 `Recognizing`、`Recognized` 和 `Canceled` 事件以获取识别结果。 若要停止识别，必须调用 [`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)。 下面是有关如何对音频输入文件执行连续识别的示例。

首先定义输入并初始化 [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer)：

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

然后，创建 `TaskCompletionSource<int>` 来管理语音识别的状态。

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

接下来，我们将订阅从 [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) 发送的事件。

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing)：事件信号，包含中间识别结果。
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized)：事件信号，包含最终识别结果（指示成功的识别尝试）。
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped)：事件信号，指示识别会话的结束（操作）。
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled)：事件信号，包含已取消的识别结果（指示因直接取消请求或者传输或协议失败导致的识别尝试取消）。

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the speech key and location/region info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

设置所有项后，调用 `StartContinuousRecognitionAsync` 开始识别。

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>听写模式

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

若要启用听写模式，请在 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) 上使用 [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation) 方法。

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>更改源语言

语音识别的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中找到 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)，并直接在其下方添加此行。

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage) 属性需要语言区域设置格式字符串。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中“区域设置”列中的任何值  。

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

若要使用短语列表，请首先创建一个 [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) 对象，然后使用 [`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase) 添加特定的单词和短语。

对 [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) 所做的任何更改都将在下一次识别或重新连接到语音服务之后生效。

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

如果需要清除短语列表： 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>提高识别精确度的其他方式

短语列表只是提高识别准确度的一种方式。 也可执行以下操作： 

* [使用自定义语音识别提高准确性](../../../custom-speech-overview.md)
* [使用租户模型提高准确性](../../../tutorial-tenant-model.md)
