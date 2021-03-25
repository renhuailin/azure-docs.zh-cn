---
title: 快速入门 - 拨打电话
titleSuffix: An Azure Communication Services quickstart
description: 本快速入门介绍如何使用 Azure 通信服务向应用添加 PSTN 呼叫功能。
author: nikuklic
ms.author: nikuklic
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 65a968747e7ed12e6ee03bd5fea4b7b4af7ee10e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104729509"
---
# <a name="quickstart-call-to-phone"></a>快速入门：拨打电话

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


通过使用通信服务呼叫客户端库向应用添加 PSTN 呼叫，开启 Azure 通信服务使用旅程。

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/pstn-call-js.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/pstn-call-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/pstn-call-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 了解[呼叫客户端库功能](./calling-client-samples.md)
- 了解有关[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)的详细信息
