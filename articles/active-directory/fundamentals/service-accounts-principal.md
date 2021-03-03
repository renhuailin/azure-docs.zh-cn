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
ms.openlocfilehash: 1c9df24a255d640c463bd62e8bdfe8a576df3b09
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693006"
---
# <a name="securing-service-principals"></a>保护服务主体

) [服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) Azure Active Directory (Azure AD 是单个租户或目录中应用程序对象的本地表示形式。  它充当应用程序实例的标识。 服务主体定义谁可以访问应用程序，以及应用程序可以访问哪些资源。 在使用应用程序的每个租户中创建一个服务主体，并引用全局唯一的应用程序对象。 租户保护服务主体的登录和资源访问权限。  

### <a name="tenant-service-principal-relationships"></a>租户-服务主体关系
单租户应用程序的主租户中只有一个服务主体。 多租户 web 应用程序或 API 需要每个租户中的服务主体。 当该租户中的用户同意应用程序或 API 使用时，将创建服务主体。 此同意将在多租户应用程序及其关联的服务主体之间创建一对多关系。

一个多租户应用程序托管在一个租户中，并且设计为在其他租户中具有实例。 大多数软件即服务 (SaaS) 应用程序是针对多租户设计的。 使用服务主体确保应用程序及其用户在单租户和多租户用例中具有正确的安全状况。

## <a name="applicationid-and-objectid"></a>ApplicationID 和 ObjectID

给定的应用程序实例具有两个不同的属性： ApplicationID (也称为 ClientID) 和 ObjectID。

> [!NOTE] 
> 你可能会发现，当在与身份验证相关的任务的上下文中对应用程序进行松散引用时，术语 "应用程序" 和 "服务主体" 可互换使用。 但 Azure AD 中的应用程序有两种不同的表示形式。
 

ApplicationID 表示全局应用程序，对所有租户中的所有应用程序实例都是相同的。 ObjectID 是应用程序对象的唯一值，表示服务主体。 与用户、组和其他资源一样，ObjectID 有助于在 Azure AD 中唯一标识应用程序实例。

有关本主题的更多详细信息，请参阅 [应用程序和服务主体关系](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

你还可以使用 Azure PowerShell、Azure CLI、Microsoft Graph、Azure 门户和其他工具在租户中 (ObjectID) 创建应用程序及其服务主体对象。 

![显示新应用程序注册的屏幕截图，其中突出显示了 "应用程序 ID" 和 "对象 ID" 字段。](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>服务主体身份验证

使用服务主体进行身份验证的机制有两种：客户端证书和客户端机密。 

![ 新应用页的屏幕截图，其中突出显示了 "证书" 和 "客户端机密" 区域。](./media/securing-service-accounts/secure-principal-certificates.png)

证书更安全：如有可能，请使用客户端证书。 与客户端密码不同的是，客户端证书不能在代码中意外嵌入。 尽可能使用证书和密钥管理的 Azure Key Vault，使用受硬件安全模块保护的密钥来加密以下资产：

* 身份验证密钥

* 存储帐户密钥

* 数据加密密钥

* .pfx 文件

* 密码 

有关 Azure Key Vault 以及如何将其用于证书和密钥管理的详细信息，请参阅 [关于 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) 并 [使用 Azure 门户分配 Key Vault 访问策略](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)。 

 ### <a name="challenges-and-mitigations"></a>挑战和缓解措施
下表介绍了使用服务主体时可能会遇到的难题。


| 挑战| 缓解措施 |
| - | - |
| 分配给特权角色的服务主体的访问评审。| 此功能处于预览阶段，尚未广泛提供。 |
| 审查服务主体的访问权限| 使用 Azure 门户手动检查资源的访问控制列表。 |
| 超过授权的服务主体| 创建自动化服务帐户或服务主体时，请仅提供任务所需的权限。 评估现有的服务主体，查看是否可以降低权限。 |
|确定对服务主体的凭据或身份验证方法的修改 |使用敏感操作报告工作簿，有助于缓解此问题。 [请参阅此博客文章中的说明](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)。|

## <a name="find-accounts-using-service-principals"></a>使用服务主体查找帐户
运行以下命令，查找使用服务主体的帐户。

使用 Azure CLI


`az ad sp list`

使用 PowerShell

`Get-AzureADServicePrincipal -All:$true` 


有关详细信息，请参阅 [get-azureadserviceprincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)

## <a name="assess-service-principal-security"></a>评估服务主体安全性

若要评估服务主体的安全性，请确保评估权限和凭据存储。

使用以下信息减少潜在挑战。
|挑战 | 缓解措施|
| - | - |
| 检测同意多租户应用的用户，并检测对多租户应用的违法许可授予 | 运行以下 PowerShell 查找多租户应用。<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>禁用用户同意。 <br>允许用户同意已验证的发布者，为所选权限 (建议)  <br> 使用条件性访问阻止不受信任的位置中的服务主体。 在用户上下文下配置这些设置，并且应使用其令牌来触发服务主体。|
|使用服务主体在脚本中使用硬编码共享机密。|使用证书或 Azure Key Vault。|
|跟踪使用证书或机密的用户| 使用 Azure AD 登录日志监视服务主体的登录。|
无法使用条件访问管理服务主体的登录。| 使用 Azure AD 登录日志监视登录
| 默认的 Azure RBAC 角色为 "参与者"。 |评估需求，并使用尽可能少的权限应用该角色，以满足该需求。|

## <a name="move-from-a-user-account-to-a-service-principal"></a>从用户帐户移到服务主体  
如果使用 Azure 用户帐户作为服务主体，请评估是否可以移动到 [托管标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) 或服务主体。 如果无法使用托管标识，请预配具有足够权限和作用域的服务主体，以运行所需的任务。 可以通过 [注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)或 [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)来创建服务主体。

使用 Microsoft Graph 时，请查看特定 API 的文档， [如本示例中所示](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-5.0.0)，确保应用程序的权限类型显示为 "受支持"。

## <a name="next-steps"></a>后续步骤

**若要了解有关服务主体的详细信息：**

[创建服务主体](../develop/howto-create-service-principal-portal.md)

 [监视服务主体登录](../reports-monitoring/concept-all-sign-ins#sign-ins-report.md)

**若要详细了解如何保护服务帐户，请执行以下操作：**

[Azure 服务帐户简介](service-accounts-introduction-azure.md)

[保护托管标识](service-accounts-managed-identities.md)

[管理 Azure 服务帐户](service-accounts-governing-azure.md)

[本地服务帐户简介](service-accounts-on-poremises.md)
