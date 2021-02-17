---
title: 如何对 HTTP 请求进行签名 C#
titleSuffix: An Azure Communication Services tutorial
description: 了解如何通过 C# 对 HTTP 请求通信服务进行签名
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 12f78ee2bae7ede26d8ef8581370b08e1a306aea
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628288"
---
# <a name="sign-an-http-request"></a>对 HTTP 请求进行签名

在本教程中，你将了解如何使用 HMAC 签名对 HTTP 请求进行签名。

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

[!INCLUDE [Sign an HTTP request C#](./includes/hmac-header-csharp.md)]

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 你将能够深入了解如何[清理 Azure 通信服务资源](../quickstarts/create-communication-resource.md#clean-up-resources)以及如何[清理 Azure Function 资源](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)

你可能还想要：

- [向应用添加聊天](../quickstarts/chat/get-started.md)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
- [了解身份验证](../concepts/authentication.md)
