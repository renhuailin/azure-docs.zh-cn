---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 5/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a822ca89128b85c24c6972c2b4fecd94a607f138
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006250"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`) 并粘贴到 ![RSS 源阅读器图标](./media/whats-new/feed-icon-16x16.png) 订阅源阅读器中，获取有关何时重新访问此页以获得更新的通知。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月之前的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="may-2021"></a>2021 年 5 月

### <a name="public-preview---azure-ad-verifiable-credentials"></a>公共预览版 - Azure AD 可验证凭据

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 用户身份验证
 
Azure AD 客户现在可以轻松地设计和颁发可验证凭据，以代表雇佣、教育或任何其他声明的证明，同时尊重隐私。 对任何人和任何企业的任何信息进行数字验证。 [了解详细信息](../verifiable-credentials/index.yml)。

---

### <a name="public-preview---device-code-flow-now-includes-an-app-verification-prompt"></a>公共预览版 - 设备代码流现在包括应用验证提示

**类型：** 新功能  
服务类别：用户身份验证  
产品功能：身份验证（登录）
 
作为一项安全改进，[设备代码流](../develop/v2-oauth2-device-code.md)已更新，包括了其他提示，用于验证用户是否正在登录他们所需的应用。 计划于 6 月开始推出，预计于 6 月 30 日完成。

为了帮助防止攻击者诱使用户登录到恶意应用程序的钓鱼攻击，会添加以下提示：“正在尝试登录到 [应用程序显示名称] 吗?”。 使用设备代码流登录时，所有用户都将看到此提示。 作为安全措施，不能删除或绕过它。 [了解详细信息](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)。

---

### <a name="public-preview---build-and-test-expressions-for-user-provisioning"></a>公共预览版 - 供用户预配的生成和测试表达式

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
表达式生成器允许创建和测试表达式，而无需等待完全同步周期。 [了解详细信息](../app-provisioning/functions-for-customizing-application-data.md)。 

---

### <a name="public-preview---enhanced-audit-logs-for-conditional-access-policy-changes"></a>公共预览版 - 增强了条件访问策略更改的审核日志

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
管理条件访问的一个重要方面是了解策略随时间推移的更改。 策略更改可能导致最终用户中断，因此，保留更改日志并允许管理员还原到以前的策略版本非常重要。 

除了显示策略更改者和更改时间，审核日志现在还会包含修改后的属性值，以便管理员可以更好地了解更改了哪些分配、条件或控制。 如果要还原到以前的策略版本，可以复制旧版本的 JSON 表示形式，并使用条件访问 API 将策略快速更改回以前的状态。 [了解详细信息](../conditional-access/concept-conditional-access-policies.md)。

---

### <a name="public-preview---sign-in-logs-include-authentication-methods-used-during-sign-in"></a>公共预览 - 登录日志包含登录时使用的身份验证方法

**类型：** 新功能  
**服务类别：** MFA  
**产品功能：** 监视和报告
 

管理员现在可以看到用户登录所用的顺序步骤，包括登录期间使用的身份验证方法。 

若要访问这些详细信息，请转到“Azure AD 登录日志”，选择“登录”，然后导航到“身份验证方法”的“详细信息”选项卡。此处提供了一些信息，如使用的方法、有关方法的详细信息（例如电话号码、电话名称）、满足的身份验证要求以及结果详细信息。 [了解详细信息](../reports-monitoring/concept-sign-ins.md)。

---

### <a name="public-preview---pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>公共预览版 - PIM 增加了对 Azure 存储角色中的 ABAC 条件的支持

**类型：** 新功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
除了针对特定 Azure RBAC 角色的基于属性的访问控制的公共预览版外，还可以在 Privileged Identity Management 中为符合条件的分配添加 ABAC 条件。 [了解详细信息](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim)。

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>正式发布 - B2C 中的条件访问和标识保护报告

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

B2C 现在支持对企业到消费者 (B2C) 应用和用户的条件访问和标识保护。 这样，客户就可以使用精细的基于风险和位置的访问控制来保护用户。 使用这些功能，客户现在可以查看信号并创建策略，为客户提供更高的安全性和访问权限。 [了解详细信息](../../active-directory-b2c/conditional-access-identity-protection-overview.md)。

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>正式发布 - 下一代用户流中现已提供 KMSI 和密码重置

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

