---
title: 包含文件
description: C#
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 6294759038112d5ca9b87818816ab4bb24d0f613
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473175"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/)。
- [Visual Studio（2019 和更高版本）](https://visualstudio.microsoft.com/vs/)。
- [.NET Core 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472)（请确保安装与 Visual Studio 实例相对应的版本：32 位或 64 位）。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource)。 需要记下你的资源连接字符串以便在本示例中使用。
- 获取新 Azure 通信服务资源的电话号码。 有关详细信息，请参阅[获取电话号码](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/get-phone-number?pivots=platform-azp)。
- 下载并安装 [ngrok](https://www.ngrok.com/download)。 由于示例在本地运行，ngrok 将允许接收所有事件。
- （可选）创建 Azure 语音资源，以生成要由应用程序播放的自定义消息。 按照[此指南](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free)创建资源。

> [!NOTE]
> 可以在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/OutboundCallReminder) 上找到本快速入门的已完成代码。 本示例使用 Microsoft 认知服务语音 SDK。 下载 Microsoft 认知服务语音 SDK 即表示你同意其[许可条款](https://aka.ms/csspeech/license201809)。

## <a name="object-model"></a>对象模型

以下类用于处理适用于 C# 的 Azure 通信呼叫服务器 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | 呼叫功能需要此类。 你将使用通信服务资源连接字符串创建 CallingServerClient 的实例，然后使用该实例来开始/结束呼叫、播放/取消音频和添加/删除参与者 |
| CommunicationIdentityClient | 创建或删除通信用户标识时需要此类。 |

## <a name="create-a-call-client"></a>创建呼叫客户端

若要创建呼叫客户端，需使用通信服务连接字符串并将其传递给呼叫客户端对象。

```csharp
this.CallClient = new CallingServerClient("<Connection_String>");
```

## <a name="create-user-identity"></a>创建用户标识

使用通信服务资源连接字符串创建 `CommunicationIdentityClient` 对象，然后使用 `CreateUserAsync` 方法创建源用户标识。

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
var user = await client.CreateUserAsync().ConfigureAwait(false);
```

## <a name="delete-user-identity"></a>删除用户标识

使用 `CommunicationIdentityClient` 对象的 `DeleteUserAsync` 方法删除用户标识：

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
await client.DeleteUserAsync(new CommunicationUserIdentifier(source)).ConfigureAwait(false);
```

## <a name="create-a-call"></a>创建呼叫

使用 `CallingServerClient` 对象的 `CreateCallConnectionAsync` 方法开始呼叫。 下面是 `CreateCallConnectionAsync` 方法的参数：
- `source`：类型为 `CommunicationIdentifier` 的呼叫方的源用户标识。
- `targets`：类型为 `IEnumerable<CommunicationIdentifier>` 的目标标识列表。
- `options`：类型为 `CreateCallOptions` 的呼叫选项。 下面是 `CreateCallOptions` 方法的参数：

    - `callbackUri`：应用程序的回调 URI。
    - `requestedModalities`：请求呼叫方式。 类型为 `IEnumerable<MediaType>`。 值可以是 Audio 或 Video。
    - `requestedCallEvents`：请求的回调事件。 其类型为 `IEnumerable<EventSubscriptionType>`。 使用此参数订阅 `EventSubscriptionType.ParticipantsUpdated` 和 `EventSubscriptionType.DtmfReceived` 事件

- `cancellationToken`：取消标记。 类型为 `CancellationToken`。

```csharp
var source = new CommunicationUserIdentifier(callConfiguration.SourceIdentity);
var target = new PhoneNumberIdentifier(targetPhoneNumber);
var createCallOption = new CreateCallOptions(
    new Uri(callConfiguration.AppCallbackUrl),
    new List<MediaType> { MediaType.Audio },
    new List<EventSubscriptionType> { EventSubscriptionType.ParticipantsUpdated, EventSubscriptionType.DtmfReceived }
    );
createCallOption.AlternateCallerId = new PhoneNumberIdentifier(callConfiguration.SourcePhoneNumber);

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing CreateCall operation");
var call = await callClient.CreateCallConnectionAsync(source,
    new List<CommunicationIdentifier>() { target },
    createCallOption, reportCancellationToken)
    .ConfigureAwait(false);
```

`CreateCallConnectionAsync` 方法返回 `CallConnection` 对象，后者可用于播放音频、取消播放音频和挂断等其他调用操作。

## <a name="play-audio"></a>播放音频

创建呼叫连接后，可以使用 `CallConnection` 对象的 `PlayAudioAsync` 方法为被呼叫方播放音频消息。 `PlayAudioAsync` 方法支持以下参数：

- `options`（必需）：音频播放选项。 类型为 `PlayAudioOptions`。 `PlayAudioOptions` 对象支持以下参数：
    - `AudioFileUri`（必需）：包含要播放的消息的音频文件的 URI。
    - `OperationContext`（必需）：请求上下文的唯一 ID。
    - `Loop`（可选）：设置为 true 会重复音频消息。
- `cancellationToken`：取消标记。 类型为 `CancellationToken`。

```csharp
// Preparing data for request
var playAudioRequest = new PlayAudioOptions()
{
    AudioFileUri = new Uri(callConfiguration.AudioFileUrl),
    OperationContext = Guid.NewGuid().ToString(),
    Loop = true,
};

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
var response = await callConnection.PlayAudioAsync(playAudioRequest, reportCancellationToken).ConfigureAwait(false);
```

`PlayAudioAsync` 方法返回 `PlayAudioResponse`，后者可用于获取 `OperationStatus`。 `OperationStatus` 可使用以下值：`NotStarted`、`Running`、`Completed` 或 `Failed`。

```csharp
Logger.LogMessage(Logger.MessageType.INFORMATION, $"Play Audio state: {response.Value.Status}");
```

## <a name="add-a-participant-to-the-call"></a>添加呼叫参与者

使用 `CallConnection` 对象的 `AddParticipantAsync` 方法添加呼叫参与者。 `AddParticipantAsync` 方法支持以下参数：

- `participant`：类型为 `CommunicationUserIdentifier` 或 `PhoneNumberIdentifier` 的参与者标识符。
- `alternateCallerId`：源呼叫方 ID。
- `operationContext`：请求上下文的唯一 ID。
- `cancellationToken`（可选）：取消标记。


```csharp
// Preparing data for request
var operationContext = Guid.NewGuid().ToString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity)
{
    var response = await callConnection.AddParticipantAsync(new CommunicationUserIdentifier(addedParticipant), null, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity)
{
    var response = await callConnection.AddParticipantAsync(new PhoneNumberIdentifier(addedParticipant), callConfiguration.SourcePhoneNumber, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
```

## <a name="cancel-media-processing"></a>取消媒体处理

使用 `CallConnection` 对象的 `CancelAllMediaOperationsAsync` 方法取消播放音频操作。 下面是 `CancelAllMediaOperationsAsync` 方法支持的参数：

- `operationContext`：请求上下文的唯一 ID。
- `reportCancellationToken`：取消标记。 类型为 `CancellationToken`。

```csharp
var operationContext = Guid.NewGuid().ToString();
var response = await callConnection.CancelAllMediaOperationsAsync(operationContext, reportCancellationToken).ConfigureAwait(false);
```

## <a name="hang-up-the-call"></a>挂断呼叫

使用 `CallConnection` 对象的 `HangupAsync` 方法挂断呼叫。 下面是 `HangupAsync` 方法支持的参数：

- `reportCancellationToken`：取消标记。 类型为 `CancellationToken`。

```csharp
var hangupResponse = await callConnection.HangupAsync(reportCancellationToken).ConfigureAwait(false);
```