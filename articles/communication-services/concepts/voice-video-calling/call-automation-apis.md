---
title: Azure 通信服务调用自动化 API 概述
titleSuffix: An Azure Communication Services concept document
description: 提供调用自动化功能和 API 的概述。
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/16/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ffbadeb29ab26cf9a6523d9ca55bf95b9a211ffa
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468624"
---
# <a name="call-automation-apis-overview"></a>调用自动化 API 概述

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

通过调用自动化 API，组织可以通过自动执行业务逻辑大规模连接其客户或员工。 你可以使用这些 API 针对约会创建自动执行的出站提醒调用，或针对诸如停电或野火等事件提供主动通知。 添加到调用的应用程序可以在参与者加入或离开时监视更新，使你可以实现丰富的报告和日志记录功能。 

## <a name="in-call-apis"></a>In-Call API

> [!NOTE] 
> In-Call 应用程序在调用参与者时按标准的 PSTN 和 VoIP 费率计费。
                                                        

### <a name="create-call"></a>创建调用
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "create-call"
}-->
```
POST /calling/calls?api-version={api-version}
Content-Type: application/json

{
 "source: {
    "communicationUser": {
        "id": "string"
      }
  },
  "targets": [
    "communicationUser": {
        "id": "string"
      }
  ],
  "subject": "string",
  "callbackUri": "string",
  "requestedModalities": [
    "audio"
  ],
  "requestedCallEvents": [
    "participantsUpdated"
  ]
}
```
**C# SDK**

```C#
// Create call client 
var connectionString = "YOUR_CONNECTION_STRING";
var callClient = new CallClient(connectionString);

//Preparing request data
var source = new CommunicationUserIdentifier("<source-identity e.g. 8:acs:guid_guid>");
var targets = new List<CommunicationIdentifier>() 
{ 
    new PhoneNumberIdentifier("<phone-number e.g. +14251001000>"),
    new CommunicationUserIdentifier("<communication-user-identity e.g. 8:acs:guid_guid>")
};
var createCallOptions = new CreateCallOptions(
    new Uri("<callback-url>"), 
    new List<CallModality> { CallModality.Audio }, 
    new List<EventSubscritionType> { EventSubscritionType.ParticipantsUpdated, EventSubscritionType.DtmfReceived });

//phone number associated with the resource
createCallOptions.AlternateCallerId = new PhoneNumberIdentifier("<phone-number>");

//Starting the call
var call = await callClient.CreateCallAsync(source, targets, createCallOption).ConfigureAwait(false);

string callLegId = call.Value.CallLegId;
```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "callLegId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
### <a name="end-a-call"></a>结束呼叫
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "hangup-call"
}-->
```
POST /calling/calls/{callId}/Hangup?api-version={api-version}
Content-Type: application/json

```
**C# SDK**

```C#
await callClient.HangupCallAsync("<call-leg-id>").ConfigureAwait(false);
```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
### <a name="play-audio-in-call"></a>在调用中播放音频
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "play-audio"
}-->
```
POST /calling/calls/{callId}/PlayAudio?api-version={api-version}
Content-Type: application/json

{
  "audioFileUri": "string",
  "loop": true,
  "operationContext": "string",
  "resourceId": "string"
}
```
**C# SDK**

```C#
// Preparing data for play audio request
var playAudioRequest = new PlayAudioRequest()
{
    AudioFileUri = "<audio-file-url",
    OperationContext = "<operation-context e.g. guid>",
    Loop = <true|false>,
    ResourceId = "<resource-id e.g. guid>"
};

var response = await callClient.PlayAudioAsync("<call-leg-id>", playAudioRequest).ConfigureAwait(false);

```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Success
Content-Type: application/json

{
  "id": "string",
  "status": "notStarted",
  "operationContext": "string",
  "resultInfo": {
    "code": 0,
    "subcode": 0,
    "message": "string"
  }
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
### <a name="cancel-media-processing"></a>取消媒体处理
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "cancel-media-processing"
}-->
```
POST /calling/calls/{callId}/CancelMediaProcessing?api-version={api-version}
Content-Type: application/json

{
  "operationContext": "string"
}
```
**C# SDK**

