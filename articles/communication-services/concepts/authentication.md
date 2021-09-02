---
title: 向 Azure 通信服务进行身份验证
titleSuffix: An Azure Communication Services concept document
description: 了解应用或服务可向通信服务进行身份验证的各种方式。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 7c2f060bbe317cbefc3b4748cbdced21053f80f7
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970301"
---
# <a name="authenticate-to-azure-communication-services"></a>向 Azure 通信服务进行身份验证

需要对每个与 Azure 通信服务的客户端交互进行身份验证。 在典型的体系结构（见[客户端和服务器体系结构](./client-and-server-architecture.md)）中，访问密钥或托管标识用于身份验证。

另一种类型的身份验证使用用户访问令牌对需要用户参与的服务进行身份验证。 例如，聊天或呼叫服务使用用户访问令牌，便于用户被添加到会话中，并彼此进行对话。

## <a name="authentication-options"></a>身份验证选项

下表列出了 Azure 通信服务 SDK 及其身份验证选项：

| SDK 中 IsInRole 中的声明    | 身份验证选项                               |
| ----------------- | ----------------------------------------------------|
| 标识          | 访问密钥或托管标识                      |
| SMS               | 访问密钥或托管标识                      |
| 电话号码     | 访问密钥或托管标识                      |
| 调用           | 用户访问令牌                                   |
| 聊天              | 用户访问令牌                                   |

下面简要介绍每个授权选项：

### <a name="access-key"></a>访问密钥

访问密钥身份验证适用于在受信任的服务环境中运行的服务应用程序。 访问密钥可以在 Azure 通信服务门户中找到。 服务应用程序将其用作初始化相应 SDK 的凭据。 请参阅有关如何在[标识 SDK](../quickstarts/access-tokens.md) 中使用它的示例。 

由于访问密钥是资源连接字符串的一部分，因此使用连接字符串进行身份验证等同于使用访问密钥进行身份验证。

若要使用访问密钥手动调用 ACS 的 API，则需要对请求进行签名。 这篇[教程](../tutorials/hmac-header-tutorial.md)中详细介绍了如何对请求进行签名。

### <a name="managed-identity"></a>托管标识

托管标识的安全性和易用性优于其他授权选项。 例如，通过使用 Azure AD，你不必像使用访问密钥授权那样在代码中存储帐户访问密钥。 尽管可以继续对通信服务应用程序使用访问密钥授权，但 Microsoft 建议尽可能迁移到 Azure AD。 

若要创建托管标识，请[使用 Azure CLI 创建已注册的应用程序](../quickstarts/identity/service-principal-from-cli.md)。 然后，可以使用终结点和凭据对 SDK 进行身份验证。 请参阅有关如何使用[托管标识](../quickstarts/identity/service-principal.md)的示例。

### <a name="user-access-tokens"></a>用户访问令牌

用户访问令牌是使用标识 SDK 生成，并与在标识 SDK 中创建的用户相关联。 请参阅有关如何[创建用户和生成令牌](../quickstarts/access-tokens.md)的示例。 然后，用户访问令牌用于对添加到聊天或呼叫 SDK 中对话的参与者进行身份验证。 有关详细信息，请参阅[向应用中添加聊天](../quickstarts/chat/get-started.md)。 用户访问令牌身份验证与访问密钥和托管标识身份验证的不同之处在于，它用于对用户进行身份验证，而不是对受保护的 Azure 资源进行身份验证。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建和管理通信服务资源](../quickstarts/create-communication-resource.md)
> [使用 Azure CLI 创建 Azure Active Directory 托管标识应用程序](../quickstarts/identity/service-principal-from-cli.md)
> [创建用户访问令牌](../quickstarts/access-tokens.md)

有关详细信息，请参阅以下文章：
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
