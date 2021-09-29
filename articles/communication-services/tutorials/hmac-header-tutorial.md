---
title: 了解如何使用 C# 为 HTTP 请求签名
titleSuffix: An Azure Communication Services tutorial
description: 了解如何通过 C# 为针对 Azure 通信服务的 HTTP 请求签名。
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 5210793b7c42d0c91ef26fb629cd3c4d57d39e2d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671538"
---
# <a name="sign-an-http-request"></a>对 HTTP 请求进行签名

在本教程中，你将了解如何使用 HMAC 签名对 HTTP 请求进行签名。

[!INCLUDE [Sign an HTTP request C#](./includes/hmac-header-csharp.md)]

## <a name="clean-up-resources"></a>清理资源

若要清理并删除通信服务订阅，请删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 你可以深入了解如何[清理 Azure 通信服务资源](../quickstarts/create-communication-resource.md#clean-up-resources)以及如何[清理 Azure Functions 资源](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)

你可能还需要：

- [向应用中添加聊天](../quickstarts/chat/get-started.md)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
- [了解身份验证](../concepts/authentication.md)
