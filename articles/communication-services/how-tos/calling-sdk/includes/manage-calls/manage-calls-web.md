---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 3ab9331dddb90803867f6cfb5118aa33bdd313d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698910"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="place-a-call"></a>拨打电话

若要创建并启动通话，请在 `callAgent` 上使用其中一个 API，并提供你通过通信服务标识 SDK 创建的用户。

创建和发起呼叫的操作是同步的。 可以通过 `call` 实例订阅通话事件。

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>向用户或 PSTN 发起一对 N 通话

若要呼叫另一个通信服务用户，请在 `callAgent` 上使用 `startCall` 方法，并传递你[使用通信服务管理库创建的](../../../../quickstarts/access-tokens.md)接收人的 `CommunicationUserIdentifier`。

对于与用户之间的一对一通话，请使用以下代码：

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

若要向公共交换电话网络 (PSTN) 发起通话，请在 `callAgent` 上使用 `startCall` 方法并传递接收方的 `PhoneNumberIdentifier`。 必须将通信服务资源配置为允许 PSTN 通话。

呼叫 PSTN 号码时，需要指定备用呼叫方 ID。 备用呼叫方 ID 是 PSTN 通话中用于标识呼叫方的电话号码（基于 E.164 标准）。 这是通话接收方看到的来电的电话号码。

> [!NOTE]
> PSTN 通话目前提供个人预览版。 若要进行访问，[请申请早期采用者计划](https://aka.ms/ACS-EarlyAdopter)。

对于与 PSTN 号码之间的一对一通话，请使用以下代码：
```js
const pstnCallee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

对于与用户和 PSTN 号码之间的一对 N 通话，请使用以下代码：

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="join-a-group-call"></a>加入群组通话

> [!NOTE]
> `groupId` 参数被视为系统元数据，Microsoft 可能会使用它来执行运行系统所需的操作。 不要在 `groupId` 值中包含个人数据。 Microsoft 不会将此参数视为个人数据，其内容可能会显示给 Microsoft 员工，也可能会被长期存储。
>
> `groupId` 参数要求数据采用 GUID 格式。 建议使用随机生成的 GUID，这些 GUID 在系统中不会被视为个人数据。
>

若要发起新的群组通话或加入正在进行的群组通话，请使用 `join` 方法并传递带有 `groupId` 属性的对象。 `groupId` 值必须为 GUID。

```js
const context = { groupId: '<GUID>'};
const call = callAgent.join(context);
```

## <a name="receive-an-incoming-call"></a>接听来电

当登录的标识收到来电时，`callAgent` 实例会发出 `incomingCall` 事件。 若要侦听此事件，请使用以下选项之一进行订阅：

```js
const incomingCallHandler = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHandler);
```

`incomingCall` 事件包括你可以接受或拒绝的一个 `incomingCall` 实例。

开始/加入/接受视频通话时，如果指定的视频相机设备被另一个进程占用或在系统中被禁用，则通话将以视频关闭的形式开始，并且将引发 cameraStartFailed: true 通话诊断。

## <a name="mute-and-unmute"></a>静音和取消静音

若要使本地终结点静音或取消静音，可使用 `mute` 和 `unmute` 异步 API：

```js
//mute local device
await call.mute();

