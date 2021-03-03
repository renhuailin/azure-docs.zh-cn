---
title: Azure 通信服务的身份验证
titleSuffix: An Azure Communication Services concept document
description: 了解应用或服务可对通信服务进行身份验证的各种方式。
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1267fc53bd6dcbae504b01610267059545353dc5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655898"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure 通信服务的身份验证

需要对每个与 Azure 通信服务的客户端交互进行身份验证。 在典型的体系结构中，请参阅 [客户端和服务器体系结构](./client-and-server-architecture.md)， *访问密钥* 或 *托管标识* 在可信用户访问服务中用于创建用户和颁发令牌。 受信任的用户访问服务颁发的和 *用户访问令牌* 用于客户端应用程序访问其他通信服务，例如聊天或呼叫服务。

Azure 通信服务 SMS 服务还接受 *访问密钥* 或用于身份验证的 *托管标识* 。 这通常发生在受信任的服务环境中运行的服务应用程序中。

下面简要介绍每个授权选项：

- 用于 SMS 和标识操作的 **访问密钥** 身份验证。 访问密钥身份验证适用于在受信任的服务环境中运行的服务应用程序。 可在 Azure Communication Service 门户中找到访问密钥。 若要使用访问密钥进行身份验证，服务应用程序使用访问密钥作为凭据来初始化相应的 SMS 或标识客户端库，请参阅 [创建和管理访问令牌](../quickstarts/access-tokens.md)。 由于访问密钥是资源连接字符串的一部分，请参阅 [创建和管理通信服务资源](../quickstarts/create-communication-resource.md)，使用连接字符串进行身份验证等效于使用访问密钥进行身份验证。
- 用于 SMS 和标识操作的 **托管标识** 身份验证。 托管标识是适用于在受信任的服务环境中运行的服务应用程序的 [托管标识](../quickstarts/managed-identity.md)。 若要使用托管标识进行身份验证，服务应用程序将使用 id 和托管标识的机密创建凭据，然后初始化相应的 SMS 或标识客户端库，请参阅 [创建和管理访问令牌](../quickstarts/access-tokens.md)。
- 用于聊天和呼叫的 **用户访问令牌** 身份验证。 用户访问令牌允许客户端应用程序针对 Azure 通信聊天和调用服务进行身份验证。 这些令牌是在你创建的 "受信任的用户访问服务" 中生成的。 然后，将它们提供给使用令牌初始化聊天和调用客户端库的客户端设备。 有关详细信息，请参阅 [向应用添加聊天](../quickstarts/chat/get-started.md) 示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建和管理通信服务资源](../quickstarts/create-communication-resource.md) 
> [从 Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  创建 Azure Active Directory 托管标识应用程序[创建用户访问令牌](../quickstarts/access-tokens.md)

有关详细信息，请参阅以下文章：
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
