---
title: 对 Azure Key Vault 进行身份验证
description: 了解如何对 Azure Key Vault 进行身份验证
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8a8fe4ed0c24d2ccda5fb844005a33a93e85a169
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365512"
---
# <a name="authenticate-to-azure-key-vault"></a>对 Azure Key Vault 进行身份验证

借助 Azure 密钥保管库，可以在集中的安全云存储库中存储机密并控制其分发，因而无需在应用程序中存储凭据。 应用程序只需要在运行时使用密钥保管库进行身份验证即可访问这些机密。

## <a name="app-identity-and-security-principals"></a>应用标识和安全主体

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

## <a name="authorize-a-security-principal-to-access-key-vault"></a>授权安全主体访问 Key Vault

密钥保管库可用于两个不同的授权级别：

- 访问策略控制是否授权用户、组或服务主体访问现有密钥保管库资源内的机密、密钥和证书（有时称为“数据平面”操作）。 访问策略通常授予用户、组和应用程序。

    若要分配访问策略，请参阅以下文章：

    - [Azure 门户](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- 角色权限控制是否授权用户、组或服务主体创建、删除及以其他方式管理密钥保管库资源（有时称为“管理平面”操作）。 此类角色通常仅授予管理员。
 
    若要分配和管理角色，请参阅以下文章：

    - [Azure 门户](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    有关角色的一般信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。


> [!IMPORTANT]
> 为了获得最高安全性，请始终遵循最小特权原则，并仅授予所需的最具体的访问策略和角色。 
    
## <a name="configure-the-key-vault-firewall"></a>配置密钥保管库防火墙

默认情况下，密钥保管库允许通过公共 IP 地址访问资源。 为了提高安全性，还可以限制对特定 IP 范围、服务终结点、虚拟网络或专用终结点的访问。

有关详细信息，请参阅[访问防火墙保护下的 Azure 密钥保管库](./access-behind-firewall.md)。


## <a name="the-key-vault-authentication-flow"></a>密钥保管库身份验证流

1. 服务主体请求使用 Azure AD 进行身份验证，例如：
    * 用户使用用户名和密码登录到 Azure 门户。
    * 应用程序调用 Azure REST API，提供客户端 ID 和密码或客户端证书。
    * Azure 资源（例如具有托管标识的虚拟机）与 [Azure 实例元数据服务 (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) REST 终结点联系以获取访问令牌。

1. 如果使用 Azure AD 成功进行身份验证，则将向服务主体授予 OAuth 令牌。

1. 服务主体通过密钥保管库的终结点 (URI) 调用密钥保管库 REST API。

1. 密钥保管库防火墙会检查以下条件。 如果满足任何条件，则允许调用。 否则，调用将被阻止并返回禁止访问响应。

    * 防火墙已禁用，并且可以从公共 Internet 访问密钥保管库的公共终结点。
    * 调用方是[密钥保管库受信任的服务](./overview-vnet-service-endpoints.md#trusted-services)，因此允许其绕过防火墙。
    * 调用方按 IP 地址、虚拟网络或服务终结点在防火墙中列出。
    * 调用方可以通过配置的专用链接连接访问密钥保管库。    

1. 如果防火墙允许该调用，则密钥保管库会调用 Azure AD 来验证服务主体的访问令牌。

1. 密钥保管库会检查服务主体是否对请求的操作具有所需的访问策略。 如果没有，则密钥保管库会返回禁止访问响应。

1. 密钥保管库会执行请求的操作并返回结果。

下面的关系图说明应用程序调用密钥保管库“获取机密”API 的过程：

![Azure 密钥保管库身份验证流](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault SDK 用于机密、证书和密钥的客户端在没有访问令牌的情况下对 Key Vault 进行了额外的调用，这导致 401 响应来检索租户信息。 有关详细信息，请参阅[身份验证、请求和响应](authentication-requests-and-responses.md)

## <a name="code-examples"></a>代码示例

下表链接到其他文章，这些文章演示如何使用相关语言的 Azure SDK 库在应用程序代码中使用密钥保管库。 为了方便起见，还包括了其他接口，例如 Azure CLI 和 Azure 门户。

| 密钥保管库机密 | 密钥保管库密钥 | Key Vault 证书 |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Azure 门户](../secrets/quick-create-portal.md) | [Azure 门户](../keys/quick-create-portal.md) | [Azure 门户](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM 模板](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>后续步骤

- [密钥保管库访问策略故障排除](troubleshooting-access-issues.md)
- [密钥保管库 REST API 错误代码](rest-error-codes.md)
- [密钥保管库开发人员指南](developers-guide.md)
- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
