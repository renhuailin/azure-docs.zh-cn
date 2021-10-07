---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: a12f5717d927e8358a503a50f314c6b80f577952
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584990"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="record-calls"></a>录制通话
> [!WARNING]
> 在 ACS 呼叫 Android SDK 的 1.1.0 版和 beta 版 1.1.0-beta.1 之前，`isRecordingActive` 和 `addOnIsRecordingActiveChangedListener` 是 `Call` 对象的一部分。 对于新的 Beta 版本，这些 API 已作为 `Call` 的扩展功能移动，如下所述。

> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 要使用此 API，请使用“beta”版 ACS 呼叫 Android SDK

通话录制是核心 `Call` API 的扩展功能。 首先需要获取录制功能 API 对象：

```java
RecordingCallFeature callRecordingFeature = call.api(Features.RECORDING);
```

然后，若要检查是否正在录制通话，请检查 `callRecordingFeature` 的 `isRecordingActive` 属性。 它将返回 `boolean`。

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

还可以订阅录制更改：

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);
```

如果要从应用程序开始录制，请先按照[通话记录概述](../../../../concepts/voice-video-calling/call-recording.md)的步骤设置通话记录。

在服务器上设置通话记录后，需要从 Android 应用程序中获取来自通话的 `ServerCallId` 值，然后将其发送到服务器以开始录制过程。 可以使用 `CallInfo` 类（可使用 `getInfo()` 在类对象中找到）中的 `getServerCallId()` 查找 `ServerCallId` 值。

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

当从服务器开始录制时，将触发事件 `handleCallOnIsRecordingChanged`，并且 `callRecordingFeature.isRecordingActive()` 的值为 `true`。

与启动通话记录一样，如果想要停止通话记录，需要获取 `ServerCallId` 并将其发送到录制服务器，这样服务器就能停止通话记录了。

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {

} catch (UnsupportedOperationException unsupportedOperationException) {

}
```

当服务器停止录制时，将触发事件 `handleCallOnIsRecordingChanged`，并且 `callRecordingFeature.isRecordingActive()` 的值为 `false`。
