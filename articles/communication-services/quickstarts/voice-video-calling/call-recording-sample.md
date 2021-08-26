---
title: Azure 通信服务通话记录 API 快速入门
titleSuffix: An Azure Communication Services quickstart document
description: 提供通话记录 API 的快速入门示例。
author: ravithanneeru
manager: GrantMeStrength
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-csharp-java
ms.openlocfilehash: 57a0f077b51be026a0a3fc9cdc95e65bb2c924a9
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253775"
---
# <a name="call-recording-api-quickstart"></a>通话记录 API 快速入门

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

本快速入门介绍了如何开始录制语音和视频通话。 本快速入门假定你已使用[通话客户端 SDK](get-started-with-video-calling.md) 来构建最终用户通话体验。 使用通话服务器 API 和 SDK 可以启用和管理录制。 

::: zone pivot="programming-language-csharp"
[!INCLUDE [Build Call Recording server sample with C#](./includes/call-recording-samples/recording-server-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Build Call Recording server sample with Java](./includes/call-recording-samples/recording-server-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 查看我们的[出色呼叫示例](../../samples/calling-hero-sample.md)
- 了解如何[调用 SDK 功能](./calling-client-samples.md)
- 了解有关[呼叫工作原理](../../concepts/voice-video-calling/about-call-types.md)的详细信息
