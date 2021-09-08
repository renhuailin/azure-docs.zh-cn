---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 40bd77f11ed7d8949672bf57b95494f24d6793f6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646722"
---
若要将语音 SDK 配置为接受压缩音频输入，请创建 `PullAudioInputStream` 或 `PushAudioInputStream`。 然后，从流类的实例创建 `AudioConfig`，并指定流的压缩格式。 在[关于语音 SDK 音频输入流 API](../../../../how-to-use-audio-input-streams.md) 中查找相关示例代码片段。

让我们假设你有一个名为 `pushStream` 的输入流类，并且使用 OPUS/OGG。 你的代码可能如下所示：

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```