---
title: 对 Azure Key Vault 进行身份验证
description: 了解如何对 Azure Key Vault 进行身份验证
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 1fa4f4b4b9b56b5acc3a3d3d9e75177889a4a8af
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479228"
---
# <a name="authentication-in-azure-key-vault"></a>Azure 密钥保管库中的身份验证

使用密钥保管库进行的身份验证可与 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 结合使用，后者负责对任何给定安全主体的标识进行身份验证。

安全主体是一个对象，表示请求访问 Azure 资源的用户、组、服务或应用程序。 Azure 为每个安全主体分配唯一的对象 ID。

* 用户安全主体标识在 Azure Active Directory 中具有配置文件的个人。

* 组安全主体标识在 Azure Active Directory 中创建的一组用户。 分配给组的任何角色或权限都将授予组内的所有用户。

* 服务主体是一类安全主体，它标识应用程序或服务，即一段代码，而不是用户或组。 服务主体的对象 ID 称为其客户端 ID，作用类似于其用户名。 服务主体的客户端密码的作用类似于其密码。

对于应用程序，有两种获取服务主体的方法：

* 建议：为应用程序启用系统分配的托管标识。

    借助托管标识，Azure 在内部管理应用程序的服务主体，并自动通过其他 Azure 服务对应用程序进行身份验证。 托管标识可用于部署到各种服务的应用程序。

    有关详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。 另请参阅[支持托管标识的 Azure 服务](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)，其链接到介绍如何为特定服务（例如应用服务、Azure Functions、虚拟机等）启用托管标识的文章。

* 如果不能使用托管标识，请改为将应用程序注册到 Azure AD 租户，如[快速入门：将应用程序注册到 Azure 标识平台](../../active-directory/develop/quickstart-register-app.md)中所述。 注册操作还会创建第二个应用程序对象，该对象在所有租户中标识该应用。

## <a name="configure-the-key-vault-firewall"></a>配置密钥保管库防火墙

默认情况下，密钥保管库允许通过公共 IP 地址访问资源。 为了提高安全性，还可以限制对特定 IP 范围、服务终结点、虚拟网络或专用终结点的访问。

有关详细信息，请参阅[访问防火墙保护下的 Azure 密钥保管库](./access-behind-firewall.md)。

## <a name="the-key-vault-request-operation-flow-with-authentication"></a>包括身份验证的密钥保管库请求操作流

密钥保管库身份验证将作为密钥保管库中每个请求操作的一部分进行。 检索令牌后，可将其重用于后续调用。 身份验证流示例：

1. 某个令牌请求使用 Azure AD 进行身份验证，例如：
    * Azure 资源（例如具有托管标识的虚拟机或应用服务应用程序）与 REST 终结点联系以获取访问令牌。
    * 用户使用用户名和密码登录到 Azure 门户。

1. 如果使用 Azure AD 成功进行身份验证，则将向安全主体授予 OAuth 令牌。

1. 通过密钥保管库的终结点 (URI) 调用密钥保管库 REST API。

1. 密钥保管库防火墙会检查以下条件。 如果满足任何条件，则允许调用。 否则，调用将被阻止并返回禁止访问响应。

    * 防火墙已禁用，并且可以从公共 Internet 访问密钥保管库的公共终结点。
    * 调用方是[密钥保管库受信任的服务](./overview-vnet-service-endpoints.md#trusted-services)，因此允许其绕过防火墙。
    * 调用方按 IP 地址、虚拟网络或服务终结点在防火墙中列出。
    * 调用方可以通过配置的专用链接连接访问密钥保管库。    

1. 如果防火墙允许该调用，则密钥保管库会调用 Azure AD 来验证安全主体的访问令牌。

1. 密钥保管库会检查安全主体是否对请求的操作拥有所需的权限。 如果没有，则密钥保管库会返回禁止访问响应。

1. 密钥保管库会执行请求的操作并返回结果。

下面的关系图说明应用程序调用密钥保管库“获取机密”API 的过程：

![Azure 密钥保管库身份验证流](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault SDK 用于机密、证书和密钥的客户端在没有访问令牌的情况下对 Key Vault 进行了额外的调用，这导致 401 响应来检索租户信息。 有关详细信息，请参阅[身份验证、请求和响应](authentication-requests-and-responses.md)

## <a name="authentication-to-key-vault-in-application-code"></a>在应用程序代码中对密钥保管库进行身份验证

密钥保管库 SDK 使用 Azure 标识客户端库，通过该库可以使用相同的代码跨环境对密钥保管库进行无缝身份验证

**Azure 标识客户端库**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure 标识 SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure 标识 SDK Python](/python/api/overview/azure/identity-readme)|[Azure 标识 SDK Java](/java/api/overview/azure/identity-readme)|[Azure 标识 SDK JavaScript](/javascript/api/overview/azure/identity-readme)|   

有关最佳做法和开发人员示例的详细信息，请参阅[在代码中对密钥保管库进行身份验证](developers-guide.md#authenticate-to-key-vault-in-code)

## <a name="next-steps"></a>后续步骤

- [密钥保管库开发人员指南](developers-guide.md)
- [使用 Azure 门户分配 Key Vault 访问策略](assign-access-policy-portal.md)
- [将 Azure RBAC 角色分配到密钥保管库](rbac-guide.md)
- [密钥保管库访问策略故障排除](troubleshooting-access-issues.md)
- [密钥保管库 REST API 错误代码](rest-error-codes.md)

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
