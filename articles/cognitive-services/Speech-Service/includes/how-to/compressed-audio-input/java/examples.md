---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: lajanuar
ms.openlocfilehash: ae0a08d0e860777ccc6ff716bef8305f7d7f1748
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122263197"
---
若要将语音 SDK 配置为接受压缩音频输入，请创建 `PullAudioInputStream` 或 `PushAudioInputStream`。 然后，从流类的实例创建 `AudioConfig`，并指定流的压缩格式。 在[语音 SDK 示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/WavStream.java)中查找相关示例代码。

让我们假设你有一个名为 `pullStream` 的输入流类，并且使用 OPUS/OGG。 你的代码可能如下所示：

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

// ... omitted for brevity

SpeechConfig speechConfig =
    SpeechConfig.fromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = 
    AudioStreamFormat.getCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig =
    AudioConfig.fromStreamInput(
        pullStream,
        audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();

String text = result.getText();
```
