---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: lajanuar
ms.openlocfilehash: fb5c858cef3ad53ccda73ef3d9524241d3f2f749
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423242"
---
本快速入门介绍使用语音 SDK 进行文本到语音合成的常见设计模式。 首先，请进行基本的配置和合成，然后通过更高级的示例来了解自定义应用程序开发，其中包括：

* 获取内存中流形式的响应
* 自定义输出采样率和比特率
* 使用 SSML（语音合成标记语言）提交合成请求
* 使用神经语音

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [Python 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。

```Python
pip install azure-cognitiveservices-speech
```

如果使用的是 macOS 且你遇到安装问题，则可能需要先运行此命令。

```Python
python3 -m pip install --upgrade pip
```

安装语音 SDK 后，在脚本顶部包含以下 import 语句。

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)。 此类包含有关你的订阅的信息，例如你的语音密钥和关联的位置/区域、终结点、主机或授权令牌。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)：

* 使用订阅：传入语音密钥和关联的位置/区域。
* 使用终结点：传入语音服务终结点。 语音密钥或授权令牌是可选项。
* 使用主机：传入主机地址。 语音密钥或授权令牌是可选项。
* 使用授权令牌：传入授权令牌和关联的位置/区域。

在此示例中，使用语音密钥和位置/区域创建 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)。 按照[免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)中的以下步骤获取这些凭据。

```python
speech_config = SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
```

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

接下来，创建一个 [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) 对象，用于执行文本到语音的转换，并将转换结果输出到扬声器、文件或其他输出流。 [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) 接受的参数包括上一步骤创建的 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) 对象，以及用于指定如何处理输出结果的 [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig) 对象。

若要开始，请创建一个 `AudioOutputConfig`，以使用 `filename` 构造函数参数自动将输出写入到 `.wav` 文件。

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

接下来，通过将 `speech_config` 对象和 `audio_config` 对象作为参数传递来实例化 `SpeechSynthesizer`。 然后，只需结合一个文本字符串运行 `speak_text_async()`，就能执行语音合成和写入文件的操作。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

运行程序，合成的 `.wav` 文件会写入指定的位置。 这只是最基本用法的一个很好示例。接下来，你将了解如何自定义输出，并将输出响应作为适用于自定义方案的内存中流进行处理。

## <a name="synthesize-to-speaker-output"></a>合成到扬声器输出

在某些情况下，你可能希望直接将合成的语音输出到扬声器。 为此，可使用上一部分中的示例，但需要通过删除 `filename` 参数来更改 `AudioOutputConfig`，并设置 `use_default_speaker=True`。 这会将语音输出到当前处于活动状态的输出设备。

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>获取内存中流形式的结果

在许多语音应用程序开发方案中，你可能需要内存中流形式的最终音频数据，而不是直接写入文件。 这样便可以构建自定义行为，包括：

* 抽取生成的字节数组，作为自定义下游服务的可搜寻流。
* 将结果与其他 API 或服务相集成。
* 修改音频数据、写入自定义 `.wav` 标头，等等。

可以轻松地在前一个示例的基础上进行此项更改。 首先删除 `AudioConfig`，因为从现在起，你将手动管理输出行为，以提高控制度。 然后在 `SpeechSynthesizer` 构造函数中为 `AudioConfig` 传递 `None`。

> [!NOTE]
> 如果为 `AudioConfig` 传递 `None`，而不是像在前面的扬声器输出示例中那样省略它，则默认不会在当前处于活动状态的输出设备上播放音频。

这一次，请将结果保存到 [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) 变量。 `audio_data` 属性包含输出数据的 `bytes` 对象。 可以手动使用此对象，也可以使用 [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) 类来管理内存中流。 此示例使用 `AudioDataStream` 构造函数获取结果中的流。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

在此处，可以使用生成的 `stream` 对象来实现任何自定义行为。

## <a name="customize-audio-format"></a>自定义音频格式

以下部分介绍如何自定义音频输出属性，包括：

* 音频文件类型
* 采样率
* 位深度

若要更改音频格式，请对 `SpeechConfig` 对象使用 `set_speech_synthesis_output_format()` 函数。 此函数需要 [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) 类型的 `enum`，用于选择输出格式。 请参阅参考文档，获取可用的[音频格式列表](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat)。

可根据要求对不同的文件类型使用不同的选项。 请注意，根据定义，`Raw24Khz16BitMonoPcm` 等原始格式不包括音频标头。 仅当你知道下游实现可以解码原始位流，或者你打算基于位深度、采样率、通道数等属性手动生成标头时，才使用原始格式。

此示例通过对 `SpeechConfig` 对象设置 `SpeechSynthesisOutputFormat` 来指定高保真 RIFF 格式 `Riff24Khz16BitMonoPcm`。 类似于上一部分中的示例，可以使用 [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) 获取结果的内存中流，然后将其写入文件。


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

再次运行程序会将自定义的 `.wav` 文件写入指定的路径。

## <a name="use-ssml-to-customize-speech-characteristics"></a>使用 SSML 自定义语音特征

借助语音合成标记语言 (SSML)，可以通过从 XML 架构中提交请求，来微调文本转语音输出的音节、发音、语速、音量等特征。 本部分演示了更改语音的示例，但如果需要更详细的指南，请参阅 [SSML 操作指南文章](../../../speech-synthesis-markup.md)。

若要开始使用 SSML 进行自定义，请做出一项切换语音的简单更改。
首先，在根项目目录中为 SSML 配置创建一个新的 XML 文件，在本示例中为 `ssml.xml`。 根元素始终是 `<speak>`。将文本包装在 `<voice>` 元素中可以使用 `name` 参数来更改语音。 查看受支持神经语音的[完整列表](../../../language-support.md#neural-voices)。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-ChristopherNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

接下来，需要更改语音合成请求以引用 XML 文件。 该请求基本上保持不变，只不过需要使用 `speak_ssml_async()` 而不是 `speak_text_async()` 函数。 此函数需要 XML 字符串，因此，请先读取字符串形式的 SSML 配置。 在此处，结果对象与前面的示例完全相同。

> [!NOTE]
> 如果 `ssml_string` 在字符串开头包含 `ï»¿`，则需要去除 BOM 格式，否则服务会返回错误。 为此，可按如下所示设置 `encoding` 参数：`open("ssml.xml", "r", encoding="utf-8-sig")`。

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

> [!NOTE]
> 若要在不使用 SSML 的情况下更改语音，可使用 `SpeechConfig.speech_synthesis_voice_name = "en-US-ChristopherNeural"` 在 `SpeechConfig` 上设置属性

## <a name="get-facial-pose-events"></a>获取人脸姿态事件

语音可以是推动面部表情动画的好办法。
[视素](../../../how-to-speech-synthesis-viseme.md)通常用以表示所观察语音中的关键姿态，包括在发出特定音素时嘴唇、下巴和舌头等的位置。
你可以在语音 SDK 中订阅视素事件。
然后，你可以应用视素事件，将字符的表面以语音音频播放形式进行动画处理。
了解[如何取得视素事件](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)。
