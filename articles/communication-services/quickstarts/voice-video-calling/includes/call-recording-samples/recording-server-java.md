---
title: 包含文件
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
ms.openlocfilehash: 7208519302ea9c12a9a0db7c3cee7032eab85d64
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339604"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/ServerRecording) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>必备知识

- 创建具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js（12.18.4 及更高版本）](https://nodejs.org/en/download/)
- [Java 开发工具包 (JDK)](/azure/developer/java/fundamentals/java-jdk-install) 11 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- [Spring Boot Framework v2.5.0](https://spring.io/projects/spring-boot)
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource)。 需要为此快速入门记录资源连接字符串。
- 一个 Azure 存储帐户和容器，有关详细信息，请参阅[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)。 需要记录你的连接字符串和容器名称，以便在本快速入门中使用 。
- 一个 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview) Webhook。

## <a name="object-model"></a>对象模型

以下类处理 Java 记录服务器应用的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | 此类用于创建 CallingServerClient 的实例。|
| CallingServerClient | 呼叫功能需要此类。 通过 CallingServerClientBuilder 获取一个实例，并使用它来开始/挂断电话、播放/取消播放音频以及添加/删除参与者 |

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

若要创建呼叫服务器客户端，需使用通信服务连接字符串并将其传递给呼叫服务器客户端对象。

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder builder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
 .connectionString(connectionString);
 callingServerClient = builder.buildClient();
```

## <a name="start-recording-session-using-startrecordingwithresponse-server-api"></a>使用“startRecordingWithResponse”服务器 API 启动记录会话

使用在发起呼叫期间收到的服务器呼叫 ID。

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");

Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri),null);
```
`startRecordingWithResponse` API 响应包含记录会话的记录 ID。

## <a name="stop-recording-session-using-stoprecordingwithresponse-server-api"></a>使用“stopRecordingWithResponse”服务器 API 停止记录会话

使用在 `startRecordingWithResponse` 响应中收到的记录 ID。

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.stopRecordingWithResponse(recordingId, null);
```

## <a name="pause-recording-session-using-pauserecordingwithresponse-server-api"></a>使用“pauseRecordingWithResponse”服务器 API 暂停记录会话

使用在 `startRecordingWithResponse` 响应中收到的记录 ID。

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.pauseRecordingWithResponse(recordingId, null);
```

## <a name="resume-recording-session-using-resumerecordingwithresponse-server-api"></a>使用“resumeRecordingWithResponse”服务器 API 继续记录会话

使用在 `startRecordingWithResponse` 响应中收到的记录 ID。

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.resumeRecordingWithResponse(serverCallId, null);
```

## <a name="download-recording-file-using-downloadtowithresponse-server-api"></a>使用“downloadToWithResponse”服务器 API 下载记录文件

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
使用 `CallingServerClient` 类的 `downloadToWithResponse` 方法下载记录的媒体。 下面是 `downloadToWithResponse` 方法支持的参数：

- `contentLocation`：内容所在的 ACS URL。
- `destinationPath`：文件位置。
- `parallelDownloadOptionss`：可选的 ParallelDownloadOptions 对象，用于修改并行下载的工作方式。
- `overwrite`：如果为 True，则会覆盖文件（如果该文件存在）。
- `context`：表示请求上下文的上下文。

```Java
Boolean overwrite = true;
ParallelDownloadOptions parallelDownloadOptions = null;
Context context = null;

String filePath = String.format(".\\%s.%s", documentId, fileType);
Path destinationPath = Paths.get(filePath);

Response<Void> downloadResponse = callingServerClient.downloadToWithResponse(contentLocation, destinationPath, parallelDownloadOptions, overwrite, context);
```
对于每个 `recordingChunk`，可以分别从 `contentLocation` 和 `documentId` 字段提取记录文件的内容位置和文档 ID。