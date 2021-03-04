---
title: 管理 Azure Active Directory 服务帐户
description: 在 Azure Active Directory 中管理服务帐户生命周期的原则和过程。
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
ms.openlocfilehash: 4311d0acc7c417bf31c71f46e6c25c65312b894d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034519"
---
# <a name="governing-azure-ad-service-accounts"></a>管理 Azure AD 服务帐户

Azure Active Directory (Azure AD 中有三种类型的服务帐户) ： [托管标识](service-accounts-managed-identities.md)、 [服务主体](service-accounts-principal.md)和用作服务帐户的用户帐户。 当你创建这些服务帐户以供自动使用时，它们将被授予访问 Azure 和 Azure AD 中的资源的权限。 资源可以包括 Microsoft 365 服务、软件即服务 (SaaS) 应用程序、自定义应用程序、数据库、HR 系统等。 管理 Azure AD 服务帐户意味着，你可以管理其创建、权限和生命周期，以确保安全性和连续性。

> [!IMPORTANT] 
> 我们不建议使用用户帐户作为服务帐户，因为这些帐户在本质上不太安全。 这包括同步到 Azure AD 的本地服务帐户，因为它们不会转换为服务主体。 相反，我们建议使用托管标识或服务主体。 请注意，在这种情况下，不能使用条件性访问策略和服务主体，但该功能即将推出。


## <a name="plan-your-service-account"></a>规划服务帐户

在创建服务帐户或注册应用程序之前，请记录服务帐户的密钥信息。 记录信息后，可以更轻松地监视和管理帐户。 建议收集以下数据，并在集中式配置管理数据库 (CMDB) 中对其进行跟踪。

| 数据| 说明| 详细信息 |
| - | - | - |
| “所有者”| 负责管理和监视服务帐户的用户或组。| 使用必要的权限预配所有者，以监视帐户并实现一种方法来缓解问题。 问题的缓解可能由所有者或通过请求来实现。 |
| 目的| 如何使用帐户。| 将服务帐户映射到特定服务、应用程序或脚本。 避免创建多用途服务帐户。 |
| 权限 (范围) | 预期的权限集。| 记录它将访问的资源以及这些资源的权限。 |
| CMDB 链接| 链接到要访问的资源，以及使用服务帐户的脚本。| 确保记录资源和脚本所有者，以便能够与更改的任何必要的上游和下游影响进行通信。 |
| 风险评估| 如果帐户受到损害，风险和业务影响。| 使用此信息来缩小权限范围，并确定谁有权访问帐户信息。 |
| 要查看的时间段| 所有者要查看服务帐户的计划。| 使用此计划来计划查看通信和评论。 记录在计划评审期后的特定时间未执行评审时应发生的情况。 |
| 生存期| 预期帐户的最大生存期。| 使用此计划安排与所有者的通信，并最终禁用并删除帐户。 在可能的情况下，为凭据设置过期日期，其中凭据无法自动滚动。 |
| 名称| 帐户的标准化名称| 创建所有服务帐户的命名架构，以便您可以轻松地对服务帐户进行搜索、排序和筛选。 |


## <a name="use-the-principle-of-least-privileges"></a>使用最少特权原则
仅授予服务帐户执行其任务所必需的权限，而不是更多。 如果服务帐户需要高级权限（例如全局管理员权限级别），请评估原因并尝试减少必需的权限。

建议为服务帐户特权执行以下操作。

**权限**

* 不要将内置角色分配给服务帐户。 相反，请使用 [Microsoft Graph 的 OAuth2 权限授予模型](/graph/api/resources/oauth2permissiongrant)，

