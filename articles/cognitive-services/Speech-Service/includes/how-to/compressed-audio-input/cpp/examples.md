---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: pafarley
ms.openlocfilehash: 53aad3299bafb606973131646cad26e09671d779
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646658"
---
若要将语音 SDK 配置为接受压缩音频输入，请创建 `PullAudioInputStream` 或 `PushAudioInputStream`。 然后，从流类的实例创建 `AudioConfig`，并指定流的压缩格式。  在[语音 SDK 示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)中查找相关示例代码。

让我们假设你有一个名为 `pushStream` 的输入流类，并且使用 OPUS/OGG。 你的代码可能如下所示：

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```