```C#
await callClient.CancelMediaProcessingAsync("<call-leg-id>", "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

{
  "id": "string",
  "status": "notStarted",
  "operationContext": "string",
  "resultInfo": {
    "code": 0,
    "subcode": 0,
    "message": "string"
  }
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
### <a name="invite-participant"></a>邀请参与者
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "invite-participant "
}-->
```
POST /calling/calls/{callId}/participants?api-version={api-version}
Content-Type: application/json

{
  "alternateCallerId": {
      "value": "<phone-number>"
  }
  "participants": [
    {
      "communicationUser": {
        "id": "<communication-user-identity>"
      }
    },
    {
      "phoneNumber": {
        "value": "<phone-number>"
      }
    }
  ],
  "operationContext": "string"
}
```
**C# SDK**
```C#
var invitedParticipants = new List<CommunicationIdentifier>()
{
    new CommunicationUserIdentifier("<communication-user-identity>"),
    new PhoneNumberIdentifier("<phone-number>")
}; 

//Alternate phone number required when inviting phone number
var alernateCallerId = "<phone-number>";

await callClient.InviteParticipantsAsync("<call-leg-id>", invitedParticipants, "<operation-context>", alernateCallerId).ConfigureAwait(false);

```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
### <a name="remove-participant"></a>删除参与者
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "remove-participant "
}-->
```
DELETE /calling/calls/{callId}/participants/{participantId}?api-version={api-version}
Content-Type: application/json

```
**C# SDK**

```C#
await callClient.RemoveParticipantAsync("<call-leg-id>", "<participant-id>").ConfigureAwait(false);

