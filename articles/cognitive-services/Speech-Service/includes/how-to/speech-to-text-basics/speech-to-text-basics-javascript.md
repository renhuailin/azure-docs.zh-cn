---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: b652b2c7b3f1fd14e401381b5a83a5cf4a987fde
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378269"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [JavaScript 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node)。

或者，请参阅[响应示例](https://github.com/Azure-Samples/AzureSpeechReactSample)，以了解如何在基于浏览器的环境中使用语音 SDK。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装适用于 Node.js 的语音 SDK，然后才能执行其他操作。 如果只是需要用于安装的包名称，请运行 `npm install microsoft-cognitiveservices-speech-sdk`。 有关引导式安装说明，请参阅[入门](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs)一文。

使用以下 `require` 语句导入 SDK。

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

有关 `require` 的详细信息，请参阅 [require 文档](https://nodejs.org/en/knowledge/getting-started/what-is-require/)。

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)。 此类包含有关订阅的信息，例如密钥和关联的位置/区域、终结点、主机或授权令牌。 使用密钥和位置/区域创建 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

可以通过以下其他几种方法初始化 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)：

* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的位置/区域。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

## <a name="recognize-from-microphone-browser-only"></a>从麦克风识别（仅限浏览器）

识别来自麦克风的语音 **在 Node.js 中不受支持**，仅在基于浏览器的 JavaScript 环境中受支持。 请参阅 GitHub 上的 [响应示例](https://github.com/Azure-Samples/AzureSpeechReactSample)，以了解如何[实现将来自麦克风的语音转换为文本](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29)。

> [!NOTE]
> 如果你想使用特定的音频输入设备，则需要在 `AudioConfig` 中指定设备 ID。 [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>从文件识别 

要从音频文件识别语音，请使用 `fromWavFileInput()` 创建一个接受 `Buffer` 对象的 `AudioConfig`。 然后初始化 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer)，传递 `audioConfig` 和 `speechConfig`。

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>从内存中流识别

对于许多用例，你的音频数据可能来自 Blob 存储，或者已经作为 [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 或类似的原始数据结构存在于内存中。 下面的代码：

* 使用 `createPushStream()` 创建推送流。
* 出于演示目的，请使用 `fs.createReadStream` 读取 `.wav` 文件，但如果你已经在 `ArrayBuffer` 中拥有音频数据，则可以直接跳过此步骤，将内容写入输入流。
* 使用推送流创建音频配置。

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

使用推送流作为输入假定音频数据是原始 PCM，例如，跳过任何标头。
如果未跳过标头，API 在某些情况下仍可正常运行，但为获得最佳结果，请考虑实现读取标头的逻辑，确保 `fs` 以 <start of the audio data> 开头。

## <a name="error-handling"></a>错误处理。

前面的示例只从 `result.text` 获取已识别的文本，但要处理错误和其他响应，需要编写一些代码来处理结果。 以下代码评估 [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) 属性并：

* 输出识别结果：`ResultReason.RecognizedSpeech`
* 如果没有识别匹配项，请通知用户：`ResultReason.NoMatch`
* 如果遇到错误，则输出错误消息：`ResultReason.Canceled`

```javascript
switch (result.reason) {
    case sdk.ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case sdk.ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case sdk.ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == sdk.CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the key and location/region info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>连续识别

前面的示例使用单步识别，可识别单个言语。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。

与此相反，当你想控制何时停止识别时，需要使用连续识别。 它要求你订阅 `Recognizing`、`Recognized` 和 `Canceled` 事件以获取识别结果。 若要停止识别，必须调用 [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync)。 下面是有关如何对音频输入文件执行连续识别的示例。

首先定义输入并初始化 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer)：

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

接下来，我们将订阅从 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) 发送的事件。

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing)：事件信号，包含中间识别结果。
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized)：事件信号，包含最终识别结果（指示成功的识别尝试）。
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped)：事件信号，指示识别会话的结束（操作）。
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled)：事件信号，包含已取消的识别结果（指示因直接取消请求或者传输或协议失败导致的识别尝试取消）。

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == sdk.ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == sdk.ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == sdk.CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the key and location/region info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

设置所有项后，调用 [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) 开始识别。

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>听写模式

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

若要启用听写模式，请在 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) 上使用 [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) 方法。

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>更改源语言

语音识别的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中找到 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)，并直接在其下方添加此行。

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) 属性需要语言区域设置格式字符串。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中“区域设置”列中的任何值  。

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

若要使用短语列表，请首先创建一个 [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) 对象，然后使用 [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) 添加特定的单词和短语。

对 [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) 所做的任何更改都将在下一次识别或重新连接到语音服务之后生效。

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

如果需要清除短语列表：

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>提高识别精确度的其他方式

短语列表只是提高识别准确度的一种方式。 也可执行以下操作： 

* [使用自定义语音识别提高准确性](../../../custom-speech-overview.md)
* [使用租户模型提高准确性](../../../tutorial-tenant-model.md)
