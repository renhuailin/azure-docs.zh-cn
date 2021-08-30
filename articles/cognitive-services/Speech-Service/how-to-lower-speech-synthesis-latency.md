---
title: 如何使用语音 SDK 降低语音合成延迟
titleSuffix: Azure Cognitive Services
description: 如何使用语音 SDK 降低语音合成延迟，包括流式处理、预连接等。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 38af8d1ffd422b151bebc2fea42c575f181cc72e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741099"
---
# <a name="lower-speech-synthesis-latency-using-speech-sdk"></a>使用语音 SDK 降低语音合成延迟

> [!NOTE]
> 本文需要使用语音 SDK 1.17.0 或更高版本。

合成延迟对应用程序至关重要。
在本文中，我们将介绍一些最佳做法，以便降低延迟并为最终用户提供最佳性能。

通常，我们根据 `first byte latency` 和 `finish latency` 来度量延迟，如下所示：

::: zone pivot="programming-language-csharp"

| 延迟 | 说明 | [SpeechSynthesisResult](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) 属性键 |
|-----------|-------------|------------|
| 第一个字节延迟 | 指示开始合成任务与接收第一个音频数据区块之间的时间延迟。 | SpeechServiceResponse_SynthesisFirstByteLatencyMs |
| 完成延迟 | 指示开始合成任务与接收全部的合成音频数据之间的时间延迟。 | SpeechServiceResponse_SynthesisFinishLatencyMs |

语音 SDK 将延迟持续时间放在 [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) 的属性集合中。 下面的示例代码显示了这些值。

```csharp
var result = await synthesizer.SpeakTextAsync(text);
Console.WriteLine($"first byte latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs)} ms");
Console.WriteLine($"finish latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs)} ms");
// you can also get the result id, and send to us when you need help for diagnosis
var resultId = result.ResultId;
```

::: zone-end

::: zone pivot="programming-language-cpp"

| 延迟 | 说明 | [SpeechSynthesisResult](/cpp/cognitive-services/speech/speechsynthesisresult) 属性键 |
|-----------|-------------|------------|
| `first byte latency` | 指示开始合成与接收第一个音频区块之间的时间延迟。 | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 指示开始合成与接收全部合成音频之间的时间延迟。 | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

语音 SDK 度量了延迟，并将结果放在 [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) 的属性包中。 若要获取它们，请参阅以下代码。

```cpp
auto result = synthesizer->SpeakTextAsync(text).get();
auto firstByteLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFirstByteLatencyMs));
auto finishedLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFinishLatencyMs));
// you can also get the result id, and send to us when you need help for diagnosis
auto resultId = result->ResultId;
```

::: zone-end

::: zone pivot="programming-language-java"

| 延迟 | 说明 | [SpeechSynthesisResult](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) 属性键 |
|-----------|-------------|------------|
| `first byte latency` | 指示开始合成与接收第一个音频区块之间的时间延迟。 | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 指示开始合成与接收全部合成音频之间的时间延迟。 | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

语音 SDK 度量了延迟，并将结果放在 [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) 的属性包中。 若要获取它们，请参阅以下代码。

```java
SpeechSynthesisResult result = synthesizer.SpeakTextAsync(text).get();
System.out.println("first byte latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs) + " ms.");
System.out.println("finish latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs) + " ms.");
// you can also get the result id, and send to us when you need help for diagnosis
String resultId = result.getResultId();
```

::: zone-end


::: zone pivot="programming-language-python"

| 延迟 | 说明 | [SpeechSynthesisResult](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) 属性键 |
|-----------|-------------|------------|
| `first byte latency` | 指示开始合成与接收第一个音频区块之间的时间延迟。 | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 指示开始合成与接收全部合成音频之间的时间延迟。 | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

语音 SDK 度量了延迟，并将结果放在 [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) 的属性包中。 若要获取它们，请参阅以下代码。

```python
result = synthesizer.speak_text_async(text).get()
first_byte_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs))
finished_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs))
# you can also get the result id, and send to us when you need help for diagnosis
result_id = result.result_id
```

::: zone-end

::: zone pivot="programming-language-objectivec"

| 延迟 | 说明 | [SPXSpeechSynthesisResult](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) 属性键 |
|-----------|-------------|------------|
| `first byte latency` | 指示开始合成与接收第一个音频区块之间的时间延迟。 | `SPXSpeechServiceResponseSynthesisFirstByteLatencyMs` |
| `finish latency` | 指示开始合成与接收全部合成音频之间的时间延迟。 | `SPXSpeechServiceResponseSynthesisFinishLatencyMs` |

语音 SDK 度量了延迟，并将结果放在 [`SPXSpeechSynthesisResult`](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) 的属性包中。 若要获取它们，请参阅以下代码。

```Objective-C
SPXSpeechSynthesisResult *speechResult = [speechSynthesizer speakText:text];
int firstByteLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFirstByteLatencyMs]];
int finishedLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFinishLatencyMs]];
// you can also get the result id, and send to us when you need help for diagnosis
NSString *resultId = result.resultId;
```

