---
title: Azure 通信服务通话自动化 API 快速入门
titleSuffix: An Azure Communication Services quickstart document
description: 提供通话自动化 API 的快速入门示例。
author: GrantMeStrength
manager: anvalent
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-csharp-java
ms.openlocfilehash: b41ed8b917e3311aa69369bc0fa5725305346056
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254605"
---
# <a name="quickstart-use-the-call-automation-apis"></a>快速入门：使用调用自动化 API

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]


通过使用通信服务通话服务器 SDK 构建自动通话路由解决方案，开始使用 Azure 通信服务。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Build IVR solution with C#](./includes/call-automation-api-samples/calling-server-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Build IVR solution with Java](./includes/call-automation-api-samples/calling-server-sdk-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 查看我们的[出色呼叫示例](../../samples/calling-hero-sample.md)
- 了解如何[调用 SDK 功能](./calling-client-samples.md)
- 了解有关[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)的详细信息
