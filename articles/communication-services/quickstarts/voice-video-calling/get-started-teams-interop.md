---
title: 快速入门 - Azure 通信服务的 Teams 互操作
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，你将了解如何通过 Azure 通信通话 SDK 加入 Teams 会议。
author: matthewrobertson
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: c2cda93534a2010ced5c98f8e1a3563f8446ea84
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487972"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>快速入门：在 Teams 会议中加入通话应用

> [!IMPORTANT]
> 若要启用/禁用 [Teams 租户互操作性](../../concepts/teams-interop.md)，请填写[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)。

通过使用 JavaScript 客户端库将通话解决方案连接到 Microsoft Teams，开始使用 Azure 通信服务。

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 查看我们的[出色呼叫示例](../../samples/calling-hero-sample.md)
- 了解[呼叫客户端库功能](./calling-client-samples.md)
- 了解有关[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)的详细信息
