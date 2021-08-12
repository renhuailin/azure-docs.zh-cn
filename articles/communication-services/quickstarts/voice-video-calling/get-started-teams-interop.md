---
title: 快速入门 - Azure 通信服务的 Teams 互操作
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，你将了解如何通过 Azure 通信通话 SDK 加入 Teams 会议。
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 1fb7373842e7edfdede482d83fdc7e27da0e6965
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107752"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>快速入门：在 Teams 会议中加入通话应用

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> 若要启用/禁用 [Teams 租户互操作性](../../concepts/teams-interop.md)，请填写[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)。

通过使用 JavaScript SDK 将呼叫解决方案连接到 Microsoft Teams，开始使用 Azure 通信服务。

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Calling with Windows](./includes/teams-interop/teams-interop-windows.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop/teams-interop-ios.md)]
::: zone-end

本文档所述的功能使用通信服务 SDK 的正式发布版。 Teams 互操作性需要通信服务 SDK 的 Beta 版本。 可以在[发行说明页](https://github.com/Azure/Communication/tree/master/releasenotes)上浏览 Beta 版本 SDK。

使用 Beta SDK 执行“安装包”步骤时，请通过在 `communication-calling` 包名称中指定版本 `@1.0.0-beta.10`（撰写本文时的版本），将包的版本修改为最新的 Beta 版本。 不需要修改 `communication-common` 包命令。 例如：

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 查看我们的[出色呼叫示例](../../samples/calling-hero-sample.md)
- 了解如何[调用 SDK 功能](./calling-client-samples.md)
- 了解有关[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)的详细信息
