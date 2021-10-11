---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: pafarley
ms.openlocfilehash: 797d2cc40dfc10de1ce8c8d6bdb623eff56a375f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378205"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [Python 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)。

## <a name="prerequisites"></a>先决条件

本文假设：

* 你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-and-import-the-speech-sdk"></a>安装和导入语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。

```Python
pip install azure-cognitiveservices-speech
```

如果使用的是 macOS 且你遇到安装问题，则可能需要先运行此命令。

```Python
python3 -m pip install --upgrade pip
```

安装语音 SDK 后，将其导入到 Python 项目中。

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)。 此类包含有关订阅的信息，例如密钥和关联的位置/区域、终结点、主机或授权令牌。 使用密钥和位置/区域创建 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
```

可以通过以下其他几种方法初始化 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)：

* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

## <a name="recognize-from-microphone"></a>从麦克风识别

若要使用设备麦克风识别语音，只需创建 `SpeechRecognizer`（无需传递 `AudioConfig`），并传递 `speech_config`。

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

如果你想使用特定的音频输入设备，则需要在 `AudioConfig` 中指定设备 ID，并将其传递给 `SpeechRecognizer` 构造函数的 `audio_config` 参数。 [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

## <a name="recognize-from-file"></a>从文件识别

如果要从音频文件（而不是使用麦克风）识别语音，请创建 [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig) 并使用 `filename` 参数。

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>错误处理。

前面的示例只从 `result.text` 获取已识别的文本，但要处理错误和其他响应，需要编写一些代码来处理结果。 以下代码评估 [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason) 属性并：

* 输出识别结果：`speechsdk.ResultReason.RecognizedSpeech`
* 如果没有识别匹配项，请通知用户：`speechsdk.ResultReason.NoMatch`
* 如果遇到错误，则输出错误消息：`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>连续识别

前面的示例使用单步识别，可识别单个言语。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。

与此相反，当你想控制何时停止识别时，需要使用连续识别。 它要求你连接到 `EventSignal` 以获取识别结果，如果要停止识别，须调用 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) 或 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition-async--)。 下面是有关如何对音频输入文件执行连续识别的示例。

首先，我们将定义输入并初始化一个 [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer)：

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

接下来，让我们创建一个变量来管理语音识别的状态。 首先，我们将此设置为“`False`”，因为在开始识别时，我们可以放心地假定该操作尚未完成。

```Python
done = False
```

现在，我们将创建一个回叫，以在接收到 `evt` 时停止连续识别。 需谨记以下几点。

* 接收到 `evt` 时，系统将输出 `evt` 消息。
* 接收到 `evt` 后，系统将调用 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) 来停止识别。
* 识别状态将更改为 `True`。

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

此代码示例演示如何将回叫连接到从 [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#start-continuous-recognition--) 发送的事件。

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognizing)：事件信号，包含中间识别结果。
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognized)：事件信号，包含最终识别结果（指示成功的识别尝试）。
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-started)：事件信号，指示识别会话的开始（操作）。
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped)：事件信号，指示识别会话的结束（操作）。
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#canceled)：事件信号，包含已取消的识别结果（指示因直接取消请求或者传输或协议失败导致的识别尝试取消）。

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

完成所有设置后，可以调用 [start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped)。

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>听写模式

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

若要启用听写模式，请在 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) 上使用 [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#enable-dictation--) 方法。

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>更改源语言

语音识别的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为德语。 在代码中找到 SpeechConfig，并直接在其下方添加此行。

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-recognition-language) 是采用字符串作为实参的形参。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中的任何值。

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

若要使用短语列表，请首先创建一个 [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) 对象，然后使用 [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar#addphrase-phrase--str-) 添加特定的单词和短语。

对 [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) 所做的任何更改都将在下一次识别或重新连接到语音服务之后生效。

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

如果需要清除短语列表： 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>提高识别精确度的其他方式

短语列表只是提高识别准确度的一种方式。 也可执行以下操作： 

* [使用自定义语音识别提高准确性](../../../custom-speech-overview.md)
* [使用租户模型提高准确性](../../../tutorial-tenant-model.md)