```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Accepted
Content-Type: application/json
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
## <a name="in-call-events"></a>In-Call 事件
在创建调用请求期间，事件通知通过 `callbackUri` 集作为 JSON 有效负载发送到调用的应用程序。

### <a name="callstate-event---establishing"></a>CallState 事件 - 建立
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/531f3600-481f-41c8-8a75-3e8b2e8e6200/callState",
        "data": {
            "ConversationId": null,
            "CallLegId": "531f3600-481f-41c8-8a75-3e8b2e8e6200",
            "CallState": "Establishing"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-05-05T20:08:39.0157964Z",
        "metadataVersion": null,
        "dataVersion": null
}
```
### <a name="callstate-event---established"></a>CallState 事件 - 已建立
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/531f3600-481f-41c8-8a75-3e8b2e8e6200/callState",
        "data": {
            "ConversationId": "aHR0cHM6Ly9jb252LXVzc2MtMDIuY29udi5za3lwZS5jb20vY29udi92RFNacTFyTEIwdVotM0dQdjBabUpnP2k9OCZlPTYzNzU1NzQzNzg4NTgzMTgxMQ",
            "CallLegId": "531f3600-481f-41c8-8a75-3e8b2e8e6200",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-05-05T20:08:59.5783985Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="callstate-event---terminating"></a>CallState 事件 - 终止
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/531f3600-481f-41c8-8a75-3e8b2e8e6200/callState",
        "data": {
            "ConversationId": "aHR0cHM6Ly9jb252LXVzc2MtMDIuY29udi5za3lwZS5jb20vY29udi92RFNacTFyTEIwdVotM0dQdjBabUpnP2k9OCZlPTYzNzU1NzQzNzg4NTgzMTgxMQ",
            "CallLegId": "531f3600-481f-41c8-8a75-3e8b2e8e6200",
            "CallState": "Terminating"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-05-05T20:13:45.7398707Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="callstate-event---terminated"></a>CallState 事件 - 已终止
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/531f3600-481f-41c8-8a75-3e8b2e8e6200/callState",
        "data": {
            "ConversationId": "aHR0cHM6Ly9jb252LXVzc2MtMDIuY29udi5za3lwZS5jb20vY29udi92RFNacTFyTEIwdVotM0dQdjBabUpnP2k9OCZlPTYzNzU1NzQzNzg4NTgzMTgxMQ",
            "CallLegId": "531f3600-481f-41c8-8a75-3e8b2e8e6200",
            "CallState": "Terminated"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-05-05T20:13:46.1541814Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="dtmf-received-event"></a>DTMF 接收事件
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/471f3600-4e1f-4cd4-9eec-4a484e4cbf00/dtmf",
        "data": {
            "ToneInfo": {
                "SequenceId": 1,
                "Tone": "Tone1"
            },
            "CallLegId": "471f3600-4e1f-4cd4-9eec-4a484e4cbf00"
        },
        "eventType": "Microsoft.Communication.DtmfReceived",
        "eventTime": "2021-05-05T20:31:00.4818813Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="playaudioresult-event"></a>PlayAudioResult 事件
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/511f3600-401f-4296-b6d0-b8da6f343b00/playAudio",
        "data": {
            "ResultInfo": {
                "Code": 200,
                "Subcode": 0,
                "Message": "Action completed successfully."
            },
            "OperationContext": "6c6cbbc7-66b2-47a8-a29c-5e5f73aee86d",
            "Status": "Completed",
            "CallLegId": "511f3600-401f-4296-b6d0-b8da6f343b00"
        },
        "eventType": "Microsoft.Communication.PlayAudioResult",
        "eventTime": "2021-05-05T20:38:22.0476663Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="cancel-media-processing-event"></a>取消媒体处理事件
```
{
        "id": null,
        "topic": null,
        "subject": "callLeg/471f3600-4e1f-4cd4-9eec-4a484e4cbf00/playAudio",
        "data": {
            "ResultInfo": {
                "Code": 400,
                "Subcode": 8508,
                "Message": "Action falied, the operation was cancelled."
            },
            "OperationContext": "d8aeabf7-47a0-4803-b0cc-6059a708440d",
            "Status": "Completed",
            "CallLegId": "471f3600-4e1f-4cd4-9eec-4a484e4cbf00"
        },
        "eventType": "Microsoft.Communication.PlayAudioResult",
        "eventTime": "2021-05-05T20:31:01.2789071Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="invite-participant-result-event"></a>邀请参与者结果事件
```
{
        "id": "52154ee2-b2ba-420f-b42f-a69c6101c516",
        "topic": null,
        "subject": "callLeg/421f6d00-18fc-4d11-bde6-e5e371494753/inviteParticipantResult",
        "data": {
            "ResultInfo": null,
            "OperationContext": "5dbcbdd4-febf-4091-a5be-543f09b2692c",
            "Status": "Completed",
            "CallLegId": "421f6d00-18fc-4d11-bde6-e5e371494753",
            "Participants": [
                {
                    "RawId": "8:acs:016a7064-0581-40b9-be73-6dde64d69d72_00000009-de04-ee58-740a-113a0d00330d",
                    "CommunicationUser": {
                        "Id": "8:acs:016a7064-0581-40b9-be73-6dde64d69d72_00000009-de04-ee58-740a-113a0d00330d"
                    },
                    "PhoneNumber": null,
                    "MicrosoftTeamsUser": null
                }
            ]
        },
        "eventType": "Microsoft.Communication.InviteParticipantResult",
        "eventTime": "2021-05-05T21:49:52.8138396Z",
        "metadataVersion": null,
        "dataVersion": null
}
```

### <a name="participants-updated-event"></a>参与者更新事件
```
{
    "id": null,
    "topic": null,
    "subject": "callLeg/411f6d00-088a-4ee4-a7bf-c064ac10afeb/participantsUpdated",
    "data": {
        "CallLegId": "411f6d00-088a-4ee4-a7bf-c064ac10afeb",
        "Participants": [
            {
                "Identifier": {
                    "RawId": "8:acs:016a7064-0581-40b9-be73-6dde64d69d72_00000009-7904-f8c2-51b9-a43a0d0010d9",
                    "CommunicationUser": {
                        "Id": "8:acs:016a7064-0581-40b9-be73-6dde64d69d72_00000009-7904-f8c2-51b9-a43a0d0010d9"
                    },
                    "PhoneNumber": null,
                    "MicrosoftTeamsUser": null
                },
                "ParticipantId": "de7539f7-019e-4934-a4c9-9a770e5e07bb",
                "IsMuted": false
            },
            {
                "Identifier": {
                    "RawId": "8:acs:016a7064-0581-40b9-be73-6dde64d69d72_00000009-547e-c56e-71bf-a43a0d002dc1",
                    "CommunicationUser": {
                        "Id": "8:acs:016a7064-0581-40b9-be73-6dde64d69d72_00000009-547e-c56e-71bf-a43a0d002dc1"
                    },
                    "PhoneNumber": null,
                    "MicrosoftTeamsUser": null
                },
                "ParticipantId": "16c3518f-5ff5-4989-8073-39255a71fb58",
                "IsMuted": false
            }
        ]
    },
    "eventType": "Microsoft.Communication.ParticipantsUpdated",
    "eventTime": "2021-04-16T06:26:37.9121542Z",
    "metadataVersion": null,
    "dataVersion": null
}
```
## <a name="out-of-call-apis"></a>Out-of-Call API

> [!NOTE] 
> 所有 Out-of-Call API 中的 conversationID 可以是从客户端获得的 conversationID，也可以是组调用的 groupID。

### <a name="join-call"></a>加入调用
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "join-call"
}-->
```
POST /calling/conversations/{conversationId}/join?api-version={api-version}
Content-Type: application/json

{
  "source: {
    "communicationUser": {
        "id": "string"
      }
  },
  "subject": "string",
  "callbackUri": "string",
  "requestedModalities": [
    "audio"
  ],
  "requestedCallEvents": [
    "participantsUpdated"
  ]
}
```
**C# SDK**

```C#
// Create conversation client 
var connectionString = "YOUR_CONNECTION_STRING";
var conversationClient = new ConversationClient(connectionString);

//Preparing request data
var source = new CommunicationUserIdentifier("<source-identity e.g. 8:acs:guid_guid>");
var createCallOptions = new CreateCallOptions(
    new Uri("<callback-url>"), 
    new List<CallModality> { CallModality.Audio }, 
    new List<EventSubscritionType> { EventSubscritionType.ParticipantsUpdated, EventSubscritionType.DtmfReceived });

//Conversation id for the call. Can be a conversation id or a group id.
var conversationId = "<group-id or base64-encoded-conversation-url>";

//Starting the call
var call = await conversationClient.JoinCallAsync(conversationId, source, createCallOption).ConfigureAwait(false);
string callLegId = call.Value.CallLegId;
```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "callLegId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```

### <a name="invite-participants"></a>邀请参与者
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "invite-participant "
}-->
```
POST /calling/conversations/{conversationId}/participants?api-version={api-version}
Content-Type: application/json

{
  "participants": [
    {
      "communicationUser": {
        "id": "string"
      }
    }
  ],
  "operationContext": "string"
}
```
**C# SDK**
```C#
var invitedParticipants = new List<CommunicationIdentifier>()
{
    new CommunicationUserIdentifier("<communication-user-identity>")
}; 

await conversationClient.InviteParticipantsAsync("<conversation-id>", invitedParticipants, new Uri("<callback-url>"), "<operation-context>").ConfigureAwait(false);

```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
### <a name="remove-participant"></a>删除参与者
#### <a name="request"></a>请求
**HTTP**
<!-- {
  "blockType": "request",
  "name": "remove-participant "
}-->
```
DELETE /calling/conversations/{conversationId}/participants/{participantId}?api-version={api-version}
Content-Type: application/json

```
**C# SDK**

```C#
await conversationClient.RemoveParticipantAsync("<conversationId>", "<participant-id>").ConfigureAwait(false);

```
#### <a name="response"></a>响应
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 202 Accepted
Content-Type: application/json
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "<error-code>",
  "message": "<error-message>",
}
```

## <a name="next-steps"></a>后续步骤
请查看我们的[示例](https://github.com/Azure/communication-preview/tree/master/samples/Server-Calling/IncidentReporter)了解详细信息。
