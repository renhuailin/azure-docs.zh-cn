---
title: include 文件
description: 包含文件
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: f025f33a041dd40970b3ea34ff723e1270d16ea6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802435"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ServerRecording) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>必备知识

- 创建具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js（12.18.4 和更高版本）](https://nodejs.org/en/download/)。
- [Visual Studio（2019 和更高版本）](https://visualstudio.microsoft.com/vs/)。
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)（请确保安装与 Visual Studio 实例相对应的版本：32 位或 64 位）。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../../create-communication-resource.md)。 需要为此快速入门记录资源连接字符串。
- 一个 Azure 存储帐户和容器，有关详细信息，请参阅[创建存储帐户](../../../../../storage/common/storage-account-create.md?tabs=azure-portal)。 需要记录你的存储连接字符串和容器名称，以便在本快速入门中使用 。
- 一个 [Azure 事件网格](../../../../../event-grid/overview.md) Webhook。

## <a name="object-model"></a>对象模型

以下类处理 C# 记录服务器应用的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | 记录功能需要此类。 你将使用 ACS 资源连接字符串创建一个 CallingServerClient 的实例，然后使用该实例来启动/停止和暂停/继续通话记录。 |

## <a name="getting-servercallid-as-a-requirement-for-call-recording-server-apis"></a>获取通话记录服务器 API 必需的 serverCallId

> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用“beta”版 ACS 呼叫 Web SDK。 [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview) 上提供了包含记录流的客户端示例。

通话记录是核心呼叫 API 的扩展功能。 首先需要获取录制功能 API 对象：

```JavaScript
const callRecordingApi = call.api(Features.Recording);
```

订阅记录更改：

```JavaScript
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```

获取可用于启动/停止/暂停/继续记录会话的服务器呼叫 ID：

接通呼叫后，使用 `getServerCallId` 方法获取服务器呼叫 ID。

```JavaScript
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```

## <a name="create-a-calling-server-client"></a>创建呼叫服务器客户端

若要创建呼叫服务器客户端，需使用 Azure 通信服务连接字符串并将其传递给呼叫服务器客户端对象。

```csharp
CallingServerClient callingServerClient = new CallingServerClient("<Resource_Connection_String>");
```

## <a name="start-recording-session-using-startrecordingasync-server-api"></a>使用“StartRecordingAsync”服务器 API 启动记录会话

使用在发起呼叫期间收到的服务器呼叫 ID。

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>").ConfigureAwait(false);
```
`StartRecordingAsync` API 响应包含记录会话的记录 ID。

## <a name="stop-recording-session-using-stoprecordingasync-server-api"></a>使用“StopRecordingAsync”服务器 API 停止记录会话

使用在 `StartRecordingAsync` 响应中收到的记录 ID。

```csharp
 var stopRecording = await callingServerClient.InitializeServerCall("<servercallid>").StopRecordingAsync("<recordingid>").ConfigureAwait(false);
```

## <a name="pause-recording-session-using-pauserecordingasync-server-api"></a>使用“PauseRecordingAsync”服务器 API 暂停记录会话

使用在 `StartRecordingAsync` 响应中收到的记录 ID。

```csharp
var pauseRecording = await callingServerClient.InitializeServerCall("<servercallid>").PauseRecordingAsync("<recordingid>");
```

## <a name="resume-recording-session-using-resumerecordingasync-server-api"></a>使用“ResumeRecordingAsync”服务器 API 继续记录会话

使用在 `StartRecordingAsync` 响应中收到的记录 ID。

```csharp
var resumeRecording = await callingServerClient.InitializeServerCall("<servercallid>").ResumeRecordingAsync("<recordingid>");
```

## <a name="download-recording-file-using-downloadstreamingasync-server-api"></a>使用“DownloadStreamingAsync”服务器 API 下载记录文件

当记录的媒体可供下载时，请使用 [Azure 事件网格](../../../../../event-grid/overview.md) Webhook 或其他触发的操作来通知服务。

下面是事件架构的示例。

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "contentLocation": string, //ACS URL where the content is located
                    "metadataLocation": string, // ACS URL where the metadata for this chunk is located
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```

使用 `DownloadStreamingAsync` API 下载记录的媒体。

```csharp
var recordingDownloadUri = new Uri(downloadLocation);
var response = callingServerClient.DownloadStreamingAsync(recordingDownloadUri);
```
可以从 `recordingChunk` 的 `contentLocation` 属性提取记录内容的 downloadLocation。 `DownloadStreamingAsync` 方法返回 `Response<Stream>` 类型（包含下载的内容）的响应。