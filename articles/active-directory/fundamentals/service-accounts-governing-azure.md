---
title: 管控 Azure Active Directory 服务帐户
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
ms.openlocfilehash: aec870e6027d9a6d2e6c3b66363f93c73fad5e78
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064136"
---
# <a name="governing-azure-ad-service-accounts"></a>管控 Azure AD 服务帐户

Azure Active Directory (Azure AD) 中有三种类型的服务帐户：[托管标识](service-accounts-managed-identities.md)、[服务主体](service-accounts-principal.md)和用作服务帐户的用户帐户。 当创建这些服务帐户以进行自动使用时，将向它们授予访问 Azure 和 Azure AD 中资源的权限。 资源可以包括 Microsoft 365 服务、软件即服务 (SaaS) 应用程序、自定义应用程序、数据库、HR 系统等。 管控 Azure AD 服务帐户意味着管理其创建、权限和生命周期，以确保安全性和连续性。

> [!IMPORTANT] 
> 不建议使用用作服务帐户的用户帐户，因为它们本身就不太安全。 这包括同步到 Azure AD 的本地服务帐户，因为它们不会转换为服务主体。 相反，建议使用托管标识或服务主体。 请注意，此时不能对服务主体使用条件访问策略，但该功能即将推出。


## <a name="plan-your-service-account"></a>规划服务帐户

创建服务帐户或注册应用程序之前，请记录服务帐户的密钥信息。 记录信息后，可以更轻松地监视和管控帐户。 建议收集以下数据并在集中式配置管理数据库 (CMDB) 中对其进行跟踪。

| 数据| 说明| 详细信息 |
| - | - | - |
| “所有者”| 负责管理和监视服务帐户的用户或组。| 为所有者提供必要的权限以监视帐户并实施一种缓解问题的方法。 可由所有者或通过请求 IT 来实现问题缓解。 |
| 目的| 如何使用帐户。| 将服务帐户映射到特定服务、应用程序或脚本。 避免创建多用途服务帐户。 |
| 权限（范围）| 预期权限集。| 记录它将访问的资源以及对这些资源的权限。 |
| CMDB 链接| 链接到要访问的资源以及在其中使用服务帐户的脚本。| 确保记录资源和脚本所有者，以便能够与更改的任何必要的上游和下游影响进行通信。 |
| 风险评估| 如果帐户受损将产生的风险和业务影响。| 使用此信息来缩小权限范围，并确定谁应有权访问帐户信息。 |
| 审查时段| 所有者查看服务帐户的时间安排。| 使用它来安排审查沟通和审查。 记录在计划评审期后如若在特定时间未执行审查应发生的情况。 |
| 生存期| 帐户预计最长生存期。| 使用它来安排与所有者的沟通，并最终禁用然后再删除帐户。 在可能的情况下，为凭据设置到期日期，这样凭据无法自动展期。 |
| 名称| 帐户的标准化名称| 为所有服务帐户创建命名架构，以便可以轻松地对服务帐户进行搜索、排序和筛选。 |


## <a name="use-the-principle-of-least-privileges"></a>使用最低特权原则
仅授予服务帐户执行其任务所必需的权限，不再授予更多。 如果服务帐户需要高级权限（例如全局管理员级别的权限），请评估原因并尝试降低必需的权限。

建议为服务帐户特权应用以下做法。

**权限**

* 不要将内置角色分配给服务帐户。 改而使用 [Microsoft Graph 的 OAuth2 权限授予模型](/graph/api/resources/oauth2permissiongrant)，

* 如果必须为服务主体分配特权角色，请考虑在限制时间的情况下为[自定义角色](../roles/custom-create.md)分配特定的必需特权。

* 请勿将服务帐户纳入具有提升权限的任何组的成员。 

* [使用 PowerShell 枚举特权角色的成员](/powershell/module/azuread/get-azureaddirectoryrolemember)，例如   
‎`Get-AzureADDirectoryRoleMember`并筛选 objectType“服务主体”。

   或者使用  
