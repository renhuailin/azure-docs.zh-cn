---
title: 为呼叫启用推送通知。
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 为呼叫启用推送通知。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 87e9aa1741808e60af14eb32e1837bc87c280062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635710"
---
# <a name="enable-push-notifications-for-calls"></a>为呼叫启用推送通知

在这里，我们将了解如何为 Azure 通信服务呼叫启用推送通知。 设置这些选项将使你的用户知道他们何时具有传入呼叫，然后他们可以应答。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../quickstarts/create-communication-resource.md)。
- 用于启用通话客户端的用户访问令牌。 有关详细信息，请参阅[创建和管理访问令牌](../../quickstarts/access-tokens.md)。
- 可选：完成快速入门[向应用程序添加语音呼叫](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Enable push notifications Android](./includes/push-notifications/push-notifications-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Enable push notifications iOS](./includes/push-notifications/push-notifications-ios.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤
- [了解如何订阅事件](./events.md)
- [了解如何管理通话](./manage-calls.md)
- [了解如何管理视频](./manage-video.md)
