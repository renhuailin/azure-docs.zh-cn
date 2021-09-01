---
title: 快速入门 - 向应用中添加聊天
titleSuffix: An Azure Communication Services quickstart
description: 此快速入门演示如何将通信服务聊天添加到应用。
author: fanche
manager: phans
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift-android
ms.openlocfilehash: c05bc5c332be311de0cafc180f16651e4d83406f
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967862"
---
# <a name="quickstart-add-chat-to-your-app"></a>快速入门：向应用中添加聊天

从 Azure 通信服务着手，方法是使用通信服务聊天 SDK 向应用程序添加实时聊天。 在本快速入门中，我们将使用聊天 SDK 创建聊天会话，使用户能够彼此进行对话。 若要了解有关聊天概念的详细信息，请访问[聊天概念文档](../../concepts/chat/concepts.md)。

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript SDK](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python SDK](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java SDK](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android SDK](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# SDK](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS SDK](./includes/chat-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

> [!div class="checklist"]
> * 创建聊天客户端
> * 创建有两个用户参与的会话
> * 向会话发送消息
> * 从会话接收消息
> * 从会话删除用户

> [!div class="nextstepaction"]
> [试用聊天英雄应用](../../samples/chat-hero-sample.md)

你可能还想要：

 - 了解[聊天概念](../../concepts/chat/concepts.md)
 - 自行熟悉[聊天 SDK](../../concepts/chat/sdk-features.md)
