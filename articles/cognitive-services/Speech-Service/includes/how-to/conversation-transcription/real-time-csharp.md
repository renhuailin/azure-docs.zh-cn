---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: lajanuar
ms.openlocfilehash: bc4709eeb59f0333a086336b4f61474671dac3af
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068966"
---
## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，使用以下说明：

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-voice-signatures"></a>创建语音签名

（如果不想使用预先注册的用户配置文件来识别特定参与者，则可以跳过此步骤。）

如果想要注册用户配置文件，第一步是为对话参与者创建语音签名，以便可以将这些参与者识别为非重复的说话人。 用于创建语音签名的输入 `.wav` 音频文件必须为 16 位、16 kHz 采样率、单通道（单声道）格式。 每个音频样本的建议时长为 30 秒至 2 分钟。 如果音频样本太短，会导致在识别说话人时准确性降低。 `.wav` 文件应该是一个人的语音样本，以便创建独一无二的语音配置文件。

以下示例展示了如何通过[使用 C# 的 REST API](https://aka.ms/cts/signaturegenservice) 来创建语音签名。 请注意，对于 `subscriptionKey`、`region` 和 `.wav` 样本文件的路径，需要替换为实际信息。

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

运行函数 `GetVoiceSignatureString()` 会返回采用正确格式的语音签名字符串。 运行该函数两次，便会得到两个字符串，它们将会用作下面 `voiceSignatureStringUser1` 和 `voiceSignatureStringUser2` 变量的输入。

> [!NOTE]
> 只能使用 REST API 来创建语音签名。

## <a name="transcribe-conversations"></a>听录对话

下面的示例代码演示了如何实时听录两名说话人的对话。 假定已经按上面所述为每名说话人创建了语音签名字符串。 请用真实信息替换 `subscriptionKey`、`region` 和要听录的音频的路径 `filepath`。

如果未使用预先注册的用户配置文件，则将未知用户首次识别为 speaker1、speaker2 等时需要多花几秒钟的时间。

> [!NOTE]
> 请确保在应用程序中使用相同的 `subscriptionKey` 来创建签名，否则会遇到错误。 

此示例代码执行以下操作：

* 从要听录的 `.wav` 样本文件创建 `AudioConfig`。
* 使用 `CreateConversationAsync()` 创建 `Conversation`。
* 使用构造函数创建 `ConversationTranscriber`，并订阅必要的事件。
* 为对话添加参与者。 字符串 `voiceSignatureStringUser1` 和 `voiceSignatureStringUser2` 应来自上述步骤中函数 `GetVoiceSignatureString()` 的输出。
* 加入该对话并开始听录。

调用函数 `TranscribeConversationsAsync()` 以开始对话听录。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

class transcribe_conversation
{
// all your other code

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
    // config.SpeechRecognitionLanguage = "zh-cn"; // en-us by default. This code specifies Chinese.
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioConfig.FromWavFileInput(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                conversationTranscriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
        }
    }
}
}
```