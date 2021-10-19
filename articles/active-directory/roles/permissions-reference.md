---
title: Azure AD 内置角色 - Azure Active Directory
description: 介绍 Azure Active Directory 内置角色和权限。
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/07/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: generated, it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cbd4732513b9c9bcc40f6eafb0a792b2d3ca06
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667870"
---
# <a name="azure-ad-built-in-roles"></a>Azure AD 内置角色

在 Azure Active Directory (Azure AD) 中，如果其他管理员或非管理员需要管理 Azure AD 资源，为其分配能够提供所需权限的 Azure AD 角色。 例如，可分配允许添加或更改用户、重置用户密码、管理用户许可证或管理域名的角色。

本文列出了可分配来管理 Azure AD 资源的 Azure AD 内置角色。 有关如何分配角色的信息，请参阅[将 Azure AD 角色分配给用户](manage-roles-portal.md)。

## <a name="all-roles"></a>所有角色

> [!div class="mx-tableFixed"]
> | 角色 | 说明 | 模板 ID |
> | --- | --- | --- |
> | [应用程序管理员](#application-administrator) | 可以创建和管理应用注册和企业应用的所有方面。 | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [应用程序开发人员](#application-developer) | 可以创建独立于“用户可注册应用程序”设置的应用程序注册。 | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [攻击有效负载作者](#attack-payload-author) | 可以创建管理员可于之后启动的攻击有效负载。 | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [攻击模拟管理员](#attack-simulation-administrator) | 可以创建和管理攻击模拟活动的各个方面。 | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [身份验证管理员](#authentication-administrator) | 可访问并查看、设置和重置任何非管理员用户的身份验证方法信息。 | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [身份验证策略管理员](#authentication-policy-administrator) | 可以创建和管理身份验证方法策略、租户范围的 MFA 设置、密码保护策略和可验证凭据。 | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [已加入 Azure AD 的设备的本地管理员](#azure-ad-joined-device-local-administrator) | 分配到此角色的用户将添加到已加入 Azure AD 的设备上的本地管理员组。 | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps 管理员](#azure-devops-administrator) | 可以管理 Azure DevOps 的组织策略和设置。 | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure 信息保护管理员](#azure-information-protection-administrator) | 可以管理 Azure 信息保护产品的所有方面。 | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF 密钥集管理员](#b2c-ief-keyset-administrator) | 可以在 Identity Experience Framework (IEF) 中管理联合机密和加密机密。 | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF 策略管理员](#b2c-ief-policy-administrator) | 可以在 Identity Experience Framework (IEF) 中创建和管理信任框架策略。 | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [计费管理员](#billing-administrator) | 可以执行与常见计费相关的任务，例如更新付款信息。 | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Cloud App Security 管理员](#cloud-app-security-administrator) | 可以管理 Cloud App Security 产品的所有方面。 | 892c5842-a9a6-463a-8041-72aa08ca3cf6 |
> | [云应用程序管理员](#cloud-application-administrator) | 可以创建和管理应用注册和企业应用的所有方面，应用代理除外。 | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [云设备管理员](#cloud-device-administrator) | 用于在 Azure AD 中管理设备的有限访问权限。 | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [合规性管理员](#compliance-administrator) | 可以读取和管理 Azure AD 和 Microsoft 365 中的合规性配置和报表。 | 17315797-102d-40b4-93e0-432062caca18 |
> | [合规性数据管理员](#compliance-data-administrator) | 创建和管理合规性内容。 | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [条件访问管理员](#conditional-access-administrator) | 可以管理条件访问功能。 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [客户密码箱访问审批者](#customer-lockbox-access-approver) | 可以批准 Microsoft 支持人员访问客户组织数据的请求。 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [桌面分析管理员](#desktop-analytics-administrator) | 可访问和管理桌面管理工具和服务。 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [目录读取者](#directory-readers) | 可以读取基本目录信息。 通常用于授予对应用程序和来宾的目录读取权限。 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [目录同步帐户](#directory-synchronization-accounts) | 仅供 Azure AD Connect 服务使用。 | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [目录写入者](#directory-writers) | 可以读取和写入基本目录信息。 用于授予对应用程序的访问权限，不针对用户。 | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [域名管理员](#domain-name-administrator) | 可以管理云中和本地的域名。 | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365 管理员](#dynamics-365-administrator) | 可以管理 Dynamics 365 产品的所有方面。 | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Edge 管理员](#edge-administrator) | 管理 Microsoft Edge 的所有方面。 | 3f1acade-1e04-4fbc-9b69-f0302cd84aef |
> | [Exchange 管理员](#exchange-administrator) | 可以管理 Exchange 产品的所有方面。 | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Exchange 收件人管理员](#exchange-recipient-administrator) | 可以在 Exchange Online 组织中创建或更新 Exchange Online 收件人。 | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [外部 ID 用户流管理员](#external-id-user-flow-administrator) | 可以创建和管理用户流的各个方面。 | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [外部 ID 用户流属性管理员](#external-id-user-flow-attribute-administrator) | 可以创建和管理对所有用户流可用的属性架构。 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [外部标识提供者管理员](#external-identity-provider-administrator) | 可以配置用于直接联合的标识提供者。 | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [全局管理员](#global-administrator) | 可以管理 Azure AD 和使用 Azure AD 标识的 Microsoft 服务的所有方面。 | 62e90394-69f5-4237-9190-012177145e10 |
> | [全局读取者](#global-reader) | 可以读取全局管理员可以读取的所有内容，但不能更新任何内容。 | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [组管理员](#groups-administrator) | 此角色的成员可以创建/管理组、创建/管理组设置（如命名和过期策略）以及查看组活动和审核报告。 | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [来宾邀请者](#guest-inviter) | 可以无视“成员可邀请来宾”设置而邀请来宾用户。 | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [支持管理员](#helpdesk-administrator) | 可以重置非管理员和支持理员的密码。 | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [混合标识管理员](#hybrid-identity-administrator) | 可以管理 AD 到 Azure AD 的云预配、Azure AD Connect 和联合身份验证设置。 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Identity Governance 管理员](#identity-governance-administrator) | 使用 Azure AD Identity Governance 方案管理访问权限。 | 45d8d3c5-c802-45c6-b32a-1d70b5e1e86e |
> | [Insights 管理员](#insights-administrator) | 在 Microsoft 365 Insights 应用中具有管理访问权限。 | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights 业务主管](#insights-business-leader) | 可通过 Microsoft 365 Insights 应用来查看和共享仪表板和见解。 | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune 管理员](#intune-administrator) | 可以管理 Intune 产品的所有方面。 | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala 管理员](#kaizala-administrator) | 可以管理 Microsoft Kaizala 的设置。 | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [知识管理员](#knowledge-administrator) | 可配置知识、学习和其他智能功能。 | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [知识经理](#knowledge-manager) | 可以组织、创建、管理和提升主题与知识。 | 744ec460-397e-42ad-a462-8b3f9747a02c |
> | [许可证管理员](#license-administrator) | 可以管理用户和组的产品许可证。 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [消息中心隐私读取者](#message-center-privacy-reader) | 只能在 Office 365 消息中心读取安全消息和更新。 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [消息中心读取者](#message-center-reader) | 只能在 Office 365 消息中心查看其组织的消息和更新。 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [现代商业用户](#modern-commerce-user) | 可以管理公司、部门或团队的商用购买内容。 | d24aef57-1500-4070-84db-2666f29cf966 |
> | [网络管理员](#network-administrator) | 可以管理网络位置，并审阅有关 Microsoft 365 软件即服务应用程序的企业网络设计见解。 | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office 应用管理员](#office-apps-administrator) | 可以管理 Office 应用云服务（包括策略和设置管理），并管理选择、取消选择和向最终用户的设备发布“新增功能”功能内容的权限。 | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [合作伙伴一线支持人员](#partner-tier1-support) | 不要使用 - 不适用于常规用途。 | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [合作伙伴二线支持人员](#partner-tier2-support) | 不要使用 - 不适用于常规用途。 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [密码管理员](#password-administrator) | 可以为非管理员和密码管理员重置密码。 | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI 管理员](#power-bi-administrator) | 可以管理 Power BI 产品的所有方面。 | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform 管理员](#power-platform-administrator) | 可以创建和管理 Microsoft Dynamics 365、Power Apps 和 Power Automate 的所有方面。 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [打印机管理员](#printer-administrator) | 可以管理打印机和打印机连接器的所有方面。 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [打印机技术人员](#printer-technician) | 可以注册和取消注册打印机，并更新打印机状态。 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [特权身份验证管理员](#privileged-authentication-administrator) | 可有权查看、设置和重置任何用户（管理员或非管理员）的身份验证方法信息。 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [特权角色管理员](#privileged-role-administrator) | 可以管理 Azure AD 中的角色分配，以及 Privileged Identity Management 的各个方面。 | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [报告读取者](#reports-reader) | 可以读取登录和审核报告。 | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [搜索管理员](#search-administrator) | 可以创建和管理 Microsoft 搜索设置的所有方面。 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [搜索编辑员](#search-editor) | 可以创建和管理书签、问答、位置、平面布置图等编辑内容。 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [安全管理员](#security-administrator) | 可以读取安全信息和报表，以及管理 Azure AD 和 Office 365 中的配置。 | 194ae4cb-b126-40b2-bd5b-6091b380977d |
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
> | [使用情况摘要报表读取者](#usage-summary-reports-reader) | 只能查看 Microsoft 365 使用情况分析和生产力分数中的租户级聚合。 | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [用户管理员](#user-administrator) | 可以管理用户和组的所有方面，包括重置有限管理员的密码。 | fe930be7-5e62-47db-91af-98c3a49a38b1 |
> | [Windows 更新部署管理员](#windows-update-deployment-administrator) | 通过适用于企业的 Windows 更新部署服务来创建和管理 Windows 更新部署的所有方面。 | 32696413-001a-46ae-978c-ce0f6b3620d2 |

## <a name="application-administrator"></a>应用程序管理员

充当此角色的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 请注意，在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

此角色还可以许可委托的权限和应用程序权限，但对 Microsoft Graph 和 Azure AD Graph 的应用程序权限除外。

> [!IMPORTANT]
> 这种例外情况意味着，你仍可以许可对其他应用（例如，非 Microsoft 应用或已注册应用）的应用程序权限。 你仍然可以在应用注册过程中请求这些权限，但授予（即许可）这些权限需要由权限较高的管理员（如全局管理员）完成。 
>
>此角色授予管理应用程序凭据这一功能。 分配有此角色的用户可以将凭据添加到应用程序，并使用这些凭据模拟应用程序的标识。 如果已向应用程序的标识授予资源访问权限，例如创建或更新用户或其他对象，那么分配到此角色的用户在模拟应用程序时可以执行这些操作。 这种模拟应用程序标识的能力可能是用户在角色分配的基础上的权限提升。 请务必了解，向用户分配应用程序管理员角色，会赋予其模拟应用程序标识的能力。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft.directory/applications/applicationProxy/read | 读取所有应用程序代理属性 |
> | microsoft.directory/applications/applicationProxy/update | 更新所有应用程序代理属性 |
> | microsoft.directory/applications/applicationProxyAuthentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/applicationProxySslCertificate/update | 更新应用程序代理的 SSL 证书设置 |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | 更新应用程序代理的 URL 设置 |
> | microsoft.directory/applications/appRoles/update | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/extensionProperties/update | 更新应用程序的扩展属性 |
> | microsoft.directory/applications/notes/update | 更新应用程序的说明 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新所有类型的应用程序的公开权限和必需权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/tag/update | 更新应用程序的标记 |
> | microsoft.directory/applications/verification/update | 更新 applicationsverification 属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/connectors/create | 创建应用程序代理连接器 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/connectorGroups/create | 创建应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/delete | 删除应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/update | 更新应用程序代理连接器组的所有属性 |
> | microsoft.directory/deletedItems.applications/delete | 永久删除不再可以还原的应用程序 |
> | microsoft.directory/deletedItems.applications/restore | 将软删除的应用程序还原到原始状态 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/applicationPolicies/create | 创建应用程序策略 |
> | microsoft.directory/applicationPolicies/delete | 删除应用程序策略 |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/read | 读取应用程序策略的所有者 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 读取应用于对象列表的应用程序策略 |
> | microsoft.directory/applicationPolicies/basic/update | 更新应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/update | 更新应用程序策略的所有者属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 管理服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序预配机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重启和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 读取服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | 以任何一个用户或所有用户的身份许可应用程序权限和委托的权限，但对 Microsoft Graph 和 Azure AD Graph 的应用程序权限除外 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的受众属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/notes/update | 更新服务主体的说明 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的标记属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="application-developer"></a>应用程序开发人员

在将设置“用户可以注册应用程序”设置为“否”时，充当此角色的用户可以创建应用程序注册。 当“用户可以同意应用代表他们访问公司数据”设置设为“否”时，此角色还能够代表自己授权同意。 在创建新应用程序注册时，会将分配到此角色的用户添加为所有者。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | 创建所有类型的应用程序，将创建者添加为第一个所有者 |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | 创建 OAuth 2.0 权限授予，并将创建者作为第一个所有者 |
> | microsoft.directory/servicePrincipals/createAsOwner | 创建服务主体，并将创建者作为第一个所有者 |

## <a name="attack-payload-author"></a>攻击有效负载作者

拥有此角色的用户可以创建攻击有效负载，但不能实际启动或调度它们。 然后，租户中的所有管理员都可以使用攻击有效负载创建模拟。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 在攻击模拟器中创建和管理攻击有效负载 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取有关攻击模拟响应和相关训练的报告 |

## <a name="attack-simulation-administrator"></a>攻击模拟管理员

拥有此角色的用户可以创建和管理攻击模拟创建的所有方面、启动/调度模拟以及查看模拟结果。 此角色的成员对租户中的所有模拟具有此访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 在攻击模拟器中创建和管理攻击有效负载 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取有关攻击模拟响应和相关训练的报告 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 在攻击模拟器中创建和管理攻击模拟模板 |

## <a name="authentication-administrator"></a>身份验证管理员

具有此角色的用户可以设置或重置非管理员和某些角色的身份验证方法（包括密码）。 身份验证管理员可以要求非管理员用户或分配给某些角色的用户使用现有的非密码凭据（例如，MFA 或 FIDO）重新注册，还可以撤销“在设备上记住 MFA”，这样系统就会在用户下次登录时提示其进行 MFA。 有关身份验证管理员可以读取或更新身份验证方法的角色的列表，请参阅[密码重置权限](#password-reset-permissions)。

[特权身份验证管理员](#privileged-authentication-administrator)角色有权强制针对所有用户进行的重新注册和多重身份验证操作。

[身份验证策略管理员](#authentication-policy-administrator)角色有权设置租户的身份验证方法策略，以确定每个用户可以注册和使用的方法。

| 角色 | 管理用户的身份验证方法 | 管理每用户 MFA | 管理 MFA 设置 | 管理身份验证方法策略 | 管理密码保护策略 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 身份验证管理员 | 对于某些用户为“是”（见上） | 对于某些用户为“是”（见上） | 否 | 否 | 否 |
| 特权身份验证管理员| 对于所有用户为“是” | 对于所有用户为“是” | 否 | 否 | 否 |
| 身份验证策略管理员 | 否 |否 | 是 | 是 | 是 |

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：
>
>* 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过此路径，身份验证管理员可以假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>* Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>* 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>* Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>* 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

> [!IMPORTANT]
> 此角色无法在旧版 MFA 管理门户中管理 MFA 设置，且无法管理硬件 OATH 令牌。 可以使用 [Set-MsolUser](/powershell/module/msonline/set-msoluser) commandlet Azure AD Powershell 模块来实现相同的功能。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | 为用户创建身份验证方法 |
> | microsoft.directory/users/authenticationMethods/delete | 删除用户的身份验证方法 |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | 读取身份验证方法的标准属性，不包括用户的个人身份信息 |
> | microsoft.directory/users/authenticationMethods/basic/update | 更新用户身份验证方法的基本属性 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="authentication-policy-administrator"></a>身份验证策略管理员

具有此角色的用户可以配置身份验证方法策略、租户范围 MFA 设置和密码保护策略。 此角色授予管理密码保护设置的权限：智能锁定配置和更新自定义禁止密码列表。

[身份验证管理员](#authentication-administrator)和[特权身份验证管理员](#privileged-authentication-administrator)角色有权管理用户的已注册的身份验证方法，并可以强制对所有用户进行重新注册和多重身份验证。

| 角色 | 管理用户的身份验证方法 | 管理每用户 MFA | 管理 MFA 设置 | 管理身份验证方法策略 | 管理密码保护策略 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 身份验证管理员 | 对于某些用户为“是”（见上） | 对于某些用户为“是”（见上） | 否 | 否 | 否 |
| 特权身份验证管理员| 对于所有用户为“是” | 对于所有用户为“是” | 否 | 否 | 否 |
| 身份验证策略管理员 | 否 | 否 | 是 | 是 | 是 |

> [!IMPORTANT]
> 此角色无法在旧版 MFA 管理门户中管理 MFA 设置，且无法管理硬件 OATH 令牌。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/allTasks | 管理组织的强身份验证属性的所有方面 |
> | microsoft.directory/userCredentialPolicies/create | 创建用户的凭据策略 |
> | microsoft.directory/userCredentialPolicies/delete | 删除用户的凭据策略 |
> | microsoft.directory/userCredentialPolicies/standard/read | 读取用户凭据策略的标准属性 |
> | microsoft.directory/userCredentialPolicies/owners/read | 读取用户凭据策略的所有者 |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | 读取 policy.appliesTo 导航链接 |
> | microsoft.directory/userCredentialPolicies/basic/update | 更新用户的基本策略 |
> | microsoft.directory/userCredentialPolicies/owners/update | 更新用户凭据策略的所有者 |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | 更新 policy.isOrganizationDefault 属性 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | 读取可验证凭据卡 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | 吊销可验证凭据卡 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | 创建可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | 读取可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | 更新可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/create | 创建创建和管理可验证凭据所需的配置 |
> | microsoft.directory/verifiableCredentials/configuration/delete | 删除创建和管理可验证凭据所需的配置，并删除其所有可验证凭据 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | 读取创建和管理可验证凭据所需的配置 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | 更新创建和管理可验证凭据所需的配置 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |

## <a name="azure-ad-joined-device-local-administrator"></a>已加入 Azure AD 的设备的本地管理员

此角色只能作为[设备设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本地管理员进行分配。 拥有此角色的用户成为所有已加入 Azure Active Directory 的 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | 读取组设置的基本属性 |
> | microsoft.directory/groupSettingTemplates/standard/read | 读取组设置模板的基本属性 |

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
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF 密钥集管理员

用户可以创建和管理用于令牌加密、令牌签名和声明加密/解密的策略密钥与机密。 通过将新密钥添加到现有密钥容器，此受限管理员可以根据需要滚动更新机密，而不会影响现有的应用程序。  即使是在创建这些机密之后，此用户也可以查看这些机密的完整内容及其过期日期。

> [!IMPORTANT]
> 这是一个敏感角色。  在生产前与生产期间，应该谨慎地审核和分配密钥集管理员角色。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | 读取和更新授权策略的所有属性 |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF 策略管理员

充当此角色的用户可以在 Azure AD B2C 中创建、读取、更新和删除所有自定义策略，因此对相关 Azure AD B2C 组织中的 Identity Experience Framework 拥有完全控制权。 通过编辑策略，此用户可以直接与外部标识提供者建立联合、更改目录架构、更改所有面向用户的内容（HTML、CSS、JavaScript）、更改完成身份验证所需满足的要求、创建新用户、将用户数据发送到外部系统（包括完整迁移），以及编辑所有用户信息（包括密码和电话号码等敏感字段）。 相比之下，此角色无法更改加密密钥，也不能编辑组织中用于联合身份验证的机密。

> [!IMPORTANT]
> B2 IEF 策略管理员是高度敏感的角色，在生产环境中应以极大的限制度将其分配给组织。  应该密切审核这些用户（尤其是生产环境中的组织的用户）的活动。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | 读取和配置 Azure Active Directory B2C 中的密钥集 |

## <a name="billing-administrator"></a>计费管理员

进行采购、管理订阅、管理支持票证，以及监视服务运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/organization/basic/update | 更新组织的基本属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 计费的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |

## <a name="cloud-app-security-administrator"></a>Cloud App Security 管理员

具有此角色的用户在 Cloud App Security 中拥有完全权限。 他们可以添加管理员、添加 Microsoft Cloud App Security (MCAS) 策略和设置、上传日志以及执行治理操作。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 在 Microsoft Cloud App Security 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="cloud-application-administrator"></a>云应用管理员

充当此角色的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 此角色授予创建和管理企业应用程序和应用程序注册的所有方面的权限。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

此角色还可以许可委托的权限和应用程序权限，但对 Microsoft Graph 和 Azure AD Graph 的应用程序权限除外。

> [!IMPORTANT]
> 这种例外情况意味着，你仍可以许可对其他应用（例如，非 Microsoft 应用或已注册应用）的应用程序权限。 你仍然可以在应用注册过程中请求这些权限，但授予（即许可）这些权限需要由权限较高的管理员（如全局管理员）完成。 
>
>此角色授予管理应用程序凭据这一功能。 分配有此角色的用户可以将凭据添加到应用程序，并使用这些凭据模拟应用程序的标识。 如果已向应用程序的标识授予资源访问权限，例如创建或更新用户或其他对象，那么分配到此角色的用户在模拟应用程序时可以执行这些操作。 这种模拟应用程序标识的能力可能是用户在角色分配的基础上的权限提升。 请务必了解，向用户分配应用程序管理员角色，会赋予其模拟应用程序标识的能力。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft.directory/applications/appRoles/update | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/extensionProperties/update | 更新应用程序的扩展属性 |
> | microsoft.directory/applications/notes/update | 更新应用程序的说明 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新所有类型的应用程序的公开权限和必需权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/tag/update | 更新应用程序的标记 |
> | microsoft.directory/applications/verification/update | 更新 applicationsverification 属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/deletedItems.applications/delete | 永久删除不再可以还原的应用程序 |
> | microsoft.directory/deletedItems.applications/restore | 将软删除的应用程序还原到原始状态 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/applicationPolicies/create | 创建应用程序策略 |
> | microsoft.directory/applicationPolicies/delete | 删除应用程序策略 |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/read | 读取应用程序策略的所有者 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 读取应用于对象列表的应用程序策略 |
> | microsoft.directory/applicationPolicies/basic/update | 更新应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/owners/update | 更新应用程序策略的所有者属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 管理服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序预配机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重启和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 读取服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | 以任何一个用户或所有用户的身份许可应用程序权限和委托的权限，但对 Microsoft Graph 和 Azure AD Graph 的应用程序权限除外 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的受众属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/notes/update | 更新服务主体的说明 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的标记属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="cloud-device-administrator"></a>云设备管理员

充当此角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。 该角色不能授予设备上其他任何属性的管理权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/devices/delete | 从 Azure AD 删除设备 |
> | microsoft.directory/devices/disable | 在 Azure AD 中禁用设备 |
> | microsoft.directory/devices/enable | 在 Azure AD 中启用设备 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 读取设备管理应用程序策略上的标准属性 |
> | microsoft.directory/deviceManagementPolicies/basic/update | 更新设备管理应用程序策略上的基本属性 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 读取设备注册策略上的标准属性 |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | 更新设备注册策略上的基本属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |

## <a name="compliance-administrator"></a>符合性管理员

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
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 权利管理中的所有属性 |
> | microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

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
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 在 Microsoft Cloud App Security 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="conditional-access-administrator"></a>条件访问管理员

具有此角色的用户能够管理 Azure Active Directory 条件访问设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | 创建条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/delete | 删除条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/standard/read | 读取条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/owners/read | 读取条件访问策略的所有者 |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | 读取条件访问策略的“适用对象”属性 |
> | microsoft.directory/conditionalAccessPolicies/basic/update | 更新条件访问策略的基本属性 |
> | microsoft.directory/conditionalAccessPolicies/owners/update | 更新条件访问策略的所有者 |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | 更新条件访问策略的默认租户 |
> | microsoft.directory/crossTenantAccessPolicies/create | 创建跨租户访问策略 |
> | microsoft.directory/crossTenantAccessPolicies/delete | 删除跨租户访问策略 |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | 读取跨租户访问策略的基本属性 |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | 读取跨租户访问策略的所有者 |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | 读取跨租户访问策略的 policyAppliedTo 属性 |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | 更新跨租户访问策略的基本属性 |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | 更新跨租户访问策略的所有者 |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | 更新跨租户访问策略的默认租户 |

## <a name="customer-lockbox-access-approver"></a>客户密码箱访问审批者

管理你的组织中的[客户密码箱请求](/office365/admin/manage/customer-lockbox-requests)。 他们接收客户密码箱请求的电子邮件通知，并且可以批准和拒绝来自 Microsoft 365 管理中心的请求。 他们还可以开启或关闭客户密码箱功能。 只有全局管理员可以重置分配到此角色的用户的密码。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | 管理客户密码箱的各个方面 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="desktop-analytics-administrator"></a>桌面分析管理员

此角色中的用户可以管理桌面分析服务。 此权限包括查看资产库存、创建部署计划、查看部署和运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理桌面分析的所有方面 |

## <a name="directory-readers"></a>目录读者

充当此角色的用户可以读取基本的目录信息。 应将此角色用于：

* 为特定的一组来宾用户授予读取访问权限，而不是将此权限授予所有来宾用户。
* 当“仅限管理员访问 Azure AD 门户”设置为“是”时，为特定的一组非管理员用户授予对 Azure 门户的访问权限。
* 当“Directory.Read.All”不是选项时，为服务主体授予对目录的访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | 读取管理单元上的基本属性 |
> | microsoft.directory/administrativeUnits/members/read | 读取管理单元的成员 |
> | microsoft.directory/applications/standard/read | 读取应用程序的标准属性 |
> | microsoft.directory/applications/owners/read | 读取应用程序的所有者 |
> | microsoft.directory/applications/policies/read | 读取应用程序策略 |
> | microsoft.directory/contacts/standard/read | 读取 Azure AD 中联系人的基本属性 |
> | microsoft.directory/contacts/memberOf/read | 读取 Azure AD 中所有联系人的组成员身份 |
> | microsoft.directory/contracts/standard/read | 读取合作伙伴合同上的基本属性 |
> | microsoft.directory/devices/standard/read | 读取设备上的基本属性 |
> | microsoft.directory/devices/memberOf/read | 读取设备成员身份 |
> | microsoft.directory/devices/registeredOwners/read | 读取设备的已注册所有者 |
> | microsoft.directory/devices/registeredUsers/read | 读取设备的已注册用户 |
> | microsoft.directory/directoryRoles/standard/read | 更新 Azure AD 角色中的基本属性 |
> | microsoft.directory/directoryRoles/eligibleMembers/read | 读取 Azure AD 角色中符合条件的成员 |
> | microsoft.directory/directoryRoles/members/read | 读取 Azure AD 角色的所有成员 |
> | microsoft.directory/domains/standard/read | 读取域上的基本属性 |
> | microsoft.directory/groups/standard/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的标准属性 |
> | microsoft.directory/groups/appRoleAssignments/read | 读取组的应用程序角色分配 |
> | microsoft.directory/groups/memberOf/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的 memberOf 属性 |
> | microsoft.directory/groups/members/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的成员 |
> | microsoft.directory/groups/owners/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的所有者 |
> | microsoft.directory/groups/settings/read | 读取组的设置 |
> | microsoft.directory/groupSettings/standard/read | 读取组设置的基本属性 |
> | microsoft.directory/groupSettingTemplates/standard/read | 读取组设置模板的基本属性 |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | 读取 OAuth 2.0 权限授予的基本属性 |
> | microsoft.directory/organization/standard/read | 更新组织的基本属性 |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | 读取无密码身份验证的受信任的证书颁发机构 |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | microsoft.directory/roleAssignments/standard/read | 读取角色分配的基本属性 |
> | microsoft.directory/roleDefinitions/standard/read | 读取角色定义的基本属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取服务主体角色分配 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取分配给服务主体的角色分配 |
> | microsoft.directory/servicePrincipals/standard/read | 读取服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/memberOf/read | 读取服务主体的组成员身份 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 读取服务主体的委托权限授予 |
> | microsoft.directory/servicePrincipals/owners/read | 读取服务主体的所有者 |
> | microsoft.directory/servicePrincipals/ownedObjects/read | 读取服务主体的拥有对象 |
> | microsoft.directory/servicePrincipals/policies/read | 读取服务主体的策略 |
> | microsoft.directory/subscribedSkus/standard/read | 读取订阅的基本属性 |
> | microsoft.directory/users/standard/read | 读取用户的基本属性 |
> | microsoft.directory/users/appRoleAssignments/read | 读取用户的应用程序角色分配 |
> | microsoft.directory/users/deviceForResourceAccount/read | 读取用户的资源帐户设备 |
> | microsoft.directory/users/directReports/read | 读取用户的直接下属 |
> | microsoft.directory/users/licenseDetails/read | 读取用户的许可证详细信息 |
> | microsoft.directory/users/manager/read | 读取用户的管理员 |
> | microsoft.directory/users/memberOf/read | 读取用户的组成员身份 |
> | microsoft.directory/users/oAuth2PermissionGrants/read | 读取用户的委托权限授予 |
> | microsoft.directory/users/ownedDevices/read | 读取用户拥有的设备 |
> | microsoft.directory/users/ownedObjects/read | 读取用户拥有的对象 |
> | microsoft.directory/users/photo/read | 读取用户的照片 |
> | microsoft.directory/users/registeredDevices/read | 读取用户已注册的设备 |
> | microsoft.directory/users/scopedRoleMemberOf/read | 读取用户的 Azure AD 角色成员身份，其范围限定为管理单元 |

## <a name="directory-synchronization-accounts"></a>目录同步帐户

请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft.directory/applications/appRoles/update | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/notes/update | 更新应用程序的说明 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新所有类型的应用程序的公开权限和必需权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/tag/update | 更新应用程序的标记 |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/organization/dirSync/update | 更新组织目录同步属性 |
> | microsoft.directory/policies/create | 在 Azure AD 中创建策略 |
> | microsoft.directory/policies/delete | 在 Azure AD 中删除策略 |
> | microsoft.directory/policies/standard/read | 读取策略的基本属性 |
> | microsoft.directory/policies/owners/read | 读取策略的所有者 |
> | microsoft.directory/policies/policyAppliedTo/read | 读取 policies.policyAppliedTo 属性 |
> | microsoft.directory/policies/basic/update | 更新策略的基本属性 |
> | microsoft.directory/policies/owners/update | 更新策略的所有者 |
> | microsoft.directory/policies/tenantDefault/update | 更新默认组织策略 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 管理服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 读取服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取服务主体角色分配 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取分配给服务主体的角色分配 |
> | microsoft.directory/servicePrincipals/standard/read | 读取服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/memberOf/read | 读取服务主体的组成员身份 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 读取服务主体的委托权限授予 |
> | microsoft.directory/servicePrincipals/owners/read | 读取服务主体的所有者 |
> | microsoft.directory/servicePrincipals/ownedObjects/read | 读取服务主体的拥有对象 |
> | microsoft.directory/servicePrincipals/policies/read | 读取服务主体的策略 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的受众属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/notes/update | 更新服务主体的说明 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的标记属性 |

## <a name="directory-writers"></a>目录编写人员

此角色中的用户可以读取和更新用户、组和服务主体的基本信息。 仅将此角色分配给不支持[同意框架](../develop/quickstart-register-app.md)的应用程序。 不应将它分配给任何用户。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/extensionProperties/update | 更新应用程序的扩展属性 |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以执行基于组的许可 |
> | microsoft.directory/groups/create | 创建安全组和 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/groups/basic/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups/classification/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的分类属性 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的动态成员身份规则 |
> | microsoft.directory/groups/groupType/update | 更新那些会影响安全组和 Microsoft 365 组（不包括可分配角色的组）的组类型的属性 |
> | microsoft.directory/groups/members/update | 更新安全组和 Microsoft 365 组的成员，不包括可分配角色的组 |
> | microsoft.directory/groups/onPremWriteBack/update | 更新要使用 Azure AD Connect 写回本地环境的 Azure Active Directory 组 |
> | microsoft.directory/groups/owners/update | 更新安全组和 Microsoft 365 组的所有者，不包括可分配角色的组 |
> | microsoft.directory/groups/settings/update | 更新组的设置 |
> | microsoft.directory/groups/visibility/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的可见性属性 |
> | microsoft.directory/groupSettings/create | 创建组设置 |
> | microsoft.directory/groupSettings/delete | 删除组设置 |
> | microsoft.directory/groupSettings/basic/update | 更新组设置的基本属性 |
> | microsoft.directory/oAuth2PermissionGrants/create | 创建 OAuth 2.0 权限授予 |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | 更新 OAuth 2.0 权限授予 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序预配机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重启和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 向服务主体授予直接访问组数据的权限 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/inviteGuest | 邀请来宾用户 |
> | microsoft.directory/users/reprocessLicenseAssignment | 重新处理用户的许可证分配 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 更新用户的管理员 |
> | microsoft.directory/users/photo/update | 更新用户照片 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |

## <a name="domain-name-administrator"></a>域名管理员

具有此角色的用户可以管理（读取、添加、验证、更新和删除）域名。 他们还可以读取有关用户、组和应用程序的目录信息，因为这些对象拥有域依赖项。 对于本地环境，具有此角色的用户可以配置联合身份验证的域名，以便始终在本地对关联的用户进行身份验证。 然后，这些用户可以通过单一登录，使用其本地密码登录到基于 Azure AD 的服务。 需要通过 Azure AD Connect 同步联合身份验证设置，因此用户也具有管理 Azure AD Connect 的权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | 创建和删除域，读取和更新所有属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |

## <a name="dynamics-365-administrator"></a>Dynamics 365 管理员

具有此角色的用户具有 Microsoft Dynamics 365 Online 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[使用服务管理员角色管理 Azure AD 组织](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Dynamics 365 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Dynamics 365 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="edge-administrator"></a>Edge 管理员

此角色的用户可以在 Microsoft Edge 上创建和管理 Internet Explorer 模式所需的企业站点列表。 此角色授予创建、编辑和发布站点列表的权限，此外还允许访问管理支持票证。 [了解详细信息](https://go.microsoft.com/fwlink/?linkid=2165707)

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.edge/allEntities/allProperties/allTasks | 管理 Microsoft Edge 的所有方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="exchange-administrator"></a>Exchange 管理员

具有此角色的用户具有 Microsoft Exchange Online 内的全局权限（如果该服务存在）。 另外还能够创建和管理所有 Microsoft 365 组，管理支持票证并监视服务运行状况。 有关详细信息，请参阅[关于 Microsoft 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Exchange 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Exchange 管理员”。 它是 [Exchange 管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)内的“Exchange Online 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的隐藏成员 |
> | microsoft.directory/groups.unified/create | 创建 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups.unified/delete | 删除 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups.unified/restore | 还原 Microsoft 365 组 |
> | microsoft.directory/groups.unified/basic/update | 更新 Microsoft 365 组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups.unified/members/update | 更新 Microsoft 365 组（不包括可分配角色的组）的成员 |
> | microsoft.directory/groups.unified/owners/update | 更新 Microsoft 365 组（不包括可分配角色的组）的所有者 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.exchange/allEntities/basic/allTasks | 管理 Exchange Online 的所有方面 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |

## <a name="exchange-recipient-administrator"></a>Exchange 收件人管理员

具有此角色的用户在 Exchange Online 中拥有对收件人的读取访问权限，以及对这些收件人的属性的写入权限。 在 [Exchange 收件人](/exchange/recipients/recipients)中了解详细信息。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | 创建和删除所有收件人，并在 Exchange Online 中读取和更新收件人的所有属性 |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | 在 Exchange Online 中管理所有邮件跟踪任务 |
> | microsoft.office365.exchange/migration/allProperties/allTasks | 在 Exchange Online 中管理与收件人迁移相关的所有任务 |

## <a name="external-id-user-flow-administrator"></a>外部 ID 用户流管理员

具有此角色的用户可以在 Azure 门户中创建和管理用户流（也称为“内置”策略）。 这些用户可以自定义 HTML/CSS/JavaScript 内容、更改 MFA 要求、在令牌中选择声明、管理 API 连接器，以及为 Azure AD 组织中的所有用户流配置会话设置。 但是，此角色无法查看用户数据，也无法对组织架构中包含的属性进行更改。 对 Identity Experience Framework 策略（也称为自定义策略）的更改也超出了此角色的权限范围。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | 读取和配置 Azure Active Directory B2C 中的用户属性 |

## <a name="external-id-user-flow-attribute-administrator"></a>外部 ID 用户流属性管理员

具有此角色的用户可以添加或删除适用于 Azure AD 组织中所有用户流的自定义属性。  因此，具有此角色的用户可以在最终用户架构中更改或新增元素，影响所有用户流的行为，间接导致更改可以请求最终用户提供的并最终作为声明发送到应用程序的数据。  此角色无法编辑用户流。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | 读取和配置 Azure Active Directory B2C 中的自定义策略 |

## <a name="external-identity-provider-administrator"></a>外部标识提供者管理员

此管理员可以管理 Azure AD 组织与外部标识提供者之间的联合。  用户可以使用此角色添加新的标识提供者及配置所有可用设置（例如身份验证路径、服务 ID 和分配的密钥容器）。  此用户可让 Azure AD 组织信任来自外部标识提供者的身份验证。  对最终用户体验造成的影响取决于组织类型：

* 员工和合作伙伴的 Azure AD 组织：添加联合身份验证（例如使用 Gmail）会立即影响所有尚未兑换的来宾邀请。 请参阅[将 Google 添加为 B2B 来宾用户的标识提供者](../external-identities/google-federation.md)。
* Azure Active Directory B2C 组织：在将标识提供者添加为用户流（也称为内置策略）中的一个选项之前，添加联合身份验证（例如，使用 Facebook 或另一个 Azure AD 组织来这样做）不会立即影响最终用户流。 有关示例，请参阅[将 Microsoft 帐户配置为标识提供者](../../active-directory-b2c/identity-provider-microsoft-account.md)。 若要更改用户流，需要使用受限角色“B2C 用户流管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | 读取和配置 Azure Active Directory B2C 中的标识提供者 |

## <a name="global-administrator"></a>全局管理员角色

具有此角色的用户有权访问 Azure Active Directory 以及使用 Azure Active Directory 标识的服务（例如 Microsoft 365 安全中心、Microsoft 365 合规中心、Exchange Online、SharePoint Online 和 Skype for Business Online）中的所有管理功能。 此外，全局管理员还可以[提升访问权限](../../role-based-access-control/elevate-access-global-admin.md)，以管理所有 Azure 订阅和管理组。 这允许全局管理员使用各自的 Azure AD 租户获得对所有 Azure 资源的完全访问权限。 注册 Azure AD 组织的人员将成为全局管理员。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

> [!NOTE]
> 作为最佳做法，Microsoft 建议将“全局管理员”角色分配给组织中五个以下的人员。 有关详细信息，请参阅 [Azure AD 角色的最佳做法](best-practices.md)。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | 在 Azure AD 中创建和删除访问评审、读取和更新访问评审的所有属性以及管理组的访问评审 |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 创建和管理管理单元（包括成员） |
> | microsoft.directory/applications/allProperties/allTasks | 创建和删除应用程序，读取和更新所有属性 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/users/authenticationMethods/create | 为用户创建身份验证方法 |
> | microsoft.directory/users/authenticationMethods/delete | 删除用户的身份验证方法 |
> | microsoft.directory/users/authenticationMethods/standard/read | 读取用户身份验证方法的标准属性 |
> | microsoft.directory/users/authenticationMethods/basic/update | 更新用户身份验证方法的基本属性 |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | 管理授权策略的各个方面 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 在 Microsoft Cloud App Security 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.directory/connectors/create | 创建应用程序代理连接器 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/connectorGroups/create | 创建应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/delete | 删除应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/update | 更新应用程序代理连接器组的所有属性 |
> | microsoft.directory/contacts/allProperties/allTasks | 创建和删除联系人，读取和更新所有属性 |
> | microsoft.directory/contracts/allProperties/allTasks | 创建和删除合作伙伴合同，读取和更新所有属性 |
> | microsoft.directory/deletedItems/delete | 永久删除不再可以还原的对象 |
> | microsoft.directory/deletedItems/restore | 将软删除的对象还原到原始状态 |
> | microsoft.directory/devices/allProperties/allTasks | 创建和删除设备，读取和更新所有属性 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 读取设备管理应用程序策略上的标准属性 |
> | microsoft.directory/deviceManagementPolicies/basic/update | 更新设备管理应用程序策略上的基本属性 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 读取设备注册策略上的标准属性 |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | 更新设备注册策略上的基本属性 |
> | microsoft.directory/directoryRoles/allProperties/allTasks | 创建和删除目录角色，以及读取和更新所有属性 |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | 创建和删除 Azure AD 就是模板，读取和更新所有属性 |
> | microsoft.directory/domains/allProperties/allTasks | 创建和删除域，读取和更新所有属性 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | 在 Azure AD 权利管理中创建和删除资源，读取和更新所有属性 |
> | microsoft.directory/groups/allProperties/allTasks | 创建和删除组，以及读取和更新所有属性 |
> | microsoft.directory/groupsAssignableToRoles/create | 创建可分配角色的组 |
> | microsoft.directory/groupsAssignableToRoles/delete | 删除可分配角色的组 |
> | microsoft.directory/groupsAssignableToRoles/restore | 还原可分配角色的组 |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | 更新可分配角色的组 |
> | microsoft.directory/groupSettings/allProperties/allTasks | 创建和删除组设置，读取和更新所有属性 |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | 创建和删除组设置模板，读取和更新所有属性 |
> | microsoft.directory/identityProtection/allProperties/allTasks | 在 Azure AD 标识保护中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | 创建和删除 loginTenantBranding，读取和更新所有属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/organization/allProperties/allTasks | 读取和更新组织的所有属性 |
> | microsoft.directory/policies/allProperties/allTasks | 创建和删除策略，读取和更新所有属性 |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | 管理条件访问策略的所有属性 |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/allTasks |  |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除角色分配，读取和更新所有角色分配属性 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除角色定义，读取和更新所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，读取和更新所有属性 |
> | microsoft.directory/serviceAction/activateService | 可以对服务执行“激活服务”操作 |
> | microsoft.directory/serviceAction/disableDirectoryFeature | 可以对服务执行“禁用目录功能”操作 |
> | microsoft.directory/serviceAction/enableDirectoryFeature | 可以对服务执行“启用目录功能”操作 |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | 可以执行 getAvailableExtentionProperties 服务操作 |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | 创建和删除服务主体，读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | 许可对任意应用程序的任何权限 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 向服务主体授予直接访问组数据的权限 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | 购买和管理订阅以及删除订阅 |
> | microsoft.directory/users/allProperties/allTasks | 创建和删除用户，读取和更新所有属性 |
> | microsoft.directory/permissionGrantPolicies/create | 创建权限授予策略 |
> | microsoft.directory/permissionGrantPolicies/delete | 删除权限授予策略 |
> | microsoft.directory/permissionGrantPolicies/standard/read | 读取权限授予策略的标准属性 |
> | microsoft.directory/permissionGrantPolicies/basic/update | 更新权限授予策略的基本属性 |
> | microsoft.directory/servicePrincipalCreationPolicies/create | 创建服务主体创建策略 |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | 删除服务主体创建策略 |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | 读取服务主体创建策略的标准属性 |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | 更新服务主体创建策略的基本属性 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | 读取可验证凭据卡 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | 吊销可验证凭据卡 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | 创建可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | 读取可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | 更新可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/create | 创建创建和管理可验证凭据所需的配置 |
> | microsoft.directory/verifiableCredentials/configuration/delete | 删除创建和管理可验证凭据所需的配置，并删除其所有可验证凭据 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | 读取创建和管理可验证凭据所需的配置 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | 更新创建和管理可验证凭据所需的配置 |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | 管理 Azure 高级威胁防护的各个方面 |
> | microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | 管理 Windows 365 的所有方面 |
> | microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 计费的各个方面 |
> | microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面 |
> | microsoft.edge/allEntities/allProperties/allTasks | 管理 Microsoft Edge 的所有方面 |
> | microsoft.flow/allEntities/allTasks | 管理 Microsoft Power Automate 的各个方面 |
> | microsoft.intune/allEntities/allTasks | 管理 Microsoft Intune 的各个方面 |
> | microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理桌面分析的所有方面 |
> | microsoft.office365.exchange/allEntities/basic/allTasks | 管理 Exchange Online 的所有方面 |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | 在 Microsoft 365 管理中心读取和更新内容理解的所有属性 |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | 在 Microsoft 365 管理中心阅读有关内容理解的分析报告 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | 在 Microsoft 365 管理中心读取和更新知识网络的所有属性 |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | 在 Microsoft 365 管理中心管理知识网络的主题可见性 |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | 在学习应用中管理学习资源及其所有属性。 |
> | microsoft.office365.lockbox/allEntities/allTasks | 管理客户密码箱的各个方面 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.messageCenter/securityMessages/read | 在 Microsoft 365 管理中心读取消息中心中的安全消息 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | 管理安全与合规中心的所有方面 |
> | microsoft.office365.search/content/manage | 在 Microsoft 搜索中创建和删除内容，读取和更新所有属性 |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | 在 Microsoft 365 安全与合规中心中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.sharePoint/allEntities/allTasks | 在 SharePoint 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.userCommunication/allEntities/allTasks | 读取和更新新增功能消息的可见性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.powerApps/allEntities/allTasks | 管理 Power Apps 的各个方面 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的各个方面 |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | 管理 Microsoft Defender for Endpoint 的各个方面 |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | 读取和配置 Windows 更新服务的各个方面 |

## <a name="global-reader"></a>全局读取者

充当此角色的用户可以跨 Microsoft 365 服务读取设置和管理信息，但无法执行管理操作。 全局读取者是对应于全局管理员的只读角色。 满足规划、审核或调查目的时，请分配全局读取者，而不要分配全局管理员。 将全局读取者与其他受限管理员角色（例如 Exchange 管理员）结合使用可以更轻松地完成工作，且无需分配全局管理员角色。 全局读取者可使用 Microsoft 365 管理中心、Exchange 管理中心、SharePoint 管理中心、Teams 管理中心、安全中心、合规中心、Azure AD 管理中心和设备管理管理中心。

> [!NOTE]
> 全局读取者角色目前存在一些限制 -
>
>- [OneDrive 管理中心](https://admin.onedrive.com/) - OneDrive 管理中心不支持全局读取者角色
>- [Microsoft 365 管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage) - 全局读取者无法读取集成应用。 在 Microsoft 365 管理中心左侧窗格中的“设置”下找不到“集成应用”选项卡 。
>- [Office 安全与合规中心](https://sip.protection.office.com/homepage) - 全局读取者不能读取 SCC 审核日志、进行内容搜索或查看“安全分数”。
>- [Teams 管理中心](https://admin.teams.microsoft.com) - 全局读取者无法读取“Teams 生命周期”、“分析和报告”、“IP 电话设备管理”和“应用目录”。   
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) 不支持全局读取者角色。
>- [Azure 信息保护](/azure/information-protection/what-is-information-protection) - 仅当 Azure AD 组织不在[统一标记平台](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上时，才支持全局读取者[在中心位置进行报告](/azure/information-protection/reports-aip)。
> - [SharePoint](https://admin.microsoft.com/sharepoint) - 全局读取者目前无法使用 PowerShell 来访问 SharePoint。
>
> 这些功能目前正在开发中。
>

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/read | 读取访问评审的所有属性 |
> | microsoft.directory/administrativeUnits/allProperties/read | 读取管理单元的所有属性 |
> | microsoft.directory/applications/allProperties/read | 读取所有应用程序类型上的所有属性（包括特权属性） |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | 读取身份验证方法的标准属性，不包括用户的个人身份信息 |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/cloudAppSecurity/allProperties/read | 读取 Cloud App Security 的所有属性 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/contacts/allProperties/read | 读取联系人的所有属性 |
> | microsoft.directory/devices/allProperties/read | 读取设备上的所有属性 |
> | microsoft.directory/directoryRoles/allProperties/read | 读取 Azure AD 角色的所有属性 |
> | microsoft.directory/directoryRoleTemplates/allProperties/read | 读取角色模板的所有属性 |
> | microsoft.directory/domains/allProperties/read | 读取域的所有属性 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 权利管理中的所有属性 |
> | microsoft.directory/groups/allProperties/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的所有属性（包括特权属性） |
> | microsoft.directory/groupSettings/allProperties/read | 读取组设置的所有属性 |
> | microsoft.directory/groupSettingTemplates/allProperties/read | 读取组设置模板的所有属性 |
> | microsoft.directory/identityProtection/allProperties/read | 读取 Azure AD 标识保护中的所有资源 |
> | microsoft.directory/loginOrganizationBranding/allProperties/read | 读取组织的带品牌登录页的所有属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/read | 读取 OAuth 2.0 权限授予的所有属性 |
> | microsoft.directory/organization/allProperties/read | 读取组织的所有属性 |
> | microsoft.directory/permissionGrantPolicies/standard/read | 读取权限授予策略的标准属性 |
> | microsoft.directory/policies/allProperties/read | 读取策略的所有属性 |
> | microsoft.directory/conditionalAccessPolicies/allProperties/read | 读取条件访问策略的所有属性 |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/read | 读取跨租户策略的所有属性 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 读取设备管理应用程序策略上的标准属性 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 读取设备注册策略上的标准属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/roleAssignments/allProperties/read | 读取角色分配的所有属性 |
> | microsoft.directory/roleDefinitions/allProperties/read | 读取角色定义的所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/read | 查看管理单元中的成员 |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | 可以执行 getAvailableExtentionProperties 服务操作 |
> | microsoft.directory/servicePrincipals/allProperties/read | 读取服务主体上的所有属性（包括特权属性） |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | 读取服务主体创建策略的标准属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.directory/subscribedSkus/allProperties/read | 读取产品订阅的所有属性 |
> | microsoft.directory/users/allProperties/read | 读取用户的所有属性 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | 读取可验证凭据卡 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | 读取可验证凭据协定 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | 读取创建和管理可验证凭据所需的配置 |
> | microsoft.cloudPC/allEntities/allProperties/read | 读取 Windows 365 的所有方面 |
> | microsoft.commerce.billing/allEntities/read | 读取 Office 365 计费的所有资源 |
> | microsoft.edge/allEntities/allProperties/read | 读取 Microsoft Edge 的所有方面 |
> | microsoft.office365.exchange/allEntities/standard/read | 读取 Exchange Online 的所有资源 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.messageCenter/securityMessages/read | 在 Microsoft 365 管理中心读取消息中心中的安全消息 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | 在安全与合规中心读取所有属性 |
> | microsoft.office365.securityComplianceCenter/allEntities/read | 读取 Microsoft 365 安全与合规中心中的标准属性 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/read | 读取 Windows 更新服务的各个方面 |

## <a name="groups-administrator"></a>组管理员

此角色中的用户可以创建/管理组及其设置，如命名和过期策略。 重要的是要了解，将用户分配到此角色后，他们还可以跨各种工作负荷（如 Teams、SharePoint、Yammer 和 Outlook）管理组织中的所有组。 此外，用户还能够跨各种管理门户（如 Microsoft 管理中心、Azure 门户）以及特定于工作负载的门户（如 Teams 和 SharePoint 管理中心）管理各种组设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以执行基于组的许可 |
> | microsoft.directory/groups/create | 创建安全组和 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups/delete | 删除安全组和 Microsoft 365 组，不包括可分配角色的组 |
> | microsoft.directory/groups/hiddenMembers/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的隐藏成员 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/basic/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups/classification/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的分类属性 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的动态成员身份规则 |
> | microsoft.directory/groups/groupType/update | 更新那些会影响安全组和 Microsoft 365 组（不包括可分配角色的组）的组类型的属性 |
> | microsoft.directory/groups/members/update | 更新安全组和 Microsoft 365 组的成员，不包括可分配角色的组 |
> | microsoft.directory/groups/onPremWriteBack/update | 更新要使用 Azure AD Connect 写回本地环境的 Azure Active Directory 组 |
> | microsoft.directory/groups/owners/update | 更新安全组和 Microsoft 365 组的所有者，不包括可分配角色的组 |
> | microsoft.directory/groups/settings/update | 更新组的设置 |
> | microsoft.directory/groups/visibility/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的可见性属性 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 向服务主体授予直接访问组数据的权限 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="guest-inviter"></a>来宾邀请者

此角色的用户可在“成员可以邀请”用户设置设置为“否”时管理 Azure Active Directory B2B 来宾用户邀请。 [关于 Azure AD B2B 协作](../external-identities/what-is-b2b.md)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | 邀请来宾用户 |
> | microsoft.directory/users/standard/read | 读取用户的基本属性 |
> | microsoft.directory/users/appRoleAssignments/read | 读取用户的应用程序角色分配 |
> | microsoft.directory/users/deviceForResourceAccount/read | 读取用户的资源帐户设备 |
> | microsoft.directory/users/directReports/read | 读取用户的直接下属 |
> | microsoft.directory/users/licenseDetails/read | 读取用户的许可证详细信息 |
> | microsoft.directory/users/manager/read | 读取用户的管理员 |
> | microsoft.directory/users/memberOf/read | 读取用户的组成员身份 |
> | microsoft.directory/users/oAuth2PermissionGrants/read | 读取用户的委托权限授予 |
> | microsoft.directory/users/ownedDevices/read | 读取用户拥有的设备 |
> | microsoft.directory/users/ownedObjects/read | 读取用户拥有的对象 |
> | microsoft.directory/users/photo/read | 读取用户的照片 |
> | microsoft.directory/users/registeredDevices/read | 读取用户已注册的设备 |
> | microsoft.directory/users/scopedRoleMemberOf/read | 读取用户的 Azure AD 角色成员身份，其范围限定为管理单元 |

## <a name="helpdesk-administrator"></a>支持管理员

具有此角色的用户可以更改密码、使刷新令牌失效、管理服务请求和监视服务运行状况。 使刷新令牌失效会强制用户重新登录。 支持管理员是否可以重置用户的密码和使刷新令牌失效取决于分配给用户的角色。 有关支持管理员可以为其重置密码和使刷新令牌失效的角色的列表，请参阅[密码重置权限](#password-reset-permissions)。

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予支持人员管理员的特权。 通过此路径，支持人员管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置具有访问权限。
>- 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

使用[管理单元](administrative-units.md)可向一部分用户委托管理权限，并向一部分用户应用策略。

在 [Azure 门户](https://portal.azure.com/)中，此角色以前称为“密码管理员”。 Azure AD 中的“支持管理员”名称现在与其在 Azure AD PowerShell 和 Microsoft Graph API 中的名称匹配。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="hybrid-identity-administrator"></a>混合标识管理员

充当此角色的用户可以使用云预配来创建、管理和部署从 AD 到 Azure AD 的预配配置设置，以及管理 Azure AD Connect 和联合设置。 用户还可以使用此角色对日志进行故障排除和监视。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/create | 创建所有类型的应用程序 |
> | microsoft.directory/applications/delete | 删除所有类型的应用程序 |
> | microsoft.directory/applications/appRoles/update | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/notes/update | 更新应用程序的说明 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新所有类型的应用程序的公开权限和必需权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/tag/update | 更新应用程序的标记 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | 读取和配置 Azure AD 云配置服务的所有属性。 |
> | microsoft.directory/deletedItems.applications/delete | 永久删除不再可以还原的应用程序 |
> | microsoft.directory/deletedItems.applications/restore | 将软删除的应用程序还原到原始状态 |
> | microsoft.directory/domains/allProperties/read | 读取域的所有属性 |
> | microsoft.directory/domains/federation/update | 更新域的联合属性 |
> | microsoft.directory/organization/dirSync/update | 更新组织目录同步属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 管理应用程序预配机密和凭据 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 启动、重启和暂停应用程序预配同步作业 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 创建和管理应用程序预配同步作业和架构 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的受众属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据 |
> | microsoft.directory/servicePrincipals/notes/update | 更新服务主体的说明 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的标记属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |

## <a name="identity-governance-administrator"></a>Identity Governance 管理员

具有此角色的用户可以管理 Azure AD Identity Governance 配置（包括访问包、访问评审、目录和策略），从而确保访问经过批准和评审，以及删除不再需要访问权限的来宾用户。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | 在 Azure AD 中创建和删除访问评审、读取和更新访问评审的所有属性以及管理组的访问评审 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | 在 Azure AD 权利管理中创建和删除资源，读取和更新所有属性 |
> | microsoft.directory/groups/members/update | 更新安全组和 Microsoft 365 组的成员，不包括可分配角色的组 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |

## <a name="insights-administrator"></a>Insights 管理员

充当此角色的用户可以访问 [Microsoft 365 Insights 应用程序](https://go.microsoft.com/fwlink/?linkid=2129521)中的全套管理功能。 此角色能够读取目录信息，监视服务运行状况，提交支持票证，并访问 Insights 各方面的管理员设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.insights/allEntities/allTasks | 管理 Insights 应用的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="insights-business-leader"></a>Insights 业务主管

充当此角色的用户可以通过 [Microsoft 365 Insights 应用程序](https://go.microsoft.com/fwlink/?linkid=2129521)访问一组仪表板和见解。 其中包括对所有仪表板以及提供的见解和数据探索功能的完全访问权限。 具有此角色的用户无权访问由 Insights 管理员角色负责的产品配置设置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.insights/reports/read | 在 Insights 应用中查看报表和面板 |
> | microsoft.insights/programs/update | 在 Insights 应用中部署和管理计划 |

## <a name="intune-administrator"></a>Intune 管理员

具有此角色的用户具有 Microsoft Intune Online 内的全局权限（如果该服务存在）。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。 有关详细信息，请参阅[使用 Microsoft Intune 进行基于角色的管理控制 (RBAC)](/intune/role-based-access-control)。

此角色可创建和管理所有安全组。 但是，Intune 管理员对 Office 组没有管理员权限。 这意味着管理员无法更新组织中所有 Office 组的所有者或成员身份， 但可以管理其自己创建的 Office 组，这是其最终用户权限的一部分。 因此，他们创建的任何 Office 组（非安全组）都应计入其 250 的配额。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色被标识为“Intune 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Intune 管理员”。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/devices/create | 创建设备（在 Azure AD 中注册） |
> | microsoft.directory/devices/delete | 从 Azure AD 删除设备 |
> | microsoft.directory/devices/disable | 在 Azure AD 中禁用设备 |
> | microsoft.directory/devices/enable | 在 Azure AD 中启用设备 |
> | microsoft.directory/devices/basic/update | 更新设备上的基本属性 |
> | microsoft.directory/devices/extensionAttributeSet1/update | 在设备上更新 extensionAttribute1 到 extensionAttribute5 属性 |
> | microsoft.directory/devices/extensionAttributeSet2/update | 在设备上更新 extensionAttribute6 到 extensionAttribute10 属性 |
> | microsoft.directory/devices/extensionAttributeSet3/update | 在设备上更新 extensionAttribute11 到 extensionAttribute15 属性 |
> | microsoft.directory/devices/registeredOwners/update | 更新设备的已注册所有者 |
> | microsoft.directory/devices/registeredUsers/update | 更新设备的已注册用户 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 读取设备管理应用程序策略上的标准属性 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 读取设备注册策略上的标准属性 |
> | microsoft.directory/groups/hiddenMembers/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的隐藏成员 |
> | microsoft.directory/groups.security/create | 创建安全组（不包括可分配角色的组） |
> | microsoft.directory/groups.security/delete | 删除安全组（不包括可分配角色的组） |
> | microsoft.directory/groups.security/basic/update | 更新安全组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups.security/classification/update | 更新安全组（不包括可分配角色的组）的分类属性 |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | 更新安全组的动态成员身份规则，不包括可分配角色的组 |
> | microsoft.directory/groups.security/members/update | 更新安全组（不包括可分配角色的组）的成员 |
> | microsoft.directory/groups.security/owners/update | 更新安全组（不包括可分配角色的组）的所有者 |
> | microsoft.directory/groups.security/visibility/update | 更新安全组（不包括可分配角色的组）的可见性属性 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 更新用户的管理员 |
> | microsoft.directory/users/photo/update | 更新用户照片 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | 管理 Windows 365 的所有方面 |
> | microsoft.intune/allEntities/allTasks | 管理 Microsoft Intune 的各个方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="kaizala-administrator"></a>Kaizala 管理员

具有此角色的用户拥有在 Microsoft Kaizala 中管理设置的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 此外，用户还可以访问与组织成员采用和使用 Kaizala 有关的报告，以及使用 Kaizala 操作生成的业务报告。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="knowledge-administrator"></a>知识管理员

充当此角色的用户对 Microsoft 365 管理中心内的所有知识、学习和智能功能设置拥有完全访问权限。 用户大致了解产品套件、许可详细信息，并负责控制访问权限。 知识管理员可创建和管理内容，例如主题、首字母缩写词和学习资源。 此外，这些用户可以创建内容中心、监视服务运行状况和创建服务请求。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups.security/create | 创建安全组（不包括可分配角色的组） |
> | microsoft.directory/groups.security/createAsOwner | 创建安全组（不包括可分配角色的组）。 添加“创建者”作为第一个所有者。 |
> | microsoft.directory/groups.security/delete | 删除安全组（不包括可分配角色的组） |
> | microsoft.directory/groups.security/basic/update | 更新安全组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups.security/members/update | 更新安全组（不包括可分配角色的组）的成员 |
> | microsoft.directory/groups.security/owners/update | 更新安全组（不包括可分配角色的组）的所有者 |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | 在 Microsoft 365 管理中心读取和更新内容理解的所有属性 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | 在 Microsoft 365 管理中心读取和更新知识网络的所有属性 |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | 在学习应用中管理学习资源及其所有属性。 |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | 在安全与合规中心读取敏感度标签的所有属性 |
> | microsoft.office365.sharePoint/allEntities/allTasks | 在 SharePoint 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |

## <a name="knowledge-manager"></a>知识经理

具有此角色的用户可以创建和管理内容，例如主题、首字母缩写词和学习内容。 这些用户主要负责知识的质量和结构。 此用户对主题管理操作具有完整权限，可确认主题、批准编辑或删除主题。 此角色还可以管理作为术语库管理工具的一部分的分类并创建内容中心。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups.security/create | 创建安全组（不包括可分配角色的组） |
> | microsoft.directory/groups.security/createAsOwner | 创建安全组（不包括可分配角色的组）。 添加“创建者”作为第一个所有者。 |
> | microsoft.directory/groups.security/delete | 删除安全组（不包括可分配角色的组） |
> | microsoft.directory/groups.security/basic/update | 更新安全组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups.security/members/update | 更新安全组（不包括可分配角色的组）的成员 |
> | microsoft.directory/groups.security/owners/update | 更新安全组（不包括可分配角色的组）的所有者 |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | 在 Microsoft 365 管理中心阅读有关内容理解的分析报告 |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | 在 Microsoft 365 管理中心管理知识网络的主题可见性 |
> | microsoft.office365.sharePoint/allEntities/allTasks | 在 SharePoint 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="license-administrator"></a>许可证管理员

具有此角色的用户可以添加、删除和更新用户、组（使用基于组的许可）的许可分配，以及管理用户的使用位置。 该角色不授予在使用位置之外购买或管理订阅、创建或管理组，或者创建或管理用户的权限。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以执行基于组的许可 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/reprocessLicenseAssignment | 重新处理用户的许可证分配 |
> | microsoft.directory/users/usageLocation/update | 更新用户的使用位置 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="message-center-privacy-reader"></a>消息中心隐私读取者

充当此角色的用户可以监视消息中心的所有通知，包括数据隐私消息。 消息中心隐私读取者会收到电子邮件通知（包括与数据隐私相关的通知），并可以使用邮件中心首选项取消订阅。 只有全局管理员和消息中心隐私读取者才能阅读数据隐私消息。 此外，此角色还能查看组、域和订阅。 此角色无权查看、创建或管理服务请求。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.messageCenter/securityMessages/read | 在 Microsoft 365 管理中心读取消息中心中的安全消息 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="message-center-reader"></a>消息中心读取者

具有此角色的用户可以在其组织的[消息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)监视 Exchange、Intune 和 Microsoft Teams 等已配置服务的通知和公告运行状况更新。 消息中心读者会收到包含帖子和更新的每周电子邮件摘要，并能在 Microsoft 365 内共享消息中心帖子。 在 Azure AD 中，分配到此角色的用户对 Azure AD 服务只拥有只读访问权限，如用户和组。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="modern-commerce-user"></a>现代商业用户

请勿使用。 此角色是从“商务”自动分配的，不可用于其他任何用途。 请查看下面的详细信息。

“现代商务用户”角色向特定的用户授予访问 Microsoft 365 管理中心，以及查看左侧导航栏中“主页”、“计费”和“支持”条目的权限。   这些区域中提供的内容由分配给用户的[商务特定角色](../../cost-management-billing/manage/understand-mca-roles.md)控制，这些角色可让用户管理他们为自己或者为组织购买的产品。 这可能包括各种任务，例如支付账单，或访问计费帐户和计费配置文件。

具有“现代商务用户”角色的用户通常在其他 Microsoft 购买系统中拥有管理权限，但没有可用于访问管理中心的“全局管理员”或“计费管理员”角色。

**什么情况下会分配现代商务用户角色？**

* **Microsoft 365 管理中心自助购买** – 自助购买使用户有机会通过自行购买或注册新产品来试用这些产品。 这些产品在管理中心进行管理。 进行自助购买的用户将分配到商务系统中的某个角色和现代商务用户角色，因此他们可以在管理中心管理其购买内容。 管理员可以通过 [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) 阻止自助购买（适用于 Power BI、Power Apps、Power Automate）。 有关详细信息，请参阅[自助购买常见问题解答](/microsoft-365/commerce/subscriptions/self-service-purchase-faq)。
* **从 Microsoft 商业市场购买** - 类似于自助购买，当用户从 Microsoft AppSource 或 Azure 市场购买产品或服务时，如果他们没有全局管理员角色或计费管理员角色，则会为其分配现代商业用户角色。 在某些情况下，可能会阻止用户进行此类购买。 有关详细信息，请参阅 [Microsoft 商业市场](../../marketplace/marketplace-faq-publisher-guide.yml#what-could-block-a-customer-from-completing-a-purchase-)。
* Microsoft 的推荐 - 推荐是 Microsoft 提出的正式建议，让你的组织可以优惠价格购买 Microsoft 产品和服务。 如果接受此推荐的人员在 Azure AD 中不具备全局管理员或计费管理员角色，则会为他们分配商业特定角色来购买推荐商品，同时分配现代商业用户角色以访问管理中心。 当他们访问管理中心时，只能使用其商务特定角色授权的功能。
* **商务特定角色** – 会将某些用户分配到商务特定角色。 如果用户不是全局管理员或计费管理员，将为其分配现代商业用户角色以访问管理中心。

如果取消分配了用户的现代商务用户角色，他们将失去 Microsoft 365 管理中心的访问权限。 在此情况下，他们无法管理自己或组织的任何产品。 这些任务可能包括分配许可证、更改付款方式、支付账单或其他订阅管理任务。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | 读取 Microsoft 365 计费的合作伙伴属性 |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | 管理批量许可服务中心的各个方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/basic/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="network-administrator"></a>网络管理员

充当此角色的用户可以查看 Microsoft 根据其用户位置发出的网络遥测数据提供的网络外围体系结构建议。 Microsoft 365 的网络性能依赖于精心规划的企业客户网络外围体系结构，而该体系结构通常特定于用户位置。 利用此角色，可以编辑已发现的用户位置并配置这些位置的网络参数，以促进改善遥测结果并设计建议

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | 管理网络位置的各个方面 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="office-apps-administrator"></a>Office 应用管理员

充当此角色的用户可以管理 Microsoft 365 应用的云设置。 这包括云策略管理、自助下载管理，以及查看与 Office 应用相关的报表的功能。 此外，该角色还可以在主管理中心管理支持票证和监视服务运行状况。 分配了此角色的用户还可以管理 Office 应用中新功能的通信。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.userCommunication/allEntities/allTasks | 读取和更新新增功能消息的可见性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="partner-tier1-support"></a>合作伙伴一线支持人员

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

> [!IMPORTANT]
> 此角色只能为非管理员重置密码，并使刷新令牌失效。 此角色已弃用，并且不会再在 API 中返回，所以不应使用此角色。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/notes/update | 更新应用程序的说明 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新所有类型的应用程序的公开权限和必需权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/tag/update | 更新应用程序的标记 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/groups/create | 创建安全组和 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups/delete | 删除安全组和 Microsoft 365 组，不包括可分配角色的组 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/members/update | 更新安全组和 Microsoft 365 组的成员，不包括可分配角色的组 |
> | microsoft.directory/groups/owners/update | 更新安全组和 Microsoft 365 组的所有者，不包括可分配角色的组 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/delete | 删除用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/restore | 还原已删除的用户 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 更新用户的管理员 |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.directory/users/photo/update | 更新用户照片 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="partner-tier2-support"></a>合作伙伴二线支持人员

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

> [!IMPORTANT]
> 此角色可为所有非管理员和管理员（包括全局管理员）重置密码，并使刷新令牌失效。 此角色已弃用，并且不会再在 API 中返回，所以不应使用此角色。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | 更新所有类型的应用程序上的 appRoles 属性 |
> | microsoft.directory/applications/audience/update | 更新应用程序的受众属性 |
> | microsoft.directory/applications/authentication/update | 更新所有类型的应用程序的身份验证 |
> | microsoft.directory/applications/basic/update | 更新应用程序的基本属性 |
> | microsoft.directory/applications/credentials/update | 更新应用程序凭据 |
> | microsoft.directory/applications/notes/update | 更新应用程序的说明 |
> | microsoft.directory/applications/owners/update | 更新应用程序的所有者 |
> | microsoft.directory/applications/permissions/update | 更新所有类型的应用程序的公开权限和必需权限 |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/applications/tag/update | 更新应用程序的标记 |
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/domains/allProperties/allTasks | 创建和删除域，读取和更新所有属性 |
> | microsoft.directory/groups/create | 创建安全组和 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups/delete | 删除安全组和 Microsoft 365 组，不包括可分配角色的组 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/members/update | 更新安全组和 Microsoft 365 组的成员，不包括可分配角色的组 |
> | microsoft.directory/groups/owners/update | 更新安全组和 Microsoft 365 组的所有者，不包括可分配角色的组 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/organization/basic/update | 更新组织的基本属性 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除角色分配，读取和更新所有角色分配属性 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除角色定义，读取和更新所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/subscribedSkus/standard/read | 读取订阅的基本属性 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/delete | 删除用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/restore | 还原已删除的用户 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 更新用户的管理员 |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.directory/users/photo/update | 更新用户照片 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="password-administrator"></a>密码管理员

具有此角色的用户可以管理密码，但权限受限。 此角色不会授予管理服务请求或监视服务运行状况的能力。 密码管理员是否可以重置用户的密码取决于分配给用户的角色。 有关密码管理员可以为其重置密码的角色的列表，请参阅[密码重置权限](#password-reset-permissions)。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

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
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的各个方面 |

## <a name="power-platform-administrator"></a>Power Platform 管理员

充当此角色的用户可以创建和管理环境、Power Apps、Flows、数据丢失防护策略的所有方面。 另外，具有此角色的用户可以管理支持票证并监视服务运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面 |
> | microsoft.flow/allEntities/allTasks | 管理 Microsoft Power Automate 的各个方面 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.powerApps/allEntities/allTasks | 管理 Power Apps 的各个方面 |

## <a name="printer-administrator"></a>打印机管理员

充当此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理所有打印机配置的各个方面，包括“通用打印连接器”设置。 他们可以同意所有委托的打印权限请求。 打印机管理员还有权访问打印报告。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | 在 Microsoft Print 中创建和删除打印机和连接器，读取和更新所有属性 |

## <a name="printer-technician"></a>打印机技术人员

具有此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理打印机状态。 他们还可以读取所有连接器信息。 打印机技术人员无法执行的重要任务是设置用户对打印机的权限以及共享打印机。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | 在 Microsoft Print 中读取连接器的所有属性 |
> | microsoft.azure.print/printers/allProperties/read | 在 Microsoft Print 中读取打印机的所有属性 |
> | microsoft.azure.print/printers/register | 在 Microsoft Print 中注册打印机 |
> | microsoft.azure.print/printers/unregister | 在 Microsoft Print 中取消注册打印机 |
> | microsoft.azure.print/printers/basic/update | 在 Microsoft Print 中更新打印机的基本属性 |

## <a name="privileged-authentication-administrator"></a>特权身份验证管理员

具有此角色的用户可以设置或重置任何用户（包括全局管理员）的身份验证方法（包括密码）。 特权身份验证管理员可以强制用户使用现有的非密码凭据（例如 MFA 或 FIDO）重新注册，以及撤销“在设备上记住 MFA”，让系统在所有用户下次登录时提示其执行 MFA。

[身份验证管理员](#authentication-administrator)角色有权强制对标准用户和具有某些管理角色的用户进行重新注册和多重身份验证。

[身份验证策略管理员](#authentication-policy-administrator)角色有权设置租户的身份验证方法策略，以确定每个用户可以注册和使用的方法。

| 角色 | 管理用户的身份验证方法 | 管理每用户 MFA | 管理 MFA 设置 | 管理身份验证方法策略 | 管理密码保护策略 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 身份验证管理员 | 对于某些用户为“是”（见上） | 对于某些用户为“是”（见上） | 否 | 否 | 否 |
| 特权身份验证管理员| 对于所有用户为“是” | 对于所有用户为“是” | 否 | 否 | 否 |
| 身份验证策略管理员 | 否 | 否 | 是 | 是 | 是 |

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：
>
>* 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过此路径，身份验证管理员可以假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>* Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>* 安全组和 Microsoft 365 组所有者，可以管理组成员资格。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>* Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>* 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。


> [!IMPORTANT]
> 此角色目前不能管理旧版 MFA 管理门户中的每用户 MFA。 可以使用 [Set-MsolUser](/powershell/module/msonline/set-msoluser) commandlet Azure AD Powershell 模块来实现相同的功能。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | 为用户创建身份验证方法 |
> | microsoft.directory/users/authenticationMethods/delete | 删除用户的身份验证方法 |
> | microsoft.directory/users/authenticationMethods/standard/read | 读取用户身份验证方法的标准属性 |
> | microsoft.directory/users/authenticationMethods/basic/update | 更新用户身份验证方法的基本属性 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="privileged-role-administrator"></a>特权角色管理员

具有此角色的用户可以管理 Azure Active Directory 和 Azure AD Privileged Identity Management 中的角色分配。 他们可以创建和管理可被分配给 Azure AD 角色的组。 此外，此角色允许管理 Privileged Identity Management 和管理单元的所有方面。

> [!IMPORTANT]
> 此角色授予管理所有 Azure AD 角色（包括全局管理员角色）的分配的能力。 此角色不包括 Azure AD 中的任何其他权限功能，如创建或更新用户。 但是，分配到此角色的用户可通过分配其他角色，授予自己或其他人额外的特权。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 创建和管理管理单元（包括成员） |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | 管理授权策略的各个方面 |
> | microsoft.directory/directoryRoles/allProperties/allTasks | 创建和删除目录角色，以及读取和更新所有属性 |
> | microsoft.directory/groupsAssignableToRoles/create | 创建可分配角色的组 |
> | microsoft.directory/groupsAssignableToRoles/delete | 删除可分配角色的组 |
> | microsoft.directory/groupsAssignableToRoles/restore | 还原可分配角色的组 |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | 更新可分配角色的组 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | 在 Privileged Identity Management 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除角色分配，读取和更新所有角色分配属性 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除角色定义，读取和更新所有属性 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | 许可对任意应用程序的任何权限 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="reports-reader"></a>报告读者

具有此角色的用户可在 Microsoft 365 管理中心和 Power BI 中的采用上下文包内查看使用情况报告数据和报告仪表板。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="search-administrator"></a>搜索管理员

充当此角色的用户对 Microsoft 365 管理中心内的所有 Microsoft 搜索管理功能拥有完全访问权限。 此外，这些用户可以查看消息中心、监视服务运行状况和创建服务请求。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.search/content/manage | 在 Microsoft 搜索中创建和删除内容，读取和更新所有属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="search-editor"></a>搜索编辑员

充当此角色的用户可以在 Microsoft 365 管理中心创建、管理和删除 Microsoft 搜索的内容，包括书签、问答和位置。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | 在 Microsoft 365 管理中心读取消息中心中的消息，不包括安全消息 |
> | microsoft.office365.search/content/manage | 在 Microsoft 搜索中创建和删除内容，读取和更新所有属性 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

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
[Microsoft 365 服务运行状况](/office365/enterprise/view-service-health) | 查看 Microsoft 365 服务的运行状况
[智能锁定](../authentication/howto-password-smart-lockout.md) | 定义在发生登录失败事件时实施锁定的阈值和持续时间。
[密码保护](../authentication/concept-password-ban-bad.md) | 配置自定义受禁密码列表或本地密码保护。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/applications/policies/update | 更新应用程序策略 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 权利管理中的所有属性 |
> | microsoft.directory/identityProtection/allProperties/read | 读取 Azure AD 标识保护中的所有资源 |
> | microsoft.directory/identityProtection/allProperties/update | 更新 Azure AD 标识保护中的所有资源 |
> | microsoft.directory/policies/create | 在 Azure AD 中创建策略 |
> | microsoft.directory/policies/delete | 在 Azure AD 中删除策略 |
> | microsoft.directory/policies/basic/update | 更新策略的基本属性 |
> | microsoft.directory/policies/owners/update | 更新策略的所有者 |
> | microsoft.directory/policies/tenantDefault/update | 更新默认组织策略 |
> | microsoft.directory/conditionalAccessPolicies/create | 创建条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/delete | 删除条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/standard/read | 读取条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/owners/read | 读取条件访问策略的所有者 |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | 读取条件访问策略的“适用对象”属性 |
> | microsoft.directory/conditionalAccessPolicies/basic/update | 更新条件访问策略的基本属性 |
> | microsoft.directory/conditionalAccessPolicies/owners/update | 更新条件访问策略的所有者 |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | 更新条件访问策略的默认租户 |
> | microsoft.directory/crossTenantAccessPolicies/create | 创建跨租户访问策略 |
> | microsoft.directory/crossTenantAccessPolicies/delete | 删除跨租户访问策略 |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | 读取跨租户访问策略的基本属性 |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | 读取跨租户访问策略的所有者 |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | 读取跨租户访问策略的 policyAppliedTo 属性 |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | 更新跨租户访问策略的基本属性 |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | 更新跨租户访问策略的所有者 |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | 更新跨租户访问策略的默认租户 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.protectionCenter/allEntities/standard/read | 在安全与合规中心读取所有资源的标准属性 |
> | microsoft.office365.protectionCenter/allEntities/basic/update | 在安全与合规中心更新所有资源的基本属性 |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 在攻击模拟器中创建和管理攻击有效负载 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取有关攻击模拟响应和相关训练的报告 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 在攻击模拟器中创建和管理攻击模拟模板 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="security-operator"></a>安全操作员

具有此角色的用户可以管理警报，并对安全相关功能（包括 Microsoft 365 安全中心、Azure Active Directory、标识保护、Privileged Identity Management 以及 Office 365 安全与合规中心的所有信息）拥有全局只读访问权限。 [安全与合规中心内的权限](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)提供了有关 Office 365 权限的详细信息。

| In | 有权执行的操作 |
| --- | --- |
| [Microsoft 365 安全中心](https://protection.office.com) | 安全读取者角色的所有权限<br/>查看、调查和响应安全威胁警报<br/>管理安全中心的安全设置 |
| [Azure AD 标识保护](../identity-protection/overview-identity-protection.md) | 安全读取者角色的所有权限<br>此外，还能够执行除重置密码和配置警报电子邮件以外的所有“标识保护中心”操作。 |
| [Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 安全读取者角色的所有权限 |
| [Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 安全读取者角色的所有权限<br>查看、调查和响应安全警报 |
| Windows Defender ATP 和 EDR | 安全读取者角色的所有权限<br>查看、调查和响应安全警报 |
| [Intune](/intune/role-based-access-control) | 安全读取者角色的所有权限 |
| [Cloud App Security](/cloud-app-security/manage-admins) | 安全读取者角色的所有权限 |
| [Microsoft 365 服务运行状况](/microsoft-365/enterprise/view-service-health) | 查看 Microsoft 365 服务的运行状况 |

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | 在 Microsoft Cloud App Security 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.directory/identityProtection/allProperties/allTasks | 在 Azure AD 标识保护中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | 管理 Azure 高级威胁防护的各个方面 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.intune/allEntities/read | 读取 Microsoft Intune 中的所有资源 |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | 在 Microsoft 365 安全与合规中心中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | 管理 Microsoft Defender for Endpoint 的各个方面 |

## <a name="security-reader"></a>安全读取者

具有此角色的用户对安全相关的功能具有全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、标识保护、Privileged Identity Management 中的所有信息，并且能够阅读 Azure Active Directory 登录报告和审核日志，还授予了对 Office 365 安全与合规中心的只读权限。 [安全与合规中心内的权限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务查看与安全相关的策略<br>查看安全威胁和警报<br>查看报表
标识保护中心 | 读取安全功能的所有安全报告和设置信息<br><ul><li>反垃圾邮件<li>加密<li>数据丢失防护<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 以只读方式访问 Azure AD Privileged Identity Management 中显示的所有信息：Azure AD 角色分配的策略和报告以及安全评审。<br>**无法** 注册 Azure AD Privileged Identity Management 或对其进行任何更改。 充当此角色的人员可以在 Privileged Identity Management 门户中或通过 PowerShell 为符合条件的用户激活其他角色（例如全局管理员或特权角色管理员）。
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 查看安全策略<br>查看并调查安全威胁<br>查看报表
Windows Defender ATP 和 EDR | 查看并调查警报。 在 Windows Defender ATP 中启用基于角色的访问控制后，拥有只读权限的用户（例如 Azure AD 安全读取者角色）在被分配到 Windows Defender ATP 角色之前会失去访问权限。
[Intune](/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息。 无法对 Intune 进行更改。
[Cloud App Security](/cloud-app-security/manage-admins) | 拥有读取权限，并可管理警报
[Microsoft 365 服务运行状况](/office365/enterprise/view-service-health) | 查看 Microsoft 365 服务的运行状况

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志中的所有属性，包括特权属性 |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/bitlockerKeys/key/read | 读取设备上的 BitLocker 元数据和密钥 |
> | microsoft.directory/entitlementManagement/allProperties/read | 读取 Azure AD 权利管理中的所有属性 |
> | microsoft.directory/identityProtection/allProperties/read | 读取 Azure AD 标识保护中的所有资源 |
> | microsoft.directory/policies/standard/read | 读取策略的基本属性 |
> | microsoft.directory/policies/owners/read | 读取策略的所有者 |
> | microsoft.directory/policies/policyAppliedTo/read | 读取 policies.policyAppliedTo 属性 |
> | microsoft.directory/conditionalAccessPolicies/standard/read | 读取条件访问策略 |
> | microsoft.directory/conditionalAccessPolicies/owners/read | 读取条件访问策略的所有者 |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | 读取条件访问策略的“适用对象”属性 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | 读取 Privileged Identity Management 中的所有资源 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报告上的所有属性，包括特权属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.protectionCenter/allEntities/standard/read | 在安全与合规中心读取所有资源的标准属性 |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | 读取攻击模拟器中攻击有效负载的所有属性 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 读取有关攻击模拟响应和相关训练的报告 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | 读取攻击模拟器中攻击模拟模板的所有属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="service-support-administrator"></a>服务支持管理员

具有此角色的用户可以向 Microsoft 提交有关 Azure 和 Microsoft 365 服务的支持请求，还可以在 [Azure 门户](https://portal.azure.com)和 [Microsoft 365 管理中心](https://admin.microsoft.com)查看服务仪表板和消息中心。 在[关于管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中了解详细信息。

> [!NOTE]
> 以前，此角色在 [Azure 门户](https://portal.azure.com)和 [Microsoft 365 管理中心](https://admin.microsoft.com)内称为“服务管理员”。 我们已将其重命名为“服务支持管理员”，以与 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中的现有名称保持一致。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="sharepoint-administrator"></a>SharePoint 管理员

具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并且能够创建和管理所有 Microsoft 365 组，管理支持票证并监视服务运行状况。 在[关于管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中了解详细信息。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“SharePoint 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“SharePoint 管理员”。

> [!NOTE]
> 此角色还将作用域内权限授予用于 Microsoft Intune 的 Microsoft Graph API，从而允许管理和配置与 SharePoint 和 OneDrive 资源相关的策略。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/groups.unified/create | 创建 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups.unified/delete | 删除 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups.unified/restore | 还原 Microsoft 365 组 |
> | microsoft.directory/groups.unified/basic/update | 更新 Microsoft 365 组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups.unified/members/update | 更新 Microsoft 365 组（不包括可分配角色的组）的成员 |
> | microsoft.directory/groups.unified/owners/update | 更新 Microsoft 365 组（不包括可分配角色的组）的所有者 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.sharePoint/allEntities/allTasks | 在 SharePoint 中创建和删除所有资源，读取和更新标准属性 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

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
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="teams-administrator"></a>Teams 管理员

充当此角色的用户可以通过 Microsoft Teams 和 Skype for Business 管理中心以及相应的 PowerShell 模块来管理 Microsoft Teams 工作负荷的所有方面。 这包括（但不限于）与电话、消息、会议和 Teams 自身相关的所有管理工具。 另外，利用此角色，还可以创建和管理所有 Microsoft 365 组，管理支持票证并监视服务运行状况。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.directory/groups/hiddenMembers/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的隐藏成员 |
> | microsoft.directory/groups.unified/create | 创建 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups.unified/delete | 删除 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups.unified/restore | 还原 Microsoft 365 组 |
> | microsoft.directory/groups.unified/basic/update | 更新 Microsoft 365 组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups.unified/members/update | 更新 Microsoft 365 组（不包括可分配角色的组）的成员 |
> | microsoft.directory/groups.unified/owners/update | 更新 Microsoft 365 组（不包括可分配角色的组）的所有者 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 向服务主体授予直接访问组数据的权限 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.teams/allEntities/allProperties/allTasks | 管理 Teams 中的所有资源 |

## <a name="teams-communications-administrator"></a>Teams 通信管理员

充当此角色的用户可以管理 Microsoft Teams 工作负荷的语音与电话相关方面。 这包括用于分配电话号码的管理工具、语音和会议策略，以及通话分析工具集的完全访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | 阅读 Office 365 使用情况报告 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.teams/callQuality/allProperties/read | 读取通话质量仪表板 (CQD) 中的所有数据 |
> | microsoft.teams/meetings/allProperties/allTasks | 管理会议，包括会议策略、配置和会议网桥 |
> | microsoft.teams/voice/allProperties/allTasks | 管理语音，包括呼叫策略以及电话号码清单和分配 |

## <a name="teams-communications-support-engineer"></a>Teams 通信支持工程师

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户可以查看所有参与方的完整通话记录信息。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.teams/callQuality/allProperties/read | 读取通话质量仪表板 (CQD) 中的所有数据 |

## <a name="teams-communications-support-specialist"></a>Teams 通信支持专家

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户只能查看他们所查找的特定用户的通话中的用户详细信息。 此角色无权查看、创建或管理支持票证。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 读取授权策略的标准属性 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.teams/callQuality/standard/read | 读取通话质量仪表板 (CQD) 中的基本数据 |

## <a name="teams-devices-administrator"></a>Teams 设备管理员

具有此角色的用户可以在 Teams 管理中心管理 [Teams 认证的设备](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)。 此角色允许同时查看所有设备，并能够搜索和筛选设备。 用户可以检查每个设备的详细信息，包括设备的登录帐户、品牌和型号。 用户可以更改设备上的设置并更新软件版本。 此角色不会授权检查 Teams 活动和设备的通话质量。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |
> | microsoft.teams/devices/standard/read | 管理经 Teams 认证的设备的各个方面，包括配置策略 |

## <a name="usage-summary-reports-reader"></a>使用情况摘要报表读取者

具有此角色的用户可以访问 Microsoft 365 管理中心内使用情况和生产力分数的租户级聚合数据和相关见解，但无法访问任何用户级详细信息或见解。 在 Microsoft 365 管理中心的这两个报表中，我们区分了租户级聚合数据和用户级详细信息。 此角色为客户和法律团队共同请求的单个用户身份数据提供了一层额外的保护。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | 在 Microsoft 365 管理中心中读取所有网络性能属性 |
> | microsoft.office365.usageReports/allEntities/standard/read | 读取租户级聚合的 Office 365 使用情况报表 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心中读取所有资源的基本属性 |

## <a name="user-administrator"></a>用户管理员

具有此角色的用户可以创建用户并管理用户的所有方面（存在一些限制，具体请参阅下表），还可以更新密码过期策略。 此外，具有此角色的用户可以创建和管理所有组。 此角色还能够创建和管理用户视图、管理支持票证和监视服务运行状况。 用户管理员无权管理充当大部分管理员角色的用户的某些用户属性。 具有此角色的管理员无权管理 MFA 或管理共享邮箱。 下表列出了不存在这种限制的角色。

| 用户管理员权限 | 说明 |
| --- | --- |
| 创建用户和组<br/>创建和管理用户视图<br/>管理 Office 支持票证<br/>更新密码过期策略 |  |
| 管理许可证<br/>管理除用户主体名称之外的所有用户属性 | 适用于所有用户，包括所有管理员 |
| 删除和还原<br/>禁用和启用<br/>管理包括用户主体名称在内的所有用户属性<br/>更新 (FIDO) 设备密钥 | 适用于非管理员用户或具有以下任一管理员角色的用户：<ul><li>支持管理员</li><li>无角色的用户</li><li>用户管理员</li></ul> |
| 使刷新令牌失效<br/>重置密码 | 有关用户管理员可以为其重置密码和使刷新令牌失效的角色的列表，请参阅[密码重置权限](#password-reset-permissions)。 |

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
> | microsoft.directory/contacts/create | 创建联系人 |
> | microsoft.directory/contacts/delete | 删除联系人 |
> | microsoft.directory/contacts/basic/update | 更新联系人的基本属性 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | 在 Azure AD 权利管理中创建和删除资源，读取和更新所有属性 |
> | microsoft.directory/groups/assignLicense | 将产品许可证分配给组以执行基于组的许可 |
> | microsoft.directory/groups/create | 创建安全组和 Microsoft 365 组（不包括可分配角色的组） |
> | microsoft.directory/groups/delete | 删除安全组和 Microsoft 365 组，不包括可分配角色的组 |
> | microsoft.directory/groups/hiddenMembers/read | 读取安全组和 Microsoft 365 组（包括可分配角色的组）的隐藏成员 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 重新处理基于组的许可的许可证分配 |
> | microsoft.directory/groups/restore | 还原已删除的组 |
> | microsoft.directory/groups/basic/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的基本属性 |
> | microsoft.directory/groups/classification/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的分类属性 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的动态成员身份规则 |
> | microsoft.directory/groups/groupType/update | 更新那些会影响安全组和 Microsoft 365 组（不包括可分配角色的组）的组类型的属性 |
> | microsoft.directory/groups/members/update | 更新安全组和 Microsoft 365 组的成员，不包括可分配角色的组 |
> | microsoft.directory/groups/onPremWriteBack/update | 更新要使用 Azure AD Connect 写回本地环境的 Azure Active Directory 组 |
> | microsoft.directory/groups/owners/update | 更新安全组和 Microsoft 365 组的所有者，不包括可分配角色的组 |
> | microsoft.directory/groups/settings/update | 更新组的设置 |
> | microsoft.directory/groups/visibility/update | 更新安全组和 Microsoft 365 组（不包括可分配角色的组）的可见性属性 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 OAuth 2.0 权限授予，读取和更新所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/users/assignLicense | 管理用户许可证 |
> | microsoft.directory/users/create | 添加用户 |
> | microsoft.directory/users/delete | 删除用户 |
> | microsoft.directory/users/disable | 禁用用户 |
> | microsoft.directory/users/enable | 启用用户 |
> | microsoft.directory/users/inviteGuest | 邀请来宾用户 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 通过让用户刷新令牌失效来强制执行注销 |
> | microsoft.directory/users/reprocessLicenseAssignment | 重新处理用户的许可证分配 |
> | microsoft.directory/users/restore | 还原已删除的用户 |
> | microsoft.directory/users/basic/update | 更新用户的基本属性 |
> | microsoft.directory/users/manager/update | 更新用户的管理员 |
> | microsoft.directory/users/password/update | 重置所有用户的密码 |
> | microsoft.directory/users/photo/update | 更新用户照片 |
> | microsoft.directory/users/userPrincipalName/update | 更新用户的用户主体名称 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况 |
> | microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | 在 Microsoft 365 管理中心读取和配置服务运行状况 |
> | microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Microsoft 365 服务请求 |
> | microsoft.office365.webPortal/allEntities/standard/read | 在 Microsoft 365 管理中心读取所有资源的基本属性 |

## <a name="windows-update-deployment-administrator"></a>Windows 更新部署管理员

此角色中的用户可以通过适用于企业的 Windows 更新部署服务来创建和管理 Windows 更新部署的所有方面。 利用该部署服务，用户可以定义部署更新的时间和方式的相关设置，并指定向其租户中的设备组提供哪些更新。 该服务还允许用户监视更新进度。

> [!div class="mx-tableFixed"]
> | 操作 | 说明 |
> | --- | --- |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | 读取和配置 Windows 更新服务的各个方面 |

## <a name="how-to-understand-role-permissions"></a>如何了解角色权限

权限的架构不严格遵循 Microsoft Graph 的 REST 格式：

`<namespace>/<entity>/<propertySet>/<action>`

例如：

`microsoft.directory/applications/credentials/update`

| 权限元素 | 说明 |
| --- | --- |
| 命名空间 | 公开任务的产品或服务，前面附加了 `microsoft`。 例如，Azure AD 中的所有任务都使用 `microsoft.directory` 命名空间。 |
| 实体 | 服务在 Microsoft Graph 中公开的逻辑功能或组件。 例如，Azure AD 公开“用户”和“组”，OneNote 公开“笔记”，Exchange 公开“邮箱”和“日历”。 有一个特殊的 `allEntities` 关键字用于指定命名空间中的所有实体。 此关键字通常在授予对整个产品的访问权限的角色中使用。 |
| propertySet | 要为其授予访问权限的实体的特定属性或方面。 例如，`microsoft.directory/applications/authentication/read` 授予读取 Azure AD 中应用程序对象上的回复 URL、注销 URL 和隐式流属性的能力。<ul><li>`allProperties` 指定实体的所有属性（包括特权属性）。</li><li>`standard` 指定通用属性，但排除与 `read` 操作相关的特权属性。 例如，`microsoft.directory/user/standard/read` 包括读取标准属性（例如，公用电话号码和电子邮件地址，但不包括用于多重身份验证的私人备用电话号码或电子邮件地址）。</li><li>`basic` 指定通用属性，但排除与 `update` 操作相关的特权属性。 你可以读取的属性集可能不同于你可以更新的属性集。 这正是我们提供 `standard` 和 `basic` 关键字来反映这一点的原因。</li></ul> |
| action | 被授予的操作，最常见的是创建、读取、更新或删除 (CRUD)。 有一个特殊的 `allTasks` 关键字用于指定所有上述功能（创建、读取、更新和删除）。 |

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
设备联接 | 已放弃 | [已弃用角色的文档](#deprecated-roles)
设备管理器 | 已放弃 | [已弃用角色的文档](#deprecated-roles)
设备用户 | 已放弃 | [已弃用角色的文档](#deprecated-roles)
目录同步帐户 | 未显示，因为不应使用它 | [目录同步帐户文档](#directory-synchronization-accounts)
来宾用户 | 未显示，因为无法使用它 | NA
合作伙伴层 1 支持 | 未显示，因为不应使用它 | [合作伙伴一线支持人员文档](#partner-tier1-support)
合作伙伴层 2 支持 | 未显示，因为不应使用它 | [合作伙伴二线支持人员文档](#partner-tier2-support)
受限来宾用户 | 未显示，因为无法使用它 | NA
用户 | 未显示，因为无法使用它 | 不可用
工作区设备联接 | 已放弃 | [已弃用角色的文档](#deprecated-roles)

## <a name="password-reset-permissions"></a>密码重置权限

列标题表示可以重置密码的角色。 表行包含其密码可以被重置的角色。

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
用户<br/>（无管理员角色） | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
用户<br/>（无管理员角色，但为可分配角色的组的成员） | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
用户管理员 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
使用情况摘要报表读取者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* 全局管理员无法删除其自己的全局管理员分配。 这是为了防止组织无全局管理员的情况。

## <a name="next-steps"></a>后续步骤

- [向组分配 Azure AD 角色](groups-assign-role.md)
- [了解不同角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [将用户分配为 Azure 订阅的管理员](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
