---
title: Azure AD 内置角色-Azure Active Directory
description: 介绍 Azure Active Directory 内置角色和权限。
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 02/17/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65b91e3dff3ef412dad8bbe57383a9dbf8c7765
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032239"
---
# <a name="azure-ad-built-in-roles"></a>Azure AD 内置角色

使用 Azure Active Directory (Azure AD) 时，可以指定有限的管理员以权限较低的角色来管理标识任务。 可出于以下目的分配管理员：添加或更改用户、分配管理角色、重置用户密码、管理用户许可证，以及管理域名。 只能在 Azure AD 中的用户设置中更改[默认用户权限](../fundamentals/users-default-permissions.md)。

## <a name="limit-use-of-global-administrator"></a>限制全局管理员的使用

分配到全局管理员角色的用户可以读取和修改 Azure AD 组织中的每个管理设置。 默认情况下，当用户注册 Microsoft 云服务时，将创建一个 Azure AD 租户，并使该用户成为全局管理员角色的成员。 当你向现有租户添加订阅时，不会为你分配全局管理员角色。 只有全局管理员和特权角色管理员才能委派管理员角色。 为了降低业务风险，我们建议将此角色分配给组织中尽量少的人员。

建议将此角色分配给组织中五个以下的人员，这是最佳做法。 如果已将“全局管理员”角色分配给组织中五个以上的管理员，可通过以下方法减少该角色的使用。

### <a name="find-the-role-you-need"></a>找到所需的角色

如果你很难从包含众多角色的列表中找到所需的角色，Azure AD 可以根据角色类别显示角色的子集。 请查看适用于 [Azure AD 角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)的新“类型”筛选器，使用它可以仅显示所选类型的角色。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>由于你分配了 "全局管理员" 角色，因此存在一个不存在的角色

有可能将一个或多个角色添加到 Azure AD 中，这些权限在您将某些用户提升为全局管理员时不是一个选项。 随着时间的推移，我们将推出其他角色来完成只有全局管理员角色才能执行的任务。 可以在以下 [所有角色](#all-roles)中查看它们。

## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色

若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](manage-roles-portal.md)。

