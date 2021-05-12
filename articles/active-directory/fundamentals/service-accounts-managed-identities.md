---
title: 在 Azure Active Directory 中保护托管标识
description: 介绍如何查找、评估和提高托管标识的安全性。
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
ms.openlocfilehash: 0a08c464e5c6a1fb5d4337e0902c9bd84b3b7772
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109732913"
---
# <a name="securing-managed-identities"></a>保护托管标识

开发人员通常面临如何管理密码和凭据，以确保不同服务之间的通信安全的挑战。 托管标识是安全的 Azure Active Directory (Azure AD) 标识，创建的目的是为 Azure 资源提供标识。

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>对 Azure 资源使用托管标识的优势

使用托管标识的优势如下：

* 你无需管理凭据， 使用托管标识时，凭据完全受 Azure 管理、轮换和保护。 标识将自动提供，并与 Azure 资源一起删除。 托管标识使 Azure 资源可以与支持 Azure AD 身份验证的所有服务进行通信。

* 任何人（包括任何全局管理员）都无权访问凭据，因此凭据不会被意外泄露，例如，包含在代码中。

## <a name="when-to-use-managed-identities"></a>何时使用托管标识？

托管标识最好用于在支持 Azure AD 身份验证的服务之间通信。 

源系统请求访问目标服务。 任何 Azure 资源都可以是源系统。 例如，Azure VM、Azure Function 实例和 Azure 应用服务实例支持托管标识。

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>身份验证和授权的工作原理

使用托管标识时，源系统可以从 Azure AD 获取令牌，而无需源所有者管理凭据。 Azure 管理凭据。 源系统获取的令牌将提供给目标系统进行身份验证。 

目标系统需要对源系统进行身份验证（确定身份）和授权之后才允许访问。 当目标服务支持基于 Azure AD 的身份验证时，它将接受 Azure AD 颁发的访问令牌。 

Azure 具有控制平面和数据平面。 在控制平面中可以创建资源，在数据平面中可以访问这些资源。 例如，在控制平面中创建 Cosmos 数据库，但在数据平面中进行查询。

目标系统接受用于身份验证的令牌后，可以支持控制平面和数据平面的不同授权机制。

Azure 的所有控制平面操作都由 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)管理，并使用[基于 Azure 角色的访问控制](../../role-based-access-control/overview.md)。 在数据平面中，每个目标系统都有自己的授权机制。 Azure 存储在数据平面上支持 Azure RBAC。 例如，使用 Azure 应用服务的应用程序可以从 Azure 存储读取数据，使用 Azure Kubernetes 服务的应用程序可以读取存储在 Azure Key Vault 中的机密。

有关控制平面和数据平面的详细信息，请参阅[控制平面和数据平面操作 - Azure 资源管理器](../../azure-resource-manager/management/control-plane-and-data-plane.md)。

所有 Azure 服务最终将支持托管标识。 有关详细信息，请参阅[支持 Azure 资源托管标识的服务](../managed-identities-azure-resources/services-support-managed-identities.md)。

## <a name="types-of-managed-identities"></a>托管标识类型

有两种类型的托管标识：系统分配的托管标识和用户分配的托管标识。

系统分配的托管标识具有以下属性：

* 它们与 Azure 资源之间为 1:1 关系。 例如，每个 VM 关联一个唯一的托管标识。

* 它们与 Azure 资源的生命周期相关联。 删除资源时，与之关联的托管标识会自动删除，从而消除了与孤立帐户关联的风险。 

用户分配的托管标识具有以下属性：

* 这些标识的生命周期独立于 Azure 资源，你必须管理生命周期。 删除 Azure 资源时，用户分配的托管标识不会自动删除。

* 可以将单个用户分配的托管标识分配给零个或多个 Azure 资源。

* 可以提前创建这些托管标识，然后分配给资源。

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>在 Azure AD 中查找托管标识服务主体

可以通过多种方式来查找托管标识：

* 使用 Azure 门户中的“企业应用程序”页

* 使用 Microsoft Graph

### <a name="using-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure Active Directory 中选择“企业应用程序”。

2. 选择“托管标识”筛选器。 

   ![“所有应用程序”屏幕图，其中“应用程序类型”下拉列表中突出显示“托管标识”。](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>使用 Microsoft Graph

你可以使用向 Microsoft Graph 发出的以下 GET 请求来获取租户中所有托管标识的列表：

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

可以对这些请求进行筛选。 有关详细信息，请参阅 Graph 文档中的 [GET servicePrincipal](/graph/api/serviceprincipal-get)。

## <a name="assess-the-security-of-managed-identities"></a>评估托管标识的安全性 

可以通过以下方式评估托管标识的安全性：

* 检查特权并确保选择最小特权模型。 使用以下 PowerShell cmdlet 获取分配给托管标识的权限。

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* 确保托管标识不属于任何特权组（如管理员组）。  
‎可以通过使用 PowerShell 枚举具有高特权的组的成员来实现此目的。

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [确保你了解托管标识正在访问哪些资源](../../role-based-access-control/role-assignments-list-powershell.md)。

## <a name="move-to-managed-identities"></a>移动到托管标识

如果你使用的是服务主体或 Azure AD 用户帐户，请评估是否可以改用托管标识，这样即无需保护、轮换和管理凭据。 

## <a name="next-steps"></a>后续步骤

有关创建托管标识的信息，请参阅： 

[创建用户分配的托管标识](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

[在资源创建过程中启用系统分配的托管标识](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[在现有资源上启用系统分配的托管标识](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

有关服务帐户的详细信息，请参阅：

[Azure Active Directory 服务帐户简介](service-accounts-introduction-azure.md)

[保护服务主体](service-accounts-principal.md)

[管控 Azure 服务帐户](service-accounts-governing-azure.md)

[本地服务帐户简介](service-accounts-on-premises.md)

 

 

