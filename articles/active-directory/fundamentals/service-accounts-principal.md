---
title: 保护 Azure Active Directory 中的服务主体
description: 查找、评估和保护服务主体。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f101c8a3d3af9658414e816afff9050402d71ce
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064064"
---
# <a name="securing-service-principals"></a>保护服务主体

Azure Active Directory (Azure AD) [服务主体](../develop/app-objects-and-service-principals.md)是单个租户或目录中应用对象的本地表示形式。  ‎它可作为应用程序实例的标识。 服务主体定义谁可以访问应用程序，以及应用程序可以访问哪些资源。 在使用应用程序的每个租户中创建服务主体，并引用全局唯一的应用程序对象。 租户确保服务主体可登录并访问资源。  

### <a name="tenant-service-principal-relationships"></a>租户-服务主体关系
单租户应用程序在其宿主租户中只有一个服务主体。 多租户 Web 应用程序或 API 需要每个租户的服务主体。 当该租户的用户同意使用应用程序或 API 时，将创建服务主体。 此同意在多租户应用程序与其相关服务主体之间建立了一对多关系。

多租户应用程序位于单个租户中，旨在为其他租户提供实例。 大多数软件即服务 (SaaS) 应用程序都专为多租户而设计。 使用服务主体，确保在单租户和多租户使用案例中为应用程序及其用户提供正确的安全状况。

## <a name="applicationid-and-objectid"></a>ApplicationID 和 ObjectID

给定的应用程序实例具有两个不同的属性：ApplicationID（也称为 ClientID）和 ObjectID。

> [!NOTE] 
> 你可能会发现，在身份验证相关任务中松散地引用应用程序时，应用程序和服务主体的术语可互换使用。 然而，它们是 Azure AD 中应用的两种不同表示形式。
 

ApplicationID 代表全局应用程序，对于租户的所有应用程序实例都是一样的。 ObjectID 是应用程序对象的唯一值，代表服务主体。 与用户、组和其他资源一样，ObjectID 有助于在 Azure AD 中唯一标识应用程序实例。

有关本主题的详细信息，请参阅[应用程序和服务主体关系](../develop/app-objects-and-service-principals.md)。

还可使用 Azure PowerShell、Azure CLI、Microsoft Graph 和 Azure 门户以及其他工具在租户中创建应用程序及其服务主体对象 (ObjectID)。 

![显示新的应用程序注册的屏幕截图，其中突出显示了“应用程序 ID”和“对象 ID”字段。](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>服务主体身份验证

使用服务主体的身份验证有两种机制：客户端证书和客户端机密。 

![ “新应用”页的屏幕截图，其中突出显示了“证书”和“客户端机密”区域。](./media/securing-service-accounts/secure-principal-certificates.png)

证书更安全：如果可能，请使用客户端证书。 与客户端机密不同，客户端证书不能被意外嵌入代码中。 应尽可能使用 Azure Key Vault 进行证书和机密管理，通过使用受硬件安全模块保护的密钥加密以下资产：

* 身份验证密钥

* 存储帐户密钥

* 数据加密密钥

* .pfx 文件

* 密码 

有关 Azure Key Vault 以及如何将其用于证书和机密管理的详细信息，请参阅[关于 Azure Key Vault](../../key-vault/general/overview.md) 和[使用 Azure 门户分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-portal.md)。 

 ### <a name="challenges-and-mitigations"></a>挑战和缓解措施
下表为你在使用服务主体时可能遇到的挑战提供了缓解措施。


| 挑战| 缓解措施 |
| - | - |
| 针对分配给特权角色的服务主体的访问评审。| 此功能处于预览阶段，尚未广泛提供。 |
| 评审服务主体的访问权限| 使用 Azure 门户手动检查资源的访问控制列表。 |
| 过度授权的服务主体| 创建自动化服务帐户或服务主体时，仅提供任务所需的权限。 评估现有服务主体，看看是否可以减少特权。 |
|识别对服务主体凭据或身份验证方法的修改 |使用 Sensitive Operations Report 工作簿帮助缓解此问题。 [请参阅此博客文章中的说明](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)。|

## <a name="find-accounts-using-service-principals"></a>使用服务主体查找帐户
运行以下命令，使用服务主体查找帐户。

使用 Azure CLI


`az ad sp list`

使用 PowerShell

`Get-AzureADServicePrincipal -All:$true` 


有关详细信息，请参阅 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>评估服务主体安全性

若要评估服务主体的安全性，请确保评估特权和凭据存储。

使用以下信息缓解潜在挑战。

|挑战 | 缓解措施|
| - | - |
| 检测同意多租户应用程序的用户，并检测对多租户应用的非法同意授权 | 运行以下 PowerShell 以找到多租户应用。<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>禁用用户同意。 <br>允许用户同意来自经过验证的发布者具有所选权限（推荐） <br> 使用条件访问来阻止来自不受信任位置的服务主体。 在用户上下文下配置它们，并且应使用其令牌来触发服务主体。|
|使用服务主体在脚本中使用硬编码共享机密。|使用证书或 Azure Key Vault。|
|跟踪使用证书或机密的用户| 使用 Azure AD 登录日志监视服务主体的登录。|
无法通过条件访问管理服务主体的登录。| 使用 Azure AD 登录日志监视登录
| 默认的 Azure RBAC 角色是“参与者”。 |评估需求，并应用具有尽可能少的权限的角色来满足这一需求。|

## <a name="move-from-a-user-account-to-a-service-principal"></a>从用户帐户移动到服务主体  
‎如果使用 Azure 用户帐户作为服务主体，请评估是否可以移动到[托管标识](../../app-service/overview-managed-identity.md?tabs=dotnet)或服务主体。 如果不能使用托管标识，请预配仅有足够的权限和范围来运行所需任务的服务主体。 可以通过[注册应用程序](../develop/howto-create-service-principal-portal.md)或 [PowerShell](../develop/howto-authenticate-service-principal-powershell.md) 来创建服务主体。

使用 Microsoft Graph 时，请检查特定 API 的文档（[如本示例中所示](/powershell/azure/create-azure-service-principal-azureps)），‎并确保应用程序的权限类型显示为支持。

## <a name="next-steps"></a>后续步骤

**详细了解服务主体：**

[创建服务主体](../develop/howto-create-service-principal-portal.md)

 [监视服务主体登录](../reports-monitoring/concept-sign-ins.md)

**详细了解保护服务帐户：**

[Azure 服务帐户简介](service-accounts-introduction-azure.md)

[保护托管标识](service-accounts-managed-identities.md)

[管控 Azure 服务帐户](service-accounts-governing-azure.md)

[本地服务帐户简介](service-accounts-on-premises.md)
