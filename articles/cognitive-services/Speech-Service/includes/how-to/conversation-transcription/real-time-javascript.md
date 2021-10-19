---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: pafarley
ms.openlocfilehash: 9a69750da0ae64fc1467785e174fef14a22ab396
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725881"
---
## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript 语音 SDK</a>，然后才能执行操作。 根据你的平台，使用以下说明：

- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web 浏览器 </a>

## <a name="create-voice-signatures"></a>创建语音签名

（如果不想使用预先注册的用户配置文件来识别特定参与者，则可以跳过此步骤。）

如果想要注册用户配置文件，第一步是为对话参与者创建语音签名，以便可以将这些参与者识别为非重复的说话人。 用于创建语音签名的输入 `.wav` 音频文件必须为 16 位、16 kHz 采样率、单通道（单声道）格式。 每个音频样本的建议时长为 30 秒至 2 分钟。 如果音频样本太短，会导致在识别说话人时准确性降低。 `.wav` 文件应该是一个人的语音样本，以便创建独一无二的语音配置文件。

以下示例展示了如何通过[使用 JavaScript 的 REST API](https://aka.ms/cts/signaturegenservice) 来创建语音签名。 请注意，对于 `subscriptionKey`、`region` 和 `.wav` 样本文件的路径，需要替换为实际信息。

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

运行此脚本会返回变量 `voiceSignatureString` 中的语音签名字符串。 运行该函数两次，便会得到两个字符串，它们将会用作下面 `voiceSignatureStringUser1` 和 `voiceSignatureStringUser2` 变量的输入。

> [!NOTE]
> 只能使用 REST API 来创建语音签名。

## <a name="transcribe-conversations"></a>听录对话

下面的示例代码演示了如何实时听录两名说话人的对话。 假定已经按上面所述为每名说话人创建了语音签名字符串。 请用真实信息替换 `subscriptionKey`、`region` 和要听录的音频的路径 `filepath`。

如果未使用预先注册的用户配置文件，则将未知用户首次识别为 speaker1、speaker2 等时需要多花几秒钟的时间。

> [!NOTE]
> 请确保在应用程序中使用相同的 `subscriptionKey` 来创建签名，否则会遇到错误。 

此示例代码执行以下操作：

* 创建要用于听录的推送流，并将 `.wav` 样本文件写入到该推送流。
* 使用 `createConversationAsync()` 创建 `Conversation`。
* 使用该构造函数创建 `ConversationTranscriber`。
* 为对话添加参与者。 字符串 `voiceSignatureStringUser1` 和 `voiceSignatureStringUser2` 应来自上述步骤的输出。
* 注册到事件并开始听录。
* 如果要在不提供语音示例的情况下区分说话人，请启用[对话听录概览](../../../conversation-transcription.md)中的 `DifferentiateGuestSpeakers` 功能。 

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // en-us by default. Adding this code to specify other languages, like zh-cn.
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```