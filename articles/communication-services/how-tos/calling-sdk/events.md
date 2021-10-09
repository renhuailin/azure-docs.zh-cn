---
title: 订阅 SDK 事件
titleSuffix: An Azure Communication Services how-to guide
description: 使用 Azure 通信服务 SDK 订阅 SDK 事件。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 25f5575340ac35fa79ad7f8a1dc8e611a1820802
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699107"
---
# <a name="subscribe-to-sdk-events"></a>订阅 SDK 事件

Azure 通信服务 SDK 是动态的，包含很多属性。 当这些属性更改时，作为开发人员，你可能想要了解何时发生了变更，更重要的是发生了哪些更改。 以下是操作方法！

::: zone pivot="platform-web"
[!INCLUDE [Events JavaScript](./includes/events/events-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Events Android](./includes/events/events-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Events iOS](./includes/events/events-ios.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤
- [尝试我们的通话快速入门](../../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [尝试我们的视频通话快速入门](../../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- [了解如何启用推送通知](./push-notifications.md)