* 如果必须为服务主体分配特权角色，请考虑以时间限制的方式将 [自定义角色](https://docs.microsoft.com/azure/active-directory/roles/custom-create) 分配给特定的、必需的特权。

* 请勿将服务帐户包括为具有提升权限的任何组的成员。 

* [使用 PowerShell 枚举特权角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)，例如   
`Get-AzureADDirectoryRoleMember`和筛选 objectType "服务主体"。

   或使用  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [使用 OAuth 2.0 范围](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) 限制服务帐户可访问资源的功能。
* 服务主体和托管标识可以在正在模拟登录用户的委托上下文中使用 OAuth 2.0 作用域，或者在应用程序上下文中将其用作服务帐户。 在应用程序上下文中，未登录。

* 检查范围服务帐户对资源的请求，以确保它们是合适的。 例如，如果某个帐户正在请求文件. ReadWrite，则评估它是否确实只需要 File. Read. All。 有关权限的详细信息，请参阅 to [Microsoft Graph 权限引用](https://docs.microsoft.com/graph/permissions-reference)。

* 确保将应用程序或 API 的开发人员与请求资源的访问权限进行了信任。

**持续时间**

* 将客户端机密 (的服务帐户凭据限制) 到预期的使用期限。

* 计划定期查看服务帐户的使用和目的。 确保在帐户过期之前执行审阅。

明确了解用途、范围和必要权限后，创建服务帐户。 

[创建和使用托管标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

[创建和使用服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

尽可能使用托管标识。 如果无法使用托管标识，请使用服务主体。 如果无法使用服务主体，则只能使用 Azure AD 的用户帐户。

 

## <a name="build-a-lifecycle-process"></a>构建生命周期过程

管理服务帐户的生命周期从规划开始，并以永久删除方式结束。 

本文先前介绍了规划和创建部分。 还必须监视、查看权限、确定帐户的持续使用情况，并最终取消设置帐户。

### <a name="monitor-service-accounts"></a>监视服务帐户

主动监视服务帐户，以确保服务帐户的使用模式反映了预期模式，并且服务帐户仍在使用。

**使用以下方法之一收集和监视服务帐户登录：**

* 使用 Azure AD Sign-In Azure AD 门户中的日志。

* 将 Azure AD Sign-In 日志导出到 [Azure 存储](https://docs.microsoft.com/azure/storage/)、 [azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)或 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/logs/data-platform-logs)。


![显示服务主体登录屏幕的屏幕截图。](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**你应在 Sign-In 日志中查找的智能包括：**

* 是否有服务帐户无法再登录到租户？

* 服务帐户的登录模式是否正在更改？

建议导出 Azure AD 登录日志，并将其导入现有的安全信息和事件管理 (SIEM) 工具，如 Azure Sentinel。 使用 SIEM 生成警报和仪表板。

### <a name="review-service-account-permissions"></a>查看服务帐户权限

定期查看服务帐户所授予的权限和范围，以确定是否可以减少这些权限。

* 使用 [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) [构建自动化，以检查和记录](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) 向服务帐户授予同意的作用域。

* 使用 PowerShell [查看现有服务主体的凭据](https://github.com/AzureAD/AzureADAssessment) 并检查其有效性。

* 不要将服务主体的凭据设置为 "永不过期"。

* 尽可能使用 Azure KeyVault 中存储的证书或凭据。

Microsoft 的免费 PowerShell 示例收集了服务主体的 OAuth2 授权和凭据信息，将它们记录在逗号分隔值文件中 (CSV) ，并使用 Power BI 示例仪表板来解释和使用数据。 有关详细信息，请参阅 [AzureAD/AzureADAssessment：用于评估 Azure AD 租户状态和配置的工具 (github.com) ](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>再次验证服务帐户使用

建立审阅过程，以确保服务帐户定期按其所有者和安全或 IT 团队进行审核。 

**该过程应包括：**

* 如何确定应该在 CMDB) 中记录的每个服务帐户的检查循环 (。

* 在评审开始之前，与所有者和安全或 IT 团队之间的通信。

* 缺少评审时的警告通信的时间和内容。

* 有关所有者未能查看或响应时要执行的操作的说明。 例如，你可能想要禁用 (但不删除帐户) ，直到完成审阅。

* 有关如何确定上游和下游依赖关系并向其他资源所有者通知任何影响的说明。

**审查应包括所有者及其 IT 合作伙伴验证：**

* 帐户仍是必需的。

* 授予该帐户的权限是足够的和必要的，或者需要进行更改。

* 帐户的访问权限和凭据是受控制的。

* 帐户使用的凭据适用于对帐户进行评估的风险 (凭据类型和凭据生存期) 

* 自上一次重新认证以来，帐户的风险评分尚未更改

* 在帐户的预期生存期和下一个重新认证日期进行更新。

### <a name="deprovision-service-accounts"></a>取消预配服务帐户

* * 在下列情况下取消设置服务帐户： * * * *

* 为其创建服务帐户的脚本或应用程序已停用。

* 脚本或应用程序中的函数使用服务帐户进行 (例如，对特定资源) 的访问被停用。


* 服务帐户被替换为不同的服务帐户。

* 凭据已过期，或者该帐户不起作用，并且没有任何投诉。

**取消预配的进程应包括以下任务。**

1. 取消预配关联的应用程序或脚本后，可通过服务帐户 [监视登录](../reports-monitoring/concept-all-sign-ins.md#sign-ins-report) 和资源访问。

   * 如果帐户仍处于活动状态，则在执行后续步骤之前，确定其使用方式。
 
2. 如果这是托管服务标识，则禁用服务帐户登录，但不要将其从目录中删除。

3. 撤消角色分配，并对服务帐户 OAuth2 许可授予。

4. 在定义的时间段过后，如果所有者出现了大量警告，请从目录中删除该服务帐户。

## <a name="next-steps"></a>后续步骤
有关保护 Azure 服务帐户的详细信息，请参阅：

[Azure 服务帐户简介](service-accounts-introduction-azure.md)

[保护托管标识](service-accounts-managed-identities.md)

[保护服务原则](service-accounts-principal.md)




 

 
