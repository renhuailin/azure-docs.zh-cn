---
title: 加入 Teams 会议
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 加入 Teams 会议。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: a2581efd7d3de01451012badfbfb459002b5d9a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635672"
---
# <a name="join-a-teams-meeting"></a>加入 Teams 会议

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Azure 通信服务 SDK 允许用户加入定期 Microsoft Teams 会议。 下面是操作方法！

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../quickstarts/access-tokens.md)。
- 可选：完成快速入门[向应用程序添加语音呼叫](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

> [!NOTE]
> 此 API 以预览状态提供给开发者，可能根据我们收到的反馈更改。 请勿在生产环境中使用此 API。 若要使用此 API，请使用 ACS 通话 Web SDK 的 beta 版本

若要加入 Teams 会议，请使用 `join` 方法并传递会议链接或会议坐标。

使用会议链接加入：

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

使用会议坐标加入：

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```
## <a name="next-steps"></a>后续步骤
- [了解如何管理通话](./manage-calls.md)
- [了解如何管理视频](./manage-video.md)
- [了解如何转移呼叫](./transfer-calls.md)