> [!Note]
> 如果你有 Azure AD Premium P2 许可证，并且已是 Privileged Identity Management (PIM) 用户，则所有角色管理任务都会在 Privilege Identity Management 中执行，而不会在 Azure AD 中执行。
>
> ![为已使用 PIM 且具有 Premium P2 许可证的用户在 PIM 中管理的 Azure AD 角色](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="all-roles"></a>所有角色

> [!div class="mx-tableFixed"]
> | 角色 | 说明 | 模板 ID |
> | --- | --- | --- |
> | [应用程序管理员](#application-administrator) | 可以创建和管理应用注册和企业应用的所有方面。 | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [应用程序开发人员](#application-developer) | 可以创建独立于“用户可注册应用程序”设置的应用程序注册。 | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [攻击有效负载作者](#attack-payload-author) | 可以创建管理员稍后可以启动的攻击负载。 | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [攻击模拟管理员](#attack-simulation-administrator) | 可以创建和管理攻击模拟活动的各个方面。 | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [身份验证管理员](#authentication-administrator) | 可以访问以查看、设置和重置任何非管理员用户的身份验证方法信息。 | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [身份验证策略管理员](#authentication-policy-administrator) | 可以创建和管理身份验证方法和密码保护策略的所有方面。 | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [已加入 Azure AD 的设备的本地管理员](#azure-ad-joined-device-local-administrator) | 分配到此角色的用户将添加到已加入 Azure AD 的设备上的本地管理员组。 | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps 管理员](#azure-devops-administrator) | 可以管理 Azure DevOps 的组织策略和设置。 | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure 信息保护管理员](#azure-information-protection-administrator) | 可以管理 Azure 信息保护产品的所有方面。 | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF 密钥集管理员](#b2c-ief-keyset-administrator) | 可以在标识体验框架中管理用于联合身份验证和加密的机密 (IEF) 。 | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF 策略管理员](#b2c-ief-policy-administrator) | 可以在标识体验框架中创建和管理信任框架策略 (IEF) 。 | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [计费管理员](#billing-administrator) | 可以执行与常见计费相关的任务，例如更新付款信息。 | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [云应用程序管理员](#cloud-application-administrator) | 可以创建和管理应用注册和企业应用的所有方面，应用代理除外。 | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [云设备管理员](#cloud-device-administrator) | 在 Azure AD 中管理设备的有限访问权限。 | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [合规性管理员](#compliance-administrator) | 可以读取和管理 Azure AD 和 Microsoft 365 中的合规性配置和报表。 | 17315797-102d-40b4-93e0-432062caca18 |
> | [合规性数据管理员](#compliance-data-administrator) | 创建和管理合规性内容。 | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [条件访问管理员](#conditional-access-administrator) | 可以管理条件访问功能。 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [客户密码箱访问审批者](#customer-lockbox-access-approver) | 可以批准 Microsoft 支持人员访问客户组织数据的请求。 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [桌面分析管理员](#desktop-analytics-administrator) | 可以访问和管理桌面管理工具和服务。 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [目录读取者](#directory-readers) | 可以读取基本目录信息。 通常用于授予对应用程序和来宾的目录读取访问权限。 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [目录同步帐户](#directory-synchronization-accounts) | 仅供 Azure AD Connect 服务使用。 | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [目录写入者](#directory-writers) | 可以读取和写入基本的目录信息。 用于授予对应用程序的访问权限，不针对用户。 | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [域名管理员](#domain-name-administrator) | 可以在云和本地管理域名。 | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365 管理员](#dynamics-365-administrator) | 可以管理 Dynamics 365 产品的所有方面。 | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange 管理员](#exchange-administrator) | 可以管理 Exchange 产品的所有方面。 | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [外部 ID 用户流管理员](#external-id-user-flow-administrator) | 可以创建和管理用户流的所有方面。 | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [外部 ID 用户流属性管理员](#external-id-user-flow-attribute-administrator) | 可以创建和管理可用于所有用户流的属性架构。 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [外部标识提供者管理员](#external-identity-provider-administrator) | 可以配置标识提供程序以在直接联合中使用。 | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [全局管理员](#global-administrator) | 可以管理 Azure AD 和使用 Azure AD 标识的 Microsoft 服务的所有方面。 | 62e90394-69f5-4237-9190-012177145e10 |
> | [全局读取者](#global-reader) | 可以读取全局管理员可以使用的所有内容，但不能更新任何内容。 | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [组管理员](#groups-administrator) | 此角色的成员可以创建/管理组、创建/管理组设置（如命名和过期策略）以及查看组活动和审核报告。 | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [来宾邀请者](#guest-inviter) | 可以无视“成员可邀请来宾”设置而邀请来宾用户。 | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [支持管理员](#helpdesk-administrator) | 可以重置非管理员和支持理员的密码。 | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [混合标识管理员](#hybrid-identity-administrator) | 可以管理 AD 到 Azure AD 的云预配和联合设置。 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights 管理员](#insights-administrator) | 在 Microsoft 365 Insights 应用中具有管理访问权限。 | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights 业务主管](#insights-business-leader) | 可通过 M365 Insights 应用来查看和共享仪表板和见解。 | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune 管理员](#intune-administrator) | 可以管理 Intune 产品的所有方面。 | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala 管理员](#kaizala-administrator) | 可以管理 Microsoft Kaizala 的设置。 | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [许可证管理员](#license-administrator) | 可以管理用户和组的产品许可证。 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [消息中心隐私读取者](#message-center-privacy-reader) | 只能读取 Office 365 消息中心中的安全消息和更新。 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [消息中心读取者](#message-center-reader) | 只能在 Office 365 消息中心查看其组织的消息和更新。 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [现代商业用户](#modern-commerce-user) | 可以管理公司、部门或团队的商用购买内容。 | d24aef57-1500-4070-84db-2666f29cf966 |
> | [网络管理员](#network-administrator) | 可以管理网络位置，并审阅有关 Microsoft 365 软件即服务应用程序的企业网络设计见解。 | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office 应用管理员](#office-apps-administrator) | 可以管理 Office 应用的云服务，包括策略和设置管理，并管理对最终用户的设备的选择、取消选择和发布 "新增功能" 功能的功能。 | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [合作伙伴一线支持人员](#partner-tier1-support) | 不要使用 - 不适用于常规用途。 | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [合作伙伴二线支持人员](#partner-tier2-support) | 不要使用 - 不适用于常规用途。 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [密码管理员](#password-administrator) | 可以重置非管理员和密码管理员的密码。 | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI 管理员](#power-bi-administrator) | 可以管理 Power BI 产品的所有方面。 | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform 管理员](#power-platform-administrator) | 可以创建和管理 Microsoft Dynamics 365、PowerApps 和 Microsoft Flow 的所有方面。 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [打印机管理员](#printer-administrator) | 可以管理打印机和打印机连接器的所有方面。 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [打印机技术人员](#printer-technician) | 可以注册和取消注册打印机，并更新打印机状态。 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [特权身份验证管理员](#privileged-authentication-administrator) | 可以访问查看、设置和重置任何用户 (管理员或非管理员) 的身份验证方法信息。 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [特权角色管理员](#privileged-role-administrator) | 可以管理 Azure AD 中的角色分配，以及 Privileged Identity Management 的所有方面。 | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [报告读取者](#reports-reader) | 可以读取登录和审核报告。 | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [搜索管理员](#search-administrator) | 可以创建和管理 Microsoft 搜索设置的所有方面。 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [搜索编辑员](#search-editor) | 可以创建和管理书签、问答、位置、平面布置图等编辑内容。 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [安全管理员](#security-administrator) | 可以读取安全信息和报告，并管理 Azure AD 和 Office 365 中的配置。 | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [安全操作员](#security-operator) | 创建和管理安全事件。 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [安全读取者](#security-reader) | 可以读取 Azure AD 和 Office 365 中的安全信息和报表。 | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [服务支持管理员](#service-support-administrator) | 可以读取服务运行状况信息和管理支持票证。 | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint 管理员](#sharepoint-administrator) | 可以管理 SharePoint 服务的所有方面。 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype for Business 管理员](#skype-for-business-administrator) | 可以管理 Skype for Business 产品的所有方面。 | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams 管理员](#teams-administrator) | 可以管理 Microsoft Teams 服务。 | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams 通信管理员](#teams-communications-administrator) | 可以管理 Microsoft Teams 服务中的通话和会议功能。 | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Teams 通信支持工程师](#teams-communications-support-engineer) | 可以使用高级工具排查 Teams 中的通信问题。 | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Teams 通信支持专家](#teams-communications-support-specialist) | 可以使用基本工具排查 Teams 中的通信问题。 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams 设备管理员](#teams-devices-administrator) | 可在 Teams 认证的设备上执行管理相关任务。 | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [使用情况摘要报表读取者](#usage-summary-reports-reader) | 只能查看 Microsoft 365 使用情况分析和生产力评分中的租户级聚合。 | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [用户管理员](#user-administrator) | 可以管理用户和组的所有方面，包括重置有限管理员的密码。 | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>应用程序管理员

充当此角色的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 请注意，在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

此角色还授予许可委托权限和应用程序权限的能力，但对于 Microsoft Graph 和 Azure AD 关系图的应用程序权限例外。

> [!IMPORTANT]
> 此例外表明你仍可以同意 _其他_ 应用的应用程序权限 (例如，你已注册) 的非 Microsoft 应用或应用。 你仍可以在应用注册过程中 _请求_ 这些权限，但 _授予_ (即同意) 这些权限需要更高权限的管理员，如全局管理员。
>
>此角色授予管理应用程序凭据这一功能。 分配有此角色的用户可以将凭据添加到应用程序，并使用这些凭据模拟应用程序的标识。 如果已向应用程序的标识授予资源访问权限，例如创建或更新用户或其他对象，那么分配到此角色的用户在模拟应用程序时可以执行这些操作。 这种模拟应用程序标识的能力可能是用户在角色分配的基础上的权限提升。 请务必了解，向用户分配应用程序管理员角色，会赋予其模拟应用程序标识的能力。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft.directory/applications/applicationProxy/read | 读取所有应用程序代理属性 |
> | microsoft.directory/applications/applicationProxy/update | 更新所有应用程序代理属性 |
> | microsoft 目录/应用程序/applicationProxyAuthentication/更新 | 更新应用程序代理身份验证属性 |
> | microsoft 目录/应用程序/applicationProxySslCertificate/更新 | 更新应用程序代理自定义域 |
> | microsoft 目录/应用程序/applicationProxyUrlSettings/更新 | 更新应用程序代理内部和外部 Url |
> | microsoft 目录/应用程序/appRoles/更新 | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新公开的权限以及所有类型的应用程序所需的权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft. 目录/应用程序/验证/更新 | Update applicationsverification 属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/connectors/create | 创建应用程序代理连接器 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/connectorGroups/create | 创建应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/delete | 删除应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/update | 更新应用程序代理连接器组的所有属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | microsoft.directory/applicationPolicies/create | 创建应用程序策略 |
> | microsoft.directory/applicationPolicies/delete | 删除应用程序策略 |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/read | 读取应用程序策略的所有者 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 读取应用到对象列表的应用程序策略 |
> | microsoft.directory/applicationPolicies/basic/update | 更新应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/update | 更新应用程序策略的所有者属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 管理服务主体上的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序设置机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重新启动和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 读取服务主体上的密码单一登录凭据 |
> | 服务主体/managePermissionGrantsForAll. microsoft-application-admin | 代表任何用户或所有用户授予应用程序权限和委托权限的许可（Microsoft Graph 和 Azure AD 关系图的应用程序权限除外） |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的访问群体属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的 tag 属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="application-developer"></a>应用程序开发人员

在将设置“用户可以注册应用程序”设置为“否”时，充当此角色的用户可以创建应用程序注册。 当“用户可以同意应用代表他们访问公司数据”设置设为“否”时，此角色还能够代表自己授权同意。 在创建新应用程序注册或企业应用程序时，会将分配到此角色的用户添加为所有者。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | 创建所有类型的应用程序，并且创建者作为第一个所有者添加 |
> | microsoft.directory/appRoleAssignments/createAsOwner | 创建应用程序角色分配，将 creator 作为第一个所有者 |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | 创建 OAuth 2.0 权限授予，创建 creator 作为第一个所有者 |
> | microsoft.directory/servicePrincipals/createAsOwner | 创建服务主体，并将 creator 作为第一个所有者 |

## <a name="attack-payload-author"></a>攻击有效负载作者

拥有此角色的用户可以创建攻击有效负载，但不能实际启动或调度它们。 然后，租户中的所有管理员都可以使用攻击有效负载创建模拟。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 在攻击模拟器中创建和管理攻击负载 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取攻击模拟、响应和相关培训的报告 |

## <a name="attack-simulation-administrator"></a>攻击模拟管理员

拥有此角色的用户可以创建和管理攻击模拟创建的所有方面、启动/调度模拟以及查看模拟结果。 此角色的成员对租户中的所有模拟具有此访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 在攻击模拟器中创建和管理攻击负载 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取攻击模拟、响应和相关培训的报告 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 在攻击模拟器中创建和管理攻击模拟模板 |

## <a name="authentication-administrator"></a>身份验证管理员

具有此角色的用户可以设置或重置任何身份验证方法 (包括非管理员和某些角色的密码) 。 身份验证管理员可以要求非管理员用户或分配给某些角色的用户使用现有的非密码凭据（例如，MFA 或 FIDO）重新注册，还可以撤销“在设备上记住 MFA”，这样系统就会在用户下次登录时提示其进行 MFA。 有关身份验证管理员可读取或更新身份验证方法的角色的列表，请参阅 [密码重置权限](#password-reset-permissions)。

[特权身份验证管理员](#privileged-authentication-administrator)角色有权强制对所有用户进行重新注册和多重身份验证。

[身份验证策略管理员](#authentication-policy-administrator)角色有权设置租户的身份验证方法策略，以确定每个用户可以注册和使用哪些方法。

| 角色 | 管理用户的身份验证方法 | 管理每用户 MFA | 管理 MFA 设置 | 管理身份验证方法策略 | 管理密码保护策略 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 身份验证管理员 | 对于某些用户 (参阅 "是")  | 对于某些用户 (参阅 "是")  | 否 | 否 | 否 |
| 特权身份验证管理员| 对于所有用户为 "是" | 对于所有用户为 "是" | 否 | 否 | 否 |
| 身份验证策略管理员 | 否 |否 | 是 | 是 | 是 |

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：
>
>* 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过此路径，身份验证管理员可以假定应用程序所有者的身份，然后通过更新应用程序的凭据进一步假定特权应用程序的标识。
>* Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>* 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>* Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>* 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

> [!IMPORTANT]
> 此角色目前无法在旧版 MFA 管理门户中管理按用户的 MFA。 可以使用 [set-msoluser](/powershell/module/msonline/set-msoluser) Commandlet Azure AD Powershell 模块来完成相同的功能。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/strongAuthentication/update | 为用户更新强身份验证属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="authentication-policy-administrator"></a>身份验证策略管理员

具有此角色的用户可以配置身份验证方法策略、租户范围的 MFA 设置和密码保护策略。 此角色授予管理密码保护设置的权限：智能锁定配置和更新自定义禁止密码列表。

[身份验证管理员](#authentication-administrator)和[特权身份验证管理员](#privileged-authentication-administrator)角色有权管理用户的已注册身份验证方法，并可以强制对所有用户进行重新注册和多重身份验证。

| 角色 | 管理用户的身份验证方法 | 管理每用户 MFA | 管理 MFA 设置 | 管理身份验证方法策略 | 管理密码保护策略 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 身份验证管理员 | 对于某些用户 (参阅 "是")  | 对于某些用户 (参阅 "是")  | 否 | 否 | 否 |
| 特权身份验证管理员| 对于所有用户为 "是" | 对于所有用户为 "是" | 否 | 否 | 否 |
| 身份验证策略管理员 | 否 | 否 | 是 | 是 | 是 |

> [!IMPORTANT]
> 此角色目前无法在旧版 MFA 管理门户中管理 MFA 设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft. directory/组织/strongAuthentication/update | 更新组织的强身份验证属性 |
> | userCredentialPolicies/create | 为用户创建凭据策略 |
> | userCredentialPolicies/删除 | 删除用户的凭据策略 |
> | userCredentialPolicies/standard/read | 读取用户的凭据策略的标准属性 |
> | userCredentialPolicies/物主/物主/读取 | 读取用户的凭据策略所有者 |
> | userCredentialPolicies/policyAppliedTo/read | 读取策略。 appliesTo 导航链接 |
> | userCredentialPolicies/basic/update | 为用户更新基本策略 |
> | userCredentialPolicies/物主/物主/更新 | 更新用户的凭据策略所有者 |
> | userCredentialPolicies/tenantDefault/update | 更新 isOrganizationDefault 属性 |

## <a name="azure-ad-joined-device-local-administrator"></a>已加入 Azure AD 的设备的本地管理员

此角色只能作为[设备设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本地管理员进行分配。 拥有此角色的用户成为所有已加入 Azure Active Directory 的 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | groupSettings/standard/read | 读取组设置的基本属性 |
> | groupSettingTemplates/standard/read | 读取组设置模板上的基本属性 |

## <a name="azure-devops-administrator"></a>Azure DevOps 管理员

具有此角色的用户可以管理 Azure DevOps 策略，这样就可以仅限一组可配置的用户或组新建 Azure DevOps 组织。 充当此角色的用户可以通过由公司 Azure AD 组织支持的任何 Azure DevOps 组织来管理此策略。 此角色不会授予该公司 Azure AD 组织支持的任何 Azure DevOps 组织内的任何其他 Azure DevOps 特定权限（例如，项目集合管理员）。

充当此角色的用户可以管理所有企业 Azure DevOps 策略。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | 读取和配置 Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Azure 信息保护管理员

具有此角色的用户拥有 Azure 信息保护服务中的所有权限。 此角色可以配置 Azure 信息保护策略的标签、管理保护模板，以及激活保护。 此角色不会授予标识保护中心、Privileged Identity Management、监视 Microsoft 365 服务运行状况或 Office 365 安全与合规中心的权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的所有方面 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF 密钥集管理员

用户可以创建和管理用于令牌加密、令牌签名和声明加密/解密的策略密钥与机密。  通过将新密钥添加到现有密钥容器，此受限管理员可以根据需要滚动更新机密，而不会影响现有的应用程序。  即使是在创建这些机密之后，此用户也可以查看这些机密的完整内容及其过期日期。

> [!IMPORTANT]
> 这是一个敏感角色。  在生产前与生产期间，应该谨慎地审核和分配密钥集管理员角色。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | b2cTrustFrameworkKeySet/allProperties/allTasks | 读取和更新授权策略的所有属性 |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF 策略管理员

充当此角色的用户可以在 Azure AD B2C 中创建、读取、更新和删除所有自定义策略，因此对相关 Azure AD B2C 组织中的 Identity Experience Framework 拥有完全控制权。 通过编辑策略，此用户可以直接与外部标识提供者建立联合、更改目录架构、更改所有面向用户的内容（HTML、CSS、JavaScript）、更改完成身份验证所需满足的要求、创建新用户、将用户数据发送到外部系统（包括完整迁移），以及编辑所有用户信息（包括密码和电话号码等敏感字段）。 相比之下，此角色无法更改加密密钥，也不能编辑组织中用于联合身份验证的机密。

> [!IMPORTANT]
> B2 IEF 策略管理员是高度敏感的角色，在生产环境中应以极大的限制度将其分配给组织。  应该密切审核这些用户（尤其是生产环境中的组织的用户）的活动。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | b2cTrustFrameworkPolicy/allProperties/allTasks | 在 Azure Active Directory B2C 中读取和配置密钥集 |

## <a name="billing-administrator"></a>计费管理员

进行采购、管理订阅、管理支持票证，以及监视服务运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/organization/basic/update | 更新组织的基本属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帐单的所有方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="cloud-application-administrator"></a>云应用管理员

充当此角色的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 此角色授予创建和管理企业应用程序和应用程序注册的所有方面的权限。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

此角色还授予许可委托权限和应用程序权限的能力，但对于 Microsoft Graph 和 Azure AD 关系图的应用程序权限例外。

> [!IMPORTANT]
> 此例外表明你仍可以同意 _其他_ 应用的应用程序权限 (例如，你已注册) 的非 Microsoft 应用或应用。 你仍可以在应用注册过程中 _请求_ 这些权限，但 _授予_ (即同意) 这些权限需要更高权限的管理员，如全局管理员。
>
>此角色授予管理应用程序凭据这一功能。 分配有此角色的用户可以将凭据添加到应用程序，并使用这些凭据模拟应用程序的标识。 如果已向应用程序的标识授予资源访问权限，例如创建或更新用户或其他对象，那么分配到此角色的用户在模拟应用程序时可以执行这些操作。 这种模拟应用程序标识的能力可能是用户在角色分配的基础上的权限提升。 请务必了解，向用户分配应用程序管理员角色，会赋予其模拟应用程序标识的能力。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft 目录/应用程序/appRoles/更新 | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新公开的权限以及所有类型的应用程序所需的权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft. 目录/应用程序/验证/更新 | Update applicationsverification 属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | microsoft.directory/applicationPolicies/create | 创建应用程序策略 |
> | microsoft.directory/applicationPolicies/delete | 删除应用程序策略 |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/read | 读取应用程序策略的所有者 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 读取应用到对象列表的应用程序策略 |
> | microsoft.directory/applicationPolicies/basic/update | 更新应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/update | 更新应用程序策略的所有者属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 管理服务主体上的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序设置机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重新启动和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 读取服务主体上的密码单一登录凭据 |
> | 服务主体/managePermissionGrantsForAll. microsoft-application-admin | 代表任何用户或所有用户授予应用程序权限和委托权限的许可（Microsoft Graph 和 Azure AD 关系图的应用程序权限除外）  |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的访问群体属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的 tag 属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="cloud-device-administrator"></a>云设备管理员

充当此角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。 该角色不能授予设备上其他任何属性的管理权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/devices/delete | 从 Azure AD 删除设备 |
> | microsoft.directory/devices/disable | 禁用 Azure AD 中的设备 |
> | microsoft.directory/devices/enable | 在 Azure AD 中启用设备 |
> | microsoft.directory/devices/extensionAttributes/update | 更新 extensionAttributes 属性的所有值 |
> | deviceManagementPolicies/standard/read | 读取设备管理应用程序策略的标准属性 |
> | deviceManagementPolicies/basic/update | 更新设备管理应用程序策略的基本属性 |
> | deviceRegistrationPolicy/standard/read | 读取设备注册策略的标准属性 |
> | deviceRegistrationPolicy/basic/update | 更新设备注册策略的基本属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |

## <a name="compliance-administrator"></a>合规性管理员

具有此角色的用户有权管理 Microsoft 365 合规中心、Microsoft 365 管理中心、Azure 和 Office 365 安全与合规中心中的合规性相关功能。 被分配者还可以管理 Exchange 管理中心、Teams 和 Skype for Business 管理中心内的所有功能，并可创建适用于 Azure 和 Microsoft 365 的支持票证。 [关于 Microsoft 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中提供了详细信息。

In | 有权执行的操作
----- | ----------
[Microsoft 365 合规中心](https://protection.office.com) | 跨 Microsoft 365 服务保护和管理组织数据<br>管理合规性警报
[合规性管理器](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求<br><br>此角色的权限与 Office 365 安全与合规中心基于角色的访问控制中的[合规性管理员角色组](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同。
[Intune](/intune/role-based-access-control) | 查看所有 Intune 审核数据
[Cloud App Security](/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br>可以查看数据管理下的所有内置报表

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 的权利管理中的所有属性 |
> | microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="compliance-data-administrator"></a>符合性数据管理员

具有此角色的用户有权在 Microsoft 365 合规中心、Microsoft 365 管理中心和 Azure 中跟踪数据。 这些用户还可以在 Exchange 管理中心、Compliance Manager、Teams 和 Skype for Business 管理中心跟踪合规数据，并可创建适用于 Azure 和 Microsoft 365 的支持票证。 [此文档](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)详细介绍了合规性管理员与合规性数据管理员之间的差别。

In | 有权执行的操作
----- | ----------
[Microsoft 365 合规中心](https://protection.office.com) | 跨 Microsoft 365 服务监视与合规性相关的策略<br>管理合规性警报
[合规性管理器](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求<br><br>此角色的权限与 Office 365 安全与合规中心基于角色的访问控制中的[合规性数据管理员角色组](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同。
[Intune](/intune/role-based-access-control) | 查看所有 Intune 审核数据
[Cloud App Security](/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br>可以查看数据管理下的所有内置报表

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 创建和删除所有资源，并读取和更新 Microsoft Cloud App Security 中的标准属性 |
> | microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的所有方面 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="conditional-access-administrator"></a>条件访问管理员

具有此角色的用户能够管理 Azure Active Directory 条件访问设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | conditionalAccessPolicies/create | 创建条件访问策略 |
> | conditionalAccessPolicies/删除 | 删除条件性访问策略 |
> | conditionalAccessPolicies/standard/read | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/物主/物主/读取 | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/policyAppliedTo/read | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/basic/update | 更新条件访问策略的基本属性 |
> | conditionalAccessPolicies/物主/物主/更新 | 更新 conditionalAccess 属性 |
> | conditionalAccessPolicies/tenantDefault/update | 更新 conditionalAccess 属性 |
> | crossTenantAccessPolicies/create | 创建跨租户访问策略 |
> | crossTenantAccessPolicies/删除 | 删除跨租户访问策略 |
> | crossTenantAccessPolicies/standard/read | 读取跨租户访问策略的基本属性 |
> | crossTenantAccessPolicies/物主/物主/读取 | 读取跨租户访问策略的所有者 |
> | crossTenantAccessPolicies/policyAppliedTo/read | 读取跨租户访问策略的 policyAppliedTo 属性 |
> | crossTenantAccessPolicies/basic/update | 更新跨租户访问策略的基本属性 |
> | crossTenantAccessPolicies/物主/物主/更新 | 更新跨租户访问策略的所有者 |
> | crossTenantAccessPolicies/tenantDefault/update | 更新跨租户访问策略的默认租户 |

## <a name="customer-lockbox-access-approver"></a>客户密码箱访问审批者

管理你的组织中的[客户密码箱请求](/office365/admin/manage/customer-lockbox-requests)。 他们接收客户密码箱请求的电子邮件通知，并且可以批准和拒绝来自 Microsoft 365 管理中心的请求。 他们还可以开启或关闭客户密码箱功能。 只有全局管理员才能重置分配给此角色的用户的密码。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | 管理客户密码箱的所有方面 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="desktop-analytics-administrator"></a>桌面分析管理员

充当此角色的用户可以管理桌面分析以及 Office 自定义和策略服务。 对于 Desktop Analytics，此权限包括查看资产库存、创建部署计划、查看部署和运行状态。 对于 Office 自定义和策略服务，此角色可让用户管理 Office 策略。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理桌面分析的所有方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="directory-readers"></a>目录读者

充当此角色的用户可以读取基本的目录信息。 应将此角色用于：

* 为特定的一组来宾用户授予读取访问权限，而不是将此权限授予所有来宾用户。
* 当“仅限管理员访问 Azure AD 门户”设置为“是”时，为特定的一组非管理员用户授予对 Azure 门户的访问权限。
* 当“Directory.Read.All”不是可用选项时，为服务主体授予对目录的访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | administrativeUnits/standard/read | 读取管理单元的基本属性 |
> | microsoft.directory/administrativeUnits/members/read | 读取管理单元的成员 |
> | microsoft.directory/applications/standard/read | 读取应用程序的标准属性 |
> | microsoft.directory/applications/owners/read | 读取应用程序的所有者 |
> | microsoft.directory/applications/policies/read | 读取应用程序的策略 |
> | microsoft. directory/contacts/standard/read | 读取 Azure AD 中联系人的基本属性 |
> | microsoft.directory/contacts/memberOf/read | 读取 Azure AD 中的所有联系人的组成员身份 |
> | microsoft. directory/协定/标准/读取 | 读取合作伙伴合同的基本属性 |
> | microsoft. directory/设备/标准/读取 | 读取设备上的基本属性 |
> | microsoft.directory/devices/memberOf/read | 读取设备成员身份 |
> | microsoft.directory/devices/registeredOwners/read | 读取设备的已注册所有者 |
> | microsoft.directory/devices/registeredUsers/read | 读取设备的已注册用户 |
> | directoryRoles/standard/read | 更新 Azure AD 角色中的基本属性 |
> | microsoft.directory/directoryRoles/eligibleMembers/read | 阅读 Azure AD 角色的合格成员 |
> | microsoft.directory/directoryRoles/members/read | 读取 Azure AD 角色的所有成员 |
> | microsoft. directory/域/标准/读取 | 读取域上的基本属性 |
> | microsoft. 目录/组/标准/读取 | 读取组的基本属性 |
> | microsoft.directory/groups/appRoleAssignments/read | 读取组的应用程序角色分配 |
> | microsoft.directory/groups/memberOf/read | 读取组作为其成员的组 Azure AD |
> | microsoft.directory/groups/members/read | 读取组的成员 |
> | microsoft.directory/groups/owners/read | 读取组的所有者 |
> | microsoft.directory/groups/settings/read | 读取组的设置 |
> | groupSettings/standard/read | 读取组设置的基本属性 |
> | groupSettingTemplates/standard/read | 读取组设置模板上的基本属性 |
> | oAuth2PermissionGrants/standard/read | 读取 OAuth 2.0 权限授予的基本属性 |
> | microsoft. directory/组织/标准/读取 | 读取组织的基本属性 |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | 读取受信任的证书颁发机构以进行无密码 authentication |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | roleAssignments/standard/read | 读取角色分配的基本属性 |
> | roleDefinitions/standard/read | 读取角色定义的基本属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取服务主体角色分配 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取分配给服务主体的角色分配 |
> | microsoft.directory/servicePrincipals/standard/read | 读取服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/memberOf/read | 读取服务主体上的组成员身份 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 在服务主体上读取委托权限授予 |
> | microsoft.directory/servicePrincipals/owners/read | 读取服务主体的所有者 |
> | microsoft.directory/servicePrincipals/ownedObjects/read | 读取服务主体的拥有对象 |
> | microsoft.directory/servicePrincipals/policies/read | 读取服务主体的策略 |
> | subscribedSkus/standard/read | 读取订阅的基本属性 |
> | microsoft. 目录/用户/标准/读取 | 读取用户的基本属性 |
> | microsoft.directory/users/appRoleAssignments/read | 读取用户的应用程序角色分配 |
> | microsoft.directory/users/directReports/read | 读取用户的直接下属 |
> | microsoft.directory/users/manager/read | 用户的读取管理器 |
> | microsoft.directory/users/memberOf/read | 读取用户的组成员身份 |
> | microsoft. directory/users/oAuth2PermissionGrants/read | 读取用户的委托权限授予 |
> | microsoft.directory/users/ownedDevices/read | 读取用户的拥有设备 |
> | microsoft.directory/users/ownedObjects/read | 读取用户的拥有对象 |
> | microsoft.directory/users/registeredDevices/read | 读取用户的已注册设备 |

## <a name="directory-synchronization-accounts"></a>目录同步帐户

请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft 目录/应用程序/appRoles/更新 | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新公开的权限以及所有类型的应用程序所需的权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/organization/dirSync/update | 更新组织目录同步属性 |
> | microsoft.directory/policies/create | 在 Azure AD 中创建策略 |
> | microsoft.directory/policies/delete | 删除 Azure AD 中的策略 |
> | microsoft.directory/policies/standard/read | 读取策略的基本属性 |
> | microsoft.directory/policies/owners/read | 读取策略的所有者 |
> | microsoft. directory/策略/policyAppliedTo/read | 读取 policyAppliedTo 属性 |
> | microsoft.directory/policies/basic/update | 更新策略的基本属性 |
> | microsoft.directory/policies/owners/update | 更新策略的所有者 |
> | microsoft.directory/policies/tenantDefault/update | 更新默认组织策略 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 管理服务主体上的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 读取服务主体上的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取服务主体角色分配 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取分配给服务主体的角色分配 |
> | microsoft.directory/servicePrincipals/standard/read | 读取服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/memberOf/read | 读取服务主体上的组成员身份 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 在服务主体上读取委托权限授予 |
> | microsoft.directory/servicePrincipals/owners/read | 读取服务主体的所有者 |
> | microsoft.directory/servicePrincipals/ownedObjects/read | 读取服务主体的拥有对象 |
> | microsoft.directory/servicePrincipals/policies/read | 读取服务主体的策略 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的访问群体属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的 tag 属性 |

## <a name="directory-writers"></a>目录编写人员

此角色中的用户可以读取和更新用户、组和服务主体的基本信息。 仅将此角色分配给不支持 [同意框架](../develop/quickstart-register-app.md)的应用程序。 不应将它分配给任何用户。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以进行基于组的许可 |
> | microsoft.directory/groups/create | 创建组，排除角色可分配的组 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/groups/basic/update | 更新组的基本属性，不包括角色可分配的组 |
> | microsoft.directory/groups/classification/update | 更新组的分类属性，不包括角色可分配的组 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 更新组的动态成员身份规则，排除角色可分配的组 |
> | microsoft.directory/groups/groupType/update | 更新组的 groupType 属性 |
> | microsoft.directory/groups/members/update | 更新组的成员，排除角色可分配的组 |
> | microsoft. directory/groups/onPremWriteBack/update | 更新要写回本地的 Azure AD 组 |
> | microsoft.directory/groups/owners/update | 更新组的所有者，不包括角色可分配的组 |
> | microsoft.directory/groups/settings/update | 更新组的设置 |
> | microsoft.directory/groups/visibility/update | 更新组的可见性属性 |
> | microsoft.directory/groupSettings/create | 创建组设置 |
> | microsoft.directory/groupSettings/delete | 删除组设置 |
> | microsoft.directory/groupSettings/basic/update | 更新组设置的基本属性 |
> | microsoft.directory/oAuth2PermissionGrants/create | 创建 OAuth 2.0 权限授予 |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | 更新 OAuth 2.0 权限授予 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序设置机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重新启动和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | 服务主体/managePermissionGrantsForGroup. microsoft-all-application-permissions | 授予服务主体直接访问组数据的权限 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/reprocessLicenseAssignment | 重新处理用户的许可证分配 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 用户的更新管理器 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |

## <a name="domain-name-administrator"></a>域名管理员

具有此角色的用户可以管理 (读取、添加、验证、更新和删除) 域名。 它们还可以读取有关用户、组和应用程序的目录信息，因为这些对象具有域依赖项。 对于本地环境，具有此角色的用户可以为联合配置域名，以便始终在本地对关联的用户进行身份验证。 然后，这些用户可以通过单一登录，通过其本地密码登录到基于 Azure AD 的服务。 需要通过 Azure AD Connect 同步联合身份验证设置，因此用户还具有管理 Azure AD Connect 的权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | 创建和删除域，并读取和更新所有属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |

## <a name="dynamics-365-administrator"></a>Dynamics 365 管理员

具有此角色的用户具有 Microsoft Dynamics 365 Online 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[使用服务管理员角色管理 Azure AD 组织](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Dynamics 365 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Dynamics 365 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="exchange-administrator"></a>Exchange 管理员

具有此角色的用户具有 Microsoft Exchange Online 内的全局权限（如果该服务存在）。 另外还能够创建和管理所有 Microsoft 365 组，管理支持票证并监视服务运行状况。 有关详细信息，请参阅[关于 Microsoft 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Exchange 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Exchange 管理员”。 它是 [Exchange 管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)内的“Exchange Online 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | 读取组的隐藏成员 |
> | microsoft. 目录/组。统一/创建 | 创建 Microsoft 365 组，排除角色可分配的组 |
> | microsoft. directory/groups. 统一/删除 | 删除 Microsoft 365 组，排除角色可分配的组 |
> | microsoft. 目录/组。统一/还原 | 还原 Microsoft 365 组 |
> | microsoft. directory/groups. 统一/基本/更新 | 更新 Microsoft 365 组的基本属性，排除角色可分配的组 |
> | microsoft. 目录/组。统一/成员/更新 | 更新 Microsoft 365 组的成员，排除角色可分配的组 |
> | microsoft. 目录/组。统一/所有者/更新 | 更新 Microsoft 365 组的所有者，排除角色可分配的组 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有方面 |
> | microsoft.office365.network/performance/allProperties/read | 读取 Microsoft 365 管理中心中的所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="external-id-user-flow-administrator"></a>外部 ID 用户流管理员

具有此角色的用户可以在 Azure 门户中创建和管理用户流（也称为“内置”策略）。 这些用户可以自定义 HTML/CSS/JavaScript 内容、更改 MFA 要求、在令牌中选择声明、管理 API 连接器，以及为 Azure AD 组织中的所有用户流配置会话设置。 但是，此角色无法查看用户数据，也无法对组织架构中包含的属性进行更改。 对 Identity Experience Framework 策略（也称为自定义策略）的更改也超出了此角色的权限范围。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | b2cUserFlow/allProperties/allTasks | 在 Azure Active Directory B2C 中读取和配置用户属性 |

## <a name="external-id-user-flow-attribute-administrator"></a>外部 ID 用户流属性管理员

具有此角色的用户可以添加或删除适用于 Azure AD 组织中所有用户流的自定义属性。  因此，具有此角色的用户可以在最终用户架构中更改或新增元素，影响所有用户流的行为，间接导致更改可以请求最终用户提供的并最终作为声明发送到应用程序的数据。  此角色无法编辑用户流。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | b2cUserAttribute/allProperties/allTasks | 在 Azure Active Directory B2C 中读取和配置自定义策略 |

## <a name="external-identity-provider-administrator"></a>外部标识提供者管理员

此管理员可以管理 Azure AD 组织与外部标识提供者之间的联合。  用户可以使用此角色添加新的标识提供者及配置所有可用设置（例如身份验证路径、服务 ID 和分配的密钥容器）。  此用户可让 Azure AD 组织信任来自外部标识提供者的身份验证。  对最终用户体验造成的影响取决于组织类型：

* 为员工和合作伙伴 Azure AD 组织：添加联合 (例如，使用 Gmail) 会立即影响尚未兑换的所有来宾邀请。 请参阅[将 Google 添加为 B2B 来宾用户的标识提供者](../external-identities/google-federation.md)。
* Azure Active Directory B2C 组织：在将标识提供者添加为用户流（也称为内置策略）中的一个选项之前，添加联合身份验证（例如，使用 Facebook 或另一个 Azure AD 组织来这样做）不会立即影响最终用户流。 有关示例，请参阅[将 Microsoft 帐户配置为标识提供者](../../active-directory-b2c/identity-provider-microsoft-account.md)。  若要更改用户流，需要使用受限角色“B2C 用户流管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | identityProviders/allProperties/allTasks | 在 Azure Active Directory B2C 中读取和配置标识提供者 |

## <a name="global-administrator"></a>全局管理员角色

具有此角色的用户有权访问 Azure Active Directory 以及使用 Azure Active Directory 标识的服务（例如 Microsoft 365 安全中心、Microsoft 365 合规中心、Exchange Online、SharePoint Online 和 Skype for Business Online）中的所有管理功能。 此外，全局管理员还可以 [提升其访问权限](../../role-based-access-control/elevate-access-global-admin.md) ，以管理所有 Azure 订阅和管理组。 这允许全局管理员使用各自的 Azure AD 租户获取对所有 Azure 资源的完全访问权限。 注册 Azure AD 组织的人员将成为全局管理员。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 创建和管理管理单元（包括成员） |
> | microsoft.directory/applications/allProperties/allTasks | 创建和删除应用程序，并读取和更新所有属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | 创建和删除 appRoleAssignments，并读取和更新所有属性 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | authorizationPolicy/allProperties/allTasks | 管理授权策略的所有方面 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 创建和删除所有资源，并读取和更新 Microsoft Cloud App Security 中的标准属性 |
> | microsoft.directory/connectors/create | 创建应用程序代理连接器 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/connectorGroups/create | 创建应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/delete | 删除应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/update | 更新应用程序代理连接器组的所有属性 |
> | microsoft.directory/contacts/allProperties/allTasks | 创建和删除联系人，并读取和更新所有属性 |
> | microsoft.directory/contracts/allProperties/allTasks | 创建和删除合作伙伴合同，并读取和更新所有属性 |
> | microsoft.directory/devices/allProperties/allTasks | 创建和删除设备，并读取和更新所有属性 |
> | deviceManagementPolicies/standard/read | 读取设备管理应用程序策略的标准属性 |
> | deviceManagementPolicies/basic/update | 更新设备管理应用程序策略的基本属性 |
> | deviceRegistrationPolicy/standard/read | 读取设备注册策略的标准属性 |
> | deviceRegistrationPolicy/basic/update | 更新设备注册策略的基本属性 |
> | microsoft.directory/directoryRoles/allProperties/allTasks | 创建和删除目录角色，并读取和更新所有属性 |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | 创建和删除 Azure AD 角色模板，并读取和更新所有属性 |
> | microsoft.directory/domains/allProperties/allTasks | 创建和删除域，并读取和更新所有属性 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | 创建和删除资源，并读取和更新 Azure AD 权限管理中的所有属性 |
> | microsoft.directory/groups/allProperties/allTasks | 创建和删除组，并读取和更新所有属性 |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | 将 isAssignableToRole 属性设置为 true 的更新组 |
> | microsoft.directory/groupsAssignableToRoles/create | 创建 isAssignableToRole 属性设置为 true 的组 |
> | microsoft.directory/groupsAssignableToRoles/delete | 删除 isAssignableToRole 属性设置为 true 的组 |
> | microsoft.directory/groupSettings/allProperties/allTasks | 创建和删除组设置，并读取和更新所有属性 |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | 创建和删除组设置模板，并读取和更新所有属性 |
> | identityProtection/allProperties/allTasks | 创建和删除所有资源，并读取和更新 Azure AD Identity Protection 中的标准属性 |
> | loginOrganizationBranding/allProperties/allTasks | 创建和删除 loginTenantBranding，并读取和更新所有属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | microsoft.directory/organization/allProperties/allTasks | 创建和删除组织，并读取和更新所有属性 |
> | microsoft.directory/policies/allProperties/allTasks | 创建和删除策略，并读取和更新所有属性 |
> | conditionalAccessPolicies/allProperties/allTasks | 管理条件访问策略的所有属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除角色分配，并读取和更新所有角色分配属性 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除角色定义，并读取和更新所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，并读取和更新所有属性 |
> | microsoft.directory/serviceAction/activateService | 可以为服务执行 "激活服务" 操作 |
> | microsoft.directory/serviceAction/disableDirectoryFeature | 可以执行 "禁用目录功能" 服务操作 |
> | microsoft.directory/serviceAction/enableDirectoryFeature | 可以执行 "启用目录功能" 服务操作 |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | 可以执行 Getavailableextentionproperties 服务操作 |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | 创建和删除服务主体，并读取和更新所有属性 |
> | 服务主体/managePermissionGrantsForAll. microsoft-company-admin | 向任何应用程序授予任何权限的许可 |
> | 服务主体/managePermissionGrantsForGroup. microsoft-all-application-permissions | 授予服务主体直接访问组数据的权限  |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | 购买和管理订阅并删除订阅 |
> | microsoft.directory/users/allProperties/allTasks | 创建和删除用户，并读取和更新所有属性 |
> | microsoft.directory/permissionGrantPolicies/create | 创建权限授予策略 |
> | microsoft.directory/permissionGrantPolicies/delete | 删除权限授予策略 |
> | microsoft.directory/permissionGrantPolicies/standard/read | 读取权限授予策略的标准属性 |
> | microsoft.directory/permissionGrantPolicies/basic/update | 更新权限授予策略的基本属性 |
> | advancedThreatProtection/allEntities/allTasks | 管理 Azure 高级威胁防护的所有方面 |
> | microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的所有方面 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帐单的所有方面 |
> | microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有方面 |
> | microsoft.flow/allEntities/allTasks | 管理 Microsoft 电源自动执行的所有方面 |
> | microsoft.intune/allEntities/allTasks | 管理 Microsoft Intune 的所有方面 |
> | microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理桌面分析的所有方面 |
> | microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有方面 |
> | microsoft.office365.lockbox/allEntities/allTasks | 管理客户密码箱的所有方面 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | microsoft.office365.messageCenter/securityMessages/read | 在 Microsoft 365 管理中心的邮件中心中阅读安全消息 |
> | office365. protectionCenter/allEntities/allProperties/allTasks | 管理 Office 365 保护中心的所有方面 |
> | office365/内容/管理 | 创建和删除内容，并读取和更新 Microsoft 搜索中的所有属性 |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | 创建和删除所有资源，并读取和更新 Microsoft 365 安全性和符合性中心中的标准属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | office365/allEntities/allTasks | 创建和删除所有资源，并读取和更新 SharePoint 中的标准属性 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.userCommunication/allEntities/allTasks | 阅读并更新新增内容的消息可见性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft.powerApps/allEntities/allTasks | 管理电源应用的所有方面 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的所有方面 |
> | defenderAdvancedThreatProtection/allEntities/allTasks | 管理 Microsoft Defender for Endpoint 的所有方面 |

## <a name="global-reader"></a>全局读取者

充当此角色的用户可以跨 Microsoft 365 服务读取设置和管理信息，但无法执行管理操作。 全局读取器是全局管理员的只读对应项。 为计划、审核或调查分配全局读取器而不是全局管理员。 将全局读取者与其他受限管理员角色（例如 Exchange 管理员）结合使用可以更轻松地完成工作，且无需分配全局管理员角色。 全局读取者可使用 Microsoft 365 管理中心、Exchange 管理中心、SharePoint 管理中心、Teams 管理中心、安全中心、合规中心、Azure AD 管理中心和设备管理管理中心。

> [!NOTE]
> 全局读取者角色目前存在一些限制 -
>
>- [OneDrive 管理中心](https://admin.onedrive.com/) - OneDrive 管理中心不支持全局读取者角色
>- [M365 管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage) - 全局读取者无法读取客户密码箱请求。 在 M365 管理中心左窗格中的“支持”下，找不到“客户密码箱请求”选项卡。 
>- [Office 安全与合规中心](https://sip.protection.office.com/homepage) - 全局读取者无法读取 SCC 审核日志、执行内容搜索或查看安全评分。
>- [Teams 管理中心](https://admin.teams.microsoft.com) - 全局读取者无法读取“Teams 生命周期”、“分析和报告”、“IP 电话设备管理”和“应用目录”。   
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) 不支持全局读取者角色。
>- [Azure 信息保护](/azure/information-protection/what-is-information-protection) - 仅当 Azure AD 组织不在[统一标记平台](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上时，才支持全局读取者[在中心位置进行报告](/azure/information-protection/reports-aip)。
>
> 这些功能目前正在开发中。
>

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | 读取所有应用程序代理属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 的权利管理中的所有属性 |
> | deviceManagementPolicies/standard/read | 读取设备管理应用程序策略的标准属性 |
> | deviceRegistrationPolicy/standard/read | 读取设备注册策略的标准属性 |
> | microsoft.directory/groups/hiddenMembers/read | 读取组的隐藏成员 |
> | microsoft.directory/policies/standard/read | 读取策略的基本属性 |
> | microsoft.directory/policies/owners/read | 读取策略的所有者 |
> | microsoft. directory/策略/policyAppliedTo/read | 读取 policyAppliedTo 属性 |
> | conditionalAccessPolicies/standard/read | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/物主/物主/读取 | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/policyAppliedTo/read | 读取 conditionalAccess 属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/authentication/read | 读取服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.directory/users/strongAuthentication/read | 读取用户的强身份验证属性 |
> | microsoft.commerce.billing/allEntities/read | 读取 Office 365 帐单的所有资源 |
> | microsoft.office365.exchange/allEntities/read | 读取 Exchange Online 的所有资源 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | microsoft.office365.messageCenter/securityMessages/read | 在 Microsoft 365 管理中心的邮件中心中阅读安全消息 |
> | microsoft.office365.network/performance/allProperties/read | 读取 Microsoft 365 管理中心中的所有网络性能属性 |
> | office365. protectionCenter/allEntities/allProperties/read | 阅读 Office 365 保护中心的所有方面 |
> | microsoft.office365.securityComplianceCenter/allEntities/read | 读取 Microsoft 365 安全性和符合性中心中的标准属性 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="groups-administrator"></a>组管理员

此角色中的用户可以创建/管理组及其设置，如命名和过期策略。 重要的是要了解，将用户分配到此角色后，他们还可以跨各种工作负荷（如 Teams、SharePoint、Yammer 和 Outlook）管理组织中的所有组。 此外，用户还将能够跨各种管理门户（如Microsoft 管理中心、Azure 门户）以及特定于工作负荷的门户（如 Teams 和 SharePoint 管理中心）管理各种组设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以进行基于组的许可 |
> | microsoft.directory/groups/create | 创建组，排除角色可分配的组 |
> | microsoft.directory/groups/delete | 删除组，不包括角色可分配的组 |
> | microsoft.directory/groups/hiddenMembers/read | 读取组的隐藏成员 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/basic/update | 更新组的基本属性，不包括角色可分配的组 |
> | microsoft.directory/groups/classification/update | 更新组的分类属性，不包括角色可分配的组 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 更新组的动态成员身份规则，排除角色可分配的组 |
> | microsoft.directory/groups/groupType/update | 更新组的 groupType 属性 |
> | microsoft.directory/groups/members/update | 更新组的成员，排除角色可分配的组 |
> | microsoft. directory/groups/onPremWriteBack/update | 更新要写回本地的 Azure AD 组 |
> | microsoft.directory/groups/owners/update | 更新组的所有者，不包括角色可分配的组 |
> | microsoft.directory/groups/settings/update | 更新组的设置 |
> | microsoft.directory/groups/visibility/update | 更新组的可见性属性 |
> | 服务主体/managePermissionGrantsForGroup. microsoft-all-application-permissions | 授予服务主体直接访问组数据的权限  |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="guest-inviter"></a>来宾邀请者

此角色的用户可在“成员可以邀请”用户设置设置为“否”时管理 Azure Active Directory B2B 来宾用户邀请。 [关于 Azure AD B2B 协作](../external-identities/what-is-b2b.md)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | 邀请来宾用户 |
> | microsoft. 目录/用户/标准/读取 | 读取用户的基本属性 |
> | microsoft.directory/users/appRoleAssignments/read | 读取用户的应用程序角色分配 |
> | microsoft.directory/users/directReports/read | 读取用户的直接下属 |
> | microsoft.directory/users/manager/read | 用户的读取管理器 |
> | microsoft.directory/users/memberOf/read | 读取用户的组成员身份 |
> | microsoft. directory/users/oAuth2PermissionGrants/read | 读取用户的委托权限授予 |
> | microsoft.directory/users/ownedDevices/read | 读取用户的拥有设备 |
> | microsoft.directory/users/ownedObjects/read | 读取用户的拥有对象 |
> | microsoft.directory/users/registeredDevices/read | 读取用户的已注册设备 |

## <a name="helpdesk-administrator"></a>支持管理员

具有此角色的用户可以更改密码、使刷新令牌失效、管理服务请求和监视服务运行状况。 使刷新令牌失效会强制用户重新登录。 支持人员管理员是否可以重置用户的密码并使刷新令牌无效取决于用户分配的角色。 有关支持管理员可以为其重置密码和使刷新令牌无效的角色的列表，请参阅 [密码重置权限](#password-reset-permissions)。

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予支持人员管理员的特权。 通过此路径，支持人员管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置具有访问权限。
>- 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

可以通过 [管理单元](administrative-units.md)委托用户子集的管理权限，并将策略应用到部分用户。

在 [Azure 门户](https://portal.azure.com/)中，此角色以前称为“密码管理员”。 Azure AD 中的“支持管理员”名称现在与其在 Azure AD PowerShell 和 Microsoft Graph API 中的名称匹配。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/password/update | 为所有用户重置密码 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="hybrid-identity-administrator"></a>混合标识管理员

此角色中的用户可以使用云预配和管理联合设置来创建、管理和部署从 AD 到 Azure AD 的预配配置设置。 用户还可以使用此角色对日志进行故障排除和监视。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft 目录/应用程序/appRoles/更新 | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新公开的权限以及所有类型的应用程序所需的权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | 读取和配置 Azure AD 云配置服务的所有属性。 |
> | microsoft.directory/domains/allProperties/read | 读取域的所有属性 |
> | microsoft.directory/domains/federation/update | 更新域的联合属性 |
> | microsoft.directory/organization/dirSync/update | 更新组织目录同步属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序设置机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重新启动和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的访问群体属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的 tag 属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="insights-administrator"></a>Insights 管理员

此角色中的用户可以访问 [M365 Insights 应用程序](https://go.microsoft.com/fwlink/?linkid=2129521)中的全套管理员功能。 此角色能够读取目录信息，监视服务运行状况，提交支持票证，并访问 Insights 各方面的管理设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.insights/allEntities/allTasks | 管理见解应用的所有方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="insights-business-leader"></a>Insights 业务主管

此角色中的用户可以通过 [M365 Insights 应用程序](https://go.microsoft.com/fwlink/?linkid=2129521)访问一组仪表板和见解。 其中包括对所有仪表板以及提供的见解和数据探索功能的完全访问权限。 此角色中的用户无权访问由 Insights 管理员角色负责的产品配置设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.insights/reports/read | 在 Insights 应用中查看报表和仪表板 |
> | microsoft.insights/programs/update | 部署和管理 Insights 应用中的程序 |

## <a name="intune-administrator"></a>Intune 管理员

具有此角色的用户具有 Microsoft Intune Online 内的全局权限（如果该服务存在）。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。 有关详细信息，请参阅[使用 Microsoft Intune 进行基于角色的管理控制 (RBAC)](/intune/role-based-access-control)。

此角色可创建和管理所有安全组。 但是，Intune 管理员对 Office 组没有管理员权限。 这意味着管理员无法更新组织中所有 Office 组的所有者或成员身份， 但可以管理其自己创建的 Office 组，这是其最终用户权限的一部分。 因此，他们创建的任何 Office 组（非安全组）都应计入其 250 的配额。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色被标识为“Intune 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Intune 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/devices/create | 在 Azure AD) 中创建 (注册的设备 |
> | microsoft.directory/devices/delete | 从 Azure AD 删除设备 |
> | microsoft.directory/devices/disable | 禁用 Azure AD 中的设备 |
> | microsoft.directory/devices/enable | 在 Azure AD 中启用设备 |
> | microsoft.directory/devices/basic/update | 更新设备上的基本属性 |
> | microsoft.directory/devices/extensionAttributes/update | 更新 extensionAttributes 属性的所有值 |
> | microsoft.directory/devices/registeredOwners/update | 更新设备的已注册所有者 |
> | microsoft.directory/devices/registeredUsers/update | 更新设备的已注册用户 |
> | deviceManagementPolicies/standard/read | 读取设备管理应用程序策略的标准属性 |
> | deviceRegistrationPolicy/standard/read | 读取设备注册策略的标准属性 |
> | microsoft.directory/groups/hiddenMembers/read | 读取组的隐藏成员 |
> | microsoft. directory/groups。安全性/创建 | 创建安全组，排除角色可分配的组 |
> | microsoft. directory/groups。安全/删除 | 删除具有角色可分配组排除的安全组 |
> | microsoft. directory/groups。安全性/基本/更新 | 更新安全组的基本属性，排除角色可分配的组 |
> | microsoft. 目录/组。安全性/分类/更新 | 具有角色可分配组排除的安全组的更新分类属性 |
> | microsoft. directory/groups. security/dynamicMembershipRule/update | 更新安全组的 dynamicMembershipRule 属性，排除角色可分配的组 |
> | microsoft. directory/groups. security/groupType/update | 具有角色可分配组排除的安全组的更新组类型属性 |
> | microsoft. 目录/组。安全性/成员/更新 | 更新安全组的成员，排除角色可分配的组 |
> | microsoft. directory/groups. security/物主/update | 更新安全组的所有者，排除角色可分配的组 |
> | microsoft. 目录/组。安全性/可见性/更新 | 具有角色可分配组排除的安全组的更新可见性属性 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 用户的更新管理器 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.intune/allEntities/allTasks | 管理 Microsoft Intune 的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="kaizala-administrator"></a>Kaizala 管理员

具有此角色的用户拥有在 Microsoft Kaizala 中管理设置的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 此外，用户还可以访问与组织成员采用和使用 Kaizala 有关的报告，以及使用 Kaizala 操作生成的业务报告。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="license-administrator"></a>许可证管理员

具有此角色的用户可以添加、删除和更新用户、组（使用基于组的许可）的许可分配，以及管理用户的使用位置。 该角色不授予在使用位置之外购买或管理订阅、创建或管理组，或者创建或管理用户的权限。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以进行基于组的许可 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/reprocessLicenseAssignment | 重新处理用户的许可证分配 |
> | microsoft.directory/users/usageLocation/update | 更新用户的使用位置 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="message-center-privacy-reader"></a>消息中心隐私读取者

充当此角色的用户可以监视消息中心的所有通知，包括数据隐私消息。 消息中心隐私读取者会收到电子邮件通知（包括与数据隐私相关的通知），并可以使用邮件中心首选项取消订阅。 只有全局管理员和消息中心隐私读取者才能阅读数据隐私消息。 此外，此角色还能查看组、域和订阅。 此角色无权查看、创建或管理服务请求。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | microsoft.office365.messageCenter/securityMessages/read | 在 Microsoft 365 管理中心的邮件中心中阅读安全消息 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="message-center-reader"></a>消息中心读取者

具有此角色的用户可以在其组织的[消息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)监视 Exchange、Intune 和 Microsoft Teams 等已配置服务的通知和公告运行状况更新。 消息中心读者会收到包含帖子和更新的每周电子邮件摘要，并能在 Microsoft 365 内共享消息中心帖子。 在 Azure AD 中，分配到此角色的用户对 Azure AD 服务只拥有只读访问权限，如用户和组。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="modern-commerce-user"></a>现代商业用户

请勿使用。 此角色是从“商务”自动分配的，不可用于其他任何用途。 请查看下面的详细信息。

新式商务用户角色向特定用户授予访问 Microsoft 365 管理中心的权限，并查看 **Home**、 **计费** 和 **支持** 的左侧导航条目。 这些区域中提供的内容由分配给用户的[商务特定角色](../../cost-management-billing/manage/understand-mca-roles.md)控制，这些角色可让用户管理他们为自己或者为组织购买的产品。 这可能包括各种任务，例如支付账单，或访问计费帐户和计费配置文件。

具有新式商务用户角色的用户在其他 Microsoft 购买系统中通常具有管理权限，但没有用于访问管理中心的全局管理员或计费管理员角色。

**何时分配新式商务用户角色？**

* **Microsoft 365 管理中心自助购买** – 自助购买使用户有机会通过自行购买或注册新产品来试用这些产品。 这些产品在管理中心进行管理。 进行自助服务购买的用户会在商业系统中分配一个角色，以及新式商务用户角色，以便他们可以在管理中心管理他们的购买情况。 管理员可以通过 [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) 阻止自助购买（适用于 Power BI、Power Apps、Power Automate）。 有关详细信息，请参阅[自助购买常见问题解答](/microsoft-365/commerce/subscriptions/self-service-purchase-faq)。
* **Microsoft 商用 Marketplace 购买** -类似于自助购买，当用户从 Microsoft AppSource 或 Azure marketplace 购买产品或服务时，如果没有 "全局管理员" 或 "计费管理员" 角色，则会分配新式商务用户角色。 在某些情况下，可能会阻止用户进行此类购买。 有关详细信息，请参阅 [Microsoft 商业市场](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)。
* **Microsoft 的提议** –提议是 microsoft 为你的组织购买 microsoft 产品和服务的正式优惠。 如果接受此提议的人员在 Azure AD 中没有全局管理员或计费管理员角色，则会为他们分配一个特定于商业的角色来完成该建议，并使用现代商务用户角色来访问管理中心。 当他们访问管理中心时，只能使用其商务特定角色授权的功能。
* **商务特定角色** – 会将某些用户分配到商务特定角色。 如果用户不是全局管理员或计费管理员，他们将获得新式商务用户角色，以便他们能够访问管理中心。

如果没有为用户分配新式商务用户角色，则他们将失去 Microsoft 365 管理中心的访问权限。 在此情况下，他们无法管理自己或组织的任何产品。 这些任务可能包括分配许可证、更改付款方式、支付账单或其他订阅管理任务。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Microsoft 365 计费的读取伙伴属性 |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | 管理批量许可服务中心的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/basic/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="network-administrator"></a>网络管理员

充当此角色的用户可以查看 Microsoft 根据其用户位置发出的网络遥测数据提供的网络外围体系结构建议。 Microsoft 365 的网络性能依赖于精心规划的企业客户网络外围体系结构，而该体系结构通常特定于用户位置。 利用此角色，可以编辑已发现的用户位置并配置这些位置的网络参数，以促进改善遥测结果并设计建议

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | 管理网络位置的所有方面 |
> | microsoft.office365.network/performance/allProperties/read | 读取 Microsoft 365 管理中心中的所有网络性能属性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="office-apps-administrator"></a>Office 应用管理员

充当此角色的用户可以管理 Microsoft 365 应用的云设置。 这包括云策略管理、自助下载管理，以及查看与 Office 应用相关的报表的功能。 此外，该角色还可以在主管理中心管理支持票证和监视服务运行状况。 分配了此角色的用户还可以管理 Office 应用中新功能的通信。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.userCommunication/allEntities/allTasks | 阅读并更新新增内容的消息可见性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="partner-tier1-support"></a>合作伙伴一线支持人员

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

> [!IMPORTANT]
> 此角色可以重置密码，并使仅非管理员的刷新令牌失效。 不应使用此角色，因为它已被弃用，并且将不再在 API 中返回。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft 目录/应用程序/appRoles/更新 | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新公开的权限以及所有类型的应用程序所需的权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/groups/create | 创建组，排除角色可分配的组 |
> | microsoft.directory/groups/delete | 删除组，不包括角色可分配的组 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/members/update | 更新组的成员，排除角色可分配的组 |
> | microsoft.directory/groups/owners/update | 更新组的所有者，不包括角色可分配的组 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/delete | 删除用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/restore | 还原已删除的用户 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 用户的更新管理器 |
> | microsoft.directory/users/password/update | 为所有用户重置密码 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="partner-tier2-support"></a>合作伙伴二线支持人员

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

> [!IMPORTANT]
> 此角色可以重置密码，并使所有非管理员和管理员 (包括全局管理员) 的刷新令牌无效。 不应使用此角色，因为它已被弃用，并且将不再在 API 中返回。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft 目录/应用程序/appRoles/更新 | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新公开的权限以及所有类型的应用程序所需的权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft. directory/域/基本/allTasks | 创建和删除域，并读取和更新标准属性 |
> | microsoft.directory/groups/create | 创建组，排除角色可分配的组 |
> | microsoft.directory/groups/delete | 删除组，不包括角色可分配的组 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/members/update | 更新组的成员，排除角色可分配的组 |
> | microsoft.directory/groups/owners/update | 更新组的所有者，不包括角色可分配的组 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | microsoft.directory/organization/basic/update | 更新组织的基本属性 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除角色分配，并读取和更新所有角色分配属性 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除角色定义，并读取和更新所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，并读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | subscribedSkus/standard/read | 读取订阅的基本属性 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/delete | 删除用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/restore | 还原已删除的用户 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 用户的更新管理器 |
> | microsoft.directory/users/password/update | 为所有用户重置密码 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="password-administrator"></a>密码管理员

具有此角色的用户可以管理密码，但权限受限。 此角色不会授予管理服务请求或监视服务运行状况的能力。 密码管理员是否可以重置用户的密码取决于该用户分配的角色。 有关密码管理员可为其重置密码的角色的列表，请参阅 [密码重置权限](#password-reset-permissions)。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/password/update | 为所有用户重置密码 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="power-bi-administrator"></a>Power BI 管理员

具有此角色的用户具有 Microsoft Power BI 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[了解 Power BI 管理员角色](/power-bi/service-admin-role)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Power BI 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Power BI 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的所有方面 |

## <a name="power-platform-administrator"></a>Power Platform 管理员

充当此角色的用户可以创建和管理环境、PowerApps、Flows、数据丢失防护策略的所有方面。 另外，具有此角色的用户可以管理支持票证并监视服务运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有方面 |
> | microsoft.flow/allEntities/allTasks | 管理 Microsoft 电源自动执行的所有方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft.powerApps/allEntities/allTasks | 管理电源应用的所有方面 |

## <a name="printer-administrator"></a>打印机管理员

充当此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理所有打印机配置的各个方面，包括“通用打印连接器”设置。 他们可以同意所有委托的打印权限请求。 打印机管理员还有权访问打印报告。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | 创建和删除打印机和连接器，并读取和更新 Microsoft Print 中的所有属性 |

## <a name="printer-technician"></a>打印机技术人员

具有此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理打印机状态。 他们还可以读取所有连接器信息。 打印机技术人员无法执行的重要任务是设置用户对打印机的权限以及共享打印机。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | 读取 Microsoft 打印中连接器的所有属性 |
> | microsoft.azure.print/printers/allProperties/read | 读取 Microsoft 打印中打印机的所有属性 |
> | microsoft.azure.print/printers/register | 在 Microsoft 打印中注册打印机 |
> | microsoft.azure.print/printers/unregister | 在 Microsoft 打印中取消注册打印机 |
> | microsoft.azure.print/printers/basic/update | 在 Microsoft 打印中更新打印机的基本属性 |

## <a name="privileged-authentication-administrator"></a>特权身份验证管理员

具有此角色的用户可以设置或重置任何身份验证方法 (包括任何用户（包括全局管理员）) 的密码。 特权身份验证管理员可以强制用户使用现有的非密码凭据（例如 MFA 或 FIDO）重新注册，以及撤销“在设备上记住 MFA”，让系统在所有用户下次登录时提示其执行 MFA。

[身份验证管理员](#authentication-administrator)角色有权为标准用户和具有某些管理员角色的用户强制重新注册和多重身份验证。

[身份验证策略管理员](#authentication-policy-administrator)角色有权设置租户的身份验证方法策略，以确定每个用户可以注册和使用哪些方法。

| 角色 | 管理用户的身份验证方法 | 管理每用户 MFA | 管理 MFA 设置 | 管理身份验证方法策略 | 管理密码保护策略 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 身份验证管理员 | 对于某些用户 (参阅 "是")  | 对于某些用户 (参阅 "是")  | 否 | 否 | 否 |
| 特权身份验证管理员| 对于所有用户为 "是" | 对于所有用户为 "是" | 否 | 否 | 否 |
| 身份验证策略管理员 | 否 | 否 | 是 | 是 | 是 |

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：
>
>* 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过此路径，身份验证管理员可以假定应用程序所有者的身份，然后通过更新应用程序的凭据进一步假定特权应用程序的标识。
>* Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>* 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>* Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>* 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。


> [!IMPORTANT]
> 此角色目前无法在旧版 MFA 管理门户中管理按用户的 MFA。 可以使用 [set-msoluser](https://docs.microsoft.com/powershell/module/msonline/set-msoluser) Commandlet Azure AD Powershell 模块来完成相同的功能。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/strongAuthentication/update | 为用户更新强身份验证属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="privileged-role-administrator"></a>特权角色管理员

具有此角色的用户可以管理 Azure Active Directory 和 Azure AD Privileged Identity Management 中的角色分配。 他们可以创建和管理可被分配给 Azure AD 角色的组。 此外，此角色允许管理 Privileged Identity Management 和管理单元的所有方面。

> [!IMPORTANT]
> 此角色授予管理所有 Azure AD 角色（包括全局管理员角色）的分配的能力。 此角色不包括 Azure AD 中的任何其他权限功能，如创建或更新用户。 但是，分配到此角色的用户可通过分配其他角色，授予自己或其他人额外的特权。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 创建和管理管理单元（包括成员） |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | 创建和删除 appRoleAssignments，并读取和更新所有属性 |
> | authorizationPolicy/allProperties/allTasks | 管理授权策略的所有方面 |
> | microsoft.directory/directoryRoles/allProperties/allTasks | 创建和删除目录角色，并读取和更新所有属性 |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | 将 isAssignableToRole 属性设置为 true 的更新组 |
> | microsoft.directory/groupsAssignableToRoles/create | 创建 isAssignableToRole 属性设置为 true 的组 |
> | microsoft.directory/groupsAssignableToRoles/delete | 删除 isAssignableToRole 属性设置为 true 的组 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | privilegedIdentityManagement/allProperties/allTasks | 创建和删除所有资源，并读取和更新 Privileged Identity Management 中的标准属性 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除角色分配，并读取和更新所有角色分配属性 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除角色定义，并读取和更新所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，并读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | 服务主体/managePermissionGrantsForAll. microsoft-company-admin | 向任何应用程序授予任何权限的许可 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="reports-reader"></a>报告读者

具有此角色的用户可在 Microsoft 365 管理中心和 Power BI 中的采用上下文包内查看使用情况报告数据和报告仪表板。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="search-administrator"></a>搜索管理员

充当此角色的用户对 Microsoft 365 管理中心内的所有 Microsoft 搜索管理功能拥有完全访问权限。 此外，这些用户可以查看消息中心、监视服务运行状况和创建服务请求。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | office365/内容/管理 | 创建和删除内容，并读取和更新 Microsoft 搜索中的所有属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="search-editor"></a>搜索编辑员

充当此角色的用户可以在 Microsoft 365 管理中心创建、管理和删除 Microsoft 搜索的内容，包括书签、问答和位置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心内的邮件中心读取消息，不包括安全消息 |
> | office365/内容/管理 | 创建和删除内容，并读取和更新 Microsoft 搜索中的所有属性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="security-administrator"></a>安全管理员

具有此角色的用户有权管理 Microsoft 365 安全中心、Azure Active Directory 标识保护、Azure Active Directory 身份验证、Azure 信息保护和 Office 365 安全与合规中心内与安全相关的功能。 [安全与合规中心内的权限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务监视与安全相关的策略<br>管理安全威胁和警报<br>查看报表
标识保护中心 | 安全读取者角色的所有权限<br>此外，还能够执行除了重置密码以外的所有“标识保护中心”操作
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 安全读取者角色的所有权限<br>无法管理 Azure AD 角色分配或设置
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全策略<br>查看、调查和响应安全威胁<br>查看报表
Azure 高级威胁防护 | 监视和响应可疑安全活动
Windows Defender ATP 和 EDR | 分配角色<br>管理计算机组<br>配置终结点威胁检测和自动修正<br>查看、调查并响应警报
[Intune](/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息<br>无法对 Intune 进行更改
[Cloud App Security](/cloud-app-security/manage-admins) | 添加管理员、添加策略和设置、上传日志以及执行管理操作
[Azure 安全中心](../../key-vault/managed-hsm/built-in-roles.md) | 可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议、关闭警报和建议
[Microsoft 365 服务运行状况](/office365/enterprise/view-service-health) | 查看 Microsoft 365 服务的运行状况
[智能锁定](../authentication/howto-password-smart-lockout.md) | 定义在发生登录失败事件时实施锁定的阈值和持续时间。
[密码保护](../authentication/concept-password-ban-bad.md) | 配置自定义受禁密码列表或本地密码保护。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 的权利管理中的所有属性 |
> | microsoft.directory/identityProtection/allProperties/read | 读取 Azure AD Identity Protection 中的所有资源 |
> | microsoft.directory/identityProtection/allProperties/update | 更新 Azure AD Identity Protection 中的所有资源 |
> | microsoft.directory/policies/create | 在 Azure AD 中创建策略 |
> | microsoft.directory/policies/delete | 删除 Azure AD 中的策略 |
> | microsoft.directory/policies/basic/update | 更新策略的基本属性 |
> | microsoft.directory/policies/owners/update | 更新策略的所有者 |
> | microsoft.directory/policies/tenantDefault/update | 更新默认组织策略 |
> | conditionalAccessPolicies/create | 创建条件访问策略 |
> | conditionalAccessPolicies/删除 | 删除条件性访问策略 |
> | conditionalAccessPolicies/standard/read | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/物主/物主/读取 | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/policyAppliedTo/read | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/basic/update | 更新条件访问策略的基本属性 |
> | conditionalAccessPolicies/物主/物主/更新 | 更新 conditionalAccess 属性 |
> | conditionalAccessPolicies/tenantDefault/update | 更新 conditionalAccess 属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | office365. protectionCenter/allEntities/standard/read | 读取 Office 365 保护中心中所有资源的标准属性 |
> | office365. protectionCenter/allEntities/basic/update | 更新 Office 365 保护中心中所有资源的基本属性 |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 在攻击模拟器中创建和管理攻击负载 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取攻击模拟、响应和相关培训的报告 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 在攻击模拟器中创建和管理攻击模拟模板 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="security-operator"></a>安全操作员

具有此角色的用户可以管理警报，并对安全相关功能（包括 Microsoft 365 安全中心、Azure Active Directory、标识保护、Privileged Identity Management 以及 Office 365 安全与合规中心的所有信息）拥有全局只读访问权限。 [安全与合规中心内的权限](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 安全读取者角色的所有权限<br>查看、调查和响应安全威胁警报
Azure AD 标识保护 | 安全读取者角色的所有权限<br>此外，除了重置密码和配置警报电子邮件之外，还可以执行所有 Identity Protection 中心操作。
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 安全读取者角色的所有权限
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 安全读取者角色的所有权限<br>查看、调查和响应安全警报
Windows Defender ATP 和 EDR | 安全读取者角色的所有权限<br>查看、调查和响应安全警报
[Intune](/intune/role-based-access-control) | 安全读取者角色的所有权限
[Cloud App Security](/cloud-app-security/manage-admins) | 安全读取者角色的所有权限
[Microsoft 365 服务运行状况](/office365/enterprise/view-service-health) | 查看 Microsoft 365 服务的运行状况

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 创建和删除所有资源，并读取和更新 Microsoft Cloud App Security 中的标准属性 |
> | identityProtection/allProperties/allTasks | 创建和删除所有资源，并读取和更新 Azure AD Identity Protection 中的标准属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | advancedThreatProtection/allEntities/allTasks | 管理 Azure 高级威胁防护的所有方面 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft intune/allEntities/read | 读取 Microsoft Intune 中的所有资源 |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | 创建和删除所有资源，并读取和更新 Microsoft 365 安全性和符合性中心中的标准属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | defenderAdvancedThreatProtection/allEntities/allTasks | 管理 Microsoft Defender for Endpoint 的所有方面 |

## <a name="security-reader"></a>安全读取者

具有此角色的用户对安全相关的功能具有全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、标识保护、Privileged Identity Management 中的所有信息，并且能够阅读 Azure Active Directory 登录报告和审核日志，还授予了对 Office 365 安全与合规中心的只读权限。 [安全与合规中心内的权限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务查看与安全相关的策略<br>查看安全威胁和警报<br>查看报表
标识保护中心 | 读取安全功能的所有安全报告和设置信息<br><ul><li>反垃圾邮件<li>加密<li>数据丢失防护<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 以只读方式访问 Azure AD Privileged Identity Management 中显示的所有信息：Azure AD 角色分配的策略和报告以及安全评审。<br>**无法** 注册 Azure AD Privileged Identity Management 或对其进行任何更改。 在 Privileged Identity Management 门户中或通过 PowerShell，此角色中的用户可以激活其他角色 (例如，全局管理员或特权角色管理员) ，前提是用户有资格获得这些角色。
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 查看安全策略<br>查看并调查安全威胁<br>查看报表
Windows Defender ATP 和 EDR | 查看并调查警报。 在 Windows Defender ATP 中启用基于角色的访问控制后，拥有只读权限的用户（例如 Azure AD 安全读取者角色）在被分配到 Windows Defender ATP 角色之前会失去访问权限。
[Intune](/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息。 无法对 Intune 进行更改。
[Cloud App Security](/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报
[Azure 安全中心](../../key-vault/managed-hsm/built-in-roles.md) | 可以查看建议和警报、查看安全策略、查看安全状态，但不能进行更改
[Microsoft 365 服务运行状况](/office365/enterprise/view-service-health) | 查看 Microsoft 365 服务的运行状况

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 bitlocker 元数据和密钥 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 的权利管理中的所有属性 |
> | microsoft.directory/identityProtection/allProperties/read | 读取 Azure AD Identity Protection 中的所有资源 |
> | microsoft.directory/policies/standard/read | 读取策略的基本属性 |
> | microsoft.directory/policies/owners/read | 读取策略的所有者 |
> | microsoft. directory/策略/policyAppliedTo/read | 读取 policyAppliedTo 属性 |
> | conditionalAccessPolicies/standard/read | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/物主/物主/读取 | 读取 conditionalAccess 属性 |
> | conditionalAccessPolicies/policyAppliedTo/read | 读取 conditionalAccess 属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | office365. protectionCenter/allEntities/standard/read | 读取 Office 365 保护中心中所有资源的标准属性 |
> | office365/protectionCenter/attackSimulator/负载/allProperties/read | 读取攻击模拟器中攻击负载的所有属性 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取攻击模拟、响应和相关培训的报告 |
> | office365/protectionCenter/attackSimulator/模拟/allProperties/read | 读取攻击模拟器中攻击模拟模板的所有属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="service-support-administrator"></a>服务支持管理员

具有此角色的用户可以打开 Microsoft for Azure 和 Microsoft 365 服务的支持请求，并在 [Azure 门户](https://portal.azure.com) 和 [Microsoft 365 管理中心](https://admin.microsoft.com)中查看服务仪表板和消息中心。 在[关于管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中了解详细信息。

> [!NOTE]
> 以前，此角色在 [Azure 门户](https://portal.azure.com)和 [Microsoft 365 管理中心](https://admin.microsoft.com)内称为“服务管理员”。 我们已将其重命名为“服务支持管理员”，以便与 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中的现有名称保持一致。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="sharepoint-administrator"></a>SharePoint 管理员

具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并且能够创建和管理所有 Microsoft 365 组，管理支持票证并监视服务运行状况。 在[关于管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中了解详细信息。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“SharePoint 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“SharePoint 管理员”。

> [!NOTE]
> 此角色还将作用域内权限授予用于 Microsoft Intune 的 Microsoft Graph API，从而允许管理和配置与 SharePoint 和 OneDrive 资源相关的策略。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft. 目录/组。统一/创建 | 创建 Microsoft 365 组，排除角色可分配的组 |
> | microsoft. directory/groups. 统一/删除 | 删除 Microsoft 365 组，排除角色可分配的组 |
> | microsoft. 目录/组。统一/还原 | 还原 Microsoft 365 组 |
> | microsoft. directory/groups. 统一/基本/更新 | 更新 Microsoft 365 组的基本属性，排除角色可分配的组 |
> | microsoft. 目录/组。统一/成员/更新 | 更新 Microsoft 365 组的成员，排除角色可分配的组 |
> | microsoft. 目录/组。统一/所有者/更新 | 更新 Microsoft 365 组的所有者，排除角色可分配的组 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.network/performance/allProperties/read | 读取 Microsoft 365 管理中心中的所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | office365/allEntities/allTasks | 创建和删除所有资源，并读取和更新 SharePoint 中的标准属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="skype-for-business-administrator"></a>Skype for Business 管理员

具有此角色的用户具有 Microsoft Skype for Business 中的全局权限，以及管理 Azure Active Directory 中的特定于 Skype 的用户属性。 此外，此角色可授予管理支持票证、监视服务运行状况以及访问 Teams 和 Skype for Business 管理中心的能力。 帐户必须获取 Teams 许可证，否则无法运行 Teams PowerShell cmdlet。 有关详细信息，请参阅[关于 Skype for Business 管理员角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)；有关 Teams 许可信息，请参阅 [Skype for Business 和 Microsoft Teams 附加许可](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Lync 服务管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“Skype for Business 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="teams-administrator"></a>Teams 管理员

充当此角色的用户可以通过 Microsoft Teams 和 Skype for Business 管理中心以及相应的 PowerShell 模块来管理 Microsoft Teams 工作负荷的所有方面。 这包括（但不限于）与电话、消息、会议和 Teams 自身相关的所有管理工具。 另外，利用此角色，还可以创建和管理所有 Microsoft 365 组，管理支持票证并监视服务运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | 读取组的隐藏成员 |
> | microsoft. 目录/组。统一/创建 | 创建 Microsoft 365 组，排除角色可分配的组 |
> | microsoft. directory/groups. 统一/删除 | 删除 Microsoft 365 组，排除角色可分配的组 |
> | microsoft. 目录/组。统一/还原 | 还原 Microsoft 365 组 |
> | microsoft. directory/groups. 统一/基本/更新 | 更新 Microsoft 365 组的基本属性，排除角色可分配的组 |
> | microsoft. 目录/组。统一/成员/更新 | 更新 Microsoft 365 组的成员，排除角色可分配的组 |
> | microsoft. 目录/组。统一/所有者/更新 | 更新 Microsoft 365 组的所有者，排除角色可分配的组 |
> | 服务主体/managePermissionGrantsForGroup. microsoft-all-application-permissions | 授予服务主体直接访问组数据的权限  |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.network/performance/allProperties/read | 读取 Microsoft 365 管理中心中的所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft.teams/allEntities/allProperties/allTasks | 管理团队中的所有资源 |

## <a name="teams-communications-administrator"></a>Teams 通信管理员

充当此角色的用户可以管理 Microsoft Teams 工作负荷的语音与电话相关方面。 这包括用于分配电话号码的管理工具、语音和会议策略，以及通话分析工具集的完全访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | office365. usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft.teams/callQuality/allProperties/read | 阅读呼叫质量仪表板中的所有数据 (CQD)  |
> | microsoft.teams/meetings/allProperties/allTasks | 管理会议（包括会议策略、配置和会议桥） |
> | microsoft.teams/voice/allProperties/allTasks | 管理语音，包括呼叫策略和电话号码库存和分配 |

## <a name="teams-communications-support-engineer"></a>Teams 通信支持工程师

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户可以查看所有参与方的完整通话记录信息。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的所有方面 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft.teams/callQuality/allProperties/read | 阅读呼叫质量仪表板中的所有数据 (CQD)  |

## <a name="teams-communications-support-specialist"></a>Teams 通信支持专家

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户只能查看他们所查找的特定用户的通话中的用户详细信息。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的所有方面 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | callQuality/standard/read | 阅读电话质量仪表板中的基本数据 (CQD)  |

## <a name="teams-devices-administrator"></a>Teams 设备管理员

具有此角色的用户可以在 Teams 管理中心管理 [Teams 认证的设备](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)。 此角色允许同时查看所有设备，并能够搜索和筛选设备。 用户可以检查每个设备的详细信息，包括设备的登录帐户、品牌和型号。 用户可以更改设备上的设置并更新软件版本。 此角色不会授权检查 Teams 活动和设备的通话质量。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |
> | microsoft. 团队/设备/标准/读取 | 管理已认证的团队所有方面的设备，包括配置策略 |

## <a name="usage-summary-reports-reader"></a>使用情况摘要报表读取者

拥有此角色的用户可以访问 Microsoft 365 管理中心内使用情况和生产力分数的租户级聚合数据和相关见解，但无法访问任何用户级详细信息或见解。 在 Microsoft 365 管理中心的这两个报表中，我们区分了租户级聚合数据和用户级详细信息。 此角色为客户和法律团队共同请求的单个用户身份数据提供了一层额外的保护。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.usageReports/allEntities/standard/read | 读取租户级聚合 Office 365 使用情况报表 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="user-administrator"></a>用户管理员

具有此角色的用户可以创建用户并管理用户的所有方面（存在一些限制，具体请参阅下表），还可以更新密码过期策略。 此外，具有此角色的用户可以创建和管理所有组。 此角色还能够创建和管理用户视图、管理支持票证和监视服务运行状况。 用户管理员无权管理充当大部分管理员角色的用户的某些用户属性。 具有此角色的用户无权管理 MFA。 下表列出了不存在这种限制的角色。

| 用户管理员权限 | 说明 |
| --- | --- |
| 创建用户和组<br/>创建和管理用户视图<br/>管理 Office 支持票证<br/>更新密码过期策略 |  |
| 管理许可证<br/>管理除用户主体名称之外的所有用户属性 | 适用于所有用户（包括所有管理员） |
| 删除和还原<br/>禁用和启用<br/>管理包括用户主体名称在内的所有用户属性<br/>更新 (FIDO) 设备密钥 | 适用于非管理员用户或以下任意角色的用户：<ul><li>支持管理员</li><li>无角色的用户</li><li>用户管理员</li></ul> |
| 使刷新令牌失效<br/>重置密码 | 有关用户管理员可以为其重置密码和使刷新令牌无效的角色的列表，请参阅 [密码重置权限](#password-reset-permissions)。 |

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予用户管理员的特权。 通过此路径，用户管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>- 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | 创建应用程序角色分配 |
> | microsoft.directory/appRoleAssignments/delete | 删除应用程序角色分配 |
> | appRoleAssignments/basic/update | 更新应用程序角色分配的基本属性 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | 创建和删除资源，并读取和更新 Azure AD 权限管理中的所有属性 |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以进行基于组的许可 |
> | microsoft.directory/groups/create | 创建组，排除角色可分配的组 |
> | microsoft.directory/groups/delete | 删除组，不包括角色可分配的组 |
> | microsoft.directory/groups/hiddenMembers/read | 读取组的隐藏成员 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/basic/update | 更新组的基本属性，不包括角色可分配的组 |
> | microsoft.directory/groups/classification/update | 更新组的分类属性，不包括角色可分配的组 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 更新组的动态成员身份规则，排除角色可分配的组 |
> | microsoft.directory/groups/groupType/update | 更新组的 groupType 属性 |
> | microsoft.directory/groups/members/update | 更新组的成员，排除角色可分配的组 |
> | microsoft. directory/groups/onPremWriteBack/update | 更新要写回本地的 Azure AD 组 |
> | microsoft.directory/groups/owners/update | 更新组的所有者，不包括角色可分配的组 |
> | microsoft.directory/groups/settings/update | 更新组的设置 |
> | microsoft.directory/groups/visibility/update | 更新组的可见性属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，并读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/delete | 删除用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/inviteGuest | 邀请来宾用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过使用户刷新令牌失效来强制注销 |
> | microsoft.directory/users/reprocessLicenseAssignment | 重新处理用户的许可证分配 |
> | microsoft.directory/users/restore | 还原已删除的用户 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 用户的更新管理器 |
> | microsoft.directory/users/password/update | 为所有用户重置密码 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 读取 Microsoft 365 管理中心中所有资源的基本属性 |

## <a name="deprecated-roles"></a>已弃用的角色

不应使用以下角色。 这些角色已弃用，并将从 Azure AD 中删除。

* 即席许可证管理员
* 设备联接
* 设备管理器
* 设备用户
* 经电子邮件验证的用户创建者
* 邮箱管理员
* 工作区设备联接

## <a name="roles-not-shown-in-the-portal"></a>门户中未显示的角色

Azure 门户中不一定会显示 PowerShell 或 MS Graph API 返回的每个角色。 下表整理了这些差异。

API 名称 | Azure 门户中的名称 | 说明
-------- | ------------------- | -------------
设备联接 | 不推荐使用 | [已弃用角色的文档](#deprecated-roles)
设备管理器 | 不推荐使用 | [已弃用角色的文档](#deprecated-roles)
设备用户 | 不推荐使用 | [已弃用角色的文档](#deprecated-roles)
目录同步帐户 | 未显示，因为不应使用它 | [目录同步帐户文档](#directory-synchronization-accounts)
来宾用户 | 未显示，因为无法使用它 | NA
合作伙伴层 1 支持 | 未显示，因为不应使用它 | [合作伙伴一线支持人员文档](#partner-tier1-support)
合作伙伴层 2 支持 | 未显示，因为不应使用它 | [合作伙伴二线支持人员文档](#partner-tier2-support)
受限来宾用户 | 未显示，因为无法使用它 | NA
用户 | 未显示，因为无法使用它 | NA
工作区设备联接 | 不推荐使用 | [已弃用角色的文档](#deprecated-roles)

## <a name="password-reset-permissions"></a>密码重置权限

列标题表示可以重置密码的角色。 表行包含其密码可以重置的角色。

可以重置密码 | 密码管理员 | 支持管理员 | 身份验证管理员 | 用户管理员 | 特权身份验证管理员 | 全局管理员
------ | ------ | ------ | ------ | ------ | ------ | ------
身份验证管理员 | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
目录读者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
全局管理员 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
组管理员 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
来宾邀请者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
支持管理员 | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
消息中心读取者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
密码管理员 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
特权身份验证管理员 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
特权角色管理员 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
报告读者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
不 (管理员角色的用户)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
用户管理员 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
使用情况摘要报表读取者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* 全局管理员无法删除自己的全局管理员分配。 这是为了防止组织有0个全局管理员的情况。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何将用户分配为 Azure 订阅的管理员，请参阅 [将用户分配为 azure 订阅的管理员](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
* 若要详细了解如何在 Microsoft Azure 中控制资源访问，请参阅[了解不同的角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 有关订阅和 Azure AD 租户之间的关系的详细信息，或者有关关联或添加订阅的说明，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
