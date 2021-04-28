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
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d8ef544029e7097d90651707da24d603bcecc9
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137952"
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

有关其他指导，请参阅[在环境中启用 TLS 1.2 支持，以便弃用 Azure AD TLS 1.1 和 1.0](/troubleshoot/azure/active-directory/enable-support-tls-environment)。

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
 
有了这个新功能，连接器组就可以被分配给承载着应用程序的最靠近的区域性应用程序代理服务。 当应用承载在主租户的区域之外的区域中时，这可以提高应用性能。 [了解详细信息](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)。 
 
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

[Bambuser Live Video Shopping](https://lcx.bambuser.com/)、[DeepDyve Inc](https://www.deepdyve.com/azure-sso)、[Moqups](../saas-apps/moqups-tutorial.md)、[RICOH Spaces Mobile](https://ricohspaces.app/welcome)、[Flipgrid](https://auth.flipgrid.com/)、[hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md)、[SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx)、[TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md)、[SimplificaCI](https://app.simplificaci.com.br/)、[Thrive LXP](../saas-apps/thrive-lxp-tutorial.md)、[Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md)、[Exium](../saas-apps/exium-tutorial.md)、[Sapient](../saas-apps/sapient-tutorial.md)、[TrueChoice](../saas-apps/truechoice-tutorial.md)、[RICOH Spaces](https://ricohspaces.app/welcome)、[Saba Cloud](../saas-apps/learning-at-work-tutorial.md)、[Acunetix 360](../saas-apps/acunetix-360-tutorial.md)、[Exceed.ai](../saas-apps/exceed-ai-tutorial.md)、[GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md)、[Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb)、[Smartlook](../saas-apps/smartlook-tutorial.md)、[Accenture Academy](../saas-apps/accenture-academy-tutorial.md)、[Onshape](../saas-apps/onshape-tutorial.md)、[Tradeshift](../saas-apps/tradeshift-tutorial.md)、[JuriBlox](../saas-apps/juriblox-tutorial.md)、[SecurityStudio](../saas-apps/securitystudio-tutorial.md)、[ClicData](https://app.clicdata.com/)、[Evergreen](../saas-apps/evergreen-tutorial.md)、[Patchdeck](https://patchdeck.com/ad_auth/authenticate/)、[FAX.PLUS](../saas-apps/fax.plus-tutorial.md)、[ValidSign](../saas-apps/validsign-tutorial.md)、[AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md)、[Nura Space](https://dashboard.nuraspace.com/login)、[Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md)、[FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

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
 
Azure AD 应用程序代理对基于标头的身份验证的原生支持功能现已正式发布。 使用此功能，你可以将所需的用户属性配置为应用程序的 HTTP 标头，而无需部署其他组件。 [了解详细信息](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)。

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

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>弃用 Azure Active Directory TLS 1.0、TLS 1.1 和 3DES

**类型：** 更改计划  
**服务类别：** 所有 Azure AD 应用程序  
**产品功能：** 标准

从 2021 年 6 月 30 日开始，Azure Active Directory 将在 Azure Active Directory 全球区域弃用以下协议：

- TLS 1.0
- TLS 1.1
- 3DES 密码套件 (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

受影响的环境包括：
- Azure 商业云
- Office 365 GCC 和 WW

相关公告：所有客户端-服务器和浏览器-服务器组合都应使用 TLS 1.2 和新式密码套件来维护与 Azure Active Directory、Office 365 和 Microsoft 365 服务的安全连接。 这一更改与[在 US Gov 云中弃用 Azure Active Directory TLS 1.0、Azure Active Directory TLS 1.1 和 3DES 密码套件](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)相关。

有关删除弃用的协议依赖项的指导，请参阅[在环境中启用 TLS 1.2 支持，以便弃用 Azure AD TLS 1.1 和 1.0](/troubleshoot/azure/active-directory/enable-support-tls-environment)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD 应用程序库推出了新联合应用 - 2020 年 11 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020 年 11 月，我们在应用库中添加了以下 52 个支持联合的新应用程序：

[旅游与支出管理](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning 文件选取器](https://pmteam.itslearning.com/), [危机控制](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum - Study Island](https://app.studyisland.com/cfw/login/), [虚拟风险管理员](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview - 招聘](https://recruit.talview.com/login), 实时翻译, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak 租户体验平台](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [EVA 签入](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid（所有产品）](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence 应用程序安全](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [运费度量](https://www.gpcsl.com/freight.html), [Terraform 云](../saas-apps/terraform-cloud-tutorial.md), [性质研究](../saas-apps/nature-research-tutorial.md), [播放数字告示](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise 软件关系 CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>公共预览版 - 适用于企业应用的自定义角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
 [用于委托企业应用程序管理的自定义 RBAC 角色](../roles/custom-available-permissions.md)现提供公共预览版。 这些新权限以用于应用注册管理的自定义角色为基础，允许对管理员拥有的访问权限进行精细控制。 随着时间的推移，将发布用于委托 Azure AD 管理的附加权限。

一些常见的委托方案：
- 分配可访问基于 SAML 的单一登录应用程序的用户和组
- 创建 Azure AD 库应用程序
- 更新和读取基于 SAML 的单一登录应用程序的基本 SAML 配置
- 管理基于 SAML 的单一登录应用程序的签名证书
- 更新基于 SAML 的单一登录应用程序即将到期的登录证书通知电子邮件地址
- 更新基于 SAML 的单一登录应用程序的 SAML 令牌签名和登录算法
- 创建、删除和更新基于 SAML 的单一登录应用程序的用户特性和声明
- 打开、关闭和重启预配作业的功能
- 更新属性映射
- 读取与对象关联的预配设置的能力
- 读取与服务主体关联的预配设置的能力
- 授权应用程序访问以进行预配的能力

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>公共预览版 - Azure AD 应用程序代理原生支持对使用标头进行身份验证的应用程序进行单一登录访问

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制
 
Azure Active Directory (Azure AD) 应用程序代理原生支持对使用标头进行身份验证的应用程序进行单一登录访问。 你可以在 Azure AD 中配置应用程序所需的标头值。 标头值将通过应用程序代理发送到应用程序。 若要了解详细信息，请参阅[通过 Azure AD 应用代理对本地应用使用基于标头的单一登录](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>正式发布 - 使用自定义策略的 Azure AD B2C 电话注册和登录

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

通过电话号码注册和登录，开发人员和企业可以允许其客户使用通过短信发送到用户电话号码的一次性密码进行注册和登录。 此功能还允许客户在失去对其电话的访问权限时更改其电话号码。 借助自定义策略的强大功能，开发人员和企业可以通过自定义页面传达其品牌。 了解如何[在 Azure AD B2C 中通过自定义策略设置电话注册和登录](../../active-directory-b2c/phone-authentication-user-flows.md)。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 11 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

有关详细信息，请参阅[使用 Azure AD 自动预配 SaaS 应用程序的用户](../app-provisioning/user-provisioning.md)。
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>公共预览版 - 现在可通过分阶段推出部署使用 ProxyAddresses 的电子邮件登录

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
租户管理员现在可以使用分阶段推出将使用 ProxyAddresses 的电子邮件登录部署到特定 Azure AD 组。 这有助于在通过主领域发现策略将该功能部署到整个租户之前试用该功能。 [文档](../authentication/howto-authentication-use-email-signin.md)中提供了通过分阶段推出部署使用 ProxyAddresses 的电子邮件登录的说明。
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>有限预览版 - 登录诊断

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
使用登录诊断的初始预览版本，管理员现在可以查看用户登录。管理员可以收到有关登录过程中发生的事情以及如何解决问题的上下文详细信息和指导、具体详细信息和指导，以及相关详细信息和指导。 诊断在 Azure AD 级别以及“条件访问诊断和解决”边栏选项卡中均可用。 此版本中涵盖的诊断方案包括条件访问、多重身份验证和成功登录。

有关详细信息，请参阅[什么是 Azure AD 中的登录诊断？](../reports-monitoring/overview-sign-in-diagnostics.md)。
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>改进了不熟悉的登录属性

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

  不熟悉的登录属性检测已更新。 客户可能会注意到更多不熟悉的高风险登录属性检测。 有关详细信息，请参阅[什么是风险？](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>云预配代理的公共预览版刷新现已发布（版本：1.1.281.0）

**类型：** 已更改的功能  
**服务类别：** Azure AD 云配置  
**产品功能：** 标识生命周期管理
 
云预配代理已发布公共预览版，现在通过门户提供。 此版本包含多项改进，包括对域的 GMSA 支持（提供更好的安全性）、改进的初始同步周期以及对大型组的支持。 有关更多详细信息，请查看发布版本[历史记录](../app-provisioning/provisioning-agent-release-version-history.md)。 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker 恢复密钥 API 终结点现位于 /informationProtection 下

**类型：** 已更改的功能  
**服务类别：** 访问管理  
**产品功能：** 设备识生命周期管理
 
以前，可通过 /bitlocker 终结点恢复 BitLocker 密钥。 我们最终将弃用此终结点，客户应该开始使用现在位于 /informationProtection 下的 API。 

请参阅 [BitLocker 恢复 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true)，获取反映这些更改的文档更新。

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>对远程桌面服务 HTML5 Web 客户端的应用程序代理支持已正式发布

**类型：** 已更改的功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制
 
对远程桌面服务 (RDS) Web 客户端的 Azure AD 应用程序代理支持现已正式发布。 RDS Web 客户端允许用户通过任何支持 HTLM5 的浏览器（如 Microsoft Edge、Internet Explorer 11 和 Google Chrome 等）访问远程桌面基础结构。 用户可以与远程应用或桌面进行交互，就像从任何位置与本地设备进行交互一样。 

在使用 Azure AD 应用程序代理的情况下，你可以通过对所有类型的富客户端应用强制执行预身份验证和条件访问策略，来提高 RDS 部署的安全性。 若要了解详细信息，请参阅[使用 Azure AD 应用程序代理发布远程桌面](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>新的增强型动态组服务现在提供公共预览版

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 协作
 
增强型动态组服务现在提供公共预览版。 在其租户中创建动态组的新客户将使用这一新服务。 创建动态组所需的时间与要创建的组的大小（而不是租户大小）成正比。 当客户创建较小的组时，此更新将显著提高大型租户的性能。 

新服务还致力于在几分钟内完成成员的添加和删除，因为只需更改属性。 此外，单一处理失败不会阻碍租户处理。 若要详细了解如何创建动态组，请参阅[文档](../enterprise-users/groups-create-rule.md)。
 
---
## <a name="october-2020"></a>2020 年 10 月

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>受 Azure TLS 证书更改影响的 Azure AD 本地混合代理

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 平台

Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 进行此更新的原因是当前 CA 证书不符合某个 CA/浏览器论坛基线要求。 此更改将影响本地安装的 Azure AD 混合代理，这些代理已使用一系列固定的根证书来强化环境，并且需要更新这些代理以信任新的证书颁发者。

如果不立即执行操作，此更改将导致服务中断。 这些代理包括用于远程访问本地内容的[应用程序代理连接器](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy)、用于允许用户使用相同密码登录应用程序的[直通身份验证](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理，以及用于执行 AD 到 Azure AD 同步的[云预配预览版](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理。 

如果环境中的防火墙规则设置为仅允许对特定证书吊销列表 (CRL) 下载进行出站调用，则需要允许以下 CRL 和 OCSP URL。 有关此更改以及允许访问的 CRL 和 OCSP URL 的完整详细信息，请参阅 [Azure TLS 证书更改](../../security/fundamentals/tls-certificate-changes.md)。

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>预配事件将从审核日志中删除，并仅发布到预配日志

**类型：** 更改计划  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
SCIM [预配服务](../app-provisioning/user-provisioning.md)的活动记录在审核日志和预配日志中。 这包括以下活动：在 ServiceNow 中创建用户、在 GSuite 中创建组或从 AWS 导入角色等等。 将来，这些事件只会发布到预配日志中。 实现此更改是为了避免跨日志重复事件，并避免因客户在 Log Analytics 中使用日志而产生额外的费用。 

我们将在完成删除后提供更新。 此弃用并不是计划在 2020 年进行的。 

> [!NOTE]
> 这不会影响审核日志中预配服务发出的同步事件之外的任何事件。 创建应用程序、条件访问策略、目录中的用户等事件将继续在审核日志中发出。 [了解详细信息](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)。
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>受 Azure 传输层安全性 (TLS) 证书更改影响的 Azure AD 本地混合代理

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 平台
 
Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 进行此更新的原因是当前的 CA 证书不符合某个 CA/浏览器论坛基线要求。 此更改将影响本地安装的 Azure AD 混合代理，这些代理已使用一系列固定的根证书来强化环境。 需要更新这些代理以信任新的证书颁发者。

如果不立即执行操作，此更改将导致服务中断。 这些代理包括： 
- 用于远程访问本地内容的[应用程序代理连接器](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- 允许用户使用相同密码登录应用程序的[直通身份验证](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理
- 执行 AD 到 Azure AD 同步的[云预配预览版](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理。 

如果环境中的防火墙规则设置为仅允许对特定证书吊销列表 (CRL) 下载进行出站调用，则需要允许 CRL 和 OCSP URL。 有关此更改以及允许访问的 CRL 和 OCSP URL 的完整详细信息，请参阅 [Azure TLS 证书更改](../../security/fundamentals/tls-certificate-changes.md)。
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>在 US Gov 云中弃用 Azure Active Directory TLS 1.0、TLS 1.1 和 3DES

**类型：** 更改计划  
**服务类别：** 所有 Azure AD 应用程序  
**产品功能：** 标准
 
从 2021 年 3 月 31 日开始，Azure Active Directory 将弃用以下协议：
- TLS 1.0
- TLS 1.1
- 3DES 密码套件 (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

所有客户端-服务器和浏览器-服务器组合都应使用 TLS 1.2 和新式密码套件来维护与 Azure Active Directory、Office 365 和 Microsoft 365 服务的安全连接。

受影响的环境包括：
- Azure US Gov
- [Office 365 GCC High 和 DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

有关删除弃用的协议依赖项的指导，请参阅[在环境中启用 TLS 1.2 支持，以便弃用 Azure AD TLS 1.1 和 1.0](/troubleshoot/azure/active-directory/enable-support-tls-environment)。
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>向管理单元和对象范围中的角色分配应用程序

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
借助此功能，可以将应用程序 (SPN) 分配给管理单元范围内的管理员角色。 若要了解详细信息，请参阅[将作用域内角色分配给管理单元](../roles/admin-units-assign-roles.md)。

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>现在当来宾用户被拒绝访问资源时，你可禁用并删除该用户

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识治理
 
禁用并删除是 Azure AD 访问评审的一项高级控制，可帮助组织更好地管理组和应用中的外部来宾。 如果在访问评审中拒绝了来宾，则“禁用并删除”功能会在 30 天内自动阻止他们登录。 30 天后，系统会将他们从租户中完全删除。

有关此功能的详细信息，请参阅[使用 Azure AD 访问评审禁用并删除外部标识](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)。
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>访问评审创建者可在发送给审阅者的电子邮件中添加自定义消息

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识治理
 
在 Azure AD 访问评审中，创建评审的管理员现在可以向审阅者编写自定义消息。 审阅者将在收到的电子邮件中看到这些提示他们完成评审的消息。 若要详细了解如何使用此功能，请参阅[创建一个或多个访问评审](../governance/create-access-review.md#create-one-or-more-access-reviews)部分的步骤 14。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 10 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [全局中继标识同步](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C 的集成助手

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
集成助手（预览版）体验现可用于 Azure AD B2C 应用注册。 此体验可帮助指导你针对常见情况配置应用程序。 详细了解 [Microsoft 标识平台最佳做法和建议](../develop/identity-platform-integration-checklist.md)。
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>在 Azure 门户 UI 中查看角色模板 ID

**类型：** 新功能  
**服务类别：** Azure 角色  
**产品功能：** 访问控制
 

现在可在 Azure 门户中查看每个 Azure AD 角色的模板 ID。 在 Azure AD 中，选择所选角色的“说明”。 

建议客户在其 PowerShell 脚本和代码中使用角色模板 ID，而不是显示名称。 角色模板 ID 适用于 [directoryRoles](/graph/api/resources/directoryrole) 和 [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) 对象。 有关角色模板 ID 的详细信息，请参阅 [Azure AD 内置角色](../roles/permissions-reference.md)。

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C 注册用户流的 API 连接器现在提供公共预览版

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 

API 连接器现在可用于 Azure Active Directory B2C。 借助 API 连接器，可使用 Web API 来自定义注册用户流并与外部云系统集成。 可以使用 API 连接器：

- 与自定义审批工作流集成
- 验证用户输入数据
- 覆盖用户属性 
- 运行自定义业务逻辑 

 有关详细信息，请访问[使用 API 连接器来自定义和扩展注册](../../active-directory-b2c/api-connectors-overview.md)文档。

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>权利管理中连接组织的 State 属性

**类型：** 新功能  
**服务类别：** 管理 **产品功能：** 权利管理
 

 所有连接组织现在都有一个名为“State”的附加属性。 状态将控制在引用“所有已配置的连接组织”的策略中如何使用连接组织。 该设置的值将为“configured”（表示组织在使用“all”子句的策略范围内）或“proposed”（表示组织不在范围内）。  

手动创建的连接组织的默认设置为“configured”。 同时，自动创建的连接组织（通过允许 Internet 中的任何用户请求访问权限的策略创建）的默认设置为“proposed”。  2020 年 9 月 9 日之前创建的任何连接组织都将设置为“configured”。 管理员可以根据需要更新此属性。 [了解详细信息](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)。
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory 外部标识现在为 B2C 提供高级安全设置

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
[Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md) 中现在提供标识保护的基于风险的条件访问和风险检测功能。 借助这些高级安全功能，客户现在可以：
- 利用智能见解来评估 B2C 应用和最终用户帐户的风险。 检测内容包括异常位置登录、匿名 IP 地址、受恶意软件感染的 IP 地址和 Azure AD 威胁智能。 还提供了基于门户和 API 的报告。
- 通过为 B2C 用户配置自适应身份验证策略来自动解决风险问题。 应用开发人员和管理员可以根据所检测到的用户风险级别以及基于位置、组和应用提供的附加控制措施，要求用户进行多重身份验证 (MFA) 或阻止访问，从而降低实时风险。
- 与 Azure AD B2C 用户流和自定义策略集成。 条件可以从 Azure AD B2C 中的内置用户流触发，也可以合并到 B2C 自定义策略中。 与 B2C 用户流的其他方面一样，可以自定义最终用户体验消息。 根据组织的语音、品牌和缓解方案进行自定义。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD 应用程序库推出了新联合应用 - 2020 年 10 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 10 月，我们在应用库中添加了以下 27 个支持联合的新应用程序：

[Sentry](../saas-apps/sentry-tutorial.md)、[Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login)、[ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md)、[EAComposer](../saas-apps/eacomposer-tutorial.md)、[Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/)、[Zone Technologies Portal](https://portail.zonetechnologie.com/signin)、[Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md)、[Datawiza Access Broker](https://console.datawiza.com/)、[ZOKRI](https://app.zokri.com/)、[CheckProof](../saas-apps/checkproof-tutorial.md)、[Ecochallenge.org](https://events.ecochallenge.org/users/login)、[atSpoke](http://atspoke.com/login)、[Appointment Reminder](https://app.appointmentreminder.co.nz/account/login)、[Cloud.Market](https://cloud.market/)、[TravelPerk](../saas-apps/travelperk-tutorial.md)、[Greetly](https://app.greetly.com/)、[OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md)、[Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md)、[Loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md)、[Starmind](../saas-apps/starmind-tutorial.md)、[Workstem](https://hrm.workstem.com/login)、[Retail Zipline](../saas-apps/retail-zipline-tutorial.md)、[Hoxhunt](../saas-apps/hoxhunt-tutorial.md)、[MEVISIO](../saas-apps/mevisio-tutorial.md)、[Samsara](../saas-apps/samsara-tutorial.md)、[Nimbus](../saas-apps/nimbus-tutorial.md)、[Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>预配日志现在可以流式传输到 Log Analytics

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 

将预配日志发布到 Log Analytics，以便：
- 存储预配日志超过 30 天
- 定义自定义警报和通知
- 生成仪表板以可视化日志
- 执行复杂查询以分析日志 

若要了解如何使用此功能，请参阅[了解预配如何与 Azure Monitor 日志集成](../app-provisioning/application-provisioning-log-analytics.md)。
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>应用程序所有者现在可以查看预配日志

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
现在可以允许应用程序所有者按预配服务来监视活动并排查问题，而无需为其提供特权角色或使 IT 成为瓶颈。 [了解详细信息](../reports-monitoring/concept-provisioning-logs.md)。
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>重命名 10 个 Azure Active Directory 角色

**类型：** 已更改的功能  
**服务类别：** Azure 角色  
**产品功能：** 访问控制
 
某些 Azure Active Directory (AD) 内置角色的名称与 Microsoft 365 管理中心、Azure AD 门户和 Microsoft Graph 中显示的名称不同。 这种不一致会导致自动化流程中出现问题。 在此更新中，我们将重命名 10 个角色名称，使它们保持一致。 下表是新的角色名称：

![一张表格，其中显示了 MS Graph API 和 Azure 门户中的角色名称，以及建议在 M365 管理中心、Azure 门户和 API 中使用的新名称。](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C 支持对 SPA 使用 MSAL JS 2.x 授权代码流

**类型：** 已更改的功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
MSAL.js 版本 2.x 现已开始支持对单页 Web 应用 (SPA) 使用授权代码流。 Azure AD B2C 现在支持在 Azure 门户上使用 SPA 应用类型，并将 MSAL.js 授权代码流与 PKCE 一起用于单页应用。 这样，使用 Azure AD B2C 的 SPA 就可以利用更高版本的浏览器来维护 SSO，并遵循更高版本的身份验证协议建议。 开始使用[在 Azure Active Directory B2C 中注册单页应用程序 (SPA)](../../active-directory-b2c/tutorial-register-spa.md) 教程。

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>“在受信任的设备上记住多重身份验证 (MFA)”设置更新

**类型：** 已更改的功能  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 

我们最近已更新“在受信任的设备上[记住多重身份验证 (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication)”功能，将身份验证有效时间延长到最长 365 天。 Azure Active Directory (Azure AD) 高级许可证还可使用[“条件访问 - 登录频率”策略](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)，为重新身份验证设置提供更大的灵活性。

为获得最佳用户体验，我们建议使用条件访问登录频率来延长受信任的设备、位置或低风险会话的会话生存期，替代“在受信任的设备上记住 MFA”设置。 若要开始使用，请查看[有关优化重新身份验证体验的最新指南](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

---