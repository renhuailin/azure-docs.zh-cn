---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 7ab4d7708dd75ff2e07e77f3be4f996068704797
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486765"
---
## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript 语音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>，然后才能执行操作。 根据你的平台，使用以下说明：

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web 浏览器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-voice-signatures"></a>创建语音签名

第一步是为会话参与者创建语音签名，以便将其识别为唯一的发言人。 `.wav`用于创建语音签名的输入音频文件应为16位、16 kHz 采样率和单通道 (单色) 格式。 每个音频采样的建议长度介于30秒到2分钟之间。 `.wav`文件应为**一个人的**语音示例，以便创建唯一的语音配置文件。

下面的示例演示如何使用 JavaScript 中 [的 REST API](https://aka.ms/cts/signaturegenservice) 创建语音签名。 请注意，你需要将你的、和的实际信息替换为 `subscriptionKey` `region` 示例文件的路径 `.wav` 。

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

运行此脚本将返回变量中的语音签名字符串 `voiceSignatureString` 。 运行函数两次，以便将两个字符串用作变量和下面的输入 `voiceSignatureStringUser1` `voiceSignatureStringUser2` 。

> [!NOTE]
> 语音签名 **只能** 使用 REST API 创建。

## <a name="transcribe-conversations"></a>转录对话

下面的示例代码演示了如何为两个扬声器实时转录会话。 假设已为每个扬声器创建了语音签名字符串，如上所示。 用真实信息替换 `subscriptionKey` 、 `region` 和 `filepath` 要转录的音频的路径。

此示例代码执行以下操作：

* 创建用于脚本的推送流，并将示例 `.wav` 文件写入其中。
* 使用创建 `Conversation` 一个 `createConversationAsync()` 。
* `ConversationTranscriber`使用构造函数创建一个。
* 向对话添加参与者。 字符串 `voiceSignatureStringUser1` 和 `voiceSignatureStringUser2` 应作为上述步骤的输出。
* 注册事件并开始脚本。

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