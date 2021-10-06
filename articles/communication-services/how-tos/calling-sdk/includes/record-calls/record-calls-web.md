---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7c739c65e638657cffbfade0c2a491cdefd6b626
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698920"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### <a name="record-calls"></a>录制通话
> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

通话录制是核心 `Call` API 的扩展功能。 首先需要获取录制功能 API 对象：

```js
const callRecordingApi = call.api(Features.Recording);
```

然后，若要检查是否正在录制通话，请检查 `callRecordingApi` 的 `isRecordingActive` 属性。 它将返回 `Boolean`。

```js
const isRecordingActive = callRecordingApi.isRecordingActive;
```

还可以订阅录制更改：

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```