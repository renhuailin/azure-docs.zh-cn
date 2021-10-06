---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: d2011720b203493b5b17fabb3d65ca912e7ec37d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585194"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!WARNING]
> 1\.1.0 和 1.1.0-beta.1（beta 版）之前版本的 ACS Calling iOS SDK 使用 `isTranscriptionActive` 作为 `Call` 对象的一部分，并且 `didChangeTranscriptionState` 属于 `CallDelegate` 委托的一部分。 对于新的 Beta 版本，这些 API 已作为 `Call` 的扩展功能移动，如下所述。
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS Calling iOS SDK 的 beta 版本

通话听录是核心 `Call` API 的扩展功能。 首先需要获取听录功能 API 对象：

```swift
let callTranscriptionFeature = call.api(Features.transcription)
```

然后，若要确认是否正在听录通话，请检查 `callTranscriptionFeature` 的 `isTranscriptionActive` 属性。 它将返回 `Bool`。

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

还可以通过使用事件 `TranscriptionCallFeatureDelegate` 在类层级实现 `didChangeTranscriptionState` 委托，以订阅听录更改：

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionCallFeatureDelegate
public func transcriptionCallFeature(_ transcriptionCallFeature: TranscriptionCallFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive
}
```
