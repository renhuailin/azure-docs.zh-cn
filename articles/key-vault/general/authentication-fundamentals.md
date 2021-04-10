---
title: Azure Key Vault 身份验证基础知识
description: 了解密钥保管库的身份验证模式的工作原理
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103572802"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault 身份验证基础知识

使用 Azure Key Vault，你可以在一个集中且安全的云存储库中安全地存储和管理应用程序凭据（例如机密、密钥和证书）。 使用 Key Vault，无需在应用程序中存储凭据。 应用程序可以在运行时向 Key Vault 进行身份验证，以检索凭据。

作为管理员，你可以严格控制哪些用户和应用程序可以访问你的密钥保管库，并且可以限制和审核他们执行的操作。 本文档介绍了密钥保管库访问模型的基本概念。 它将提供入门级知识，并从头到尾地展示如何向密钥库证明用户或应用程序的身份。

## <a name="required-knowledge"></a>必备知识

本文档假定你熟悉以下概念。 如果你不熟悉其中的任何概念，请在继续操作之前单击帮助链接。

* Azure Active Directory [链接](../../active-directory/fundamentals/active-directory-whatis.md)
* 安全主体 [链接](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>密钥保管库配置步骤摘要

1. 在 Azure Active Directory 中将你的用户或应用程序注册为安全主体。
1. 在 Azure Active Directory 中为你的安全主体配置角色分配。
1. 为安全主体配置密钥保管库访问策略。
1. 配置对密钥保管库的 Key Vault 防火墙访问权限（可选）。
1. 测试你的安全主体访问密钥保管库的权限。

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>在 Azure Active Directory 中将用户或应用程序注册为安全主体

当用户或应用程序向密钥保管库发出请求时，必须先通过 Azure Active Directory 对该请求进行身份验证。 为此，需要在 Azure Active Directory 中将用户或应用程序注册为安全主体。

请通过下面的文档链接来了解如何在 Azure Active Directory 中注册用户或应用程序。
**确保创建用于用户注册的密码，并为应用程序创建客户端机密或客户端证书凭据。**

* 在 Azure Active Directory 中注册用户 [链接](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* 在 Azure Active Directory 中注册应用程序 [链接](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>向安全主体分配角色

你可以使用 Azure 基于角色的访问控制 (Azure RBAC) 向安全主体分配权限。 这些权限称为角色分配。

在密钥保管库的上下文中，这些角色分配决定了安全主体对密钥保管库的管理平面（也称为控制平面）的访问权限级别。 这些角色分配不直接提供对数据平面机密的访问权限，但提供管理密钥保管库属性所需的访问权限。 例如，不允许分配有“读取者”角色的用户或应用程序对密钥保管库防火墙设置进行更改，而分配有“参与者”角色的用户或应用程序可以进行更改。 这两个角色都不具有对机密、密钥和证书执行操作（例如创建或检索它们的值）所需的直接访问权限，除非为其分配对密钥保管库数据平面的访问权限。 接下来的步骤中介绍了此内容。

>[!IMPORTANT]
> 尽管具有“参与者”或“所有者”角色的用户默认情况下无权对密钥保管库中存储的机密执行操作，但“参与者”和“所有者”角色提供了为在密钥保管库中存储的机密添加或删除访问策略的权限。 因此，具有这些角色分配的用户可以向自己授予访问密钥保管库中机密的权限。 因此，建议只允许管理员拥有对“参与者”或“所有者”角色的访问权限。 只需要从密钥保管库检索机密的用户和应用程序应被授予“读取者”角色。 **下一部分提供了更多详细信息。**

>[!NOTE]
> 在 Azure Active Directory 租户级别向用户分配角色分配时，此权限集会渗透到分配范围内的所有订阅、资源组和资源。 为了遵循最小特权原则，你可以在更细粒度的范围内进行此角色分配。 例如，你可以在订阅级别为用户分配“读取者”角色，并为其分配针对单个密钥保管库的“所有者”角色。 转到某个订阅、资源组或密钥保管库的标识访问管理 (IAM) 设置，在更细粒度的范围进行角色分配。

* 详细了解 Azure 角色 [链接](../../role-based-access-control/built-in-roles.md)
* 详细了解分配或删除角色分配 [链接](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>为安全主体配置密钥保管库访问策略

为用户和应用程序授予对密钥保管库的访问权限之前，请务必了解可以在密钥保管库中执行的不同类型的操作。 密钥保管库操作、管理平面（也称为控制平面）操作和数据平面操作有两种主要类型。

此表显示了由管理平面和数据平面控制的不同操作的几个示例。 更改密钥保管库属性的操作是管理平面操作。 更改或检索密钥保管库中存储的机密值的操作是数据平面操作。

|管理平面操作（示例）|数据平面操作（示例）|
| --- | --- |
| 创建密钥保管库 | 创建密钥、机密、证书
| 删除密钥保管库 | 删除密钥、机密、证书
| 添加或删除密钥保管库角色分配 | 列出和获取密钥、机密和证书的值
| 添加或删除密钥保管库访问策略 | 备份和还原密钥、机密、证书
| 修改密钥保管库防火墙设置 | 续订密钥、机密、证书
| 修改密钥保管库恢复设置 | 清除或恢复软删除的密钥、机密、证书
| 修改密钥保管库诊断日志设置

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>管理平面访问权限和 Azure Active Directory 角色分配

Azure Active Directory 角色分配授予对密钥保管库执行管理平面操作所需的访问权限。 此访问权限通常授予用户，而不是授予应用程序。 可以通过更改用户的角色分配来限制用户可以执行的管理平面操作。

例如，为用户分配密钥保管库“读取者”角色将允许用户查看密钥保管库的属性（例如访问策略），但不允许用户进行任何更改。 为用户分配“所有者”角色将为用户提供更改密钥保管库管理平面设置所需的完全访问权限。

角色分配是在密钥保管库的“访问控制(IAM)”边栏选项卡中控制的。 如果你希望特定用户具有成为读取者或成为多个密钥保管库资源的管理员所需的访问权限，则可以在保管库、资源组或订阅级别创建角色分配，然后角色分配就会被添加到分配范围内的所有资源。

可以通过以下两种方式之一添加数据平面访问权限，或添加对密钥保管库中存储的密钥、机密和证书执行操作所需的访问权限。

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>数据平面访问选项 1：经典密钥保管库访问策略

密钥保管库访问策略向用户和应用程序授予对密钥保管库执行数据平面操作所需的访问权限。

> [!NOTE]
> 此访问模型与下面记录的密钥保管库的 Azure RBAC（选项 2）不兼容。 你必须选择一个。 单击密钥保管库的“访问策略”选项卡时，你将有机会做出此选择。

经典访问策略是细粒度的，这意味着你可以允许或拒绝每个用户或应用程序在密钥保管库中执行各个操作的权限。 以下是一些示例：

* 安全主体 1 可以执行任何密钥操作，但不允许执行任何机密或证书操作。
* 安全主体 2 可以列出和读取所有密钥、机密和证书，但不能执行任何创建、删除或续订操作。
* 安全主体 3 可以备份和还原所有机密，但不能读取机密本身的值。

但是，经典访问策略不允许基于对象级别的权限，并且分配的权限将应用于单个密钥保管库的范围。 例如，如果向特定密钥保管库中的某个安全主体授予“获取机密”访问策略权限，则该安全主体将有权获取该特定密钥保管库中的所有机密。 但是，此“获取机密”权限不会自动扩展到其他密钥保管库，必须显式分配。

> [!IMPORTANT]
> 经典密钥保管库访问策略和 Azure Active Directory 角色分配相互独立。 在订阅级别为安全主体分配“参与者”角色不会自动使安全主体有权对订阅范围内的每个密钥保管库执行数据平面操作。 仍需向安全主体授权，否则安全主体就必须向自己授予执行数据平面操作所需的访问策略权限。

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>数据平面访问选项 2：Key Vault 的 Azure RBAC（预览版）

若要授予对密钥保管库数据平面的访问权限，一种新方法是使用针对密钥保管库的 Azure 基于角色的访问控制 (Azure RBAC)。

> [!NOTE]
> 此访问模型与上面显示的密钥保管库经典访问策略不兼容。 你必须选择一个。 单击密钥保管库的“访问策略”选项卡时，你将有机会做出此选择。

密钥保管库角色分配是一组内置的 Azure 角色分配，它们包含用于访问密钥、机密和证书的通用权限集。 此权限模型还实现了经典密钥保管库访问策略模型中未提供的其他功能。

* 通过允许在订阅、资源组或各个密钥保管库级别为用户分配这些角色，可以大规模地管理 Azure RBAC 权限。 用户将具有对 Azure RBAC 分配范围内的所有密钥保管库的数据平面权限。 这样就不需针对每个密钥保管库为每个用户/应用程序分配单独的访问策略权限。

* Azure RBAC 权限与 Privileged Identity Management（简称 PIM）兼容。 这允许你为特权角色（例如密钥保管库管理员）配置实时访问控制。 这是一种最佳安全做法，它通过消除对密钥保管库的常设访问权限来遵循最小特权原则。

若要详细了解 Key Vault 的 Azure RBAC，请参阅以下文档：

* Key Vault 的 Azure RBAC [链接](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* Key Vault 角色的 Azure RBAC [链接](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>配置 Key Vault 防火墙

默认情况下，密钥保管库允许来自公共 Internet 的流量通过公共终结点发送到密钥保管库。 为了增加安全性，你可以配置 Azure Key Vault 防火墙来限制对密钥保管库公共终结点的访问。

若要启用密钥保管库防火墙，请在密钥保管库门户中单击“网络”选项卡，然后选择与“允许访问来源”下的“专用终结点和所选网络”对应的单项按钮。 如果你选择启用密钥保管库防火墙，则下面是允许流量通过密钥保管库防火墙的方法。

* 将 IPv4 地址添加到密钥保管库防火墙允许列表中。 此选项最适用于具有静态 IP 地址的应用程序。

* 将虚拟网络添加到密钥保管库防火墙。 此选项最适用于具有动态 IP 地址的 Azure 资源（例如虚拟机）。 你可以将 Azure 资源添加到虚拟网络，并将虚拟网络添加到密钥保管库防火墙允许列表中。 此选项使用的服务终结点是虚拟网络内的专用 IP 地址。 这将提供一层额外的保护，因此密钥保管库与虚拟网络之间的流量不会通过公共 Internet 进行路由。 若要详细了解服务终结点，请查看以下文档。 [链接](./network-security.md)

* 向密钥保管库添加专用链接连接。 此选项可将虚拟网络直接连接到密钥保管库的特定实例，从而有效地将密钥保管库放在虚拟网络中。 若要详细了解如何配置密钥保管库的专用终结点连接，请参阅以下[链接](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>测试你的服务主体访问密钥保管库的权限

完成上述所有步骤后，你将能够在密钥保管库中设置和检索机密。

### <a name="authentication-process-for-users-examples"></a>用户的身份验证过程（示例）

* 用户可以登录到 Azure 门户来使用密钥保管库。 [Key Vault 门户快速入门](./quick-create-portal.md)

* 用户可以通过 Azure CLI 来使用密钥保管库。 [Key Vault Azure CLI 快速入门](./quick-create-cli.md)

* 用户可以通过 Azure PowerShell 来使用密钥保管库。 [Key Vault Azure PowerShell 快速入门](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>应用程序或服务的 Azure Active Directory 身份验证过程（示例）

* 应用程序在函数中提供客户端机密和客户端 ID 来获取 Azure Active Directory 令牌。 

* 应用程序提供获取 Azure Active Directory 令牌所需的证书。 

* Azure 资源使用 MSI 身份验证来获取 Azure Active Directory 令牌。 

* 详细了解 MSI 身份验证 [链接](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>应用程序的身份验证过程（Python 示例）

使用以下代码示例测试应用程序是否可以使用配置的服务主体从密钥保管库检索机密。

>[!NOTE]
>此示例仅用于演示和测试。 **请勿在生产环境中使用客户端机密身份验证**，这不是一种安全的设计做法。 你应当使用客户端证书或 MSI 身份验证，这是最佳做法。

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>后续步骤

若要更详细地了解密钥保管库身份验证，请参阅以下文档。 [Key Vault 身份验证](./authentication.md)