下一代 B2C 用户流现在支持[使我保持登录状态 (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 和密码重置。 通过使用 KMSI 功能，客户可使用永久性 Cookie 为 Web 和本机应用程序用户延长会话生存期。 即使用户关闭并重新打开浏览器，此功能也会使会话保持活动状态，并在用户注销时撤销。密码重置允许用户从“忘记密码”链接重置密码。 这也允许管理员在 Azure AD B2C 目录中强制重置用户的过期密码。 [了解详细信息](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow)。
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>正式发布 - 新的 Log Analytics 工作簿应用程序角色分配活动

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
添加了一个新的工作簿，用于呈现应用程序角色分配更改的审核事件。 [了解详细信息](../governance/entitlement-management-logs-and-reporting.md)。

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>正式发布 - 下一代 Azure AD B2C 用户流 

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
新的简化用户流体验提供与预览功能的功能奇偶一致性，并且包含所有新功能。 用户将能够在同一用户流中启用新功能，从而减少了在每个新功能发布时创建多个版本的需要。 新的用户友好型 UX 还可以简化用户流的选择和创建。 有关使用此功能的指导，请参阅[在 Azure AD B2C 中创建用户流](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow)。 [了解详细信息](../../active-directory-b2c/user-flow-versions.md)。

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>正式发布 - 针对登录风险的 Azure Active Directory 威胁情报

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 
这项新的检测作为一个即席方法，使安全团队能够在检测到攻击时，将其会话风险提升为高风险，并将关联的登录标记为“有风险”。 此检测遵循现有 Azure Active Directory 威胁智能进行用户风险检测，以全面了解 Microsoft 安全团队检测到的各种攻击。 [了解详细信息](../identity-protection/concept-identity-protection-risks.md#user-risk)。
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>正式发布 - 条件访问已命名位置改进

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
现已正式发布命名位置的 IPv6 支持。 更新包括：

- 添加了定义 IPv6 地址范围的功能
- 将命名位置限制从 90 增加到 195
- 将每个命名位置的 IP 范围限制从 1200 增加到 2000
- 添加了对命名位置进行搜索和排序并按位置类型和信任类型进行筛选的功能
- 在登录日志中添加了登录所属的命名位置
 
此外，为了防止管理员定义有问题的命名位置，还添加了额外检查，以减少配置错误的几率。 [了解详细信息](../conditional-access/location-condition.md)。

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>正式发布 - Azure AD 中的受限的来宾访问权限

**类型：** 新功能  
**服务类别：** 用户管理  
**产品功能：** 目录
 
我们已为来宾用户更新了目录级别权限。 这些权限允许管理员对外部来宾用户访问权限进行其他限制和控制。

管理员现在可以为外部来宾对用户和组的配置文件以及成员身份信息的访问添加其他限制。 客户还可以通过隐藏组成员身份，大规模管理外部用户访问权限，包括限制来宾用户查看其所在组的成员身份。 有关详细信息，请参阅[在 Azure Active Directory 中限制来宾访问权限](../enterprise-users/users-restrict-guest-permissions.md)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Azure AD 应用程序库推出了新联合应用 - 2021 年 5 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella 用户管理](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Azure AD 应用程序库推出了新联合应用 - 2021 年 5 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2021 年 5 月，我们在应用库中添加了以下 29 个支持联合的新应用程序

[InviteDesk](https://app.invitedesk.com/login)、[Webrecruit ATS](https://id-test.webrecruit.co.uk/)、[Workshop](../saas-apps/workshop-tutorial.md)、[Gravity Sketch](https://landingpad.me/)、[JustLogin](../saas-apps/justlogin-tutorial.md)、[Custellence](https://custellence.com/sso/)、[WEVO](https://hello.wevoconversion.com/login)、[AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html)、[Filemail](https://www.filemail.com/login)、[Ardoq](../saas-apps/ardoq-tutorial.md)、[Leadfamly](../saas-apps/leadfamly-tutorial.md)、[Documo](../saas-apps/documo-tutorial.md)、[Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md)、[Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md)、[BrightHire](https://app.brighthire.ai/)、[Rescana](../saas-apps/rescana-tutorial.md)、[Bluewhale](https://cloud.bluewhale.dk/)、[AlacrityLaw](../saas-apps/alacritylaw-tutorial.md)、[Equisolve](../saas-apps/equisolve-tutorial.md)、[Zip](../saas-apps/zip-tutorial.md)、[Cognician](../saas-apps/cognician-tutorial.md)、[Acra](https://www.acrasuite.com/)、[VaultMe](https://app.vaultme.com/#/signIn)、[TAP App Security](../saas-apps/tap-app-security-tutorial.md)、[Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/)、[Clebex](../saas-apps/clebex-tutorial.md)、[Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md)、[Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md)、[LogMeIn](../saas-apps/logmein-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>改进了适用于 Android 和 iOS 的条件访问消息

**类型：** 已更改的功能  
**服务类别：** 设备注册和管理  
**产品功能：** 最终用户体验
 

我们更新了用户在移动设备管理中注册其设备之前，当用户被阻止访问公司资源时，向用户展示的“条件访问”屏幕上的措辞。 这些改进适用于 Android 和 iOS/iPadOS 平台。 已更改以下内容：

- “帮助我们保证设备安全”更改为“设置设备以获取访问权限”
- “登录成功，但管理员要求设备必须由 Microsoft 进行管理才能访问此资源。” 更改为“[组织名称] 要求你保护此设备，然后才能访问 [组织名称] 电子邮件、文件和数据。” 
- “立即注册”更改为“继续”

请注意，“[注册 Android 企业版设备](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb)”中的信息已过时。

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Azure 信息保护服务将开始要求同意

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
Azure 信息保护服务使用户登录到对文档进行加密的租户中，以便提供对文档的访问。  从 6 月开始，当跨组织执行此访问时，Azure AD 将开始提示用户同意。  这可确保用户了解拥有该文档的组织将在访问文档时收集有关该用户的某些信息。 [了解详细信息](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants)。
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>预配日志架构更改影响图形 API 和 Azure Monitor 集成

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 监视和报告
 

属性“Action”和“statusInfo”将更改为“provisioningAction”和“provisoiningStatusInfo”。 请使用[预配日志图形 API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) 或 [Azure Monitor 集成](../app-provisioning/application-provisioning-log-analytics.md)来更新已创建的任何脚本。 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>用于管理 Azure 资源和 Azure AD 角色的 PIM 的新 ARM API

**类型：** 已更改的功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
已发布适用于 Azure 资源角色和 Azure AD 角色的 PIM API 的更新版本。 Azure 资源角色的 PIM API 现已在 ARM API 标准下发布，该标准与常规 Azure 角色分配的角色管理 API 保持一致。 另一方面，Azure AD 角色的 PIM API 也会在图形 API 下发布，并与 unifiedRoleManagement API 保持一致。 此更改的一些优点包括：

- 将 PIM API 与 ARM 和 Graph 中的对象保持一致，以进行角色管理。减少了调用 PIM 以载入新的 Azure 资源的需求。 
- 所有 Azure 资源自动适用于新的 PIM API。
- 减少为角色定义调用 PIM 或保留 PIM 资源 ID 的需求
- 支持在 PIM 中为 Azure AD 和 Azure 资源角色提供仅限应用的 API 权限

位于 /privilegedaccess 下的 PIM API 的以前版本将继续运行，但我们建议你迁移到此新 API。 [了解详细信息](../privileged-identity-management/pim-apis.md)。
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Azure AD 权利管理中的角色修订

**类型：** 已更改的功能  
服务类别：角色  
**产品功能：** 权利管理
 
最近引入了新角色 Identity Governance 管理员。此角色将取代“用户管理员”角色，用于在 Azure AD 权利管理中管理目录和访问包。 如果你已为“用户管理员”角色分配管理员，或让他们激活此角色以在 Azure AD 授权管理中管理访问包，请改用 Identity Governance 管理员角色。 “用户管理员”角色将不再提供对目录或访问包的管理权限。 [了解详细信息](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)。

---

## <a name="april-2021"></a>2021 年 4 月

### <a name="bug-fixed---azure-ad-will-no-longer-double-encode-the-state-parameter-in-responses"></a>Bug 修复 - Azure AD 将不再对响应中的状态参数进行双编码

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
Azure AD 已标识、测试和发布了对客户端应用程序的 `/authorize` 响应中的 bug 的修复。  将响应发送回客户端时，Azure AD 错误地将 `state` 参数 URL 编码两次。  这可能会导致客户端应用程序由于状态参数不匹配而拒绝请求。 [了解详细信息](../develop/reference-breaking-changes.md#bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice)。 

---

### <a name="users-can-only-create-security-and-microsoft-365-groups-in-azure-portal-being-deprecated"></a>用户仅可在 Azure 门户中创建安全性和 Microsoft 365 组被弃用

**类型：** 更改计划  
**服务类别：** 组管理  
**产品功能：** 目录
 
用户将不再限于仅可在 Azure 门户中创建安全性和 Microsoft 365 安全组。 新设置将允许用户在 Azure 门户、PowerShell 和 API 中创建安全组。 用户需要验证和更新新设置。 [了解详细信息](../enterprise-users/groups-self-service-management.md)。

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>公共预览版 - 使用电子邮件一次性密码帐户在 AAD 中执行外部标识自助注册

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
外部用户现在可以使用电子邮件一次性密码帐户注册或登录到 Azure AD 第一方应用和业务线应用。 [了解详细信息](../external-identities/one-time-passcode.md)。

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>正式发布 - 外部标识自助注册

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
正式版现已提供外部用户的自助注册。 借助这项新功能，外部用户现在可以自助注册应用程序。 

你可以为这些外部用户创建自定义体验，包括在注册过程中收集有关用户的信息，并允许 Facebook 和 Google 等外部标识提供商。 还可以与第三方云提供商集成，实现各种功能，例如用户身份验证或审批。 [了解详细信息](../external-identities/self-service-sign-up-overview.md)。
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>正式发布 - 使用内置策略的 Azure AD B2C 电话注册和登录

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
使用内置策略的 B2C 电话注册和登录可让组织的 IT 管理员和开发人员允许其最终用户在用户流中使用电话号码进行登录和注册。 借助此功能，可以自定义免责声明链接（如隐私策略和使用条款）并显示在页面上，然后最终用户才能继续通过短信接收一次性密码。 [了解详细信息](../../active-directory-b2c/phone-authentication-user-flows.md)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---april-2021"></a>Azure AD 应用程序库推出了新联合应用 - 2021 年 4 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2021 年 4 月，我们在应用库中添加了以下 31 个支持联合的新应用程序

[Zii Travel Azure AD Connect](http://ziitravel.com/)、[Cerby](../saas-apps/cerby-tutorial.md)、[Selflessly](https://app.selflessly.io/sign-in)、[Apollo CX](https://apollo.cxlabs.de/sso/aad)、[Pedagoo](https://account.pedagoo.com/)、[Measureup](https://account.measureup.com/)、[Wistec Education](https://wisteceducation.fi/login/index.php)、[ProcessUnity](../saas-apps/processunity-tutorial.md)、[Cisco Intersight](../saas-apps/cisco-intersight-tutorial.md)、[Codility](../saas-apps/codility-tutorial.md)、[H5mag](https://account.h5mag.com/auth/request-access/ms365)、[Check Point Identity Awareness](../saas-apps/check-point-identity-awareness-tutorial.md)、[Jarvis](https://jarvis.live/login)、[desknet's NEO](../saas-apps/desknets-neo-tutorial.md)、[SDS & Chemical Information Management](../saas-apps/sds-chemical-information-management-tutorial.md)、[Wúru App](../saas-apps/wuru-app-tutorial.md)、[Holmes](../saas-apps/holmes-tutorial.md)、[Tide 多租户](https://gallery.tideapp.co.uk/)、[Telenor](https://admin.smartansatt.telenor.no/)、[Yooz US](https://us1.getyooz.com/?kc_idp_hint=microsoft)、[Mooncamp](https://app.mooncamp.com/#/login)、[inwise SSO](https://app.inwise.com/defaultsso.aspx)、[Ecolab 数字化解决方案](https://ecolabb2c.b2clogin.com/account.ecolab.com/oauth2/v2.0/authorize?p=B2C_1A_Connect_OIDC_SignIn&client_id=01281626-dbed-4405-a430-66457825d361&nonce=defaultNonce&redirect_uri=https://jwt.ms&scope=openid&response_type=id_token&prompt=login)、[Taguchi 数字化营销系统](https://login.taguchi.com.au/)、[XpressDox EU Cloud](https://test.xpressdox.com/Authentication/Login.aspx)、[EZSSH](https://docs.keytos.io/getting-started/registering-a-new-tenant/registering_app_in_tenant/)、[EZSSH 客户端](https://portal.ezssh.io/signup)、[Verto 365](https://www.vertocloud.com/Login/)、[KPN Grip](https://www.grip-on-it.com/)、[AddressLook](https://portal.bbsonlineservices.net/Manage/AddressLook)、[Cornerstone 单一登录](../saas-apps/cornerstone-ondemand-tutorial.md)

也可访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---april-2021"></a>Azure AD 应用程序库中的新预配连接器 - 2021 年 4 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Bentley - 自动用户预配](../saas-apps/bentley-automatic-user-provisioning-tutorial.md)
- [Boxcryptor](../saas-apps/boxcryptor-provisioning-tutorial.md)
- [BrowserStack 单一登录](../saas-apps/browserstack-single-sign-on-provisioning-tutorial.md)
- [Eletive](../saas-apps/eletive-provisioning-tutorial.md)
- [Jostle](../saas-apps/jostle-provisioning-tutorial.md)
- [Olfeo SAAS](../saas-apps/olfeo-saas-provisioning-tutorial.md)
- [Proware](../saas-apps/proware-provisioning-tutorial.md)
- [Segment](../saas-apps/segment-provisioning-tutorial.md)

有关如何通过自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---

### <a name="introducing-new-versions-of-page-layouts-for-b2c"></a>介绍 B2C 页面布局的新版本

**类型：** 已更改的功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
通过引入 jQuery 和 Handlebars JS 的新版本，Azure AD B2C 上的 B2C 方案的[页面布局](../../active-directory-b2c/page-layout.md)已更新，以降低安全风险。
 
---

### <a name="updates-to-sign-in-diagnostic"></a>登录诊断更新

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
登录诊断工具的方案覆盖范围已增加。 

通过此更新，以下与事件相关的方案现在将包含在登录诊断结果中： 
- 企业应用程序配置问题事件。
- 企业应用程序服务提供商（应用程序端）事件。
- 错误的凭据事件。 

这些结果将显示有关事件以及解决这些问题要采取的操作的上下文和相关详细信息。 此外，对于没有深层上下文诊断的情况，登录诊断将呈现有关错误事件的更具描述性的内容。

有关详细信息，请参阅[什么是 Azure AD 中的登录诊断？](../reports-monitoring/overview-sign-in-diagnostics.md)

---
### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Azure AD Connect 云同步正式版刷新 
**类型：** 已更改的功能  
服务类别：Azure AD Connect 云同步 产品功能：目录

Azure AD Connect 云同步现在具有更新的代理（版本号 - 1.1.359）。 有关代理更新（包括 bug 修复）的详细信息，请查看[版本历史记录](../cloud-sync/reference-version-history.md)。 借助更新的代理，云同步客户可以使用 GMSA cmdlet 在粒度级别设置和重置其 gMSA 权限。 此外，我们已使用组范围筛选将同步成员的限制从 1499 更改为 50,000（5 万）个成员。 

查看云同步的最新提供的[表达式生成器](../cloud-sync/how-to-expression-builder.md#deploy-the-expression)，这有助于在使用属性映射将属性值从 AD 转换到 Azure AD 时生成复杂表达式和简单表达式。

---

## <a name="march-2021"></a>2021 年 3 月

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>指导你在你的环境中启用 TLS 1.2 支持，为即将弃用 Azure AD TLS 1.0/1.1 做好准备

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 标准

从 2021 年 6 月 30 日开始，Azure Active Directory 将在 Azure Active Directory 全球区域弃用以下协议：


- TLS 1.0
- TLS 1.1
- 3DES 密码套件 (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

受影响的环境包括：

- Azure 商业云
- Office 365 GCC 和 WW

有关详细信息，请参阅[在环境中启用对 TLS 1.2 的支持，以便弃用 Azure AD TLS 1.1 和 1.0](/troubleshoot/azure/active-directory/enable-support-tls-environment)。

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>公共预览版 - Azure AD 权利管理现在支持多地域 SharePoint Online

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理
 
对于使用多地域 SharePoint Online 的组织，你现在可以将特定的多地域环境中的站点包括到你的权利管理访问包中。 [了解详细信息](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview)。

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>公共预览版 - 从应用注册中还原已删除的应用

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 开发人员体验
 
现在，客户可以从 Azure 门户查看、还原和永久移除已删除的应用注册。 这仅适用于关联到目录的应用程序，不适用于来自个人 Microsoft 帐户的应用程序。 [了解详细信息](../develop/howto-restore-app.md)。
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>公共预览版 - 条件访问中用于注册或加入设备的新“用户操作”

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
 条件访问中提供了一项新的称为“注册或加入设备”的用户操作。 此用户操作允许你控制 Azure AD 设备注册的多重身份验证 (MFA) 策略。 

目前，此用户操作仅允许你在用户将设备注册或加入到 Azure AD 时将 MFA 作为控制措施启用。 此用户操作中禁用了依赖于或不适用于 Azure AD 设备注册的其他控制措施。 [了解详细信息](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)。 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>公共预览版 - 优化连接器组以使用最靠近的应用程序代理云服务

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制
 
有了这个新功能，连接器组就可以被分配给承载着应用程序的最靠近的区域性应用程序代理服务。 当应用承载在主租户的区域之外的区域中时，这可以提高应用性能。 [了解详细信息](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)。 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>公共预览版 - 使用电子邮件一次性密码帐户在 AAD 中执行外部标识自助注册

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C

外部用户现在可以使用电子邮件一次性密码帐户注册到 Azure AD 第一方应用和 LOB 应用。 [了解详细信息](../external-identities/one-time-passcode.md)。

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>公共预览版 - Azure AD 中的 AD FS 注册功能的可用性

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 监视和报告
 
AD FS 登录活动现在可以与 Azure AD 活动报告集成，从而提供混合标识基础结构的统一视图。 使用 Azure AD 登录报告、Log Analytics 和 Azure Monitor 工作簿，可以针对 AAD 和 AD FS 登录场景（例如 AD FS 帐户锁定、错误密码尝试和意外登录尝试次数峰值）执行深度分析。

若要了解详细信息，请访问[使用 Connect Health 在 Azure AD 中进行 AD FS 登录](../hybrid/how-to-connect-health-ad-fs-sign-in.md)。

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>正式发布 - 分阶段推出到云身份验证

**类型：** 新功能  
**服务类别：** AD Connect  
**产品功能：** 用户身份验证
 
分阶段推出到云身份验证功能现在已正式发布。 分阶段推出功能允许你选择性地使用云身份验证方法（例如直通身份验证 (PTA) 或密码哈希同步 (PHS)）来测试用户组。 同时，联合域中的所有其他用户将继续使用联合身份验证服务（例如 AD FS 或任何其他联合身份验证服务）对用户进行身份验证。 [了解详细信息](../hybrid/how-to-connect-staged-rollout.md)。

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>正式发布 - 现在可以在 Azure 管理门户中更新用户类型属性

**类型：** 新功能  
**服务类别：** 用户体验和管理  
**产品功能：** 用户管理
 
在 Azure 管理门户中更新用户配置文件信息时，客户现在可以更新 Azure AD 用户的用户类型。 还可以通过 Microsoft Graph 更新用户类型。 若要了解详细信息，请参阅[添加或更新用户配置文件信息](active-directory-users-profile-azure-portal.md)。
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>正式发布 - Azure Active Directory 域服务的副本集

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务
 
Azure AD DS 中的副本集功能现已正式发布。 [了解详细信息](../../active-directory-domain-services/concepts-replica-sets.md)。
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>正式发布 - 在 Azure 政府云中使用电子邮件一次性密码与合作伙伴进行协作

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Microsoft Azure 政府云中的组织现在可以允许其来宾使用电子邮件一次性密码来兑换邀请。 这可确保 Azure 政府云中任何没有 Azure AD、Microsoft 或 Gmail 帐户的来宾用户仍可通过请求并输入临时密码来登录到共享资源，从而与其合作伙伴进行协作。 [了解详细信息](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Azure AD 应用程序库推出了新联合应用 - 2021 年 3 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2021 年 3 月，我们在应用库中添加了以下 37 个支持联合的新应用程序：

[Bambuser Live Video Shopping](https://lcx.bambuser.com/)、[DeepDyve Inc](https://www.deepdyve.com/azure-sso)、[Moqups](../saas-apps/moqups-tutorial.md)、[RICOH Spaces Mobile](https://ricohspaces.app/welcome)、[Flipgrid](https://auth.flipgrid.com/)、[hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md)、[SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx)、[TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md)、[SimplificaCI](https://app.simplificaci.com.br/)、[Thrive LXP](../saas-apps/thrive-lxp-tutorial.md)、[Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md)、[Exium](../saas-apps/exium-tutorial.md)、[Sapient](../saas-apps/sapient-tutorial.md)、[TrueChoice](../saas-apps/truechoice-tutorial.md)、[RICOH Spaces](https://ricohspaces.app/welcome)、[Saba Cloud](../saas-apps/learning-at-work-tutorial.md)、[Acunetix 360](../saas-apps/acunetix-360-tutorial.md)、[Exceed.ai](../saas-apps/exceed-ai-tutorial.md)、[GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md)、[Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb)、[Smartlook](../saas-apps/smartlook-tutorial.md)、[Accenture Academy](../saas-apps/accenture-academy-tutorial.md)、[Onshape](../saas-apps/onshape-tutorial.md)、[Tradeshift](../saas-apps/tradeshift-tutorial.md)、[JuriBlox](../saas-apps/juriblox-tutorial.md)、[SecurityStudio](../saas-apps/securitystudio-tutorial.md)、[ClicData](https://app.clicdata.com/)、[Evergreen](../saas-apps/evergreen-tutorial.md)、[Patchdeck](https://patchdeck.com/ad_auth/authenticate/)、[FAX.PLUS](../saas-apps/fax-plus-tutorial.md)、[ValidSign](../saas-apps/validsign-tutorial.md)、[AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md)、[Nura Space](https://dashboard.nuraspace.com/login)、[Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md)、[FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

也可访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Azure AD 应用程序库中的新预配连接器 - 2021 年 3 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>适用于公司品牌的 MS Graph API 简介

**类型：** 已更改的功能  
**服务类别：** MS Graph  
**产品功能：** B2B/B2C

[适用于公司品牌的 MS Graph API](/graph/api/resources/organizationalbrandingproperties) 可用于 Azure AD 或 Microsoft 365 登录体验，允许以编程方式管理品牌参数。

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>正式发布 - 使用应用程序代理进行基于标头的身份验证 SSO

**类型：** 已更改的功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制
 
Azure AD 应用程序代理对基于标头的身份验证的原生支持功能现已正式发布。 使用此功能，你可以将所需的用户属性配置为应用程序的 HTTP 标头，而无需部署其他组件。 [了解详细信息](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)。

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>不再支持 MFA 服务器的双向短信

**类型：** 已弃用  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 

MFA 服务器的双向短信于 2018 年开始弃用，在 2021 年 2 月 24 日后不再受支持。 管理员应当为仍然使用双向短信的用户启用另一种方法。

电子邮件通知和 Azure 门户服务运行状况通知已于 2020 年 12 月 8 日和 2021 年 1 月 28 日发送给受影响的管理员。 警报已发送到与订阅绑定的所有者、共同所有者、管理员和服务管理员 RBAC 角色。 [了解详细信息](../authentication/how-to-authentication-two-way-sms-unsupported.md)。
 
---
 
## <a name="february-2021"></a>2021 年 2 月

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>从 2021 年 10 月开始，默认启用电子邮件一次性密码身份验证

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

从 2021 年 10 月 31 日开始，Microsoft Azure Active Directory [电子邮件一次性密码身份验证](../external-identities/one-time-passcode.md)将成为 B2B 协作场景邀请客户和租户的默认方法。 届时，Microsoft 将不再允许使用非管理的 Azure Active Directory 帐户兑换邀请。 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>如果未请求但已同意的权限会触发条件访问，则不会再将其添加到令牌中

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 平台
 
目前，使用[动态权限](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)的应用程序可获得同意它们访问的所有权限。 这包括未请求的应用程序，即使它们会触发条件访问。 例如，这可能会导致只请求 `user.read` 但也已同意 `files.read` 的应用会直接通过为 `files.read` 权限分配的条件访问。 

为了减少不必要的条件访问提示次数，Azure AD 将更改向应用程序提供未请求的范围的方式。 应用将仅触发它们明确请求的权限的条件访问。 有关详细信息，请阅读[身份验证中的新增功能](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)。
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>公共预览版 - 使用临时访问密码注册无密码凭据

**类型：** 新功能  
**服务类别：** MFA  
**产品功能：** 标识安全和保护

临时访问密码是一种有时间限制的密码，可用作强凭据，允许载入无密码凭据，并且可在用户丢失或忘记其强身份验证因素（例如 FIDO2 安全密钥或 Microsoft Authenticator）应用并需要登录注册新的强身份验证方法时恢复。 [了解详细信息](../authentication/howto-authentication-temporary-access-pass.md)。

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>公共预览版 - 下一代用户流中的“保持登录 (KMSI)”

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

下一代 B2C 用户流现在支持[保持登录 (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi)功能，通过使用该功能，客户可使用永久性 Cookie 为 Web 和本机应用程序用户延长会话生存期。  此功能使会话即使在用户关闭并重新打开浏览器时，仍保持活动状态，在用户注销时才会撤销。

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>公共预览版 - 外部标识使用 MSA 帐户在 AAD 中自助注册

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
外部用户现在可使用 Microsoft 帐户登录 Azure AD 第一方和 LOB 应用。 [了解详细信息](../external-identities/self-service-sign-up-overview.md)。

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>公共预览版 - 重置来宾用户的邀请兑换状态

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
客户现在可以重新邀请现有外部来宾用户重置其邀请兑换状态，以便保留来宾用户帐户而不丢失任何访问权限。 [了解详细信息](../external-identities/reset-redemption-status.md)。
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>公共预览版 - /synchronization（预配）API 现在支持应用程序权限

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
现在，客户可使用 application.readwrite.ownedby 作为调用同步 API 的应用程序权限。 请注意，这仅支持从 Azure AD 预配到第三方应用程序（例如 AWS、Data Bricks 等）。 目前不支持 HR 预配 (Workday/Successfactors) 或云同步（AD 到 Azure AD）。 [了解详细信息](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)。
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>正式发布 -“身份验证策略管理员”内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
具有此角色的用户可以配置身份验证方法策略、租户范围 MFA 设置和密码保护策略。 此角色授予管理密码保护设置的权限：智能锁定配置和更新自定义禁止密码列表。 [了解详细信息](../roles/permissions-reference.md#authentication-policy-administrator)。

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>正式发布 -“我的应用”上的用户集合现已发布！

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** 最终用户体验
 
用户现在可在“我的应用”应用启动器上创建自己的应用分组， 还可对管理员与他们共享的集合进行重新排序和隐藏该集合。 [了解详细信息](../user-help/my-apps-portal-user-collections.md)。

---

### <a name="general-availability---autofill-in-authenticator"></a>正式发布 - Authenticator 中的自动填充

**类型：** 新功能  
**服务类别：** Microsoft Authenticator 应用  
**产品功能：** 标识安全和保护
 
Microsoft Authenticator 提供多重身份验证 (MFA) 和帐户管理功能，现在还会在用户通过移动设备（iOS 和 Android）访问的站点和应用中自动填充密码。 

若要使用 Authenticator 上的自动填充，用户需要将其个人 Microsoft 帐户添加到 Authenticator 并使用它来同步密码。 目前不能使用工作或学校帐户同步密码。 [了解详细信息](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)。

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>正式发布 - 邀请内部用户参与 B2B 协作

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
客户现在可邀请内部来宾使用 B2B 协作，而不是向现有内部帐户发送邀请。 这允许客户保留该用户的对象 ID、UPN、组成员身份和应用分配。 [了解详细信息](../external-identities/invite-internal-users.md)。

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>正式发布 -“域名管理员”内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
具有此角色的用户可以管理（读取、添加、验证、更新和删除）域名。 他们还可以读取有关用户、组和应用程序的目录信息，因为这些对象拥有域依赖项。 

对于本地环境，具有此角色的用户可以配置联合身份验证的域名，以便始终在本地对关联的用户进行身份验证。 然后，这些用户可以通过单一登录，使用其本地密码登录到基于 Azure AD 的服务。 需要通过 Azure AD Connect 同步联合身份验证设置，因此用户也具有管理 Azure AD Connect 的权限。 [了解详细信息](../roles/permissions-reference.md#domain-name-administrator)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Azure AD 应用程序库推出了新联合应用 - 2021 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2021 年 2 月，我们在应用库中添加了以下 37 个支持联合的新应用程序：

[Loop Messenger Extension](https://loopworks.com/loop-flow-messenger/)、[Silverfort Azure AD Adapter](http://www.silverfort.com/)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[Nura Space](https://dashboard.nuraspace.com/login)、[Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft)、[UXPressia](https://uxpressia.com/users/sign-in)、[introDus Pre- and Onboarding Platform](http://app.introdus.dk/login)、[Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All)、[LeaksID](https://app.leaksid.com/)、[ShiftWizard](http://www.shiftwizard.com/)、[PingFlow SSO](https://app.pingview.io/)、[Swiftlane](https://admin.swiftlane.com/login)、[Quasydoc SSO](https://www.quasydoc.eu/login)、[Fenwick Gold Account](https://businesscentral.dynamics.com/)、[SeamlessDesk](https://www.seamlessdesk.com/login)、[Learnsoft LMS & TMS](http://www.learnsoft.com/)、[P-TH+](https://p-th.jp/)、[myViewBoard](https://api.myviewboard.com/auth/microsoft/)、[Tartabit IoT Bridge](https://bridge-us.tartabit.com/)、[AKASHI](../saas-apps/akashi-tutorial.md)、[Rewatch](../saas-apps/rewatch-tutorial.md)、[Zuddl](../saas-apps/zuddl-tutorial.md)、[Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md)、[HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md)、[IBMid](../saas-apps/ibmid-tutorial.md)、[SharingCloud](../saas-apps/sharingcloud-tutorial.md)、[PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md)、[GlobeSmart](../saas-apps/globesmart-tutorial.md)、[Samsung Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md)、[Penji](../saas-apps/penji-tutorial.md)、[Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md)、[Maptician](../saas-apps/maptician-tutorial.md)、[Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md)、[Sigma Computing](../saas-apps/sigma-computing-tutorial.md)、[CloudKnox Permissions Management Platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md)、[Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md)、[Enablon](../saas-apps/enablon-tutorial.md)

也可访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Azure AD 应用程序库中的新预配连接器 - 2021 年 2 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

有关详细信息，请参阅[使用 Azure AD 自动预配 SaaS 应用程序的用户](../app-provisioning/user-provisioning.md)。

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>正式发布 - 10 个 Azure Active Directory 角色现已重命名

**类型：** 已更改的功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
10 个 Azure AD 内置角色已重命名，以便在 [Microsoft 365 管理中心](/microsoft-365/admin/microsoft-365-admin-center-preview)、[Azure AD 门户](https://portal.azure.com/)和 [Microsoft Graph](https://developer.microsoft.com/graph/) 中保持一致。 若要详细了解这些新角色，请参阅 [Azure Active Directory 中的管理员角色权限](../roles/permissions-reference.md#all-roles)。

![一张表格，其中显示了 MS Graph API 和 Azure 门户中的角色名称，以及建议在 API、Azure 门户和 Mac 中使用的最终名称。](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>MFA/SSPR 合并注册中的新公司品牌

**类型：** 已更改的功能  
**服务类别：** 用户体验和管理  
**产品功能：** 最终用户体验
 
过去，Azure Active Directory 登录页中并未使用公司徽标。 公司品牌现在位于 MFA/SSPR 合并注册的左上角。 公司品牌还包含在“我的登录”和“安全信息”页上。 [了解详细信息](../fundamentals/customize-branding.md)。

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>正式发布 - 可以将二级管理者设置为备用审批者

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
权利管理现在提供一个可在选择审批者时使用的额外选项。 如果对于第一位审批者选择“管理员作为审批者”，备用审批者字段中将提供另一个选项“将二级管理员作为备用审批者”可供选择。 如果选择此选项，则需要添加后备审批者，以便在系统找不到二级管理员的情况下将请求转发给该审批者。 [了解详细信息](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)。
 
---

### <a name="authentication-methods-activity-dashboard"></a>身份验证方法活动仪表板

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 

刷新的身份验证方法活动仪表板为管理员提供了其租户中身份验证方法注册和使用活动的概述。 报告汇总了每种方法的注册用户数，以及在登录和密码重置过程中使用的方法。 [了解详细信息](../authentication/howto-authentication-methods-activity.md)。
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>可配置令牌生存期 (CTL) 中的刷新和会话令牌生存期可配置性已停用

**类型：** 已弃用  
**服务类别：** 其他  
**产品功能：** 用户身份验证
 
CTL 中的刷新和会话令牌生存期可配置性已停用。 Azure Active Directory 不再支持现有策略中的刷新和会话令牌配置。 [了解详细信息](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens)。
 
---
 
## <a name="january-2021"></a>2021 年 1 月

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>机密令牌将成为配置预配时的必填字段

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

过去，在自定义/BYOA 应用程序上设置预配时，机密令牌字段可保留为空。 此功能以前仅用于测试。 我们将更新 UI，使该字段成为必填字段。 

客户可通过在浏览器 URL 中使用功能标志来避开这一用于测试目的的要求。 [了解详细信息](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)。
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>公共预览版 - 为一线工作者大规模自定义和配置 Android 共享设备

**类型：** 新功能  
**服务类别：** 设备注册和管理  
**产品功能：** 标识安全和保护
 
Azure AD 和 Microsoft Endpoint Manager 团队携手合作，提供用于自定义、缩放和保护一线工作者设备的功能。

以下预览功能可用于：
- 通过 Microsoft Endpoint Manager 大规模预配 Android 共享设备
- 使用基于设备的条件访问保护轮班工作人员的访问权限
- 通过托管主屏幕自定义轮班工作人员的登录体验

若要了解详细信息，请参阅[为一线工作者大规模自定义和配置共享设备](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)。

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>公共预览版 - 现在可以采用 CSV 或 JSON 格式下载预配日志

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

客户可以通过 UI 和 Graph API 将预配日志下载为 CSV 或 JSON 文件。 若要了解详细信息，请参阅[在 Azure Active Directory 门户中预配报告](../reports-monitoring/concept-provisioning-logs.md)。

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>公共预览版 - 向 Azure AD 自定义角色和管理单元范围内的角色分配云组

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
客户可向 Azure AD 自定义角色或管理单元范围内的角色分配云组。 若要了解如何使用此功能，请参阅[在 Azure Active Directory 中使用云组管理角色分配](../roles/groups-concept.md)。

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>正式发布 - Azure AD Connect 云同步（以前称为云预配）

**类型：** 新功能  
**服务类别：** Azure AD Connect 云同步  
**产品功能：** 标识生命周期管理
 
Azure AD Connect 云同步现已向所有客户正式发布。

Azure AD Connect 云将转换逻辑的重担转移到了云，减少了本地占用空间。 此外，有多个轻型代理部署可用于提高同步可用性。 [了解详细信息](https://aka.ms/cloudsyncGA)。
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>正式发布 -“攻击模拟管理员”和“攻击有效负载作者”内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
可将基于角色的访问控制中的两个新角色（攻击模拟管理员和攻击有效负载作者）分配给用户。 

拥有[攻击模拟管理员](../roles/permissions-reference.md#attack-simulation-administrator)角色的用户可以访问租户中的所有模拟，并可以：
- 创建和管理攻击模拟创建的各个方面
- 启动/安排模拟
-  查看模拟结果。 

拥有[攻击有效负载作者](../roles/permissions-reference.md#attack-payload-author)角色的用户可以创建攻击有效负载，但不能实际启动或安排它们。 然后，租户中的所有管理员都可以使用攻击有效负载创建模拟。

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>正式发布 -“使用情况摘要报告读取者”内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
拥有“使用情况摘要报告读取者”角色的用户可以访问 Microsoft 365 管理中心内使用情况和生产力分数的租户级聚合数据和相关见解， 但不能访问任何用户级详细信息或见解。 

在 Microsoft 365 管理中心的这两个报告中，我们区分了租户级聚合数据和用户级详细信息。 此角色向个人用户身份数据添加了一层额外保护。 [了解详细信息](../roles/permissions-reference.md#usage-summary-reports-reader)。

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>正式发布 - 需要 Azure AD 条件访问中的应用保护策略授权

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
“需要应用保护策略”的 Azure AD 条件访问授权现已正式发布。 

此策略提供以下功能：
- 仅在使用支持 Intune 应用保护的移动应用程序时才允许访问
- 仅当用户将 Intune 应用保护策略传递到移动应用程序时才允许访问

在[此处](../conditional-access/app-protection-based-conditional-access.md)详细了解如何设置条件访问策略以保护应用。
 
---

### <a name="general-availability---email-one-time-passcode"></a>正式发布 - 电子邮件一次性密码

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
使用电子邮件 OTP，世界各地的组织可以通过电子邮件发送链接或邀请，与他人进行协作。 受邀用户可以使用电子邮件中发送的一次性密码来验证身份，以访问其合作伙伴的资源。 [了解详细信息](../external-identities/one-time-passcode.md)。 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Azure AD 应用程序库中的新预配连接器 - 2021 年 1 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

有关详细信息，请参阅[什么是 Azure AD 中的自动 SaaS 应用用户预配？](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Azure AD 应用程序库推出了新的联合应用 - 2021 年 1 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2021 年 1 月，我们在应用库中添加了以下 29 个支持联合的新应用程序：

[mySCView](https://dev.myscview.com/)、[Talentech](https://talentech.com/contact/)、[Bipsync](https://www.bipsync.com/)、[OroTimesheet](https://app.orotimesheet.com/login.php)、[Mio](https://app.m.io/auth/install/microsoft?scopetype=hub)、[Sovelto Easy](https://login.soveltoeasy.fi/)、[Supportbench](https://account.supportbench.net/agent/login/)、[Bienvenue Formation](https://formation.bienvenue.pro/login)、[AIDA Healthcare SSO](https://aidaforparents.com/login/organizations)、[International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md)、[NAVEX One](../saas-apps/navex-one-tutorial.md)、[LabLog](../saas-apps/lablog-tutorial.md)、[Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md)、[EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md)、[Notion](../saas-apps/notion-tutorial.md)、[Syndio](../saas-apps/syndio-tutorial.md)、[Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md)、[Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md)、[Nalco E-data](https://www.ecolab.com/)、[Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)、[Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md)、[Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md)、[Illusive Networks](../saas-apps/illusive-networks-tutorial.md)、[Proware](../saas-apps/proware-tutorial.md)、[Splan Visitor](../saas-apps/splan-visitor-tutorial.md)、[Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md)、[Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md)、[Perimeter 81](../saas-apps/perimeter-81-tutorial.md)、[Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>公共预览版 - 可以将二级管理者设置为备用审批者

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
权利管理现在提供一个可在选择审批者时使用的额外选项。 如果对于第一位审批者选择“管理员作为审批者”，备用审批者字段中将提供另一个选项“将二级管理员作为备用审批者”可供选择。 如果选择此选项，则需要添加后备审批者，以便在系统找不到二级管理员的情况下将请求转发给该审批者。 [了解详细信息](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>正式发布 - 直接从“我的访问权限”门户导航到 Teams

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
现在可以直接从“我的访问权限”门户启动 Teams。 

为此，请登录“我的访问权限”(https://myaccess.microsoft.com/) )，导航到“访问包”，然后转到“活动”选项卡以查看你有权访问的所有访问包。 展开所选访问包并将鼠标悬停在 Teams 上时，可单击“打开”按钮启动 Teams。 [了解详细信息](../governance/entitlement-management-request-access.md)。
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>改进了有风险的来宾用户的日志记录和最终用户提示

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 

更新了有风险的来宾用户的日志记录和最终用户提示。 请访问[标识保护和 B2B 用户](../identity-protection/concept-identity-protection-b2b.md)了解详细信息。
 
---
 
## <a name="december-2020"></a>2020 年 12 月

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>公共预览版 - 使用内置策略的 Azure AD B2C 电话注册和登录

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
使用内置策略的 B2C 电话注册和登录可让组织的 IT 管理员和开发人员允许其最终用户在用户流中使用电话号码进行登录和注册。 有关详细信息，请参阅[为用户流设置电话注册和登录（预览）](../../active-directory-b2c/phone-authentication-user-flows.md)。

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>正式发布 - 默认为所有新租户启用安全默认值

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 标识安全和保护
 
为了保护用户帐户，在 2020 年 11 月 12 日或之后创建的所有新租户都将启用安全默认值。 安全默认值强制实施多个策略，包括：
- 要求所有用户和管理员使用 Microsoft Authenticator 应用注册 MFA
- 要求关键管理员角色在每次登录时都使用 MFA。 如有必要，将提示所有其他用户进行 MFA。 
- 将在租户范围内阻止旧式身份验证。 

有关详细信息，请参阅[什么是安全默认值？](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>正式发布 - AADConnect 中支持包含多达 25 万名成员的组

**类型：** 已更改的功能  
**服务类别：** AD Connect  
**产品功能：** 标识生命周期管理
 
Microsoft 已部署新的 Azure AD Connect 终结点 (API)，可提高 Azure Active Directory 的同步服务操作性能。 使用新的 [V2 终结点](../hybrid/how-to-connect-sync-endpoint-api-v2.md)时，Azure AD 中的导出和导入性能会有显著提升。 这一新终结点支持以下方案：

- 同步包含多达 25 万名成员的组
- 提高 Azure AD 的导出和导入性能

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>正式发布 - 权利管理可用于 Azure 中国云中的租户

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 

权利管理功能现可用于 Azure 中国云中的所有租户。 有关信息，请访问我们的 [Identity Governance 文档](https://docs.azure.cn/zh-cn/active-directory/governance/)网站。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 12 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut 云打印管理](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [可解析](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Azure AD 应用程序库推出了新联合应用 - 2020 年 12 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 12 月，我们在应用库中添加了以下 18 个支持联合的新应用程序：

[AwareGo](../saas-apps/awarego-tutorial.md)、[HowNow SSO](https://gethownow.com/)、[ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold)、[Guider](http://www.guider-ai.com/)、[Softcrisis](https://www.softcrisis.se/sv/)、[Pims 365](http://www.omega365.com/pims)、[InformaCast](../saas-apps/informacast-tutorial.md)、[RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md)、[vonage](../saas-apps/vonage-tutorial.md)、[Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md)、[ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md)、[RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md)、[JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md)、[Shutterstock](../saas-apps/shutterstock-tutorial.md)、[FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md)、[LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md)、[Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md)、[KFAdvance](../saas-apps/kfadvance-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>直接从“我的访问权限”门户导航到 Teams

**类型：** 已更改的功能  
**服务类别：** 用户访问管理 **产品功能：** 权利管理

现在可以直接从“我的访问权限”门户启动 Teams。 为此，请登录[我的访问权限](https://myaccess.microsoft.com/)，导航到“访问包”，然后转到“活动”选项卡以查看你有权访问的所有访问包 。 展开访问包并将鼠标悬停在 Teams 上时，可单击“打开”按钮启动 Teams。 

要详细了解如何使用“我的访问权限”门户，请转到[在 Azure AD 权利管理中请求访问包的访问权限](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)。

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>公共预览版 - 可以将二级管理者设置为备用审批者

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理

权利管理的审批流程中现在提供一个额外的选项。 如果对于第一位审批者选择“管理员作为审批者”，备用审批者字段中将提供另一个选项“将二级管理员作为备用审批者”可供选择。 如果选择此选项，则需要添加备用审批者，以便在系统找不到二级管理员的情况下将请求转发给该审批者。

有关详细信息，请转到[在 Azure AD 权利管理中更改访问包的审批设置](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)。

---
