---
title: Azure 服务总线身份验证和授权 | Microsoft Docs
description: 使用共享访问签名 (SAS) 身份验证向服务总线验证应用程序。
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: 74ad1674bc09f1514264f4b7f1521526f8da2fd5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597144"
---
# <a name="service-bus-authentication-and-authorization"></a>服务总线身份验证和授权
可通过两种方式验证和授权对 Azure 服务总线资源的访问：Azure Activity Directory (Azure AD) 和共享访问签名 (SAS)。 本文将详细介绍如何使用这两种类型的安全机制。 

## <a name="azure-active-directory"></a>Azure Active Directory
服务总线资源的 Azure AD 集成提供 Azure 基于角色的访问控制 (RBAC)，用于对客户端的资源访问进行细粒度控制。 可使用 Azure RBAC 授予对安全主体的权限，该主体可以是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 令牌可用于授权某个请求来访问服务总线资源（队列、主题等）。

有关使用 Azure AD 进行身份验证的详细信息，请参阅以下文章：

- [使用托管标识进行身份验证](service-bus-managed-service-identity.md)
- [从应用程序进行身份验证](authenticate-application.md)

> [!NOTE]
> [服务总线 REST API](/rest/api/servicebus/) 支持通过 Azure AD 进行 OAuth 身份验证。

> [!IMPORTANT]
> 使用 Azure AD 返回的 OAuth 2.0 令牌授权用户或应用程序可提供比共享访问签名 (SAS) 更高的安全性和易用性。 使用 Azure AD，不需要在代码中存储令牌，也不需要冒潜在的安全漏洞风险。 我们建议你尽可能通过 Azure 服务总线应用程序使用 Azure AD。 

## <a name="shared-access-signature"></a>共享访问签名
通过 [SAS 身份验证](service-bus-sas.md)可向具有特定权限的用户授予对服务总线资源的访问权限。 服务总线中的 SAS 身份验证涉及配置具有服务总线资源相关权限的加密密钥。 客户端随后即可通过提供 SAS 令牌获取该资源的访问权限，该令牌由要访问的资源 URI 和签有已配置密钥的过期时间组成。

可以在服务总线命名空间上配置用于 SAS 的密钥。 该密钥将应用到该命名空间中的所有消息传送实体。 还可在服务总线队列和主题上配置密钥。 [Azure 中继](../azure-relay/relay-authentication-and-authorization.md)也支持 SAS。

若要使用 SAS，可以在命名空间、队列或主题上配置共享访问授权规则。 此规则由以下元素组成：

* *KeyName*：标识规则。
* *PrimaryKey*：用于对 SAS 令牌进行签名/验证的加密密钥。
* *SecondaryKey*：用于对 SAS 令牌进行签名/验证的加密密钥。密钥。
* *Rights*：表示授予的“侦听”、“发送”或“管理”权限的集合。

通过在命名空间级别配置的授权规则，可以向具有使用相应密钥签名的令牌的客户端授予对命名空间中所有实体的访问权限。 在服务总线命名空间、队列或主题上最多可配置 12 个此类规则。 默认情况下，在首次预配每个命名空间时，会为其配置具有所有权限的共享访问授权规则。

若要访问实体，客户端需要一个使用特定共享访问授权规则生成的 SAS 令牌。 SAS 令牌是通过使用资源字符串的 HMAC-SHA256 生成的，该字符串由要授予对其访问权限的资源 URI 和授权规则相关加密密钥的到期时间组成。

Azure .NET SDK 2.0 版和更高版本支持服务总线的 SAS 身份验证。 SAS 包括对共享访问授权规则的支持。 允许将连接字符串作为参数的所有 API 都支持 SAS 连接字符串。


## <a name="next-steps"></a>后续步骤
有关使用 Azure AD 进行身份验证的详细信息，请参阅以下文章：

- [使用托管标识进行身份验证](service-bus-managed-service-identity.md)
- [从应用程序进行身份验证](authenticate-application.md)

有关使用 SAS 进行身份验证的详细信息，请参阅以下文章：

- [使用 SAS 进行身份验证](service-bus-sas.md)
