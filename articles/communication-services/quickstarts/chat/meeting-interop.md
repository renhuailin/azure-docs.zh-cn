---
title: Azure 通信服务的 Teams 互操作入门
titleSuffix: An Azure Communication Services quickstart
description: 本快速入门介绍如何通过 Azure 通信聊天 SDK 加入 Teams 会议
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 09a4f044ee7084cec31c61c3a00a6e3a42d688f0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442871"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>快速入门：将你的聊天应用连接到 Teams 会议

> [!IMPORTANT]
> 若要启用/禁用 [Teams 租户互操作性](../../concepts/teams-interop.md)，请填写[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)。

通过将聊天解决方案连接到 Microsoft Teams，开始使用 Azure 通信服务。 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Teams interop with Android SDK](./includes/meeting-interop-android.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 查看我们的[出色聊天示例](../../samples/chat-hero-sample.md)
- 详细了解[聊天的工作原理](../../concepts/chat/concepts.md)
