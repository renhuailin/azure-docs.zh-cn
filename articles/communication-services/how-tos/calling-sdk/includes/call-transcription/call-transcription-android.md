---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: b880f8a8f84247c14a0f1e81577d9e252b2b78c8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585336"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> 在 ACS 呼叫 Android SDK 的 1.1.0 版和 beta 版 1.1.0-beta.1 之前，`isTranscriptionActive` 和 `addOnIsTranscriptionActiveChangedListener` 是 `Call` 对象的一部分。 对于新的 Beta 版本，这些 API 已作为 `Call` 的扩展功能移动，如下所述。

> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 要使用此 API，请使用“beta”版 ACS 呼叫 Android SDK

通话听录是核心 `Call` API 的扩展功能。 首先需要获取听录功能 API 对象：

```java
TranscriptionCallFeature callTranscriptionFeature = call.api(Features.TRANSCRIPTION);
```

然后，若要检查是否正在停录通话，请检查 `callTranscriptionFeature` 的 `isTranscriptionActive` 属性。 它将返回 `boolean`。

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

还可以订阅听录中的更改：

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);
```
