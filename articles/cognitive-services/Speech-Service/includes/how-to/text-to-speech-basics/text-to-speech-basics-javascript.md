---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 5a599e80dea9d172ddb42cdfe76325ff5e807fbc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378108"
---
本快速入门介绍使用语音 SDK 进行文本到语音合成的常见设计模式。 首先，请进行基本的配置和合成，然后通过更高级的示例来了解自定义应用程序开发，其中包括：

* 获取内存中流形式的响应
* 自定义输出采样率和比特率
* 使用 SSML（语音合成标记语言）提交合成请求
* 使用神经语音

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [JavaScript 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务资源。 如果没有帐户和资源，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

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
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

有关 `import` 的详细信息，请参阅 <a href="https://javascript.info/import-export" target="_blank">export 和 import </a>。

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

有关 `require` 的详细信息，请参阅<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什么是 require？</a>。

---


## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)。 此类包含有关资源的信息，例如语音密钥和关联的位置/区域、终结点、主机或授权令牌。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)：

* 使用资源：传入语音密钥和关联的位置/区域。
* 使用终结点：传入语音服务终结点。 语音密钥或授权令牌是可选项。
* 使用主机：传入主机地址。 语音密钥或授权令牌是可选项。
* 使用授权令牌：传入授权令牌和关联的位置/区域。

在此示例中，使用语音密钥和位置/区域创建 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)。 按照[免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)中的以下步骤获取这些凭据。 此外，你将创建一些基本的样板代码，在本文的余下部分，你将修改这些代码以进行不同的自定义操作。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

接下来，创建一个 [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) 对象，用于执行文本到语音的转换，并将转换结果输出到扬声器、文件或其他输出流。 [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) 接受的参数包括上一步骤创建的 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) 对象，以及用于指定如何处理输出结果的 [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) 对象。

若要开始，请创建一个 `AudioConfig`，以使用 `fromAudioFileOutput()` 静态函数自动将输出写入到 `.wav` 文件。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const audioConfig = sdk.AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

接下来，实例化 `SpeechSynthesizer` 并将 `speechConfig` 对象和 `audioConfig` 对象作为参数传递。 然后，只需结合一个文本字符串运行 `speakTextAsync()`，就能执行语音合成和写入文件的操作。 结果回调是调用 `synthesizer.close()` 的理想位置，实际上，必须有此调用才能使合成正常运行。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const audioConfig = sdk.AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

运行程序，合成的 `.wav` 文件将写入到指定的位置。 这只是最基本用法的一个很好示例。接下来，你将了解如何自定义输出，并将输出响应作为适用于自定义方案的内存中流进行处理。

## <a name="synthesize-to-speaker-output-browser-only"></a>合成到扬声器输出（仅限浏览器）

在某些情况下，你可能希望直接将合成的语音输出到扬声器。 为此，请使用 `fromDefaultSpeakerOutput()` 静态函数实例化 `AudioConfig`。 这会将语音输出到当前处于活动状态的输出设备。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const audioConfig = sdk.AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>获取内存中流形式的结果

在许多语音应用程序开发方案中，你可能需要内存中流形式的最终音频数据，而不是直接写入文件。 这样便可以构建自定义行为，包括：

* 抽取生成的字节数组，作为自定义下游服务的可搜寻流。
* 将结果与其他 API 或服务相集成。
* 修改音频数据、写入自定义 `.wav` 标头，等等。

可以轻松地在前一个示例的基础上进行此项更改。 首先删除 `AudioConfig` 块，因为从现在起，你将手动管理输出行为，以提高控制度。 然后在 `SpeechSynthesizer` 构造函数中为 `AudioConfig` 传递 `undefined`。

> [!NOTE]
> 如果为 `AudioConfig` 传递 `undefined`，而不是像在前面的扬声器输出示例中那样省略它，则默认不会在当前处于活动状态的输出设备上播放音频。

这一次，请将结果保存到 [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult) 变量。 `SpeechSynthesisResult.audioData` 属性返回输出数据的 `ArrayBuffer`，即默认的浏览器流类型。 对于服务器代码，请将 arrayBuffer 转换为缓冲区流。

以下代码适用于客户端代码。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

在此处，可以使用生成的 `ArrayBuffer` 对象来实现任何自定义行为。 ArrayBuffer 是浏览器中接收的并使用此格式播放的常见类型。

对于基于服务器的任何代码，如果需要以流的形式使用数据，则需要将对象转换为流，而不是 ArrayBuffer。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>自定义音频格式

以下部分介绍如何自定义音频输出属性，包括：

* 音频文件类型
* 采样率
* 位深度

若要更改音频格式，请使用 `SpeechConfig` 对象的 `speechSynthesisOutputFormat` 属性。 此属性需要 [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) 类型的 `enum`，用于选择输出格式。 请参阅参考文档，获取可用的[音频格式列表](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat)。

可根据要求对不同的文件类型使用不同的选项。 请注意，根据定义，`Raw24Khz16BitMonoPcm` 等原始格式不包括音频标头。 仅当你知道下游实现可以解码原始位流，或者你打算基于位深度、采样率、通道数等属性手动生成标头时，才使用原始格式。

此示例通过对 `SpeechConfig` 对象设置 `speechSynthesisOutputFormat` 来指定高保真 RIFF 格式 `Riff24Khz16BitMonoPcm`。 与上一部分中的示例类似，获取音频 `ArrayBuffer` 数据并与之进行交互。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

再次运行程序会将 `.wav` 文件写入到指定的路径。

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

接下来，需要更改语音合成请求以引用 XML 文件。 该请求基本上保持不变，只不过需要使用 `speakSsmlAsync()` 而不是 `speakTextAsync()` 函数。 此函数需要 XML 字符串，因此，请先创建一个加载 XML 文件并将其作为字符串返回的函数。

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

有关 `readFileSync` 的详细信息，请参阅 <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js 文件系统</a>。 在此处，结果对象与前面的示例完全相同。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

> [!NOTE]
> 若要在不使用 SSML 的情况下更改语音，可使用 `SpeechConfig.speechSynthesisVoiceName = "en-US-ChristopherNeural";` 在 `SpeechConfig` 上设置属性

## <a name="get-facial-pose-events"></a>获取人脸姿态事件

语音可以是推动面部表情动画的好办法。
[视素](../../../how-to-speech-synthesis-viseme.md)通常用以表示所观察语音中的关键姿态，包括在发出特定音素时嘴唇、下巴和舌头等的位置。
你可以在语音 SDK 中订阅视素事件。
然后，你可以应用视素事件，将字符的表面以语音音频播放形式进行动画处理。
了解[如何取得视素事件](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)。
