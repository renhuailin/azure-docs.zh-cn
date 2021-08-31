---
title: Azure 中继身份验证和授权 | Microsoft Docs
description: 本文概述了如何使用 Azure 中继服务进行共享访问签名 (SAS) 身份验证。
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: dbabee7f49e4f905c34a91dcb6095e7eab3faa0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741224"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure 中继身份验证和授权
可通过两种方式验证和授权对 Azure 中继资源的访问：Azure Activity Directory (Azure AD) 和共享访问签名 (SAS)。 本文将详细介绍如何使用这两种类型的安全机制。

## <a name="azure-active-directory-preview"></a>Azure Active Directory（预览）
Azure 中继资源的 Azure AD 集成提供了 Azure 基于角色的访问控制 (Azure RBAC)，用于对客户端的资源访问进行细粒度控制。 可使用 Azure RBAC 授予对安全主体的权限，该主体可以是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 令牌可用于授权访问 Azure 中继资源的请求。

有关使用 Azure AD 进行身份验证的详细信息，请参阅以下文章：
- [使用托管标识进行身份验证](authenticate-managed-identity.md)
- [从 Azure Active Directory 应用程序进行身份验证](authenticate-application.md)

> [!IMPORTANT]
> 使用 Azure AD 返回的 OAuth 2.0 令牌授权用户或应用程序可提供比共享访问签名 (SAS) 更高的安全性和易用性。 使用 Azure AD，不需要在代码中存储令牌，也不需要冒潜在的安全漏洞风险。 建议你尽可能通过 Azure 中继应用程序使用 Azure AD。

### <a name="built-in-roles"></a>内置角色
对于 Azure 中继，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源的管理已使用 Azure RBAC 模型进行了保护。 Azure 提供以下 Azure 内置角色，用于授权访问中继命名空间：

| Role | 说明 | 
| ---- | ----------- | 
| [Azure 中继所有者](../role-based-access-control/built-in-roles.md#azure-relay-owner) | 使用此角色授予对 Azure 中继资源的完全访问权限。 |
| [Azure 中继侦听器](../role-based-access-control/built-in-roles.md#azure-relay-listener) | 使用此角色来授予对 Azure 中继资源的侦听和实体读取访问权限。 |
| [Azure 中继发送方](../role-based-access-control/built-in-roles.md#azure-relay-sender) | 使用此角色来授予对 Azure 中继资源的发送和实体读取访问权限。 | 


## <a name="shared-access-signature"></a>共享访问签名
应用程序可以使用共享访问签名 (SAS) 身份验证对 Azure 中继进行身份验证。 通过 SAS 身份验证，应用程序能够使用在中继命名空间中配置的访问密钥向 Azure 中继服务进行身份验证。 然后可以使用此密钥生成共享访问签名令牌，客户端可用它向中继服务进行身份验证。

通过 [SAS 身份验证](../service-bus-messaging/service-bus-sas.md)可向具有特定权限的用户授予对 Azure 中继资源的访问权限。 SAS 身份验证涉及配置具有资源相关权限的加密密钥。 客户端随后即可通过提供 SAS 令牌获取该资源的访问权限，该令牌由要访问的资源 URI 和签有已配置密钥的过期时间组成。

可以在中继命名空间上配置用于 SAS 的密钥。 与服务总线消息传送不同，[中继混合连接](relay-hybrid-connections-protocol.md)支持未经授权的发件人或匿名发件人。 可在创建实体时启用它的匿名访问权限，如门户中以下屏幕截图所示：

![标题为“创建混合连接”的对话框具有一个“名称”文本框和一个标记为“需要客户端身份验证”的复选框，该复选框处于选中状态。][0]

若要使用 SAS，可在由以下属性构成的中继命名空间上配置 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 对象：

* 标识此规则的 KeyName。
* *PrimaryKey* ，是用于对 SAS 令牌进行签名/验证的加密密钥。
* *SecondaryKey* ，是用于对 SAS 令牌进行签名/验证的加密密钥。
* Rights，表示授予的侦听、发送或管理权限的集合。

在命名空间级别配置的授权规则，可以向具有使用相应密钥签名的令牌的客户端授予命名空间中所有中继连接的访问权限。 在中继命名空间上最多可配置 12 个此类授权规则。 默认情况下，首次预配时，将为每个命名空间配置具有所有权限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。

若要访问某个实体，客户端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 生成的 SAS 令牌。 SAS 令牌是通过使用资源字符串的 HMAC-SHA256 生成的，该字符串由要授予对其访问权限的资源 URI 和授权规则相关加密密钥的过期时间组成。

Azure.NET SDK 2.0 版和更高版本中包含 Azure 中继的 SAS 身份验证支持。 SAS 支持 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。 允许将连接字符串作为参数的所有 API 都支持 SAS 连接字符串。

## <a name="next-steps"></a>后续步骤

- 有关 SAS 的详细信息，请继续阅读[使用共享访问签名进行服务总线身份验证](../service-bus-messaging/service-bus-sas.md)。
- 有关混合连接功能的详细信息，请参阅[Azure 中继混合连接协议指南](relay-hybrid-connections-protocol.md)。
- 有关服务总线消息传送身份验证和授权的相关信息，请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。 

[0]: ./media/relay-authentication-and-authorization/hcanon.png