‎   `Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [使用 OAuth 2.0 范围](../develop/v2-permissions-and-consent.md)来限制服务帐户可访问资源的哪些功能。
* 服务主体和托管标识可以在正在模拟登录用户的委托上下文中使用 OAuth 2.0 范围，或者在应用程序上下文中将其用作服务帐户。 在应用程序上下文中没有用户登录。

* 检查服务帐户请求资源的范围，以确保它们是合适的。 例如，如果帐户正在请求 Files.ReadWrite.All，则评估它实际上是否只需要 File.Read.All。 有关权限的详细信息，请参阅 [Microsoft Graph 权限参考](/graph/permissions-reference)。

* 确保信任应用程序或 API 的开发人员使他们拥有访问资源所需的权限。

**持续时间**

* 将服务帐户凭据（客户端密码、证书）限制为预计的使用期限。

* 安排定期审查服务帐户的使用和目的。 确保在帐户过期之前进行审查。

明确了解用途、范围和必要权限后，创建自己的服务帐户。 

[创建和使用托管标识](../../app-service/overview-managed-identity.md?tabs=dotnet)

[创建和使用服务主体](../develop/howto-create-service-principal-portal.md)

尽可能使用托管标识。 如果无法使用托管标识，则使用服务主体。 如果无法使用服务主体，则使用 Azure AD 用户帐户，也只有在这种情况下才使用 Azure AD 帐户。

 

## <a name="build-a-lifecycle-process"></a>构建生命周期过程

管理服务帐户的生命周期始于规划并终于永久性删除。 

本文先前介绍了规划和创建部分。 还必须监视、审查权限、确定帐户的持续使用情况，并最终取消设置帐户。

### <a name="monitor-service-accounts"></a>监视服务帐户

主动监视服务帐户，以确保服务帐户的使用模式反映了预期模式，并且服务帐户仍在使用。

使用以下方法之一收集和监视服务帐户登录：

* 在 Azure AD 门户中使用 Azure AD 登录日志。

* 将 Azure AD 登录日志导出到 [Azure 存储](../../storage/index.yml)、[Azure 事件中心](../../event-hubs/index.yml)或 [Azure Monitor](../../azure-monitor/logs/data-platform-logs.md)。


![显示服务主体登录屏幕的屏幕截图。](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

应在登录日志中查找的信息包括：

* 是否有服务帐户不再登录到租户？

* 服务帐户的登录模式是否不断更改？

建议导出 Azure AD 登录日志，并将其导入现有的安全信息和事件管理 (SIEM) 工具，如 Azure Sentinel。 使用 SIEM 生成警报和仪表板。

### <a name="review-service-account-permissions"></a>审查服务帐户权限

定期审查授予的权限和服务帐户访问的范围，以确定是否可以减少甚至去除这些权限。

* 使用 [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) [生成自动化，以检查和记录](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)向服务帐户授予批准的范围。

* 使用 PowerShell [审查现有服务主体的凭据](https://github.com/AzureAD/AzureADAssessment)并检查其有效性。

* 不要将服务主体的凭据设置为“永不过期”。

* 尽可能使用 Azure KeyVault 中存储的证书或凭据。

Microsoft 的免费 PowerShell 示例收集了服务主体的 OAuth2 授权和凭据信息，将它们记录在逗号分隔值文件 (CSV) 和用以解释和使用数据的 Power BI 示例仪表板中。 有关详细信息，请参阅 [AzureAD/AzureADAssessment：用于评估 Azure AD 租户状态和配置的工具 (github.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>再次验证服务帐户使用

建立审查流程以确保服务帐户定期由其所有者和安全或 IT 团队进行审核。 

该流程应包括：

* 如何确定每个服务帐户的审查周期（应记录在 CMDB 中）。

* 在审查开始之前与所有者和安全或 IT 团队进行沟通。

* 错过审查时有关警告的沟通的时间和内容。

* 有关所有者未能进行审查或响应时要执行的操作的说明。 例如，你可能想要禁用（但不删除）帐户，直到完成审查。

* 有关如何确定上游和下游依赖关系并向其他资源所有者通知任何影响的说明。

审查应包括所有者及其 IT 合作伙伴验证以下内容：

* 帐户仍是必需的。

* 授予该帐户的权限是足够的和必要的，还是需要进行更改。

* 帐户的访问权限和凭据是受控制的。

* 就帐户评估的风险来看，帐户使用的凭据是合适的（凭据类型和凭据生存期）

* 自上次重新验证以来，帐户的风险评分尚未更改

* 对预计帐户生存期和下一个重新验证日期进行更新。

### <a name="deprovision-service-accounts"></a>取消预配服务帐户

**在下列情况下取消设置服务帐户：****

* 为其创建了服务帐户的脚本或应用程序已停用。

* 使用该服务帐户的脚本或应用程序中的功能（例如，访问特定的资源）已停用。


* 该服务帐户替换为另一服务帐户。

* 凭据已过期，或者该帐户不起作用，并且没有收到任何投诉。

取消预配的流程应包括以下任务。

1. 取消预配关联的应用程序或脚本后，[监视登录](../reports-monitoring/concept-sign-ins.md)和服务帐户的资源访问。

   * 如果帐户仍处于活动状态，则在执行后续步骤之前确定其使用方式。
 
2. 如果这是托管服务标识，则禁止服务帐户登录，但不要将其从目录中删除。

3. 撤消服务帐户的角色分配以及 OAuth2 许可批准。

4. 在定义的时段过后，并且所有者出现了大量警告，则从目录中删除该服务帐户。

## <a name="next-steps"></a>后续步骤
有关保护 Azure 服务帐户的详细信息，请参阅：

[Azure 服务帐户简介](service-accounts-introduction-azure.md)

[保护托管标识](service-accounts-managed-identities.md)

[保护服务主体](service-accounts-principal.md)




 