::: zone-end

在大多数情况下，第一个字节延迟远低于完成延迟。
第一个字节延迟与文本长度无关，而完成延迟因文本长度增加而增加。

理想情况下，我们希望将用户感受到的延迟时间（用户听到声音之前的延迟时间）尽可能降低至一次网络往返时间与语音合成服务的第一个音频区块延迟时间之和。

## <a name="streaming"></a>流式处理

流式处理对降低延迟至关重要。
收到第一个音频区块后，客户端代码可以开始播放。
在服务方案中，可以将音频区块立即转发到客户端，而不是等待整个音频完成。

::: zone pivot="programming-language-csharp"

可以使用语音 SDK 的 [`PullAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pullaudiooutputstream)、[`PushAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudiooutputstream)、[`Synthesizing` 事件](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing)和 [`AudioDateStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) 来启用流式处理。

以 `AudioDateStream` 为例：

```csharp
using (var synthesizer = new SpeechSynthesizer(config, null as AudioConfig))
{
    using (var result = await synthesizer.StartSpeakingTextAsync(text))
    {
        using (var audioDataStream = AudioDataStream.FromResult(result))
        {
            byte[] buffer = new byte[16000];
            uint filledSize = 0;
            while ((filledSize = audioDataStream.ReadData(buffer)) > 0)
            {
                Console.WriteLine($"{filledSize} bytes received.");
            }
        }
    }
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

可以使用语音 SDK 的 [`PullAudioOutputStream`](/cpp/cognitive-services/speech/audio-pullaudiooutputstream)、[`PushAudioOutputStream`](/cpp/cognitive-services/speech/audio-pushaudiooutputstream)、[`Synthesizing` 事件](/cpp/cognitive-services/speech/speechsynthesizer#synthesizing)和 [`AudioDateStream`](/cpp/cognitive-services/speech/audiodatastream) 来启用流式处理。

以 `AudioDateStream` 为例：

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto result = synthesizer->SpeakTextAsync(text).get();
auto audioDataStream = AudioDataStream::FromResult(result);
uint8_t buffer[16000];
uint32_t filledSize = 0;
while ((filledSize = audioDataStream->ReadData(buffer, sizeof(buffer))) > 0)
{
    cout << filledSize << " bytes received." << endl;
}
```

::: zone-end

::: zone pivot="programming-language-java"

可以使用语音 SDK 的 [`PullAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pullaudiooutputstream)、[`PushAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pushaudiooutputstream)、[`Synthesizing` 事件](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_Synthesizing)和 [`AudioDateStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) 来启用流式处理。

以 `AudioDateStream` 为例：

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(config, null);
SpeechSynthesisResult result = synthesizer.StartSpeakingTextAsync(text).get();
AudioDataStream audioDataStream = AudioDataStream.fromResult(result);
byte[] buffer = new byte[16000];
long filledSize = audioDataStream.readData(buffer);
while (filledSize > 0) {
    System.out.println(filledSize + " bytes received.");
    filledSize = audioDataStream.readData(buffer);
}
```

::: zone-end

::: zone pivot="programming-language-python"

可以使用语音 SDK 的 [`PullAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pullaudiooutputstream)、[`PushAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pushaudiooutputstream)、[`Synthesizing` 事件](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#synthesizing)和 [`AudioDateStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) 来启用流式处理。

以 `AudioDateStream` 为例：

```python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = speech_synthesizer.start_speaking_text_async(text).get()
audio_data_stream = speechsdk.AudioDataStream(result)
audio_buffer = bytes(16000)
filled_size = audio_data_stream.read_data(audio_buffer)
while filled_size > 0:
    print("{} bytes received.".format(filled_size))
    filled_size = audio_data_stream.read_data(audio_buffer)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

可以使用语音 SDK 的 [`SPXPullAudioOutputStream`](/objectivec/cognitive-services/speech/spxpullaudiooutputstream)、[`SPXPushAudioOutputStream`](/objectivec/cognitive-services/speech/spxpushaudiooutputstream)、[`Synthesizing` 事件](/objectivec/cognitive-services/speech/spxspeechsynthesizer#addsynthesizingeventhandler)和 [`SPXAudioDataStream`](/objectivec/cognitive-services/speech/spxaudiodatastream) 来启用流式处理。

以 `AudioDateStream` 为例：

```Objective-C
SPXSpeechSynthesizer *synthesizer = [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig audioConfiguration:nil];
SPXSpeechSynthesisResult *speechResult = [synthesizer startSpeakingText:inputText];
SPXAudioDataStream *stream = [[SPXAudioDataStream alloc] initFromSynthesisResult:speechResult];
NSMutableData* data = [[NSMutableData alloc]initWithCapacity:16000];
while ([stream readData:data length:16000] > 0) {
    // Read data here
}
```

::: zone-end

## <a name="pre-connect-and-reuse-speechsynthesizer"></a>预连接和重用 SpeechSynthesizer

语音 SDK 使用 websocket 与服务进行通信。
理想情况下，网络延迟应为一次往返时间 (RTT)。
如果连接是新建立的，网络延迟将包括建立连接所用的额外时间。
建立 websocket 连接需要进行 TCP 握手、SSL 握手、HTTP 连接和协议升级，这会导致时间延迟。
为了避免连接延迟，我们建议预连接和重用 `SpeechSynthesizer`。

### <a name="pre-connect"></a>预连接

若要进行预连接，请在知道即将需要连接后，建立与语音服务的连接。 例如，如果要在客户端中构建语音机器人，则可以在用户开始对话时预连接到语音合成服务，并在机器人回复文本准备就绪时调用 `SpeakTextAsync`。

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(uspConfig, null as AudioConfig))
{
    using (var connection = Connection.FromSpeechSynthesizer(synthesizer))
    {
        connection.Open(true);
    }
    await synthesizer.SpeakTextAsync(text);
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto connection = Connection::FromSpeechSynthesizer(synthesizer);
connection->Open(true);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, (AudioConfig) null);
Connection connection = Connection.fromSpeechSynthesizer(synthesizer);
connection.openConnection(true);
```

::: zone-end

::: zone pivot="programming-language-python"

```python
synthesizer = speechsdk.SpeechSynthesizer(config, None)
connection = speechsdk.Connection.from_speech_synthesizer(synthesizer)
connection.open(True)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer* synthesizer = [[SPXSpeechSynthesizer alloc]initWithSpeechConfiguration:self.speechConfig audioConfiguration:nil];
SPXConnection* connection = [[SPXConnection alloc]initFromSpeechSynthesizer:synthesizer];
[connection open:true];
```

::: zone-end

> [!NOTE]
> 如果合成文本可用，则只需调用 `SpeakTextAsync` 即可合成音频。 SDK 将处理连接。

### <a name="reuse-speechsynthesizer"></a>重用 SpeechSynthesizer

降低连接延迟的另一种方法是重用 `SpeechSynthesizer`，这样就不需要针对每次合成创建新的 `SpeechSynthesizer`。
建议在服务方案中使用对象池，请参阅适用于 [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) 和 [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java) 的示例代码。


## <a name="transmit-compressed-audio-over-the-network"></a>通过网络传输压缩音频

当网络不稳定或带宽有限时，负载大小也会影响延迟。
同时，压缩音频格式有助于节省用户的网络带宽，这对于移动用户非常有用。

我们支持许多压缩格式，包括 `opus`、`webm`、`mp3`、`silk` 等，请参阅 [SpeechSynthesisOutputFormat](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) 中的完整列表。
例如，`Riff24Khz16BitMonoPcm` 格式的比特率为 384 kbps，而 `Audio24Khz48KBitRateMonoMp3` 的比特率仅为 48 kbps。
设置 `pcm` 输出格式并正确安装 `GStreamer` 之后，语音 SDK 会自动使用压缩格式进行传输。
请参阅[此说明](how-to-use-codec-compressed-audio-input-streams.md)，为 Speech SDK 安装和配置 `GStreamer`。

## <a name="others-tips"></a>其他提示

### <a name="cache-crl-files"></a>缓存 CRL 文件

语音 SDK 使用 CRL 文件来检查证书。
将 CRL 文件缓存至过期，这样就无需每次下载 CRL 文件。
有关详细信息，请参阅[如何配置适用于 Linux 的 OpenSSL](how-to-configure-openssl-linux.md#certificate-revocation-checks)。

### <a name="use-latest-speech-sdk"></a>使用最新的语音 SDK

我们在持续改进语音 SDK 的性能，因此请尝试在应用程序中使用最新的语音 SDK。
例如，我们修复了 [1.16.0](releasenotes.md#speech-sdk-1160-2021-march-release) 中的 `TCP_NODELAY` 设置问题，这减少了额外的一次往返时间。

## <a name="load-test-guideline"></a>负载测试准则

可以使用负载测试来测试语音合成服务的容量和延迟。
下面是一些准则。

 - 语音合成服务具有自动缩放功能，但进行扩展需要一定时间。如果短时间内并发增加，则客户端可能会出现长时间的延迟或 `429` 错误代码（请求过多）。 因此，建议在负载测试中逐步提高并发数。 [参阅本文](speech-services-quotas-and-limits.md#general-best-practices-to-mitigate-throttling-during-autoscaling)了解更多详细信息，尤其是[此工作负载模式示例](speech-services-quotas-and-limits.md#example-of-a-workload-pattern-best-practice)。
 - 可利用示例来使用对象池（[C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) 和 [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java)）进行负载测试并获取延迟数。 可修改示例中的测试回合和并发以满足目标并发。
 - 此服务存在基于实际流量的配额限制，因此，如果要在并发数远远高于实际流量的情况下执行负载测试，请在测试之前进行连接。

## <a name="next-steps"></a>后续步骤

* [查看 GitHub 上的示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master)
