---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0a3e3d80e5b669884d19d5eb60260a31b1836de3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698917"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

呼叫转移是核心 `Call` API 的扩展功能。 首先需要获取转移功能 API 对象：

```js
const callTransferApi = call.api(Features.Transfer);
```

呼叫转移涉及三个参与方：

- 转移方：发起转移请求的人员。
- 被转移方：被转移的人员。
- 转移目标：被转移到的人员。

转移遵循以下步骤：

1. 转移方和被转移方之间已经存在接通的电话。  转移方决定将呼叫从被转移方转移到转移目标。
1. 转移方调用 `transfer` API。
1. 被转移方通过使用 `transferRequested` 事件来决定是 `accept` 还是 `reject` 到转移目标的转移请求。
1. 只有当被转移方接受转移请求时，转移目标才会收到来电。

若要转移当前呼叫，可以使用 `transfer` API。 `transfer` 接受可选的 `transferCallOptions`，这允许你设置 `disableForwardingAndUnanswered` 标志：

- `disableForwardingAndUnanswered = false`：如果转移目标未接听转移的呼叫，则转移方将遵循转移目标的转发设置和未接听设置。 
- `disableForwardingAndUnanswered = true`：如果转移目标未接听转移的呼叫，则转移尝试结束。

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer` API 允许你订阅 `transferStateChanged` 和 `transferRequested` 事件。 `transferRequested` 事件来自 `call` 实例；`transferStateChanged` 事件与转移 `state` 和 `error` 来自 `transfer` 实例。

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

被转移方可以通过在 `transferRequestedEventArgs` 中使用 `accept()` 或 `reject()` 来接受或拒绝转移方在 `transferRequested` 事件中发起的转移请求。  你可以在 `transferRequestedEventArgs` 中访问 `targetParticipant` 信息和 `accept` 或 `reject` 方法。

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```