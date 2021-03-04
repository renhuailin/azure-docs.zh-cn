---
title: 保护 Azure Active Directory 中的托管标识
description: 说明如何查找、评估和提高托管标识的安全性。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88a7600239d6e960fa2e635c9e7d9049a7c02db3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032343"
---
# <a name="securing-managed-identities"></a>保护托管标识

通常，开发人员需要管理机密和凭据，以便保护不同服务之间的通信。 托管标识是 Azure Active Directory (Azure AD 创建的) 标识，以提供 Azure 资源的标识。

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>使用 Azure 资源的托管标识的优点

下面是使用托管标识的优点：

* 你无需管理凭据， 使用托管标识，凭据完全受 Azure 管理、轮替和保护。 将自动提供和删除 Azure 资源的标识。 托管标识使 Azure 资源可以与支持 Azure AD 身份验证的所有服务进行通信。

* 没有任何一个 (包括任何全局管理员) 都有权访问这些凭据，因此它们不会被无意中泄漏，例如，在代码中包括在内。

## <a name="when-to-use-managed-identities"></a>何时使用托管标识？

托管标识最好用于支持 Azure AD 身份验证的服务之间的通信。 

源系统请求访问目标服务。 任何 Azure 资源都可以是源系统。 例如，Azure VM、Azure Function 实例和 Azure 应用服务实例支持托管标识。

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>身份验证和授权工作原理

使用托管标识时，源系统可以从 Azure AD 获取令牌，而无需源所有者管理凭据。 Azure 管理凭据。 源系统获取的令牌将提供给目标系统进行身份验证。 

目标系统需要进行身份验证 (确定) 并授权源系统，然后才允许访问。 当目标服务支持基于 Azure AD 的身份验证时，它将接受 Azure AD 颁发的访问令牌。 

Azure 具有控制面和数据平面。 在控制平面中，可以创建资源，并在访问这些资源的数据平面中创建资源。 例如，您在控制平面中创建 Cosmos 数据库，但在数据平面中进行查询。

目标系统接受用于身份验证的令牌后，它可以支持不同的机制来授权其控制面和数据平面。

所有 Azure 控制平面操作都由 [azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) 进行管理，并使用 [基于 Azure 角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)。 在数据平面中，每个目标系统都有其自己的授权机制。 Azure 存储支持数据平面上的 Azure RBAC。 例如，使用 Azure 应用 Services 的应用程序可以从 Azure 存储读取数据，使用 Azure Kubernetes Service 的应用程序可以读取存储在 Azure Key Vault 中的机密。

有关控制和数据平面的详细信息，请参阅 [控制平面和数据平面操作-Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane)。

所有 Azure 服务最终将支持托管标识。 有关详细信息，请参阅 [支持 Azure 资源的托管标识的服务](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)。

##  

## <a name="types-of-managed-identities"></a>托管标识的类型

提供了两种类型的托管标识：系统分配和用户分配。

系统分配的托管标识具有以下属性：

* 它们与 Azure 资源之间存在1:1 关系。 例如，有一个与每个 VM 关联的唯一托管标识。

* 它们与 Azure 资源的生命周期相关联。 删除资源时，与之关联的托管标识会自动删除，从而消除了与孤立帐户关联的风险。 

用户分配的托管标识具有以下属性：

* 这些标识的生命周期独立于 Azure 资源，你必须管理生命周期。 删除 Azure 资源后，不会自动删除分配的用户分配的托管标识。

* 可以将单个用户分配的托管标识分配给零个或多个 Azure 资源。

* 可以提前创建这些资源，然后将其分配给资源。

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>在 Azure AD 中查找托管标识服务主体

可以通过多种方式来查找托管标识：

* 使用 Azure 门户中的 "企业应用程序" 页

* 使用 Microsoft Graph

### <a name="using-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure AD 中，选择 "企业应用程序"。

2. 选择 "托管标识" 的筛选器 

   !["所有应用程序" 屏幕上的 "应用程序类型" 下拉列表中突出显示 "托管标识"。](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>使用 Microsoft Graph

你可以使用以下 GET 请求来获取租户中所有托管标识的列表 Microsoft Graph：

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

可以筛选这些请求。 有关详细信息，请参阅 [GET servicePrincipal](/graph/api/serviceprincipal-get?view=)的关系图文档。

## <a name="assess-the-security-of-managed-identities"></a>评估托管标识的安全性 

可以通过以下方式评估托管标识的安全性：

* 检查权限并确保选择最小特权模型。 使用以下 PowerShell cmdlet 获取分配给托管标识的权限。

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* 确保托管标识不是任何特权组（如 administrators 组）的一部分。  
可以通过使用 PowerShell 枚举高度特权组的成员来实现此目的。

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [确保你知道托管标识正在访问哪些资源](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell)。

## <a name="move-to-managed-identities"></a>移动到托管标识

如果你使用的是服务主体或 Azure AD 用户帐户，请评估是否可以改为使用托管的，以避免需要保护、轮换和管理凭据。 

## <a name="next-steps"></a>后续步骤

**有关创建托管标识的信息，请参阅：** 

[创建用户分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)。 

[在资源创建过程中启用系统分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[对现有资源启用系统分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**有关服务帐户的详细信息，请参阅：**

[Azure Active Directory 服务帐户简介](service-accounts-introduction-azure.md)

[保护服务主体](service-accounts-principal.md)

[管理 Azure 服务帐户](service-accounts-governing-azure.md)

[本地服务帐户简介](service-accounts-on-premises.md)

 

 

 
