---
title: 包含文件
description: Java 通话自动化
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 7253be0e9d7723bdb018b53e3ee655188bb3d679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803156"
---
## <a name="prerequisites"></a>先决条件
在开始之前，请务必：
- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/)。
- [Java 开发工具包 (JDK)](/azure/developer/java/fundamentals/java-jdk-install) 11 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../../create-communication-resource.md)。 需要记下你的资源连接字符串以便在本示例中使用。
- 获取新 Azure 通信服务资源的电话号码。 有关详细信息，请参阅[获取电话号码](../../../telephony-sms/get-phone-number.md?pivots=platform-azp)。
- 下载并安装 [ngrok](https://www.ngrok.com/download)。 由于示例在本地运行，ngrok 将允许接收所有事件。
- （可选）创建 Azure 语音资源，以生成要由应用程序播放的自定义消息。 按照[此指南](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free)创建资源。

> [!NOTE]
> 可以在 [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/OutboundCallReminder) 上找到本快速入门的已完成代码。 本示例使用 Microsoft 认知服务语音 SDK。 下载 Microsoft 认知服务语音 SDK，即表示你承认其[许可证](https://aka.ms/csspeech/license201809)。

## <a name="add-the-package-references-for-the-calling-server-sdk"></a>添加通话服务器 SDK 的包引用

在 POM 文件中，使用调用 API 引用 `azure-communication-callingserver` 包：

```java
<dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-callingserver</artifactId>
      <version>1.0.0-beta.2</version>
</dependency>
```

要创建用户标识，应用程序需要引用 `azure-communication-identity` 包：

```java
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.1.1</version>
      <exclusions>
        <exclusion>
          <groupId>com.azure</groupId>
          <artifactId>azure-communication-common</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```

## <a name="object-model"></a>对象模型

以下类用于处理适用于 Java 的 Azure 通信通话服务器 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | 此类用于创建 CallingServerClient 的实例。|
| CallingServerClient | 呼叫功能需要此类。 通过 CallingServerClientBuilder 获取一个实例，并使用它来开始/挂断电话、播放/取消播放音频以及添加/删除参与者 |
| CommunicationIdentityClient | 创建或删除通信用户标识时需要此类。 |

## <a name="create-a-call-client"></a>创建呼叫客户端

通信服务连接字符串和 httpClient 对象必须分别通过 `connectionString()` 和 `httpClient()` 函数传递给 `CallingServerClientBuilder`。 要创建呼叫客户端对象，请使用 `CallingServerClientBuilder` 对象的 `buildClient()` 函数。

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder callClientBuilder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
        .connectionString(this.callConfiguration.ConnectionString);

this.callingServerClient = callClientBuilder.buildClient();
```

## <a name="create-user-identity"></a>创建用户标识

使用 `CommunicationIdentityClientBuilder` 对象并通过 `connectionString()` 函数设置通信服务资源连接字符串以创建 `CommunicationIdentityClient` 对象，然后使用 `createUser` 函数创建源用户标识。

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
CommunicationUserIdentifier user = client.createUser();
```

## <a name="delete-user-identity"></a>删除用户标识

使用 `CommunicationIdentityClient` 对象的 `deleteUser` 函数删除用户标识：

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
client.deleteUser(new CommunicationUserIdentifier(source));    
```

## <a name="create-a-call"></a>创建调用

使用 `CallingServerClient` 对象的 `createCallConnectionWithResponse` 方法开始调用。 `createCallConnectionWithResponse` 方法支持以下参数：
- `source` - 类型为 `CommunicationUserIdentifier` 的调用方的源用户标识。
- `targets`：类型为 `Iterable<CommunicationIdentifier>` 的目标标识列表。
- `options`：类型为 `CreateCallOptions` 的呼叫选项。 下面是 `CreateCallOptions` 方法的参数：

    - `callbackUri`：应用程序的回调 URI。
    - `requestedModalities` - 请求调用方式。 类型为 `Iterable<MediaType>`。 值可以是音频或视频。
    - `requestedCallEvents` - 类型为 `Iterable<EventSubscriptionType>` 的请求回调事件。 使用此参数订阅 `EventSubscriptionType.PARTICIPANTS_UPDATED` 和 `EventSubscriptionType.DTMF_RECEIVED` 事件

- 使用 `CreateCallOptions` 对象的 `setAlternateCallerId()` 函数将源呼叫方 ID 设置为备用呼叫方 ID。

```java
CommunicationUserIdentifier source = new CommunicationUserIdentifier(this.callConfiguration.SourceIdentity);
PhoneNumberIdentifier target = new PhoneNumberIdentifier(targetPhoneNumber);
List<MediaType> callModality = new ArrayList<>() { {add(MediaType.AUDIO);} };
List<EventSubscriptionType> eventSubscriptionType = new ArrayList<>() {
        {add(EventSubscriptionType.PARTICIPANTS_UPDATED); add(EventSubscriptionType.DTMF_RECEIVED);}};

CreateCallOptions createCallOption = new CreateCallOptions(this.callConfiguration.appCallbackUrl,
        callModality, eventSubscriptionType);
createCallOption.setAlternateCallerId(new PhoneNumberIdentifier(this.callConfiguration.sourcePhoneNumber));

Logger.logMessage(Logger.MessageType.INFORMATION,"Performing CreateCall operation");

List<CommunicationIdentifier> targets = new ArrayList<>() { {add(target);} };

Response<CallConnection> response = this.callingServerClient.createCallConnectionWithResponse(source, targets, createCallOption, null);
callConnection = response.getValue();
```

`createCallConnectionWithResponse` 方法返回 `Response<CallConnection>` 的对象，后者用于其他调用操作，如播放音频、取消播放音频和挂断等。

## <a name="play-audio"></a>播放音频

创建调用后，可以使用 `CallConnection` 对象的 `playAudioWithResponse` 方法为被调用方播放音频消息。 `playAudioWithResponse` 方法支持以下参数：

- `audioFileUri` - 包含要播放的消息的音频文件 URI。
- `playAudioOptions` - 用于播放音频的选项。 类型为 `PlayAudioOptions`。 以下是 `PlayAudioOptions` 对象支持的参数：
    - `loop` - 若要重复音频消息，请将其设置为 true。
    - `audioFileId` - AudioFileUri 中媒体的 ID，用于缓存媒体。
    - `operationContext` - 请求上下文的唯一 ID。

```java
// Preparing data for request
String audioFileUri = callConfiguration.audioFileUrl;
Boolean loop = true;
String operationContext = UUID.randomUUID().toString();
String audioFileId = UUID.randomUUID().toString();
PlayAudioOptions playAudioOptions = new PlayAudioOptions();
playAudioOptions.setLoop(loop);
playAudioOptions.setAudioFileId(audioFileId);
playAudioOptions.setOperationContext(operationContext);

Logger.logMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
Response<PlayAudioResult> playAudioResponse = this.callConnection.playAudioWithResponse(audioFileUri, playAudioOptions, null);

PlayAudioResult response = playAudioResponse.getValue();
```

`playAudio` 方法返回 `Response<PlayAudioResult>`，后者可用于使用 `getStatus()` 方法获取 `OperationStatus`。 `OperationStatus` 可以具有以下值：`NotStarted`、`Running`、`Completed` 或 `Failed`。

```java
Logger.logMessage(Logger.MessageType.INFORMATION, "playAudioWithResponse -- > " + GetResponse(playAudioResponse) +
", Id: " + response.getOperationId() + ", OperationContext: " + response.getOperationContext() + ", OperationStatus: " +
response.getStatus().toString());
```

## <a name="add-a-participant-to-the-call"></a>添加呼叫参与者

使用 `CallConnection` 对象的 `addParticipantWithResponse` 方法添加呼叫参与者。 `addParticipantWithResponse` 方法支持以下参数：

- `participant` - 类型为 `CommunicationUserIdentifier` 或 `PhoneNumberIdentifier` 的参与者标识符。
- `alternateCallerId` - 源呼叫方 ID。
- `operationContext` - 请求上下文的唯一 ID。

```java
// Preparing data for request
CommunicationIdentifier participant = null;
String operationContext = UUID.randomUUID().toString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity) {
    participant = new CommunicationUserIdentifier(addedParticipant);

} else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity) {
    participant = new PhoneNumberIdentifier(addedParticipant);
}

Response<AddParticipantResult> response = callConnection.addParticipantWithResponse(participant, this.callConfiguration.sourcePhoneNumber, operationContext, null);
```

## <a name="cancel-media-processing"></a>取消媒体处理

使用 `CallConnection` 对象的 `cancelAllMediaOperationsWithResponse` 方法取消播放音频操作。 `cancelAllMediaOperationsWithResponse` 方法支持以下参数：

- `operationContext` - 请求上下文的唯一 ID。

```java
String operationContext = UUID.randomUUID().toString();
Response<CancelAllMediaOperationsResult> cancelmediaresponse = this.callConnection.cancelAllMediaOperationsWithResponse(operationContext, null);
```

## <a name="hang-up-the-call"></a>挂断电话

使用 `CallConnection` 对象的 `hangupWithResponse` 方法挂断电话。

```java
Response<Void> response = this.callConnection.hangupWithResponse(null);
```