//unmute local device
await call.unmute();
```

## <a name="manage-remote-participants"></a>管理远程参与者

所有远程参与者均以 `RemoteParticipant` 类型表示，可通过通话实例上的 `remoteParticipants` 集合获得。

### <a name="list-the-participants-in-a-call"></a>列出通话参与者

`remoteParticipants` 集合返回通话中远程参与者的列表：

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>向通话添加参与者

若要向通话添加参与者（用户或电话号码），可以使用 `addParticipant`。 提供 `Identifier` 类型之一。 它将同步返回 `remoteParticipant` 实例。 当参与者成功添加到通话中时，通话中将引发 `remoteParticipantsUpdated` 事件。

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>删除通话参与者

若要从通话中删除参与者（用户或电话号码），可以调用 `removeParticipant`。 你必须传递 `Identifier` 类型之一。 从通话中删除参与者后，此方法异步进行解析。 还将从 `remoteParticipants` 集合中删除该参与者。

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

### <a name="access-remote-participant-properties"></a>访问远程参与者属性

远程参与者有一组关联的属性和集合：

- `CommunicationIdentifier`：获取远程参与者的标识符。 标识是 `CommunicationIdentifier` 类型之一：

    ```js
    const identifier = remoteParticipant.identifier;
    ```

它可能是下列 `CommunicationIdentifier` 类型之一：

- `{ communicationUserId: '<ACS_USER_ID'> }`：一个对象，表示 ACS 用户。
- `{ phoneNumber: '<E.164>' }`：一个对象，表示采用 E.164 格式的电话号码。
- `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`：一个对象，表示 Teams 用户。
- `{ id: string }`：一个对象，表示不属于其他标识符类型的标识符

- `state`：获取远程参与者的状态。

    ```js
    const state = remoteParticipant.state;
    ```

此状态可能是：

- `Idle`：初始状态。
- `Connecting`：参与者正在连接到通话时的过渡状态。
- `Ringing`：参与者电话正在响铃。
- `Connected`：参与者已连接到通话。
- `Hold`：参与者已暂停通话。
- `EarlyMedia`：在参与者连接到通话之前播放的通知。
- `InLobby`：指示远程参与者位于会议厅中。
- `Disconnected`：最终状态。 参与者已断开通话连接。 如果远程参与者断开了其网络连接，则两分钟后其状态将变为 `Disconnected`。

- `callEndReason`：若要了解参与者退出通话的原因，请检查 `callEndReason` 属性：

    ```js
    const callEndReason = remoteParticipant.callEndReason;
    const callEndReasonCode = callEndReason.code // (number) code associated with the reason
    const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
    ```

- `isMuted` 状态：若要了解远程参与者是否已静音，请检查 `isMuted` 属性。 它将返回 `Boolean`。

    ```js
    const isMuted = remoteParticipant.isMuted;
    ```

- `isSpeaking` 状态：若要了解远程参与者是否正在讲话，请检查 `isSpeaking` 属性。 它将返回 `Boolean`。

    ```js
    const isSpeaking = remoteParticipant.isSpeaking;
    ```

- `videoStreams`：若要检查给定参与者在此通话中发送的所有视频流，请检查 `videoStreams` 集合。 它包含 `RemoteVideoStream` 对象。

    ```js
    const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
    ```
- `displayName`：若要获取此远程参与者的显示名称，请检查 `displayName` 属性。它将返回字符串。 

    ```js
    const displayName = remoteParticipant.displayName;
    ```

## <a name="check-call-properties"></a>检查通话属性

获取通话的唯一 ID（字符串）：

```js
const callId: string = call.id;
```
获取通话信息：
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本
```js
const callInfo = call.info;
```

通过检查“call”实例上的 `remoteParticipants` 集合了解通话中的其他参与者：

```js
const remoteParticipants = call.remoteParticipants;
```

识别来电的呼叫方：
```js
const callerIdentity = call.callerInfo.identifier;
```

`identifier` 是 `CommunicationIdentifier` 类型之一。

获取通话状态：

```js
const callState = call.state;
```

这会返回一个表示当前通话状态的字符串：

- `None`：初始通话状态。
- `Connecting`：拨打或接听电话后的初始过渡状态。
- `Ringing`：对于去电，表示远程参与者的电话正在响铃。 在远程参与者端，它是 `Incoming`。
- `EarlyMedia`：表示在接通电话前播放通知的状态。
- `Connected`：指示通话已连接。
- `LocalHold`：指示通话被本地参与者暂停。 本地终结点与远程参与者之间没有媒体流动。
- `RemoteHold`：指示通话被远程参与者暂停。 本地终结点与远程参与者之间没有媒体流动。
- `InLobby`：指示用户位于会议厅中。
- `Disconnecting`：在通话进入 `Disconnected` 状态之前的过渡状态。
- `Disconnected`：最终通话状态。 如果网络连接断开，则两分钟后状态将变为 `Disconnected`。

检查 `callEndReason` 属性来查明通话结束的原因：

```js
const callEndReason = call.callEndReason;
const callEndReasonCode = callEndReason.code // (number) code associated with the reason
const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
```

通过检查 `direction` 属性了解当前通话是来电还是去电。 它将返回 `CallDirection`。

```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

检查当前麦克风是否已静音。 它将返回 `Boolean`。

```js
const muted = call.isMuted;
```

通过检查 `isScreenSharingOn` 属性了解屏幕共享流是否来自给定终结点。 它将返回 `Boolean`。

```js
const isScreenSharingOn = call.isScreenSharingOn;
```

通过查看 `localVideoStreams` 集合检查活动视频流。 它返回 `LocalVideoStream` 对象。

```js
const localVideoStreams = call.localVideoStreams;
```