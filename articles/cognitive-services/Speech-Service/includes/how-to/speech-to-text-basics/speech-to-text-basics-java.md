---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 1f588231ad903730826b8235a52983aee60cf8f1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378062"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [Java 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，使用以下说明：

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java 运行时 </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Android </a>

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)。 此类包含有关订阅的信息，例如密钥和关联的位置/区域、终结点、主机或授权令牌。 使用密钥和位置/区域创建 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

可以通过以下其他几种方法初始化 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)：

* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

## <a name="recognize-from-microphone"></a>从麦克风识别

若要使用设备麦克风识别语音，需使用 `fromDefaultMicrophoneInput()` 创建 `AudioConfig`。 然后初始化 [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer)，传递 `audioConfig` 和 `config`。

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

如果你想使用特定的音频输入设备，则需要在 `AudioConfig` 中指定设备 ID。 [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>从文件识别

如果要从音频文件（而不是使用麦克风）识别语音，则仍需要创建 `AudioConfig`。 但创建 [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) 时，需要调用 `fromWavFileInput()`（而不是调用 `fromDefaultMicrophoneInput()`）并传递文件路径。

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>错误处理

前面的示例只使用 `result.getText()` 获取已识别的文本，但要处理错误和其他响应，需要编写一些代码来处理结果。 以下示例计算 [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.recognitionresult.getreason) 和：

* 输出识别结果：`ResultReason.RecognizedSpeech`
* 如果没有识别匹配项，请通知用户：`ResultReason.NoMatch`
* 如果遇到错误，则输出错误消息：`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>连续识别

前面的示例使用单步识别，可识别单个言语。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。

与此相反，当你想控制何时停止识别时，需要使用连续识别。 它要求你订阅 `recognizing`、`recognized` 和 `canceled` 事件以获取识别结果。 若要停止识别，必须调用 [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)。 下面是有关如何对音频输入文件执行连续识别的示例。

首先，我们将定义输入并初始化一个 [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer)：

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

接下来，让我们创建一个变量来管理语音识别的状态。 首先，我们将在类范围内声明一个 `Semaphore`。

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

我们将订阅从 [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) 发送的事件。

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing)：事件信号，包含中间识别结果。
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized)：事件信号，包含最终识别结果（指示成功的识别尝试）。
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped)：事件信号，指示识别会话的结束（操作）。
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled)：事件信号，包含已取消的识别结果（指示因直接取消请求或者传输或协议失败导致的识别尝试取消）。

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

完成所有设置后，可以调用 [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync)。

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>听写模式

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

若要启用听写模式，请在 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) 上使用 [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation) 方法。

```java
config.enableDictation();
```

## <a name="change-source-language"></a>更改源语言

语音识别的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为法语。 在代码中找到 [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)，并直接在其下方添加此行。

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage) 是采用字符串作为实参的形参。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中的任何值。

## <a name="improve-recognition-accuracy"></a>提高识别准确度

短语列表用于标识音频数据中的已知短语，如人的姓名或特定位置。 通过提供短语列表，可以提高语音识别的准确性。

例如，如果命令为“Move to”，可能的目标为可以说出来的“Ward”，则可添加条目“Move to Ward”。 添加短语会增加将音频中的“Move toward”（移向）识别为“Move to Ward”（移到病房）的概率

可以将单个词或完整短语添加到短语列表。 在识别过程中，会使用短语列表中的某个条目进一步识别列表中的单词和短语，即使条目出现在言语的中间位置。 

> [!IMPORTANT]
> “短语列表”功能适用于以下语言：en-US、de-DE、en-AU、en-CA、en-GB、en-IN、es-ES、fr-FR、it-IT、ja-JP、pt-BR、zh-CN
>
> 应在短语数量只有几百的情况下使用短语列表功能。 如果列表较大或语言当前不受支持，则[训练自定义模型](../../../custom-speech-overview.md)可能是提高准确性的更好选择。
>
> 自定义终结点不支持“短语列表”功能。 请不要将其用于自定义终结点。 而是训练包含短语的自定义模型。

若要使用短语列表，请首先创建一个 [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) 对象，然后使用 [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_) 添加特定的单词和短语。

对 [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) 所做的任何更改都将在下一次识别或重新连接到语音服务之后生效。

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

如果需要清除短语列表： 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>提高识别精确度的其他方式

短语列表只是提高识别准确度的一种方式。 也可执行以下操作： 

* [使用自定义语音识别提高准确性](../../../custom-speech-overview.md)
* [使用租户模型提高准确性](../../../tutorial-tenant-model.md)
