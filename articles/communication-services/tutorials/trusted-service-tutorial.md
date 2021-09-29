---
title: 在 Azure 通信服务中使用 Azure Functions 生成受信任的用户访问服务
titleSuffix: An Azure Communication Services tutorial
description: 了解如何使用 Azure Functions 为通信服务创建受信任的用户访问服务
author: ddematheu2
manager: chpalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 1c6a5f215d583069884f7c7c0e23b9e4b553bf9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671500"
---
# <a name="build-a-trusted-user-access-service-using-azure-functions"></a>使用 Azure Functions 生成受信任的用户访问服务

本文介绍如何使用 Azure Functions 生成受信任的用户访问服务。

> [!IMPORTANT]
> 在本教程中最终创建的终结点并不安全。 请务必阅读 [Azure Functions 安全性](../../azure-functions/security-concepts.md)一文中的安全详细信息。 需将安全措施添加到终结点，确保恶意行动者无法预配令牌。

[!INCLUDE [Trusted Service JavaScript](./includes/trusted-service-js.md)]

## <a name="securing-azure-function"></a>保护 Azure Functions

在设置受信任服务以便为用户预配访问令牌的过程中，我们需要考虑到该终结点的安全性，以确保恶意行动者无法为该服务随机创建令牌。 Azure Functions 提供内置安全功能，可让你使用不同类型的身份验证策略来保护终结点。 阅读有关 [Azure Functions 安全性](../../azure-functions/security-concepts.md)的详细信息

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 你将能够深入了解如何[清理 Azure 通信服务资源](../quickstarts/create-communication-resource.md#clean-up-resources)以及如何[清理 Azure Function 资源](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Functions 安全性](../../azure-functions/security-concepts.md)

> [!div class="nextstepaction"]
> [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)

你可能还想要：

- [向应用添加聊天](../quickstarts/chat/get-started.md)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
- [了解身份验证](../concepts/authentication.md)