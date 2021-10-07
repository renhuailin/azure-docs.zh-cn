---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 029e4ce2f699eb66f0807f47cc80a893052f7a57
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585278"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="record-calls"></a>录制通话
> [!WARNING]
> 1\.1.0 和 1.1.0-beta.1（beta 版）之前版本的 ACS Calling iOS SDK 使用 `isRecordingActive` 作为 `Call` 对象的一部分，并且 `didChangeRecordingState` 属于 `CallDelegate` 委托的一部分。 对于新的 Beta 版本，这些 API 已作为 `Call` 的扩展功能移动，如下所述。
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS Calling iOS SDK 的 beta 版本

通话录制是核心 `Call` API 的扩展功能。 首先需要获取录制功能 API 对象：

```swift
let callRecordingFeature = call.api(Features.recording)
```

然后，若要检查是否正在录制通话，请检查 `callRecordingFeature` 的 `isRecordingActive` 属性。 它将返回 `Bool`。

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

还可以通过使用事件 `RecordingCallFeatureDelegate` 在类层级实现 `didChangeRecordingState` 委托，以订阅录制更改：

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingCallFeatureDelegate
public func recordingCallFeature(_ recordingCallFeature: RecordingCallFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

如果要从应用程序开始录制，请先按照[通话记录概述](../../../../concepts/voice-video-calling/call-recording.md)的步骤设置通话记录。

在服务器上设置通话录制后，需要使用 iOS 应用程序从通话中调用 `ServerCallId` 值，然后将该值发送到服务器以启动录制过程。 可以使用 `CallInfo` 类（可使用 `getInfo()` 在类对象中找到）中的 `getServerCallId()` 查找 `ServerCallId` 值。

```swift
// Send serverCallId to your recording server to start the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

当从服务器开始录制时，将触发事件 `didChangeRecordingState`，并且 `recordingFeature.isRecordingActive` 的值为 `true`。

与启动通话记录一样，如果想要停止通话记录，需要获取 `ServerCallId` 并将其发送到录制服务器，这样服务器就能停止通话记录了。

```swift
// Send serverCallId to your recording server to stop the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

当服务器停止录制时，将触发事件 `didChangeRecordingState`，并且 `recordingFeature.isRecordingActive` 的值为 `false`。
