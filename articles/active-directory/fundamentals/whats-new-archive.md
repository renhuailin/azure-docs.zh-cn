---
title: Azure Active Directory 的新增功能存档 | Microsoft Docs
description: 此内容集的“概述”部分中的新增功能发行说明包含 6 个月的活动。 6 个月过后，这些项目将从主文章中删除并放入此存档文章中。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: af8d770752468d96dda51ad642b2e5d29cb6d23b
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537177"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能存档

[Azure Active Directory 的新增功能发行说明](whats-new.md)这篇主要文章包含过去六个月的更新，而本文包含所有较早的信息。

Azure Active Directory 的新增功能发行说明提供有关以下内容的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

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

[mySCView](https://www.myscview.com/)、[Talentech](https://talentech.com/contact/)、[Bipsync](https://www.bipsync.com/)、[OroTimesheet](https://app.orotimesheet.com/login.php)、[Mio](https://app.m.io/auth/install/microsoft?scopetype=hub)、[Sovelto Easy](https://login.soveltoeasy.fi/)、[Supportbench](https://account.supportbench.net/agent/login/)、[Bienvenue Formation](https://formation.bienvenue.pro/login)、[AIDA Healthcare SSO](https://aidaforparents.com/login/organizations)、[International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md)、[NAVEX One](../saas-apps/navex-one-tutorial.md)、[LabLog](../saas-apps/lablog-tutorial.md)、[Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md)、[EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md)、[Notion](../saas-apps/notion-tutorial.md)、[Syndio](../saas-apps/syndio-tutorial.md)、[Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md)、[Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md)、[Nalco E-data](https://www.ecolab.com/)、[Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)、[Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md)、[Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md)、[Illusive Networks](../saas-apps/illusive-networks-tutorial.md)、[Proware](../saas-apps/proware-tutorial.md)、[Splan Visitor](../saas-apps/splan-visitor-tutorial.md)、[Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md)、[Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md)、[Perimeter 81](../saas-apps/perimeter-81-tutorial.md)、[Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

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

[AwareGo](../saas-apps/awarego-tutorial.md)、[HowNow SSO](https://gethownow.com/)、[ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold)、[Guider](http://www.guider-ai.com/)、[Softcrisis](https://www.softcrisis.se/sv/)、[Pims 365](https://www.omega365.com/products/omega-pims)、[InformaCast](../saas-apps/informacast-tutorial.md)、[RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md)、[vonage](../saas-apps/vonage-tutorial.md)、[Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md)、[ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md)、[RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md)、[JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md)、[Shutterstock](../saas-apps/shutterstock-tutorial.md)、[FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md)、[LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md)、[Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md)、[KFAdvance](../saas-apps/kfadvance-tutorial.md)

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

有关删除弃用的协议依赖项的指导，请参阅[在环境中启用 TLS 1.2 支持，为即将弃用 Azure AD TLS 1.0/1.1 做好准备](/troubleshoot/azure/active-directory/enable-support-tls-environment)。

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
 
Azure Active Directory (Azure AD) 应用程序代理原生支持对使用标头进行身份验证的应用程序进行单一登录访问。 你可以在 Azure AD 中配置应用程序所需的标头值。 标头值将通过应用程序代理发送到应用程序。 若要了解详细信息，请参阅[通过 Azure AD 应用代理对本地应用使用基于标头的单一登录](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)
 
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
 
使用登录诊断的初始预览版本，管理员现在可以查看用户登录。管理员可以收到有关登录过程中发生的事情以及如何解决问题的上下文详细信息和指导、具体详细信息和指导，以及相关详细信息和指导。 诊断在 Azure AD 级别以及“条件访问诊断和解决”边栏选项卡中均可用。 此版本中涵盖的诊断方案包括条件访问、Azure Active Directory 多重身份验证和成功登录。

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

在使用 Azure AD 应用程序代理的情况下，你可以通过对所有类型的富客户端应用强制执行预身份验证和条件访问策略，来提高 RDS 部署的安全性。 若要了解详细信息，请参阅[使用 Azure AD 应用程序代理发布远程桌面](../app-proxy/application-proxy-integrate-with-remote-desktop-services.md)
 
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

[1305958](https://identitydivision.visualstudio.com/IAM/IXR/_queries?id=1305958&triage=true&fullScreen=false&_a=edit)

### <a name="azure-active-directory-tls-10--11-and-3des-cipher-suite-deprecation"></a>弃用 Azure Active Directory TLS 1.0 & 1.1 和 3DES 密码套件

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 标准

自 2022 年 1 月 31 日起，Azure Active Directory 将在全球 Azure Active Directory 区域弃用以下协议。（此日期从 2021 年 6 月 30 日推迟至 2022 年 1 月 31 日，为管理员提供更多时间来删除旧版 TLS 协议和密码（TLS 1.0、1.1 和 3DES）上的依赖项）：

- TLS 1.0
- TLS 1.1
- 3DES 密码套件 (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

受影响的环境包括：

- Azure 商业云
- Office 365 GCC 和 WW

与 Azure Active Directory 和 Microsoft Graph 交互的用户、服务和应用程序应使用 TLS 1.2 和新式密码套件来维护与 Azure Active Directory、Office 365 和 Microsoft 365 服务的安全连接。 有关更多指导，请参阅[在环境中启用 TLS 1.2 支持，为即将弃用 Azure AD TLS 1.0/1.1 做好准备](/troubleshoot/azure/active-directory/enable-support-tls-environment)。

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
- 通过为 B2C 用户配置自适应身份验证策略来自动解决风险问题。 应用开发人员和管理员可以根据所检测到的用户风险级别以及基于位置、组和应用提供的附加控制措施，要求用户进行 Active Directory 多重身份验证 (MFA) 或阻止访问，从而降低实时风险。
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

### <a name="updates-to-remember-azure-active-directory-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>“在受信任的设备上记住 Azure Active Directory 多重身份验证 (MFA)”设置更新

**类型：** 已更改的功能  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 

我们最近已更新“在受信任的设备上[记住 Azure Active Directory 多重身份验证 (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication)”功能，将身份验证有效时间延长到最长 365 天。 Azure Active Directory (Azure AD) 高级许可证还可使用[“条件访问 - 登录频率”策略](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)，为重新身份验证设置提供更大的灵活性。

为获得最佳用户体验，我们建议使用条件访问登录频率来延长受信任的设备、位置或低风险会话的会话生存期，替代“在受信任的设备上记住 MFA”设置。 若要开始使用，请查看[有关优化重新身份验证体验的最新指南](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

---

## <a name="september-2020"></a>2020 年 9 月

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 9 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>云预配公共预览版刷新

**类型：** 新功能  
**服务类别：** Azure AD 云配置 **产品功能：** 标识生命周期管理
 
Azure AD Connect 云预配公共预览版刷新具有两个根据客户反馈开发的主要增强功能： 

- 通过 Azure 门户提供的属性映射体验

    通过此功能，IT 管理员可使用目前存在的各种映射类型将用户、组或联系人属性从 AD 映射到 Azure AD。 属性映射功能用于将从 Active Directory 流向 Azure Active Directory 的属性值标准化。 可以确定是直接将属性值按原样从 AD 映射到 Azure AD，还是在预配用户时使用表达式来转换属性值。 [了解详细信息](../cloud-sync/how-to-attribute-mapping.md)

- 按需预配或测试用户体验

    设置配置后，你可能需要进行测试以查看用户转换是否按预期方式工作，然后再将配置应用到范围内的所有用户。 通过按需预配，IT 管理员可以输入 AD 用户的可分辨名称 (DN)，并查看其是否按预期同步。 按需预配提供了一种确保之前执行的属性映射按预期工作的好方法。 [了解详细信息](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD 中经过审核的 BitLocker 恢复 - 公共预览版

**类型：** 新功能  
**服务类别：** 访问管理  
**产品功能：** 设备识生命周期管理
 
IT 管理员或最终用户读取他们有权访问的 BitLocker 恢复密钥时，Azure Active Directory 现在会生成一个审核日志来捕获谁访问了恢复密钥。 相同的审核提供与 BitLocker 密钥关联的设备的详细信息。

最终用户可[通过“我的帐户”访问其恢复密钥](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key)。 IT 管理员可通过 [beta 版 BitLocker 恢复密钥 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) 或 Azure AD 门户访问恢复密钥。 若要了解详细信息，请参阅[在 Azure AD 门户中查看或复制 BitLocker 密钥](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)。

---

### <a name="teams-devices-administrator-built-in-role"></a>Teams 设备管理员内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
拥有 [Teams 设备管理员](../roles/permissions-reference.md#teams-devices-administrator)角色的用户可以在 Teams 管理中心管理 [Teams 认证的设备](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)。 

此角色允许用户快速查看所有设备，并能够搜索和筛选设备。 用户还可查看每个设备的详细信息，包括设备的登录帐户、品牌和型号。 用户可以更改设备上的设置并更新软件版本。 此角色不会授权检查 Teams 活动和设备的通话质量。
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>目录对象的高级查询功能

**类型：** 新功能  
**服务类别：** MS Graph  
**产品功能：** 开发人员体验
 
Azure AD API 中为目录对象引入的所有新查询功能现在都可在 v1.0 终结点中使用，并且已做好生产准备。 开发人员可使用标准 OData 运算符对目录对象和相关链接进行计数、搜索、筛选和排序。

若要了解详细信息，请参阅[此处](https://aka.ms/BlogPostMezzoGA)的文档，还可以通过此[简短调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)发送反馈。
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>公共预览版：适用于配置了条件访问策略的租户的连续访问评估

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 标识安全和保护
 
连续访问评估 (CAE) 现在提供公共预览版，适用于具有条件访问策略的 Azure AD 租户。 使用 CAE，可实时评估严重安全事件和策略。 这包括帐户禁用、密码重置和位置更改。 若要了解详细信息，请参阅[连续访问评估](../conditional-access/concept-continuous-access-evaluation.md)。

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>公共预览版：向请求访问包的用户提出附加问题以改进审批决策

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
管理员现在可以要求请求访问包的用户在 Azure AD 权利管理的“我的访问权限”门户中回答除业务理由以外的附加问题。 随后，用户答案将呈现给审批者，以帮助他们做出更准确的访问审批决策。 若要了解详细信息，请参阅[收集其他请求者信息以进行审批](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval)。
 
---

### <a name="public-preview-enhanced-user-management"></a>公共预览版：增强用户管理

**类型：** 新功能  
**服务类别：** 用户管理  
**产品功能：** 用户管理
 

Azure AD 门户已更新，用户可以更轻松地在“所有用户”和“已删除的用户”页中查找用户。 预览版包括以下更改： 
- 更直观的用户属性，包括对象 ID、目录同步状态、创建类型和标识颁发者。
- 搜索功能现在允许对名称、电子邮件和对象 ID 进行合并搜索。
- 增强了筛选，现在可按用户类型（成员、来宾和无）、目录同步状态、创建类型、公司名称和域名进行筛选。
- 新增了按属性（如名称、用户主体名称和删除日期）排序的功能。
- 使用任何搜索或筛选器进行更新的新用户总数。

有关详细信息，请参阅 [Azure Active Directory 中的用户管理增强功能（预览版）](../enterprise-users/users-search-enhanced.md)。

---

### <a name="new-notes-field-for-enterprise-applications"></a>适用于企业应用程序的新注释字段

**类型：** 新功能  
**服务类别：** 企业应用 **产品功能：** SSO

你可以向企业应用程序添加免费的文本注释。 可以添加任何有助于管理企业应用程序下的应用程序的相关信息。 有关详细信息，请参阅[快速入门：在 Azure Active Directory (Azure AD) 租户中配置应用程序的属性](../manage-apps/add-application-portal-configure.md)。 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD 应用程序库推出了新联合应用 - 2020 年 9 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020 年 9 月，我们在应用库中添加了以下 34 个支持联合的新应用程序：

[VMware Horizon - Unified Access Gateway]()、[Pulse Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md)、[Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md)、[Frontitude](https://services.enteksystems.de/sso/microsoft/signup)、[BookWidgets](https://www.bookwidgets.com/sso/office365)、[ZVD_Server](https://zaas.zenmutech.com/user/signin)、[HashData for Business](https://hashdata.app/login.xhtml)、[SecureLogin](https://securelogin.securelogin.nu/sso/azure/login)、[CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md)、[CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md)、[LimbleCMMS](https://auth.limblecmms.com/)、[Glint Inc](../saas-apps/glint-inc-tutorial.md)、[zeroheight](../saas-apps/zeroheight-tutorial.md)、[Gender Fitness](https://app.genderfitness.com/)、[Coeo Portal](https://my.coeo.com/)、[Grammarly](../saas-apps/grammarly-tutorial.md)、[Fivetran](../saas-apps/fivetran-tutorial.md)、[Kumolus](../saas-apps/kumolus-tutorial.md)、[RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md)、[TeamzSkill](../saas-apps/teamzskill-tutorial.md)、[raumfürraum](../saas-apps/raumfurraum-tutorial.md)、[Saviynt](../saas-apps/saviynt-tutorial.md)、[BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup)、[Mobile Locker](../saas-apps/mobile-locker-tutorial.md)、[Zengine](../saas-apps/zengine-tutorial.md)、[CloudCADI](https://app.cloudcadi.com/login)、[Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/)、[Priva Identity & Access Management](https://my.priva.com/)、[Nitro Pro](https://www.gonitro.com/nps/product-details/downloads)、[Eventfinity](../saas-apps/eventfinity-tutorial.md)、[Fexa](../saas-apps/fexa-tutorial.md)、[Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、[Secured Signing Enterprise Portal AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、[Wistec Online](https://wisteconline.com/auth/oidc)、[Oracle PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档。

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息。

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD 权利管理中的新委托角色：访问包分配管理者

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
Azure AD 权利管理中已添加了一个新的访问包分配管理者角色，用于提供管理分配的具体权限。 你现在可将任务委托给拥有此角色的用户，而该用户可以将访问包的分配管理委托给企业所有者。 但是，访问包分配管理者不能更改管理员设置的访问包策略或其他属性。 

拥有这一新角色后，委托分配管理并对所有其他访问包配置保持管理控制将只需最少的特权。 若要了解详细信息，请参阅[权利管理角色](../governance/entitlement-management-delegate.md#entitlement-management-roles)。
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>对 Privileged Identity Management 的加入流的更改

**类型：** 已更改的功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
以前，加入 Privileged Identity Management (PIM) 需要用户同意，并且需要使用 PIM 的边栏选项卡中的加入流（包括在 Azure AD MFA 中注册）。 由于最近已将 PIM 体验集成到 Azure AD 角色和管理员边栏选项卡中，我们将删除这一体验。 具有有效 P2 许可证的任何租户都将自动加入 PIM。

加入 PIM 对你的租户没有任何直接负面影响。 可能会发生以下更改：
- 当你在 PIM 或 Azure AD 角色和管理员边栏选项卡中进行分配时，会有额外的分配选项（如活动与合格，以及开始时间和结束时间）。 
- 分配体验中直接引入了管理单元和自定义角色等其他范围机制。 
- 如果你是全局管理员或特权角色管理员，可以开始获取几封额外的电子邮件，如 PIM 每周摘要。 
- 你还可能会在与角色分配有关的审核日志中看到 ms-pim 服务主体。 这种预期的更改应该不会影响你的常规工作流。

 有关详细信息，请参阅[开始使用 Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)。

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD 权利管理：访问包资源的“选择”窗格现在默认显示当前所选目录中的资源

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 

在访问包创建流中，“资源角色”选项卡下的“选择”窗格行为将发生更改。 当前，默认行为是显示用户拥有的所有资源，以及添加到所选目录的资源。 

此体验将更改为默认情况下仅显示当前添加到目录中的资源，以便用户可以从目录中轻松选取资源。 此更新将有助于发现要添加到访问包的资源，并降低意外添加用户拥有但不属于目录的资源的风险。 若要了解详细信息，请参阅[在 Azure AD 权利管理中创建新访问包](../governance/entitlement-management-access-package-create.md#resource-roles)。
 
---

## <a name="august-2020"></a>2020 年 8 月 
 
### <a name="updates-to-azure-active-directory-multi-factor-authentication-server-firewall-requirements"></a>关于 Azure Active Directory 多重身份验证服务器防火墙的要求更新

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
从 2020 年 10 月 1 日开始，Azure MFA 服务器防火墙就要求提供额外的 IP 范围。

如果组织中有出站防火墙规则，请更新规则，以便 MFA 服务器可以与所有必要的 IP 范围通信。 IP 范围记录在 [Azure ActiveDirectory 多重身份验证服务器防火墙要求](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)中。

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>对标识安全分数中的用户体验即将进行的更改

**类型：** 更改计划  
**服务类别：** 标识保护 **产品功能：** 标识安全和保护

我们即将更新标识安全分数门户，以与引入 Microsoft 安全功能分数[新版本](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)的更改相匹配。 

更改后的预览版将于 9 月初推出。 预览版中的更改包括：
- “标识安全分数”已重命名为“标识安全功能分数”，以与 Microsoft 安全功能分数保持品牌一致
- 分数已规范化为标准缩放，并以百分数而不是分数的形式进行报告

在此预览版中，客户可以在现有体验和新体验之间切换。 此预览版会持续到 2020 年 11 月底。 预览版结束后，客户将自动定向到新的 UX 体验。

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD 中新的受限来宾访问权限（公共预览版）

**类型：** 新功能  
**服务类别：** 访问控制   
**产品功能：** 用户管理

我们已为来宾用户更新了目录级别权限。 这些权限允许管理员对外部来宾用户访问权限进行其他限制和控制。 管理员现在可以为外部来宾对用户和组的配置文件以及成员身份信息的访问添加其他限制。 利用此公共预览版的功能，客户可以通过模糊处理组成员身份，大规模管理外部用户访问权限，包括限制来宾用户查看其所在组的成员身份。

有关详细信息，请参阅[受限来宾访问权限](../enterprise-users/users-restrict-guest-permissions.md)和[用户默认权限](./users-default-permissions.md)。
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>服务主体 delta 查询的正式发布

**类型：** 新功能  
**服务类别：** MS Graph  
**产品功能：** 开发人员体验
 
Microsoft Graph Delta 查询现在支持 v1.0 中的资源类型：
- Service Principal

客户端现在可以有效跟踪对这些资源的更改，并提供使这些更改与本地数据存储同步的最佳解决方案。 若要了解如何在查询中配置这些资源，请参阅[使用 delta 查询跟踪 Microsoft Graph 数据中的更改](/graph/delta-query-overview)。
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>OAuth2PermissionGrant delta 查询的正式发布

**类型：** 新功能  
**服务类别：** MS Graph  
**产品功能：** 开发人员体验

Microsoft Graph Delta 查询现在支持 v1.0 中的资源类型：
- OAuth2PermissionGrant

客户端现在可以有效跟踪对这些资源的更改，并提供使这些更改与本地数据存储同步的最佳解决方案。 若要了解如何在查询中配置这些资源，请参阅[使用 delta 查询跟踪 Microsoft Graph 数据中的更改](/graph/delta-query-overview)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD 应用程序库提供了新的联合应用 - 2020 年 8 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020 年 8 月，我们在应用库中添加了以下 25 个支持联合的新应用程序：

[Backup365](https://portal.backup365.io/login)、[Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2)、[Alma SIS](https://almau.getalma.com/)、[Enlyft Dynamics 365 Connector](http://enlyft.com/)、[Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md)、[Uniq](https://web.uniq.app/)、[Visibly](../saas-apps/visibly-tutorial.md)、[Zylo](../saas-apps/zylo-tutorial.md)、[Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login)、[CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome)、[Altamira HRM](../saas-apps/altamira-hrm-tutorial.md)、[WireWheel](../saas-apps/wirewheel-tutorial.md)、[Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)、[Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md)、[Genetec Clearance](https://www.clearance.network/)、[iSAMS](../saas-apps/isams-tutorial.md)、[VeraSMART](../saas-apps/verasmart-tutorial.md)、[Amiko](https://amiko.web.rivero.app/)、[Twingate](https://auth.twingate.com/signup)、[Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/)、[Scalefusion](https://scalefusion.com/users/sign_in/)、[Bpanda](https://goto.bpanda.com/login)、[Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect)、[FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)、[Wandera End User](https://www.wandera.com/)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>资源林现在可用于 Azure AD DS 

**类型：** 新功能 **服务类别：** Azure AD 域服务   
**产品功能：** Azure AD 域服务
 
Azure AD 域服务中的资源林功能现已正式发布。 你现在可以在没有密码哈希同步的情况下启用授权（如智能卡授权）来使用 Azure AD 域服务。 有关详细信息，请参阅 [Azure Active Directory 域服务的副本集概念和功能（预览版）](../../active-directory-domain-services/concepts-replica-sets.md)。
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>现已提供对 Azure AD DS 托管域的区域副本支持

**类型：** 新功能   
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务
 
可以扩展托管域，使每个 Azure AD 租户具有多个副本集。 副本集现在可以添加到支持 Azure AD 域服务的任何 Azure 区域中的任何对等网络。 如果某个 Azure 区域处于离线状态，则不同 Azure 区域中的其他副本集可为旧版应用程序提供地理灾难恢复。 有关详细信息，请参阅 [Azure Active Directory 域服务的副本集概念和功能（预览版）](../../active-directory-domain-services/concepts-replica-sets.md)。

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD My Sign-Ins 的正式发布

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 最终用户体验
 
Azure AD My Sign-Ins 是一项新功能，允许企业用户查看其登录历史记录以检查是否有任何异常活动。 此功能还允许最终用户就可疑活动发出报告“是我本人”或“不是我本人”。 有关使用此功能的详细信息，请参阅[在 My Sign-Ins 页查看并搜索最近的登录活动](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)。
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>从 SAP SuccessFactors 到 Azure AD 的 HR 驱动的用户预配现已正式发布

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
你现在可以将 SAP SuccessFactors 作为权威标识源与 Azure AD 集成，并利用 HR 事件（如新员工和终止）使端到端的标识生命周期自动化，以推动 Azure AD 帐户的预配和取消预配。 

有关如何将 SAP SuccessFactors 入站设置预配到 Azure AD 的详细信息，请参阅教程[配置从 SAP SuccessFactors 到 Active Directory 的用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Azure AD B2C 的 Custom Open ID Connect MS Graph API 支持

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
Custom Open ID Connect 提供程序以前只能通过 Azure 门户进行添加或管理。 Azure AD B2C 客户现在还可通过 Microsoft Graph API beta 版添加和管理这些提供程序。 有关如何利用 API 配置此资源的详细信息，请参阅 [identityProvider 资源类型](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true)。
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>将 Azure AD 内置角色分配到云组

**类型：** 新功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制

你现在可以利用此功能将 Azure AD 内置角色分配到云组 例如，可以将 SharePoint 管理员角色分配到 Contoso_SharePoint_Admins 组。 还可以使用 PIM 让组成为该角色的合格成员，而不是授予现有访问权限。 有关如何配置此功能的详细信息，请参阅[在 Azure Active Directory 中使用云组管理角色分配（预览版）](../roles/groups-concept.md)
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>现已推出 Insights 商业领袖内置角色

**类型：** 新功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
角色为 Insights 商业领袖的用户可以通过 [Microsoft 365 Insights 应用程序](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)访问一组仪表板和见解。 其中包括对所有仪表板以及提供的见解和数据探索功能的完全访问权限。 但是此角色的用户无权访问由“Insights 管理员”角色负责的产品配置设置。 有关此角色的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>现已推出 Insights 管理员内置角色

**类型：** 新功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
角色为 Insights 管理员的用户可以访问 [Microsoft 365 Insights 应用程序](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)中的全套管理功能。 此角色用户可以读取目录信息，监视服务运行状况，将支持工单归档，以及访问 Insights 管理员设置方面的内容。 有关此角色的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>应用程序管理员和云应用程序管理员可以管理应用程序的扩展属性

**类型：** 已更改的功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
以前只有全局管理员可以管理[扩展属性](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true)。 我们现在也为应用程序管理员和云应用程序管理员启用了此功能。
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 修补程序 4.6.263.0 和连接器 1.1.1301.0

**类型：** 已更改的功能  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理

[修补程序汇总包（内部版本 4.6.263.0）](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident)可用于 Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)。 此汇总包包含对 MIM CM、MIM 同步管理器和 PAM 组件的更新。 MIM 通用连接器（内部版本 1.1.1301.0）还包含对 Graph 连接器的更新。

---

## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>作为一名 IT 管理员，我想要使用条件访问来面向客户端应用

**类型：** 更改计划   
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
在条件访问中，随着客户端应用条件的 GA 版本的推出，新策略将默认应用于所有客户端应用程序。 其中包括旧身份验证客户端。 现有策略将保持不变，但是“配置(是/否)”切换开关会从现有策略中删除，以便轻松查看策略所应用的客户端应用。 

创建新策略时，请确保排除仍使用旧身份验证的用户和服务帐户；否则它们将会被阻止访问。 [了解详细信息](../conditional-access/concept-conditional-access-conditions.md)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>即将推出 SCIM 合规性修补程序

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务利用 SCIM 标准与应用程序集成。 SCIM 标准的实现正不断发展，我们希望就如何执行补丁操作以及如何设置资源上的“active”属性方面改变我们的行为。 [了解详细信息](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>将更改 Azure 管理员门户上的组所有者设置

**类型：** 更改计划  
**服务类别：** 组管理  
**产品功能：** 协作

组常规设置页上的所有者设置可以配置为在 Azure 管理门户和访问面板中将所有者分配权限限制为一组有限的用户。 我们很快就可以在这两个 UX 门户上分配组所有者特权，但也可以在后端强制执行策略，以跨终结点（例如 PowerShell 和 Microsoft Graph）提供一致的行为。 

我们将开始为不使用它的客户禁用当前设置，并将在接下来的几个月中提供一个选项来确定用户是否拥有组所有者权限。 有关更新组设置的指导，请参阅“使用 [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) 编辑组信息”。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 注册服务即将终止对 TLS 1.0 和 1.1 的支持

**类型：** 更改计划  
**服务类别：** 设备注册和管理  
**产品功能：** 平台

传输层安全性 (TLS) 1.2、更新服务器和客户端不久将会与 Azure Active Directory 设备注册服务通信。 而 TLS 1.0 和 1.1 与 Azure AD 设备注册服务的通信支持将停用，详情如下：
- 2020 年 8 月 31 日，在所有主权云中（GCC High 和 DoD 等）停用
- 2020 年 10 月 30 日，在所有商业云中停用

[详细了解](../devices/reference-device-registration-tls-1-2.md) Azure AD 注册服务的 TLS 1.2。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows Hello 企业版登录将显示在 Azure AD 登录日志中

**类型：** 已修复  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
Windows Hello 企业版允许最终用户使用手势（如 PIN 或生物识别）登录 Windows 计算机。 Azure AD 管理员可能希望将 Windows Hello 企业版登录与其他 Windows 登录区分开来，这是组织实现无密码身份验证的一部分。 

管理员现在可以通过在 Azure 门户的 Azure AD“登录”边栏选项卡中选中 Windows 登录事件的“身份验证详细信息”选项卡，来查看 Windows 身份验证是否使用了 Windows Hello 企业版。 Windows Hello 企业版身份验证包含“身份验证方法”字段中的“WindowsHelloForBusiness”。 有关解释登录日志的详细信息，请参阅[登录日志文档](../reports-monitoring/concept-sign-ins.md)。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>对组删除行为的修复和性能改进

**类型：** 已修复  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
以前当某个组从“范围内”更改为“范围外”，并且管理员在完成更改之前单击“重新启动”时，不会删除组对象。 现在当组对象超出范围（禁用、删除、未分配或未通过范围筛选器）时，会从目标应用程序删除。 [了解详细信息](../app-provisioning/how-provisioning-works.md#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>公共预览版：管理员现在可以在创建访问评审时向发送给审阅者的电子邮件添加自定义内容

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识治理
 
创建新的访问评审后，审阅者会收到一封要求其完成访问评审的电子邮件。 许多客户要求能够向电子邮件添加自定义内容（如联系信息或其他支持内容）以指导审阅者。 

公共预览版现在提供了此功能，管理员可以通过在 Azure AD 访问评审的“高级”部分添加内容来指定发送给审阅者的电子邮件中的自定义内容。 有关创建访问评审的指导，请参阅 [在 Azure AD 访问评审中创建组和应用程序的访问评审](../governance/create-access-review.md)。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>提供单页面应用的授权代码流

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 开发人员体验
 
由于新式浏览器第三方 cookie 限制（如 Safari ITP），SPA 必须使用授权代码流（而不是隐式流）来维护 SSO；MSAL.js v 2.x 现在支持授权代码流。 

Azure 门户有相应更新，因此你可以将 SPA 更新为类型“SPA”并使用授权代码流。 请参阅[使用授权代码流在 JavaScript SPA 中让用户登录并获取访问令牌](../develop/quickstart-v2-javascript-auth-code.md)以获取进一步指导。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD 应用程序代理现在支持远程桌面服务 Web 客户端

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制

Azure AD 应用程序代理现在支持远程桌面服务 (RDS) Web 客户端。 RDS Web 客户端允许用户通过任何支持 HTLM5 的浏览器（如 Microsoft Edge、Internet Explorer 11 和 Google Chrome 等）访问远程桌面基础结构。用户可以与远程应用或桌面进行交互，就像从任何位置与本地设备进行交互一样。 在使用 Azure AD 应用程序代理的情况下，你可以通过对所有类型的丰富客户端应用强制执行预身份验证和条件访问策略，来提高 RDS 部署的安全性。 有关指南，请参阅[使用 Azure AD 应用程序代理发布远程桌面](../app-proxy/application-proxy-integrate-with-remote-desktop-services.md)。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>下一代 Azure AD B2C 用户流处于公共预览阶段

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
简化的用户流体验提供与预览功能的功能奇偶一致性，并且包含所有新功能。 用户将能够在同一用户流中启用新功能，从而减少了在每个新功能发布时创建多个版本的需要。 最后，新的用户友好型 UX 可以简化用户流的选择和创建。 立即尝试[创建用户流](../../active-directory-b2c/tutorial-create-user-flows.md)。 

有关用户流的详细信息，请参阅 [Azure Active Directory B2C 中的用户流版本](../../active-directory-b2c/user-flow-versions.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD 应用程序库推出了新的联合应用 - 2020 年 7 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 7 月，我们在应用库中添加了以下 55 个支持联合的新应用程序：

[Clap Your Hands](http://www.rmit.com.ar/)、[Appreiz](https://microsoftteams.appreiz.com/)、[Inextor Vault](https://inexto.com/inexto-suite/inextor)、[Beekast](https://my.beekast.com/)、[Templafy OpenID Connect](https://app.templafy.com/)、[PeterConnects receptionist](https://msteams.peterconnects.com/)、[AlohaCloud](https://appfusions.alohacloud.com/auth)、[Control Tower](https://bpm.tnxcorp.com/sso/microsoft)、[Cocoom](https://start.cocoom.com/)、[COINS Construction Cloud](https://sso.coinsconstructioncloud.com/#login/)、[Medxnote MT](https://task.teamsmain.medx.im/authorization)、[Reflekt](https://reflekt.konsolute.com/login)、[Rever](https://app.reverscore.net/access)、[MyCompanyArchive](https://login.mycompanyarchive.com/)、[GReminders](https://app.greminders.com/o365-oauth)、[Titanfile](../saas-apps/titanfile-tutorial.md)、[Wootric](../saas-apps/wootric-tutorial.md)、[SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)、 [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md)、[Datasite](../saas-apps/datasite-tutorial.md)、[BlogIn](../saas-apps/blogin-tutorial.md)、[IntSights](../saas-apps/intsights-tutorial.md)、[kpifire](../saas-apps/kpifire-tutorial.md)、[Textline](../saas-apps/textline-tutorial.md)、[Cloud Academy - SSO](../saas-apps/cloud-academy-sso-tutorial.md)、[Community Spark](../saas-apps/community-spark-tutorial.md)、[Chatwork](../saas-apps/chatwork-tutorial.md)、[CloudSign](../saas-apps/cloudsign-tutorial.md)、[C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md)、[SmartHR](https://smarthr.jp/)、[NumlyEngage™](../saas-apps/numlyengage-tutorial.md)、[Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md)、[Egress](../saas-apps/egress-tutorial.md)、[SendSafely](../saas-apps/sendsafely-tutorial.md)、[Eletive](https://app.eletive.com/)、[Right-Hand Cybersecurity ADI](https://right-hand.ai/)、[Fyde Enterprise Authentication](https://enterprise.fyde.com/)、[Verme](../saas-apps/verme-tutorial.md)、[Lenses.io](../saas-apps/lensesio-tutorial.md)、[Momenta](../saas-apps/momenta-tutorial.md)、[Uprise](https://app.uprise.co/sign-in)、[Q](https://q.moduleq.com/login)、[CloudCords](../saas-apps/cloudcords-tutorial.md)、[TellMe Bot](https://tellme365liteweb.azurewebsites.net/)、[Inspire](https://app.inspiresoftware.com/)、[Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/)、[Smartschool (School Management System)](https://smartschoolz.com/login)、[Zepto - Intelligent timekeeping](https://user.zepto-ai.com/signin)、[Studi.ly](https://studi.ly/)、[Trackplan](http://www.trackplanfm.com/)、[Skedda](../saas-apps/skedda-tutorial.md)、[WhosOnLocation](../saas-apps/whos-on-location-tutorial.md)、[Coggle](../saas-apps/coggle-tutorial.md)、[Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)、[BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>查看所有范围内的角色分配，并且能够将其下载到 csv 文件

**类型：** 已更改的功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
你现在可以在 Azure AD 门户的“角色和管理员”选项卡中查看某个角色在所有范围内的角色分配。 还可以将每个角色的角色分配下载到 CSV 文件中。 有关查看并添加角色分配的指导，请参阅[在 Azure Active Directory 中查看并分配管理员角色](../roles/manage-roles-portal.md)。
 
---

### <a name="azure-active-directory-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Active Directory 多重身份验证软件开发 (Azure MFA SDK) 的弃用

**类型：** 已弃用  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
在 2017 年 11 月首次公布的 Azure Active Directory 多重身份验证软件开发 (Azure MFA SDK) 已于 2018 年 11 月 14 日弃用。 Microsoft 将在 2020 年 9 月 30 日关闭 SDK 服务。 任何对 SDK 的调用都将失败。

如果你的组织正在使用 Azure MFA SDK，则需要在 2020 年 9 月 30 日之前进行迁移：
- 用于 MIM 的 Azure MFA SDK：如果将 SDK 与 MIM 一起使用，则应将其迁移到 Azure MFA 服务器，并遵循以下[说明](/microsoft-identity-manager/working-with-mfaserver-for-mim)激活 Privileged Access Management (PAM)。   
- 适用于自定义应用的 Azure MFA SDK：请考虑将应用集成到 Azure AD 并使用条件访问强制执行 MFA。 若要快速入门，请查看此[页面](../manage-apps/plan-an-application-integration.md)。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>条件访问策略中的用户风险条件

**类型：** 更改计划  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 

Azure AD 条件访问策略中的用户风险支持允许创建多个用户风险策略。 不同的用户和应用可能需要不同的最低用户风险级别。 根据用户风险，你可以创建策略来阻止访问，要求多重身份验证，保证密码更改的安全性或者重定向到 Microsoft Cloud App Security 来强制执行会话策略（如额外审核）。

用户风险条件需要 Azure AD Premium P2，因为它使用的是 Azure 标识保护，这是一项 P2 服务。 有关条件访问的详细信息，请参阅 [Azure AD 条件访问文档](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>有请求时，SAML SSO 现在支持要求设置 SPNameQualifier 的应用

**类型：** 已修复  
**服务类别：** 企业应用  
**产品功能：** SSO
 
某些 SAML 应用程序在有请求时要求在断言主题中返回 SPNameQualifier。 当 NameID 策略请求中提出 SPNameQualifier 请求时，Azure AD 现在可以正确响应。 这也适用于 SP 发起的登录，以及 IdP 发起的登录。  有关 Azure Active Directory 中的 SAML 协议的详细信息，请参阅[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>在 Azure 政府租户中，Azure AD B2B 协作支持邀请 MSA 和 Google 用户

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

使用 B2B 协作功能的 Azure 政府租户现在可以邀请具有 Microsoft 或 Google 帐户的用户。 若要查明你的租户是否可以使用这些功能，请遵循[如何判断我的 Azure 美国政府租户是否提供 B2B 协作功能？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)中的说明

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 的用户对象现在包含 externalUserState 和 externalUserStateChangedDateTime 属性

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

ExternalUserState 和 externalUserStateChangedDateTime 属性可用于查找受邀但是还没有接受邀请的 B2B 来宾，还可用于生成自动化，如删除在数天后仍未接受其邀请的用户。 现在，这些属性在 MS Graph v1 中可用。 有关使用这些属性的指南，请参阅[用户资源类型](/graph/api/resources/user)。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD 条件访问中的身份验证会话管理功能现已正式发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
通过身份验证会话管理功能，你可以配置用户提供登录凭据的频率，以及用户在关闭和重新打开浏览器后是否需要提供凭据，以在环境中提供更高的安全性和灵活性。
 
此外，身份验证会话管理通常仅适用于 Azure AD 联接、混合 Azure AD 联接和 Azure AD 注册设备上的单因素身份验证。 身份验证会话管理现在也适用于 MFA。 有关详细信息，请参阅[使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 应用程序库推出了新的联合应用 - 2020 年 6 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 6 月，我们在应用库中添加了以下 29 个支持联合的新应用程序：

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md)、[Ekarda](../saas-apps/ekarda-tutorial.md)、[MailGates](../saas-apps/mailgates-tutorial.md)、[BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md)、[Raketa](../saas-apps/raketa-tutorial.md)、[Segment](../saas-apps/segment-tutorial.md)、[Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/)、[Pobuca Connect](https://app.pobu.ca/)、[Proto.io](../saas-apps/proto.io-tutorial.md)、[Gatekeeper](https://www.gatekeeperhq.com/)、[Hub Planner](../saas-apps/hub-planner-tutorial.md)、[Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement)、[IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md)、[Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md)、[ViewpointOne](https://team.viewpoint.com/)、[IntelligenceBank](../saas-apps/intelligencebank-tutorial.md)、[pymetrics](../saas-apps/pymetrics-tutorial.md)、[Zero](https://www.teamzero.com/)、[InStation](https://instation.invillia.com/)、[edX for Business SAML 2.0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md)、[MOOC Office 365](https://mooc.office365-training.com/en/)、[SmartKargo](../saas-apps/smartkargo-tutorial.md)、[PKIsigning platform](https://platform.pkisigning.nl/)、[SiteIntel](../saas-apps/siteintel-tutorial.md)、[Field iD](../saas-apps/field-id-tutorial.md)、[Curricula SAML](../saas-apps/curricula-saml-tutorial.md)、[Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md)、[MyCompliance Cloud](https://cloud.metacompliance.com/)、[Smallstep SSH](https://smallstep.com/sso-ssh/)  

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档。 有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>用于外部标识自助注册的 API 连接器现在提供公共预览版

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
外部标识 API 连接器使你能够利用 Web API 将自助注册与外部云系统集成。 这意味着，你现在可以将 Web API 作为注册流中的特定步骤调用，以触发基于云的自定义工作流。 例如，你可以使用 API 连接器执行以下操作：

- 与自定义批准工作流集成。
- 执行标识核验
- 验证用户输入数据
- 覆盖用户属性
- 运行自定义业务逻辑

有关 API 连接器所有可能的体验的详细信息，请参阅[使用 API 连接器自定义和扩展自助注册](../external-identities/api-connectors-overview.md)，或[利用 Web API 集成自定义外部标识自助注册](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>按需预配，使用户能够在几秒钟内进入应用

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务目前以循环方式运行。 该服务每 40 分钟运行一次。 使用[按需预配功能](https://aka.ms/provisionondemand)，你可以在几秒内选取用户并完成用户预配。 此功能可使你快速排查配置问题，无需通过重启来重新开始配置周期。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>在 Graph 中使用 Azure AD 权利管理的新权限

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理
 
新的委托权限 EntitlementManagement.Read.All 现在可以与 Microsoft Graph beta 版中的权利管理 API 一起使用 有关可用 API 的详细信息，请参阅[使用 Azure AD 权利管理 API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)。

---

### <a name="identity-protection-apis-available-in-v10"></a>v1.0 中可用的标识保护 API

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 
riskyUsers 和 riskDetections Microsoft Graph API 现已正式发布。 现在这些 API 已在 v1.0 终结点提供，可以将其用于生产环境。 有关详细信息，请查看 [Microsoft Graph 文档](/graph/api/resources/identityprotectionroot)。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>将策略应用于 Microsoft 365 组的敏感度标签现已正式发布

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作
 

你现在可以创建敏感度标签，并使用标签设置将策略应用于 Microsoft 365 组，其中包括隐私（公用或专用）和外部用户访问策略。 你可以创建一个标签，将隐私策略设置为专用，并将外部用户访问策略设置为不允许添加来宾用户。 当用户将此标签应用于组时，该组将为“专用”，并且不允许向该组添加来宾用户。 

敏感度标签很重要，可以保护你的关键数据，并且使你能够以合规且安全的方式大规模管理组。 有关使用敏感度标签的指导，请参阅[为 Azure Active Directory 中的 Microsoft 365 组分配敏感度标签（预览版）](../enterprise-users/groups-assign-sensitivity-labels.md)。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>为 Azure AD Premium 客户更新 Microsoft Identity Manager 支持

**类型：** 已更改的功能  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理
 
Microsoft Identity Manager 2016 的扩展支持结束后，Azure 支持现在可用于 Microsoft Identity Manager 2016 的集成组件。 有关详细信息，请参阅[为使用 Microsoft Identity Manager 的 Azure AD Premium 客户更新支持](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO 声明配置中组成员身份的使用限制已增加

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
以前，当你根据任何单个应用程序配置中的组成员身份有条件地更改声明时，可以使用的组数限制为 10 个。 现在，SSO 声明配置中组成员身份的使用限制已增加到最大值 50 组。 有关如何配置声明的详细信息，请参阅[企业应用程序 SSO 声明配置](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>支持在公司品牌的“登录页面文本”组件上进行基本格式设置。

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
Azure AD/Microsoft 365 登录体验上的公司品牌功能已更新，允许客户添加超链接和简单的格式，其中包括粗体、下划线和斜体等。 有关使用此功能的指导，请参阅[将品牌添加到组织的 Azure Active Directory 登录页面](./customize-branding.md)。

---

### <a name="provisioning-performance-improvements"></a>预配性能改进

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
预配服务已更新，缩短了[增量周期](../app-provisioning/how-provisioning-works.md#incremental-cycles)的完成时间。 这意味着将用户和组预配到其应用程序中的速度比以前更快。 在 2020 年 10 月 6 日之后创建的所有新预配作业都将自动获益于此性能改进。 在 2020 年 10 月 6 日之前为预配配置的任何应用程序都需要在 2020 年 10 月 6 日后重启一次，以便充分利用性能改进。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>宣布弃用 ADAL 和 MS Graph 奇偶校验

**类型：** 已弃用  
**服务类别：** N/A  
**产品功能：** 设备识生命周期管理

现在已推出 Microsoft 身份验证库 (MSAL)，我们将不再为 Azure Active Directory 身份验证库 (ADAL) 添加新功能并在 2022 年 6 月 30 日弃用安全修补程序。 有关如何迁移到 MSAL 的详细信息，请参阅[将应用程序迁移到 Microsoft 身份验证库 (MSAL)](../develop/msal-migration.md)。

我们还完成了使所有 Azure AD Graph 功能在 MS Graph 中均可用的工作。 因此 Azure AD Graph API 在 2022 年 6 月 30 日之前仅接收 Bug 修复和安全修补程序。 有关详细信息，请参阅[更新应用程序以使用 Microsoft 身份验证库 (MSAL) 和 Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
## <a name="may-2020"></a>2020 年 5 月

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>停用 signIn、riskyUser 和 riskDetection API 中的属性

**类型：** 更改计划  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

枚举类型目前用于表示 riskDetection API 和 riskyUserHistoryIte 中的 riskType 属性（预览版）。 枚举类型还用于 signIn API 中的 riskEventType 属性。 接下来我们会用字符串表示这些属性。 

客户应在 2020 年 9 月 9 日之前转换为 beta riskDetection 和 riskyUserHistoryItem API 中的 riskEventType 属性以及 beta signIn API 中的 riskEventType_v2 属性。 我们会在该日期停用当前的 riskType 和 riskEventType 属性。 有关详细信息，请参阅[对 Microsoft Graph 的风险事件属性和标识保护 API 的更改](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)。

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>弃用 Microsoft Graph 的 signIn v1.0 API 中的 riskEventType 属性

**类型：** 更改计划  
**服务类别：** 报告  
**产品功能：** 标识安全和保护

2020 年 9 月，枚举类型在表示 Microsoft Graph 中的风险事件类型时，会切换为字符串类型。 此更改除影响预览 API 以外，还会影响生产中的 signIn API。

我们为 signIn v1.0 API 引入了新的 riskEventsType_v2（字符串）属性。 在 2022 年 6 月 11 日，我们将根据 Microsoft Graph 弃用政策，停用当前的 riskEventType（枚举）属性。 客户应在 2022 年 6 月 11 日之前转换为 v1.0 signIn API 中的 riskEventType_v2 属性。 有关详细信息，请参阅[弃用 Microsoft Graph 的 signIn v1.0 API 中的 riskEventType 属性](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)。

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>对 MFA 电子邮件通知即将进行的更改

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 

我们将对云 MFA 的电子邮件通知进行以下更改：

电子邮件通知将从以下地址发送：azure-noreply@microsoft.com 和 msonlineservicesteam@microsoftonline.com。 我们会对欺诈警报电子邮件的内容进行更新，以便更好地指明开启使用所需的步骤。

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>新的自助服务会为联合域中的用户注册。这些用户因没有同步到 Azure Active Directory 而无法访问 Microsoft Teams。

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 

当前，如果用户位于 Azure AD 的联合域中，但未同步到租户，则无法访问 Teams。 从 6 月底开始，这项新功能将通过扩展现有电子邮件验证的注册功能来使他们能够执行此操作。 这将允许可以登录到联合 IP 但在 Azure ID 中没有用户对象的用户拥有自动创建的用户对象，并进行身份验证以访问 Teams。 其用户对象将被标记为“自助注册”。 这是对现有电子邮件验证的自助注册功能的扩展，可以由托管域中的用户执行并且可以使用同一标志进行控制。 此更改将在接下来两个月内完全推出。 可在[此处](../enterprise-users/directory-self-service-signup.md)查看文档更新。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>即将推出的修复程序：Azure 政府云的 OIDC 发现文档正在更新，以引用正确的 Graph 终结点。

**类型：** 更改计划  
**服务类别：** 主权云  
**产品功能：** 用户身份验证
 
从 6 月开始，[Azure 政府云](../develop/authentication-national-cloud.md)终结点 (login.microsoftonline.us) 上的 OIDC 发现文档、[Microsoft 标识平台和 OpenID Connect 协议](../develop/v2-protocols-oidc.md)，将开始根据提供的租户返回正确的[国家/地区云图](/graph/deployments)终结点 (https://graph.microsoft.us 或 https://dod-graph.microsoft.us) 。  它目前提供的 Graph 终结点 (graph.microsoft.com) 的“msgraph_host”字段是错误的。  

此 Bug 修复程序将在大约 2 个月内逐步推出。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure 政府用户将无法再在 login.microsoftonline.com 上登录。

**类型：** 更改计划  
**服务类别：** 主权云  
**产品功能：** 用户身份验证
 
在 2018 年 6 月 1 日，Azure 政府的官方 Azure Active Directory (Azure AD) 授权已从 https://login-us.microsoftonline.com 更改为 https://login.microsoftonline.us 。 如果你在 Azure 政府租户中拥有应用程序，则必须更新应用程序以在 .us 终结点让用户登录。

从 5 月 5 日开始，Azure AD 将开始强制更改终结点，阻止 Azure 政府用户使用公用终结点 (microsoftonline.com) 登录托管在 Azure 政府租户中的应用。 受影响的应用会显示错误 AADSTS900439 - USGClientNotSupportedOnPublicEndpoint。 

此更改将逐步推出，并且预期在 2020 年 6 月对所有应用强制完成此更改。 有关详细信息，请参阅 [Azure 政府的博客文章](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)。

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML 单一注销请求现在会发送正确格式的 NameID

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
当用户单击“注销”时（例如，在 MyApps 门户中），Azure AD 会向每个应用发送 SAML 单一注销消息。这些应用在用户会话中处于活动状态并且配置了注销 URL。 这些消息包含具有持久格式的 NameID。

如果原始 SAML 登录令牌使用不同的 NameID 格式（如电子邮件/UPN），则 SAML 应用无法将注销消息中的 NameID 关联到现有会话（因为这两条消息中的 NameID 不同），这会导致 SAML 应用放弃注销消息并使用户保持登录状态。 此修补程序会使注销消息与为应用程序配置的 NameID 一致。

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>“混合标识管理员”角色现在可用于云预配

**类型：** 新功能  
**服务类别：** Azure AD 云配置  
**产品功能：** 标识生命周期管理
 
IT 管理员现在可以将“混合管理员”角色作为最低权限角色使用，以设置 Azure AD Connect 云预配。 有了这个新角色以后，你无需再使用全局管理员角色来设置和配置云预配。 [了解详细信息](../roles/delegate-by-task.md#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD 应用程序库推出了新联合应用 - 2020 年 5 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 5 月，我们在应用库中添加了以下 36 个支持联合身份验证的新应用程序：

[Moula](https://moula.com.au/pay/merchants)、[Surveypal](https://www.surveypal.com/app)、[Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/)、[TackleBox](http://www.tacklebox.app/)、[Powell Teams](https://powell-software.com/en/powell-teams-en/)、[Talentsoft Assistant](https://msteams.talent-soft.com/)、[ASC Recording Insights](https://teams.asc-recording.app/product)、[GO1](https://www.go1.com/)、[B-Engaged](https://b-engaged.se/)、[Competella Contact Center Workgroup](http://www.competella.com/)、[Asite](http://www.asite.com/)、[ImageSoft Identity](https://identity.imagesoftinc.com/)、[My IBISWorld](https://identity.imagesoftinc.com/)、[insuite](../saas-apps/insuite-tutorial.md)、[Change Process Management](../saas-apps/change-process-management-tutorial.md)、[Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md)、[Smart Global Governance](../saas-apps/smart-global-governance-tutorial.md)、[Prezi](../saas-apps/prezi-tutorial.md)、[Mapbox](../saas-apps/mapbox-tutorial.md)、[Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md)、[Whimsical](../saas-apps/whimsical-tutorial.md)、[Trelica](../saas-apps/trelica-tutorial.md)、[EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md)、[EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md)、[EasySSO for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md)、[Torii](../saas-apps/torii-tutorial.md)、[Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md)、[Humanage](../saas-apps/humanage-tutorial.md)、[ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md)、[CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md)、[ShareVault](../saas-apps/sharevault-tutorial.md)、[Vyond](../saas-apps/vyond-tutorial.md)、[TextExpander](../saas-apps/textexpander-tutorial.md)、[Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md)、[askSpoke](../saas-apps/askspoke-tutorial.md)、[ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

你也可以访问 https://aka.ms/AppsTutorial 找到所有应用程序的文档。

有关如何在 Azure AD 应用库中列出你的应用程序的信息，请访问 https://aka.ms/AzureADAppRequest 查看详细信息。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件访问的仅限报告模式现已正式发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

通过[条件访问的仅限报告模式](../conditional-access/concept-conditional-access-report-only.md)，你可以在没有执行访问控制的情况下评估策略的结果。 你可以在启用仅限报告策略之前，对组织内的这些策略进行测试并了解其影响，以使部署更安全且更容易。 在过去几个月内，我们见证了对仅限报告模式的广泛采用：超过 2600 万名用户使用了仅限报告策略。 在今天发布的公告中，新的 Azure AD 条件访问策略默认情况下将以仅限报告模式创建。 这意味着你可以在创建策略以后监视其影响。 对于使用 MS Graph API 的用户来说，你也可以[通过编程方式管理仅限报告策略](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true)。 

---

### <a name="self-service-sign-up-for-guest-users"></a>面向来宾用户的自助注册

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
借助 Azure AD 中的外部标识，你可以允许组织外部人员访问应用和资源，同时允许他们使用自己喜欢的任何标识进行登录。 与外部用户共享应用时，你可能并不总是提前知道谁需要访问应用。 借助[自助注册](../external-identities/self-service-sign-up-overview.md)，你可以使来宾用户注册并获取业务线 (LOB) 应用的来宾帐户。 可以创建和自定义注册流以支持 Azure AD 和社交标识。 还可以在注册过程中收集有关用户的其他信息。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件访问的见解和报告工作簿现已正式发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

[见解和报告工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)为管理员提供了其租户中的 Azure AD 条件访问的摘要视图。 利用该功能，管理员可以选择单个策略，以便更好了解每个策略的作用并实时监视任何更改。 工作簿流数据存储在 Azure Monitor 之中。你可以[按照这些说明](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)，在几分钟内完成设置。 为了使仪表板更容易被看见，我们已将其移动到 Azure AD 条件访问菜单的“新建见解和报告”选项卡中。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件访问的“策略详细信息”边栏选项卡现在处于公共预览状态

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

新的[“策略详细信息”边栏选项卡](../conditional-access/troubleshoot-conditional-access.md)会显示条件访问策略评估过程中满足的分配、条件和控制。 你可以通过选择“登录详细信息”的“条件访问”或“仅限报告”选项卡中的任何一行，来访问该边栏选项卡。

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>适用于 Microsoft Graph 中的 Directory 对象的新查询功能现在处于公共预览阶段

**类型：** 新功能  
**服务类别：** MS Graph **产品功能：** 开发人员体验

Microsoft Graph Directory 对象 API 正在引入新功能，支持计数、搜索、筛选和排序等操作。 这将使开发人员能够快速查询 Directory 对象，而无需使用内存中筛选和排序等解决方法。 有关详细信息，请参阅此[博客文章](https://aka.ms/CountFilterMSGraphAAD)。

这些功能目前处于公共预览状态，期待你的反馈。 请通过此[简短调查](https://aka.ms/MsGraphAADSurveyDocs)发送你的评论。

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>使用 Microsoft Graph API（Beta 版本）配置基于 SAML 的单一登录

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
现已支持使用 MS Graph API（Beta 版本）从 Azure AD 库创建并配置应用程序。 如果你需要为应用程序的多个实例设置基于 SAML 的单一登录，请使用 MS Graph API [自动配置基于 SAML 的单一登录](/graph/application-saml-sso-configure-api)，以便节省时间。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 5 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic（按组织）](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML 令牌加密现已正式发布

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
[SAML 令牌加密](../manage-apps/howto-saml-token-encryption.md)允许将应用程序配置为接收加密的 SAML 断言。 该功能现已在所有云中正式发布。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>应用程序令牌中的组名称声明已正式发布

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
令牌中颁发的组声明现在仅限于分配到应用程序的组。  如果用户是许多组的成员，并且存在超过令牌大小限制的风险，这一点尤其重要。 推出这项新功能以后，将正式发布[将组名称添加到令牌](../hybrid/how-to-connect-fed-group-claims.md)这一功能。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback 现在支持设置工作电话号码属性

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
我们增强了 Workday Writeback 预配应用，现在支持写回工作电话号码和移动电话号码的属性。 除了电子邮件和用户名，你现在还可以配置 Workday Writeback 预配应用，使其将电话号码值从 Azure AD 传递到 Workday。 有关如何配置电话号码写回功能的详细信息，请参阅[Workday Writeback](../saas-apps/workday-writeback-tutorial.md)应用教程。 

---

### <a name="publisher-verification-preview"></a>发布者验证（预览版）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 开发人员体验
 
发布者验证（预览版）旨在帮助管理员和最终用户了解与 Microsoft 标识平台集成的应用程序开发人员的真实身份。 有关详细信息，请参阅[发布者验证（预览版）](../develop/publisher-verification-overview.md)。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>单页应用的授权代码流

**类型：** 更改的功能 **服务类别：** 身份验证 **产品功能：** 开发人员体验

由于新式浏览器[第三方 cookie 限制（如 Safari ITP）](../develop/reference-third-party-cookies-spas.md)，SPA 必须使用授权代码流（而不是隐式流）来维护 SSO；MSAL.js v 2.x 现在支持授权代码流。 Azure 门户有相应更新，因此你可以将 SPA 更新为类型“SPA”并使用授权代码流。 有关指导，请参阅[快速入门：使用授权代码流在 JavaScript SPA 中让用户登录并获取访问令牌](../develop/quickstart-v2-javascript-auth-code.md)。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>改进的设备筛选功能处于公共预览状态

**类型：** 更改的功能   
**服务类别：** 设备管理 **产品功能：** 设备生命周期管理
 
你以前只能使用“启用”和“活动日期”筛选器。 现在，你可以[根据其他属性对设备列表进行筛选](../devices/device-management-azure-portal.md#device-list-filtering-preview)，其中包括 OS 类型、联接类型和合规性等。 这些条件应该会简化查找特定设备位置的操作。

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Azure AD B2C 的新应用注册体验现已正式发布

**类型：** 更改的功能   
**服务类别：** B2C - 用户标识管理  
**产品功能：** 标识生命周期管理
 
Azure AD B2C 的新应用注册体验现已正式发布。 

以前，你需要使用旧应用程序体验将 B2C 面向使用者的应用程序与其余应用分开管理。 这意味着在 Azure 中，不同的位置可能有不同的应用创建体验。

新体验会在一个位置显示所有 B2C 应用注册和 Azure AD 应用注册，并提供一致的管理方法。 无论你是否需要通过管理面向客户的应用或有权访问 Microsoft Graph 的应用来以编程方式管理 Azure AD B2C 资源，你都只需要了解一种执行操作的方法。

你可以通过导航 Azure AD B2C 服务并选择“应用注册”边栏选项卡，来获取新体验。 也可从 Azure Active Directory 服务获得该体验。

Azure AD B2C [应用注册体验](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)是基于 Azure AD 租户的常规应用注册体验构建的，是专为 Azure AD B2C 设计的。 未来将弃用旧的“应用程序”体验。

有关详细信息，请参阅 [Azure AD B2C 的新应用注册体验](../../active-directory-b2c/app-registrations-training-guide.md)。

---
## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>组合安全信息注册体验现已正式发布

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 标识安全和保护

适用于多重身份验证和自助式密码重置的组合注册体验现已正式发布。 该项新注册体验使用户能够以循序渐进的方式注册 MFA 和 SSPR。 当你为组织部署新体验后，用户可以在更短的时间内完成注册，并且减少了麻烦。 请查看[此处](https://bit.ly/3etiRyQ)的博客文章。

---

### <a name="continuous-access-evaluation"></a>连续访问评估

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 标识安全和保护

连续访问评估是一项新的安全功能，当在 Azure AD 中发生事件时（如删除用户帐户），可对使用 Azure AD 访问令牌的信赖方准实时执行策略。 我们将首选为 Teams 和 Outlook 客户端推出此功能。 有关详细信息，请参阅我们的[博客](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)和[文档](../conditional-access/concept-continuous-access-evaluation.md)。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>短信登录：一线工作者可以使用其电话号码登录 Azure AD 支持的应用程序，无需密码

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 用户身份验证

Office 即将推出一系列移动优先的商业应用程序。这些应用程序迎合非传统组织，以及大型组织中不将电子邮件作为其主要通信方式的员工的需求。 这些应用面向一线员工、无电脑工作者、现场代理或零售员工。这些人员可能无法从其雇主那里获取电子邮件地址，无权访问计算机或 IT。 此项目将允许这些员工通过输入电话号码并来回转换代码来登录到商业应用程序。 有关详细信息，请参阅我们的[管理员文档](../authentication/howto-authentication-sms-signin.md)和[最终用户文档](../user-help/sms-sign-in-explainer.md)。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>邀请内部用户使用 B2B 协作

**类型：** 新功能

**服务类别：** B2B

**产品功能：**

我们将扩展 B2B 的邀请功能，以允许邀请现有内部帐户使用 B2B 协作凭据。 可以通过将用户对象传递到 Invite API 和受邀电子邮件地址等典型参数来执行此操作。 用户的对象 ID、UPN、组成员身份和应用分配等保持不变，但是他们将使用 B2B 主租户凭据，而不是邀请之前的内部凭据进行身份验证。 有关详细信息，请参阅[文档](../external-identities/invite-internal-users.md)。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件访问的仅限报告模式现已正式发布

**类型：** 新功能

**服务类别：** 条件访问

**产品功能：** 标识安全和保护

通过[条件访问的仅限报告模式](../conditional-access/concept-conditional-access-report-only.md)，你可以在没有执行访问控制的情况下评估策略的结果。 你可以在启用仅限报告策略之前，对组织内的这些策略进行测试并了解其影响，以使部署更安全且更容易。 在过去几个月内，我们见证了对仅限报告模式的广泛采用：超过 2600 万名用户使用了仅限报告策略。 在此公告中，新的 Azure AD 条件访问策略默认情况下将以仅限报告模式创建。 这意味着你可以在创建策略以后监视其影响。 对于使用 MS Graph API 的用户来说，你也可以[通过编程方式管理仅限报告策略](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true)。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件访问的见解和报告工作簿现已正式发布

**类型：** 新功能

**服务类别：** 条件访问

**产品功能：** 标识安全和保护

条件访问的[见解和报告工作簿](../conditional-access/howto-conditional-access-insights-reporting.md)为管理员提供了其租户中的 Azure AD 条件访问的摘要视图。 利用该功能，管理员可以选择单个策略，以便更好了解每个策略的作用并实时监视任何更改。 工作簿流数据存储在 Azure Monitor 之中。你可以[按照这些说明](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)，在几分钟内完成设置。 为了使仪表板更容易被看见，我们已将其移动到 Azure AD 条件访问菜单的“新建见解和报告”选项卡中。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件访问的“策略详细信息”边栏选项卡现在处于公共预览状态

**类型：** 新功能

**服务类别：** 条件访问

**产品功能：** 标识安全和保护

新的[“策略详细信息”边栏选项卡](../conditional-access/troubleshoot-conditional-access.md)显示了在条件访问策略评估过程中满足的分配、条件和控制。 你可以通过选择“登录详细信息”的“条件访问”或“仅限报告”选项卡中的任何一行，来访问该边栏选项卡 。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD 应用库中推出了全新联合应用 - 2020 年 4 月

**类型：** 新功能

**服务类别：** 企业应用

**产品功能：** 第三方集成

2020 年 4 月，我们在应用库中添加了以下 31 款支持联合的新应用： 

[SincroPool Apps](https://www.sincropool.com/)、[SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、[Float](../saas-apps/float-tutorial.md)、[LMS365](https://lms.365.systems/)、[IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md)、[Lunni](https://lunni.fi/)、[EasySSO for Jira](../saas-apps/easysso-for-jira-tutorial.md)、[Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)、[Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md)、[Microsoft 365 Mover](https://app.mover.io/login)、[Speaker Engage](https://speakerengage.com/login.php)、[Honestly](../saas-apps/honestly-tutorial.md)、[Ally](../saas-apps/ally-tutorial.md)、[DutyFlow](https://app.dutyflow.nl/)、[AlertMedia](../saas-apps/alertmedia-tutorial.md)、[gr8 People](../saas-apps/gr8-people-tutorial.md)、[Pendo](../saas-apps/pendo-tutorial.md)、[HighGround](../saas-apps/highground-tutorial.md)、[Harmony](../saas-apps/harmony-tutorial.md)、[Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md)、[SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md)、[empower](https://www.made-in-office.com/en/)、[Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md)、[Litmus](../saas-apps/litmus-tutorial.md)、[GroupTalk](https://recorder.grouptalk.com/)、[Frontify](../saas-apps/frontify-tutorial.md)、[MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md)、[TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md)、[COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)、[Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md)、[Trend Micro Web Security(TMWS)](../saas-apps/trend-micro-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>oAuth2PermissionGrant 的 Microsoft Graph delta 查询现已提供公共预览版

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

OAuth2PermissionGrant 的 Delta 查询现已提供公共预览版！ 你现在可以跟踪更改，而无需持续轮询 Microsoft Graph。 [了解详细信息。](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>组织联系人的 Microsoft Graph delta 查询支持现已正式发布

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

组织联系人的 Delta 查询现已正式发布！ 你现在可以跟踪生产应用中的更改，而无需持续轮询 Microsoft Graph。 通过 delta 查询替换持续轮询 orgContact 数据的任何现有代码，以显著提高性能。 [了解详细信息。](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>应用程序的 Microsoft Graph delta 查询支持现已正式发布

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

应用程序的 Delta 查询现已正式发布！ 你现在可以跟踪生产应用中的更改，而无需持续轮询 Microsoft Graph。 通过 delta 查询替换持续轮询应用程序数据的任何现有代码，以显著提高性能。 [了解详细信息。](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>管理单元的 Microsoft Graph delta 查询支持现已提供公共预览版

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 管理单元的开发人员体验 Delta 查询现已提供公共预览版！ 你现在可以跟踪更改，而无需持续轮询 Microsoft Graph。 [了解详细信息。](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>管理 Microsoft Graph beta API 中的身份验证电话号码和其他信息

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

这些 API 是管理用户身份验证方法的重要工具。 你现在可以通过编程方式预注册并管理用于 MFA 和自助式密码重置 (SSPR) 的验证器。 这是在 Azure AD MFA、SSPR 和 Microsoft Graph 空间中最希望加入的功能之一。 我们在此浪潮中发布的新 API 使你能够：

- 读取、添加、更新和删除用户的身份验证电话
- 重置用户的密码
- 打开和关闭短信登录

有关详细信息，请参阅 [Azure AD 身份验证方法 API 概述](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)。

---

### <a name="administrative-units-public-preview"></a>管理单元（公共预览版）

**类型：** 新功能

**服务类别：** Azure AD 角色

**产品功能：** 访问控制

管理单元允许你授予仅限于你定义的部门、区域或组织的其他部门的管理权限。 你能够使用管理单元将权限委派给区域管理员或以粒度级别设置策略。 例如，用户帐户管理员可以更新配置文件信息、重置密码，并仅为其管理单元中的用户分配许可证。

中心管理员通过使用管理单元，可以：

- 创建用于分散式管理资源的管理单元
- 分配一个仅对管理单元中的 Azure AD 用户具有管理权限的角色
- 根据需要使用用户和组填充管理单元

有关详细信息，请参阅 [Azure Active Directory 中的管理单元管理（预览版）](../roles/administrative-units.md)。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>打印机管理员和打印机技术人员内置角色

**类型：** 新功能

**服务类别：** Azure AD 角色

**产品功能：** 访问控制

**打印机管理员：** 具有此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机并管理所有打印机配置的各方面，其中包括“通用打印连接器”设置。 他们可以同意所有委托的打印权限请求。 打印机管理员还有权访问打印报告。 

**打印机技术人员：** 充当此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机并管理打印机状态。 他们还可以读取所有连接器信息。 打印机技术人员无法执行的重要任务是设置用户对打印机的权限以及共享打印机。 [了解详细信息。](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>混合标识管理员内置角色

**类型：** 新功能

**服务类别：** Azure AD 角色

**产品功能：** 访问控制

充当此角色的用户可以启用、配置和管理与在 Azure AD 中启用混合标识相关的服务及设置。 有了此角色，就可以将 Azure AD 配置为三种受支持的身份验证方法（密码哈希同步 (PHS)、传递身份验证 (PTA)，或联合身份验证（AD FS 或第三方联合身份验证提供程序））之一，以及部署相关本地基础结构来启用这些方法。 本地基础结构包括预配和 PTA 代理。 此角色授予在非 Windows 10 设备或非 Windows Server 2016 计算机上启用无缝单一登录 (SSO) 以实现无缝身份验证的能力。 此外，有了此角色，还可以查看登录日志并访问运行状况和分析，以便进行监视和故障排除。 [了解详细信息。](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>网络管理员内置角色

**类型：** 新功能

**服务类别：** Azure AD 角色

**产品功能：** 访问控制

充当此角色的用户可以查看 Microsoft 根据其用户位置发出的网络遥测数据提供的网络外围体系结构建议。 Microsoft 365 的网络性能依赖于精心规划的企业客户网络外围体系结构，而该体系结构通常特定于用户位置。 此角色允许编辑已发现的用户位置以及配置这些位置的网络参数，以方便改善遥测措施和设计建议。 [了解详细信息。](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 管理员门户体验中的批量活动和下载

**类型：** 新功能

**服务类别：** 用户管理

**产品功能：** 目录

你现在可以通过在 Azure AD 管理员门户体验中上传 CSV 文件来对 Azure AD 中的用户和组执行批量活动。 你可以创建用户、删除用户以及邀请来宾用户。 可以在组中添加和删除成员。

还可以从 Azure AD 管理员门户体验下载 Azure AD 资源列表。 你可以下载目录中的用户列表、目录中的组列表以及特定组的成员。

有关详细信息，请查看以下文章：

- [创建用户](../enterprise-users/users-bulk-add.md)或[邀请来宾用户](../external-identities/tutorial-bulk-invite.md)
- [删除用户](../enterprise-users/users-bulk-delete.md)或[还原已删除的用户](../enterprise-users/users-bulk-restore.md)
- [下载用户列表](../enterprise-users/users-bulk-download.md)或[下载组列表](../enterprise-users/groups-bulk-download.md)
- [添加（导入）成员](../enterprise-users/groups-bulk-import-members.md)或[删除成员](../enterprise-users/groups-bulk-remove-members.md)或[下载组的成员列表](../enterprise-users/groups-bulk-download-members.md)

---

### <a name="my-staff-delegated-user-management"></a>通过“我的员工”门户委托的用户管理

**类型：** 新功能

**服务类别：** 用户管理

**产品功能：**

“我的员工”门户使一线管理者（如店铺经理）可以确保其职员能够访问其 Azure AD 帐户。 组织可以将常见任务（如重置密码或更改电话号码等）委托给一线管理者，而不是依赖于中心支持人员。 通过“我的员工”门户，无法访问帐户的用户只需点击几下即可重新获取访问权限，无需求助支持人员或 IT 人员。 有关详细信息，请参阅[通过“我的员工”门户管理管理用户（预览版）](../roles/my-staff-configure.md)和[通过“我的员工”门户委托用户管理（预览版）](../user-help/my-staff-team-manager.md)。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>访问评审中升级的最终用户体验

**类型：** 已更改的功能

**服务类别：** 访问评审

**产品功能：** 标识治理

我们在“我的应用”门户中对 Azure AD 访问评审的审阅者体验进行了更新。 4 月底，登录到 Azure AD 访问评审的审阅者体验的审阅者将会看见一个横幅。该横幅允许其在“我的访问权限”门户中试用已更新的体验。 请注意：已更新的访问评审体验与当前体验所提供的功能一样，但是在新功能的基础上改进了用户界面，使用户能够提高工作效率。 [你可以在此处了解有关已更新的体验的详细信息](../governance/perform-access-review.md)。 此公共预览版将持续到 2020 年 7 月底。 在 7 月底，未选择加入预览体验的审阅者会自动定向到“我的访问权限”门户，以执行访问评审。 如果你现在打算在“我的访问权限”门户中将审阅者永久切换为预览体验，[请在此处提出请求](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 入站用户预配和写回应用现在支持最新版本的 Workday Web 服务 API

**类型：** 已更改的功能

**服务类别：** 应用预配

**产品功能：** 

根据客户反馈，我们现已更新了企业应用库中的 Workday 入站用户预配和写回应用，以支持最新版本的 Workday Web 服务 (WWS) API。 通过此更改，客户可以指定用于连接字符串的 WWS API 的版本。 这使客户能够检索 Workday 的各版本中可用的其他 HR 属性。 Workday Writeback 应用现在使用推荐的 Change_Work_Contact_Info Workday Web 服务来克服 Maintain_Contact_Info 的限制。

如果未指定用于连接字符串的版本，Workday 入站预配应用将默认继续使用 WWS v21.1。若要将 Workday API 切换为最新版本以进行入站用户预配，客户需要更新连接字符串（如[本教程](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)所述），并更新用于 Workday 属性的 XPATH（如 [Workday 属性参考指南](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)所述）。 

若要使用新 API 进行写回，无需对 Workday Writeback 预配应用进行任何更改。 在 Workday 端，请确保 Workday 集成系统用户 (ISU) 帐户有权调用 Change_Work_Contact 业务流程，如教程部分中的[配置业务流程安全策略权限所述](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions)。 

我们已更新了[教程指南](../saas-apps/workday-inbound-tutorial.md)，以反映新的 API 版本支持。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>充当默认访问角色的用户现已在预配范围内

**类型：** 已更改的功能

**服务类别：** 应用预配

**产品功能：** 标识生命周期管理

充当默认访问角色的用户以前在预配范围之外。 我们已收到反馈，客户希望充当此角色的用户在预配范围内。 从 2020 年 4 月 16 日起，所有新的预配配置都将允许预配充当默认访问角色的用户。 我们将逐步更改现有预配配置的行为，以支持预配充当此角色的用户。 [了解详细信息。](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>更新了预配 UI

**类型：** 已更改的功能

**服务类别：** 应用预配

**产品功能：** 标识生命周期管理

我们更新了预配体验，以创建更集中的管理视图。 当你导航到已配置的企业应用程序的“预配”边栏选项卡时，你将能够轻松监视预配进度并管理操作，如启动、停止和重新启动预配等。 [了解详细信息。](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>动态组规则验证现已提供公共预览版

**类型：** 已更改的功能

**服务类别：** 组管理

**产品功能：** 协作

Azure Active Directory (Azure AD) 现在提供了验证动态组规则的方法。 在“验证规则”选项卡上，可以对示例组成员验证动态规则，以确认规则按预期方式工作。 创建或更新动态组规则时，管理员希望了解用户或设备是否将成为该组的成员。 这有助于评估用户或设备是否满足规则条件，并有助于在不希望其具有成员身份时进行故障排除。

有关详细信息，请参阅[验证动态组成员身份规则（预览版）](../enterprise-users/groups-dynamic-rule-validation.md)。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>标识安全分数 - 安全性默认值和 MFA 改进操作更新

**类型：** 已更改的功能

**服务类别：** N/A

**产品功能：** 标识安全和保护

**支持 Azure AD 改进操作的安全默认值：** Microsoft 安全功能分数将更新改进操作，以支持 [Azure AD 中的安全默认值](./concept-fundamentals-security-defaults.md)，这有助于通过针对常见攻击预配置的安全设置来保护组织。 这将影响以下改进操作：

- 确保所有用户都可以完成多重身份验证以实现安全访问
- 要求对管理员角色执行 MFA
- 启用阻止旧式身份验证的策略
 
MFA 改进操作更新：为了反映企业在应用与业务相关的策略时确保最高安全性的需求，Microsoft 安全功能分数删除了三项围绕多重身份验证 (MFA) 的改进操作，然后添加了两项改进操作。

删除的改进操作：

- 要求所有用户都注册多重身份验证
- 要求所有用户执行 MFA
- 要求所有 Azure AD 特权角色执行 MFA

添加的改进操作：

- 确保所有用户都可以完成多重身份验证以实现安全访问
- 要求对管理员角色执行 MFA

这些新的改进操作会要求目录范围内的用户或管理员注册多重身份验证 (MFA)，并建立符合组织需求的合适的策略集。 主要目标是具有灵活性，同时确保所有用户和管理员可以使用多重身份或基于风险的标识验证提示进行身份验证。 这可以采用具有多个策略的形式，这些策略应用范围内的决策，或设置安全默认值（自 3月 16 日起），让 Microsoft 决定何时针对 MFA 质询用户。 [有关详细信息，请参阅 Microsoft 安全功能分数中的新增功能](/microsoft-365/security/mtp/microsoft-secure-score#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>2021 年 3 月 B2B 中的非托管 Azure Active Directory 帐户更新

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
从 2021 年 3 月 31 日开始，Microsoft 将不再支持通过创建适用于 B2B 协作场景的非托管的 Azure Active Directory (Azure AD) 帐户和租户兑换邀请。 为此，我们建议你选择[发送一次性密码身份验证的电子邮件](../external-identities/one-time-passcode.md)。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>充当默认访问角色的用户将在预配范围内

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
充当默认访问角色的用户以前在预配范围之外。 我们已收到反馈，客户希望充当此角色的用户在预配范围内。 我们正在努力部署更改，以便所有新的预配配置都将允许预配充当默认访问角色的用户。 我们将逐步更改现有预配配置的行为，以支持预配充当此角色的用户。 客户无需执行任何操作。 我们会在完成此更改后，将更新发布到[文档](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 协作将在由世纪互联（Azure 中国世纪互联）租户运营的 Microsoft Azure 中提供

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能将在由世纪互联（Azure 中国世纪互联）租户运营的 Microsoft Azure 中提供，使 Azure 中国世纪互联租户中的用户能够与其他 Azure 中国世纪互联租户的用户进行无缝协作。 [了解有关 Azure AD B2B 协作的详细信息](/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>重新设计 Azure AD B2B 协作邀请电子邮件

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
将重新设计由 Azure AD B2B 协作邀请服务发送的邀请用户到目录的[电子邮件](../external-identities/invitation-email-elements.md)以使邀请信息和用户的后续步骤更加清晰。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery 策略的更改将显示在审核日志中

**类型：** 已修复  
**服务类别：** 审核  
**产品功能：** 监视和报告
 
我们修复了一个 Bug，即 [HomeRealmDiscovery 策略](../manage-apps/configure-authentication-for-federated-users-portal.md)的更改不包含在审核日志中。 你现在能够查看策略的更改时间、更改方式以及更改者。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD 应用库中推出了新的联合应用 - 2020 年 3 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 3 月，我们在应用库中添加以下 51 款支持联合的新应用： 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)、[Zoho One China](../saas-apps/zoho-one-china-tutorial.md)、[PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)、[Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md)、[iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md)、[contexxt.ai SPHERE](https://contexxt-sphere.com/login)、[Wisdom By Invictus](../saas-apps/wisdom-by-invictus-tutorial.md)、[Flare Digital Signage](https://pixelnebula.com/)、[Logz.io - Cloud Observability for Engineers](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md)、[SpectrumU](../saas-apps/spectrumu-tutorial.md)、[BizzContact](https://www.bizzcontact.app/)、[Elqano SSO](../saas-apps/elqano-sso-tutorial.md)、[MarketSignShare](http://www.signshare.com/)、[CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md)、[Netvision Compas](../saas-apps/netvision-compas-tutorial.md)、[FCM HUB](../saas-apps/fcm-hub-tutorial.md)、[RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757)、[GoLinks](../saas-apps/golinks-tutorial.md)、[Datadog](../saas-apps/datadog-tutorial.md)、[Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md)、[LIFT](../saas-apps/lift-tutorial.md)、[Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md)、[WatchTeams](https://www.devfinition.com/)、[Aster](https://demo.asterapp.io/login)、[Skills Workflow](../saas-apps/skills-workflow-tutorial.md)、[Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)、[IP Platform](../saas-apps/ip-platform-tutorial.md)、[InVision](../saas-apps/invision-tutorial.md)、[Pipedrive](../saas-apps/pipedrive-tutorial.md)、[Showcase Workshop](https://app.showcaseworkshop.com/)、[Greenlight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md)、[Greenlight Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md)、[Grok Learning](../saas-apps/grok-learning-tutorial.md)、[Miradore Online](https://login.online.miradore.com/)、[Khoros Care](../saas-apps/khoros-care-tutorial.md)、[AskYourTeam](../saas-apps/askyourteam-tutorial.md)、[TruNarrative](../saas-apps/trunarrative-tutorial.md)、[Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)、[Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md)、[insuiteX](https://www.insuite.jp/)、[sybo](https://www.systexsoftware.com.tw/)、[Britive](../saas-apps/britive-tutorial.md)、[WhosOffice](../saas-apps/whosoffice-tutorial.md)、[E-days](../saas-apps/e-days-tutorial.md)、[Kollective SDN](https://portal.kollective.app/login)、[Witivio](https://app.witivio.com/)、[Playvox](https://my.playvox.com/login)、[Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md)、[Campus Café](../saas-apps/campus-cafe-tutorial.md)、[Catchpoint](../saas-apps/catchpoint-tutorial.md)、[Code42](../saas-apps/code42-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure 政府租户中已提供 Azure AD B2B 协作

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能现已在一些 Azure 政府租户间可用。  若要查明你的租户是否能够使用这些功能，请遵循[如何判断在我的 Azure 美国政府租户中是否提供 B2B 协作功能？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)中的说明。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>用于 Azure 日志的 Azure Monitor 集成现已在 Azure 政府版中提供

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
Azure Monitor 与 Azure AD 日志的集成现已在 Azure 政府版中提供。 你可以将 Azure AD 日志（审核和登录日志）路由到存储帐户、事件中心和 Log Analytics。 请查看适用于 Azure AD 场景的[详细文档](../reports-monitoring/concept-activity-logs-azure-monitor.md)以及[报告和监视的部署计划](../reports-monitoring/plan-monitoring-and-reporting.md)。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure 政府中的标识保护刷新

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

我们很高兴能够与大家分享，我们现已在 [Microsoft Azure 政府门户](https://portal.azure.us/)中推出了已更新的 [Azure AD 标识保护](../identity-protection/overview-identity-protection.md) 体验。 有关详细信息，请参阅[公告博客文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>灾难恢复：下载并存储预配配置

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务提供了一组丰富的配置功能。 客户需要能够保存其配置，以便以后对其进行引用或回退到已知的正常版本。 我们添加了这样一种功能，即，将预配配置下载为 JSON 文件并在需要时上传该文件。 [了解详细信息](../app-provisioning/export-import-provisioning-configuration.md)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR（自助式密码重置）现在需要在由世纪互联（Azure 中国世纪互联）运营的 Microsoft Azure 中为管理员提供两个入口 

**类型：** 已更改的功能  
**服务类别：** 自助式密码重置  
**产品功能：** 标识安全和保护
 
以前在由世纪互联（Azure 中国世纪互联）运营的 Microsoft Azure 中，当管理员使用自助式密码重置 (SSPR) 来重置其密码时，只需要一个入口（质询）来证明其身份。 在公有云和其他国家/地区云中，当管理员使用 SSPR 时，通常必须使用两个入口来证明其身份。 但是由于我们在 Azure 中国世纪互联中不支持短信和电话呼叫，因此允许管理员通过一个入口重置密码。

我们将在 Azure 中国世纪互联与公有云之间创建 SSPR 功能奇偶一致性。 管理员今后使用 SSPR 时必须使用两个入口。 将支持短信、电话呼叫、Authenticator 应用通知和代码。 [了解详细信息](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密码长度不得超过 256 个字符。

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
为了确保 Azure AD 服务的可靠性，用户密码长度现在不得超过 256 个字符。 密码超过此长度的用户将被要求在后续登录中更改其密码，可通过联系其管理员或者使用自助式密码重置功能进行更改。

此更改已于 2020 年 3 月 13 日 10AM PST (18:00 UTC) 启用，错误为 AADSTS 50052，InvalidPasswordExceedsMaxLength。 有关详细信息，请参阅[中断性变更通知](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD 登录日志现在可以通过 Azure 门户供所有免费租户使用

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
从现在开始，具有免费租户的客户可以[从 Azure 门户访问 Azure AD 登录日志](../reports-monitoring/concept-sign-ins.md)（最长 7 天）。 登录日志以前仅供具有 Azure Active Directory Premium 许可的用户使用。 借助此更改，所有租户都可以通过门户访问这些日志。

> [!NOTE]
> 若要通过 Microsoft Graph API 和 Azure Monitor 访问登录日志，客户仍需要高级许可（Azure Active Directory Premium P1 或 P2）。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>弃用了 Azure 门户中“组”的“常规”设置中的“目录范围组”选项

**类型：** 已弃用  
**服务类别：** 组管理  
**产品功能：** 协作

为了给客户提供一种更灵活的方式来创建最符合其需求的目录范围组，我们已将 Azure 门户中“组” > “常规”设置的“目录范围组”选项替换为指向[动态组文档](../enterprise-users/groups-dynamic-membership.md)的链接  。 我们已对文档进行了改进并提供了更多说明，以便管理员可以创建包含或排除来宾用户的所有用户组。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>对自定义控件即将进行的更改

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
我们计划将目前的自定义控件预览替换为一种方法。该方法允许合作伙伴提供的身份验证功能与 Azure Active Directory 管理员和最终用户体验无缝协作。 合作伙伴 MFA 解决方案目前面临以下限制：仅在输入密码后工作；在其他重要场景不充当升级身份验证的 MFA；不与最终用户或管理凭据管理功能集成。 该新实现将允许合作伙伴提供的身份验证因素与内置因素一起用于重要场景，其中包括注册、使用、MFA 声明、升级身份验证、报告和日志记录。 

自定义控件将继续在预览版和新设计中受支持，直到其正式发布。 届时，我们将为客户留出时间以迁移到新设计。 由于目前方法的限制，直到提供新设计，我们才能加入新提供商。 我们正与客户和提供商密切合作，并且随着关系越来越紧密，我们将交流时间线。 [了解详细信息](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>标识安全分数 - MFA 改进操作更新

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
为了反映企业在应用与业务相关的策略时确保最高安全性的需求，Microsoft 安全功能分数删除了三项围绕多重身份验证 (MFA) 的改进操作，然后添加了两项改进操作。

将删除以下改进操作：

- 要求所有用户注册 MFA
- 要求所有用户执行 MFA
- 要求所有 Azure AD 特权角色执行 MFA

将添加以下改进操作：

- 确保所有用户都可以完成 MFA 以实现安全访问
- 要求对管理员角色执行 MFA

这些新的改进操作会要求目录范围内的用户或管理员注册多重身份验证 (MFA)，并建立符合组织需求的合适的策略集。 主要目标是具有灵活性，同时确保所有用户和管理员可以使用多重身份或基于风险的标识验证提示进行身份验证。 这可以采取设置安全默认值的形式，使 Microsoft 决定何时针对 MFA 质询用户，或者何时具有多个应用范围决定的策略。 作为这些改进操作更新的一部分，基线保护策略将不再纳入评分计算的范围。 [有关详细信息，请参阅 Microsoft 安全功能分数中即将推出的功能](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD 域服务 SKU 选择

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务
 
我们已收到反馈，Azure AD 域服务的客户希望在选择其实例的性能级别时具有更高的灵活性。 从 2020 年 2 月 1 日开始，我们从动态模型（其中 Azure AD 根据对象计数决定性能层和定价层）切换为了自选模型。 客户现在可以选择与其环境相匹配的性能层。 此更改还允许我们启用资源林等新场景以及每日备份等高级功能。 对象计数现在对于所有 SKU 均不受限制，但是我们将继续为每个层提供对象计数建议。

**客户此时无需执行任何操作**。 对于现有客户，于 2020 年 2 月 1 日使用的动态层会决定新的默认层。 此更改不会影响定价或性能。 今后随着目录大小和工作负载特性的变化，Azure AD DS 客户将需要评估性能要求。 在服务层之间进行切换将继续是一个无故障时间的操作，并且我们将不再根据目录的增长自动将客户移动到新的层。 此外，价格不会提高，并且新的定价将与当前的定价模型保持一致。 有关详细信息，请参阅 [Azure AD DS SKU 文档](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus)和 [Azure AD 域服务定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 应用库中已推出新的联合应用 - 2020 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已于 2020 年 2 月将以下 27 款支持联合的新应用添加到了应用库中： 

[IamIP Patent Platform](../saas-apps/iamip-patent-platform-tutorial.md)、[Experience Cloud](../saas-apps/experience-cloud-tutorial.md)、[NS1 SSO For Azure](../saas-apps/ns1-sso-azure-tutorial.md)、[Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure)、[ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad)、[In Case of Crisis - Online Portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md)、[BIC Cloud Design](../saas-apps/bic-cloud-design-tutorial.md)、[Beekeeper Azure AD Data Connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md)、[Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess)、[Verkada Command](../saas-apps/verkada-command-tutorial.md)、[Splashtop](../saas-apps/splashtop-tutorial.md)、[Syxsense](../saas-apps/syxsense-tutorial.md)、[EAB Navigate](../saas-apps/eab-navigate-tutorial.md)、[New Relic (Limited Release)](../saas-apps/new-relic-limited-release-tutorial.md)、[Thulium](https://admin.thulium.com/login/instance)、[Ticket Manager](../saas-apps/ticketmanager-tutorial.md)、[Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams)、[Beesy](https://www.beesy.me/index.php/site/login)、[Health Support System](../saas-apps/health-support-system-tutorial.md)、[MURAL](https://app.mural.co/signup)、[Hive](../saas-apps/hive-tutorial.md)、[LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、[Wakelet](https://wakelet.com/login)、[Firmex VDR](../saas-apps/firmex-vdr-tutorial.md)、[ThingLink for Teachers and Schools](https://www.thinglink.com/)、[Coda](../saas-apps/coda-tutorial.md)、[NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product)、[WEDO](../saas-apps/wedo-tutorial.md)、[InvitePeople](https://invitepeople.com/login)、[Reprints Desk - Article Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md)、[TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>混合环境中对 FIDO2 安全密钥的 Azure AD 支持

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
我们将宣布推出混合环境中对 FIDO2 安全密钥的 Azure AD 支持的公共预览版。 用户现在可以使用 FIDO2 安全密钥登录到其已建立混合 Azure AD 联接的 Windows 10 设备，并无缝登录到其本地和云资源。 自从我们最初在已建立 Azure AD 联接的设备中发布 FIDO2 支持的公共预览版之后，无密码客户就一直希望加入对混合环境的支持功能。 无密码身份验证使用生物识别和公钥/私钥加密等高级技术，在保证安全性的同时，提供便利性和易用性。 借助此公共预览版，你现在可以使用 FIDO2 安全密钥等新式身份验证来访问传统 Active Directory 资源。 有关详细信息，请参阅 [SSO 到本地资源](../authentication/howto-authentication-passwordless-security-key-on-premises.md)。 

若要快速入门，请访问[为租户启用 FIDO2 安全密钥](../authentication/howto-authentication-passwordless-security-key.md)以获取分步说明。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新的我的帐户体验现已正式发布

**类型：** 已更改的功能  
**服务类别：** 我的配置文件/帐户  
**产品功能：** 最终用户体验
 
我的帐户，即满足所有最终用户帐户管理需求的一站式商店，现已正式发布！ 最终用户可以通过 URL 或者在新的“我的应用”体验的标头中访问此新站点。 有关该新体验提供的所有自助服务功能的详细信息，请参阅[“我的帐户”门户概述](../user-help/my-account-portal-overview.md)。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>“我的帐户”站点 URL 将更新为 myaccount.microsoft.com

**类型：** 已更改的功能  
**服务类别：** 我的配置文件/帐户  
**产品功能：** 最终用户体验
 
新的“我的帐户”最终用户体验会在下个月将其 URL 更新为 `https://myaccount.microsoft.com`。 有关该体验以及向最终用户提供的所有帐户自助服务功能的详细信息，请参阅[“我的帐户”门户帮助](../user-help/my-account-portal-overview.md)。

---

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新的“我的应用”门户现已正式发布

**类型：** 更改计划  
**服务类别：** 我的应用  
**产品功能：** 最终用户体验
 
将组织升级为现已正式发布的新的“我的应用”门户！ 有关新门户和集合的详细信息，请参阅[在“我的应用”门户创建集合](../manage-apps/access-panel-collections.md)。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD 中的工作区已重命名为集合

**类型：** 已更改的功能  
**服务类别：** 我的应用   
**产品功能：** 最终用户体验
 
工作区，即管理员可以配置以组织其用户应用的筛选器，现在称为集合。 有关如何对其进行配置的详细信息，请参阅[在“我的应用”门户创建集合](../manage-apps/access-panel-collections.md)。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>使用自定义策略的 Azure AD B2C 电话注册和登录（公共预览版）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
通过电话号码注册和登录，开发人员和企业可以允许其客户使用通过短信发送到用户电话号码的一次性密码进行注册和登录。 此功能还允许客户在失去对其电话的访问权限时更改其电话号码。 借助自定义策略以及电话注册和登录，开发人员和企业可以通过自定义页面传达其品牌。 了解如何[在 Azure AD B2C 中通过自定义策略设置电话注册和登录](../../active-directory-b2c/phone-authentication-user-flows.md)。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 1 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 应用库中已推出新的联合应用 - 2020 年 1 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已于 2020 年 1 月将以下 33 款支持联合的新应用添加到了应用库中： 

[JOSA](../saas-apps/josa-tutorial.md)、[Fastly Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md)、[Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md)、[Spintr SSO](../saas-apps/spintr-sso-tutorial.md)、[Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik)、[SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、[Upshotly](../saas-apps/upshotly-tutorial.md)、[LeaveBot](https://appsource.microsoft.com/en-us/product/office/WA200001175)、[DataCamp](../saas-apps/datacamp-tutorial.md)、[TripActions](../saas-apps/tripactions-tutorial.md)、[SmartWork](https://www.intumit.com/teams-smartwork/)、[Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md)、[SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS、PeopleSoft、and JDE](../saas-apps/ssogen-tutorial.md)、[Hosted MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md)、[Yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md)、[LumApps](https://sites.lumapps.com/login)、[Upwork Enterprise](../saas-apps/upwork-enterprise-tutorial.md)、[Talentsoft](../saas-apps/talentsoft-tutorial.md)、[SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/)、[PressPage](../saas-apps/presspage-tutorial.md)、[ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md)、[Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md)、[Helpshift](../saas-apps/helpshift-tutorial.md)、[PortalTalk 365](https://www.portaltalk.com/)、[CoreView](https://portal.coreview.com/)、Squelch Cloud Office365 Connector、[PingFlow Authentication](https://app-staging.pingview.io/)、[ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md)、[Taskize Connect](../saas-apps/taskize-connect-tutorial.md)、[Sandwai](https://app.sandwai.com/)、[EZRentOut](../saas-apps/ezrentout-tutorial.md)、[AssetSonar](../saas-apps/assetsonar-tutorial.md)、[Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="two-new-identity-protection-detections"></a>两个新的标识保护检测

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 
我们已为标识保护添加了两个新的登录链接的检测类型：可疑收件箱操作规则和不可能旅行。 这些脱机检测通过 Microsoft Cloud App Security (MCAS) 发现，并在标识保护中影响用户和登录风险。 有关这些检测的详细信息，请参阅[登录风险类型](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>中断性变更：URL 片段不再通过登录重定向传递

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
从 2020 年 2 月 8 日开始，当让用户登录的请求发送到 login.microsoftonline.com 时，服务将对请求追加一个空片段。  这可以确保浏览器擦除请求中的任何现有片段，防止出现一类重定向攻击。 任何应用程序都不应该依赖此行为。 有关详细信息，请参阅 Microsoft 标识平台文档中的[中断性变更](../develop/reference-breaking-changes.md#february-2020)。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>将 SAP SuccessFactors 预配集成到 Azure AD 和本地 AD（公共预览版）

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

你现在可以在 Azure AD 中将 SAP SuccessFactors 作为权威标识源进行集成 此集成有助于将端到端的标识生命周期自动化，包括使用基于 HR 的事件（如新员工或终止）来控制 Azure AD 帐户的预配。

有关如何将 SAP SuccessFactors 入站预配设置到 Azure AD 的详细信息，请参阅[配置 SAP SuccessFactors 自动预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)教程。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C 中的自定义电子邮件支持（公共预览版）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

你现在可以在用户注册使用应用时，使用 Azure AD B2C 创建自定义的电子邮件。 通过使用 DisplayControls（目前处于预览状态）和第三方电子邮件提供商（如 [SendGrid](https://sendgrid.com/)、[SparkPost](https://sparkpost.com/) 或自定义 REST API），你可以使用自己的电子邮件模板、发件人地址和主题文本，支持本地化，还可以自定义一次性密码 (OTP) 设置。

有关详细信息，请参阅[在 Azure Active Directory B2C 中自定义电子邮件验证](../../active-directory-b2c/custom-email-sendgrid.md)。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>将基线策略替换为安全默认值

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 标识安全和保护

作为身份验证默认安全模型的一部分，我们即将从所有租户中删除现有基线保护策略。 此删除预计在 2 月底完成。 我们将使用安全默认值替换这些基线保护策略。 如果你一直在使用基线保护策略，则必须计划移动到新的安全默认值策略或者条件访问。 如果你尚未使用这些策略，则不需要执行任何操作。

有关新的安全默认值的详细信息，请参阅[什么是安全默认值？](./concept-fundamentals-security-defaults.md) 有关条件访问策略的详细信息，请参阅[常见条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 属性和 Chrome 80 支持

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

作为 Cookie 安全默认模型的一部分，Chrome 80 浏览器将要改变其处理不带 `SameSite` 属性的 Cookie 的方式。 没有指定 `SameSite` 属性的任何 Cookie 都将被视作已设置为 `SameSite=Lax`，这将导致 Chrome 阻止某些跨域的 Cookie 共享场景，而你的应用可能会依赖这些场景。 若要维持较旧的 Chrome 行为，你可以使用 `SameSite=None` 属性并添加额外的 `Secure` 属性，以便跨站点的 Cookie 只能通过 HTTPS 连接访问。 Chrome 计划在 2020 年 2 月 4 日完成此更改。

我们建议所有开发人员都使用以下指导来测试其应用程序：

- 将“使用安全 Cookie”的默认值设置为“是” 。

- 将“SameSite”属性的默认值设置为“无” 。

- 添加一个额外的 `SameSite` 属性，即 Secure。

有关详细信息，请参阅 [ASP.NET 和 ASP.NET Core 中即将发生的对 SameSite Cookie 的更改](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)和 [Chrome 79 及以上版本中客户网站、Microsoft 产品和服务的潜在中断](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) 的新修补程序

**类型：** 已修复  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理

修补程序汇总包（版本 4.6.34.0）可用于 Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)。 此汇总包可以解决问题并添加在“此更新中修复的问题和添加的改进”部分中介绍的改进。

有关详细信息以及下载修补程序包，请参阅 [Microsoft Identity Manager 2016 Service Pack 2（版本 4.6.34.0）更新汇总现已推出](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>有助于将应用迁移到 Azure AD 的新的 AD FS 应用活动报告（公共预览版）

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

使用 Azure 门户中的 Active Directory 联合服务 (AD FS) 应用活动报告，可以确定哪些应用能够迁移到 Azure AD。 该报告会评估所有 AD FS 与 Azure AD 兼容性，检查是否有任何问题，并提供单个应用的迁移准备指导。

有关详细信息，请参阅[使用 AD FS 应用程序活动报告将应用程序迁移到 Azure AD](../manage-apps/migrate-adfs-application-activity.md)。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>用户请求管理员同意的新工作流（公共预览版）

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 访问控制

管理员可以通过新的管理员许可工作流向需要管理员批准的应用授予访问权限。 如果用户尝试访问某个应用但无法提供同意，则他们现在可以发送请求以获取管理员的批准。 通过电子邮件将请求发送给被指定为审阅者的所有管理员，并将其放置在可以从 Azure 门户访问的队列中。 在审阅者对挂起的请求执行操作之后，会向请求用户通知该操作。

有关详细信息，请参阅[配置管理员许可工作流（预览版）](../manage-apps/configure-admin-consent-workflow.md)。

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>用于管理可选声明的新的 Azure AD 应用注册令牌配置体验（公共预览版）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 开发人员体验

Azure 门户上的新“Azure AD 应用注册令牌配置”边栏选项卡现在会向应用开发人员显示其应用可选声明的动态列表。 此新体验有助于简化 Azure AD 应用迁移过程，并最大程度减少可选声明的配置错误。

有关详细信息，请参阅[向 Azure AD 应用提供可选声明](../develop/active-directory-optional-claims.md)。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 权利管理中的新的两阶段批准工作流（公共预览版）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理

我们引入了新的两阶段批准工作流，允许要求两个审批者批准用户对访问包的请求。 例如，你可以对其进行设置，要求请求用户的管理员必须首先批准，然后还可以要求资源所有者批准。 如果其中一个审批者未批准，则不授予访问权限。

有关详细信息，请参阅[在 Azure AD 权利管理中更改访问包的请求和审批设置](../governance/entitlement-management-access-package-request-policy.md)。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>对“我的应用”页面和新工作区的更新（公共预览版）

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** 第三方集成

你现在可以自定义组织用户查看和访问已更新的“我的应用”体验的方式。 此新体验还包括新工作区功能，这使用户查找和管理应用更加容易。

有关新的“我的应用”体验和创建工作区的详细信息，请参阅[在“我的应用”门户中创建工作区。](../manage-apps/access-panel-collections.md)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B 协作的 Google 社交 ID 支持（正式发布）

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** 用户身份验证

Azure AD 中使用 Google 社交 ID（Gmail 帐户）的新支持有助于使用户与合作伙伴更轻松地进行协作。 你的合作伙伴无需再创建和管理新的 Microsoft 帐户。 Microsoft Teams 现在完全支持所有客户端上的 Google 用户，以及常见身份验证终结点和租户相关的身份验证终结点内的 Google 用户。

有关详细信息，请参阅[将 Google 添加为 B2B 来宾用户的标识提供者](../external-identities/google-federation.md)。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>条件访问和单一登录的 Microsoft Edge 移动支持（正式发布）

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

iOS 和 Android 上 Microsoft Edge 的 Azure AD 现在支持 Azure AD 单一登录和条件访问：

- **Microsoft Edge 单一登录 (SSO)：** 单一登录现在可用于本机客户端（如 Microsoft Outlook 和 Microsoft Edge）中所有 Azure AD 连接的应用。

- **Microsoft Edge 条件访问：** 通过基于应用程序的条件访问策略，用户必须使用 Microsoft Intune 保护的浏览器，如 Microsoft Edge。

有关条件访问和 Microsoft Edge SSO 的详细信息，请参阅[条件访问和单一登录的 Microsoft Edge 移动支持现已正式发布](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)这一博客文章。 有关如何使用[基于应用的条件访问](../conditional-access/app-based-conditional-access.md)或[基于设备的条件访问](../conditional-access/require-managed-devices.md)设置客户端应用的详细信息，请参阅[使用 Microsoft Intune 策略保护的浏览器管理 Web 访问](/intune/apps/app-configuration-managed-browser)。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 权利管理（正式发布）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理

Azure AD 权利管理是一项新的标识治理功能，可帮助组织大规模管理标识和访问生命周期。 这项新功能有助于实现访问请求工作流的自动化，可跨组、应用和 SharePoint 站点访问分配、评审和过期。

借助 Azure AD 权利管理，你可以更有效地管理员工以及组织外部需要访问这些资源的用户的访问权限。

有关详细信息，请参阅[什么是 Azure AD 权利管理？](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动为这些新支持的 SaaS 应用执行用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成  

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

[SAP Cloud Platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md)、[RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md)、[SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md)、[Miro](../saas-apps/miro-provisioning-tutorial.md)、[Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md)、[Infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md)、[OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md)、[Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 应用库中推出了新的联合应用 - 2019 年 11 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

我们已于 2019 年 11 月将以下 21 款支持联合的新应用添加到了应用库：

[Airtable](../saas-apps/airtable-tutorial.md)、[Hootsuite](../saas-apps/hootsuite-tutorial.md)、[Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md)、[Bitly](../saas-apps/bitly-tutorial.md)、[Riva](../saas-apps/riva-tutorial.md)、[ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、[NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md)、[TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、[Motus](../saas-apps/motus-tutorial.md)、[MyAryaka](../saas-apps/myaryaka-tutorial.md)、[BlueMail](https://loginself1.bluemail.me/)、[Beedle](https://teams-web.beedle.co/#/)、[Visma](../saas-apps/visma-tutorial.md)、[OneDesk](../saas-apps/onedesk-tutorial.md)、[Foko Retail](../saas-apps/foko-retail-tutorial.md)、[Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md)、[Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md)、[uniFLOW Online](../saas-apps/uniflow-online-tutorial.md)、[Claromentis](../saas-apps/claromentis-tutorial.md)、[Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md)、[e4enable](https://portal.e4enable.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>已改进的新 Azure AD 应用程序库

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们更新了 Azure AD 应用程序库，使你能够更轻松地查找在 Azure Active Directory 租户上支持预配、OpenID 连接和 SAML 的预集成应用。

有关详细信息，请参阅[向 Azure Active Directory 租户添加应用程序](../manage-apps/add-application-portal.md)。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>应用角色定义长度限制从 120 增加为 240 个字符

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们已从客户那里听说，在某些应用和服务中应用角色定义值的长度限制为 120 个字符，这对于他们来说太短了。 作为响应，我们已将角色值定义的最大长度增加为 240 个字符。

有关使用特定于应用程序的角色定义的详细信息，请参阅[在应用程序中添加应用角色并在令牌中接收它们](../develop/howto-add-app-roles-in-azure-ad-apps.md)。

---

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>弃用用于 Azure AD 标识保护风险检测的 identityRiskEvent API

**类型：** 更改计划 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

为了响应开发人员的反馈，Azure AD Premium P2 订阅者现在可以使用用于 Microsoft Graph 的新 riskDetection API 来对 Azure AD 标识保护的风险检测数据执行复杂的查询。 现有的 [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API beta 版本将于 2020 年 1 月 10 日左右停止返回数据。 如果你的组织使用的是 identityRiskEvent API，你应将其转换到新的 riskDetection API。

有关新 riskDetection API 的详细信息，请参阅[风险检测 API 参考文档](/graph/api/resources/riskdetection)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>应用程序代理支持 SameSite 属性和 Chrome 80

**类型：** 更改计划 **服务类别：** 应用代理 **产品功能：** 访问控制

在 Chrome 80 浏览器发布之前的几周，我们计划更新应用程序代理 Cookie 处理 SameSite 属性的方式。 随着 Chrome 80 的发布，任何未指定 SameSite 属性的 Cookie 都将被视为已设置为 `SameSite=Lax`。

为了帮助避免由于此更改而产生的潜在负面影响，我们将通过以下方式更新应用程序代理访问和会话 Cookie：

- 将“使用安全 Cookie”设置的默认值设置为“是” 。

- 将“SameSite”属性的默认值设置为“无” 。

    >[!NOTE]
    > 应用程序代理访问 Cookie 始终只通过安全通道进行传输。 这些更改仅适用于会话 Cookie。

有关应用程序代理 Cookie 设置的详细信息，请参阅[用于在 Azure Active Directory 中访问本地应用程序的 Cookie 设置](../app-proxy/application-proxy-configure-cookie-settings.md)。

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>应用程序注册门户 (apps.dev.microsoft.com) 中的应用注册（旧版）和应用管理不再可用

**类型：** 更改计划 **服务类别：** 不适用 **产品功能：** 开发人员体验

具有 Azure AD 帐户的用户不能再使用应用程序注册门户 (apps.dev.microsoft.com) 注册或管理应用程序，也不能在 Azure 门户的应用注册（旧版）体验中注册和管理应用程序。

要详细了解新的应用注册体验，请参阅 [Azure 门户中的应用注册培训指南](../develop/quickstart-register-app.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>从每用户 MFA 迁移到基于条件访问的 MFA 过程中，用户无需重新注册

**类型：** 已修复 **服务类别：** MFA **产品功能：** 标识安全性和保护

我们已经修复了一个已知问题，即如果为用户禁用了每用户多重身份验证 (MFA)，然后通过条件访问策略启用了 MFA，则用户需要重新注册。

如需用户重新注册，可以从 Azure AD 门户中的用户身份验证方法中选择“要求重新注册 MFA”选项。 

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>转换和发送 SAML 令牌中的声明的新功能

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

我们添加了其他功能，可帮助自定义和发送 SAML 令牌中的声明。 这些新功能包括：

- 其他声明转换功能，可帮助修改声明中发送的值。

- 能够将多个转换应用于单个声明。

- 能够根据用户类型和用户所属的组来指定声明源。

有关这些新功能（包括如何使用它们）的详细信息，请参阅[自定义 SAML 令牌中为企业应用程序发出的声明](../develop/active-directory-saml-claims-customization.md)。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 中最终用户的新的“我的登录”页面

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 监视和报告

我们添加了新的“我的登录”页面 (https://mysignins.microsoft.com) )，通过该页面，组织的用户可以查看他们最近的登录历史记录，以检查是否存在异常活动。 通过此新页面，用户可以看到：

- 是否有人试图猜测他们的密码。

- 是否有攻击者成功登录他们的帐户以及登录位置。

- 攻击者尝试访问的应用。

有关详细信息，请参阅[用户现在可以检查其登录历史记录以了解异常活动](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)博客。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>将 Azure AD 域服务 (Azure AD DS) 从经典虚拟网络迁移到 Azure 资源管理器虚拟网络

**类型：** 新功能 **服务类别：** Azure AD 域服务 **产品功能：** Azure AD 域服务

对于一直使用经典虚拟网络的客户来说，这是一个好消息！ 现在，你可以执行从经典虚拟网络到现有资源管理器虚拟网络的一次性迁移。 迁移到资源管理器虚拟网络后，你将能够利用其他已升级的功能，例如精细密码策略、电子邮件通知和审核日志。

有关详细信息，请参阅[预览 - 将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](../../active-directory-domain-services/migrate-from-classic-vnet.md)。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 页面合同布局的更新

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

我们针对 Azure AD B2C 的页面协定 1.2.0 版引入了一些新的更改。 在此更新后的版本中，你现在可以控制元素的加载顺序，这也有助于停止在加载样式表 (CSS) 时发生的闪烁。

有关对页面合同所做的更改的完整列表，请参阅[版本更改日志](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd)。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>“我的应用”页面和新工作区（公共预览版）的更新

**类型：** 新功能 **服务类别：** 我的应用 **产品功能：** 访问控制

你现在可以自定义你的组织用户查看和访问全新“我的应用”体验（包括使用新的工作区功能）的方式，使他们更轻松地查找应用。 新的工作区功能充当你的组织用户已经可以访问的应用的筛选器。

有关如何推出新的“我的应用”体验和创建工作区的详细信息，请参阅[在“我的应用”（预览版）门户上创建工作区](../manage-apps/access-panel-collections.md)。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>支持基于月度活跃用户的计费模式（正式发布版）

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

Azure AD B2C 现在支持月度活跃用户 (MAU) 计费。 MAU 计费基于日历月内有身份验证活动的唯一的用户数。 现有客户可以随时切换到这种新的计费方法。

自 2019 年 11 月 1 日起，所有新客户将自动使用此方法计费。 此计费方法通过成本优势和提前计划功能，为客户带来好处。

有关详细信息，请参阅[升级到月度活跃用户计费模型](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 应用库中推出了新的联合应用 - 2019 年 10 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 10 月将这 35 款支持联合的新应用添加到了应用库：

[In Case of Crisis - Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md)、[Juno Journey](../saas-apps/juno-journey-tutorial.md)、[ExponentHR](../saas-apps/exponenthr-tutorial.md)、[Tact](https://www.tact.ai/products/tact-assistant)、[OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm)、[Salestim](https://www.salestim.com/)、[Learnster](../saas-apps/learnster-tutorial.md)、[Dynatrace](../saas-apps/dynatrace-tutorial.md)、[HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、[Freshworks](../saas-apps/freshworks-tutorial.md)、[eCornell](../saas-apps/ecornell-tutorial.md)、[ShipHazmat](../saas-apps/shiphazmat-tutorial.md)、[Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md)、[Contentful](../saas-apps/contentful-tutorial.md)、[Bindtuning](https://bindtuning.com/login)、[HireVue Coordinate - Europe](https://www.hirevue.com/)、[HireVue Coordinate - USOnly](https://www.hirevue.com/)、[HireVue Coordinate - US](https://www.hirevue.com/)、[WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)、[Cloudmore](../saas-apps/cloudmore-tutorial.md)、[Visit.org](../saas-apps/visitorg-tutorial.md)、[Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup)、[Paylocity](../saas-apps/paylocity-tutorial.md)、[Mail Luck!](../saas-apps/mail-luck-tutorial.md)、[Teamie](https://theteamie.com/)、[Velocity for Teams](https://velocity.peakup.org/teams/login)、[SIGNL4](https://account.signl4.com/manage)、[EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md)、[ScreenMeet](https://console.screenmeet.com/)、[Omega Point](https://pi.ompnt.com/)、[Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)、[Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)、[ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md)、[iHealthHome Care Navigation System](https://ihealthnav.com/account/signin)、[Qubie](https://www.qubie.app/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 门户中的“合并的安全性”菜单项

**类型：** 已更改的功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

现在，可以从 Azure 门户中新的“安全性”菜单项和“搜索”栏中访问所有可用的 Azure AD 安全功能 。 此外，新的“安全性”登陆页面（称为“安全性 - 入门”）将提供指向公共文档、安全性指南和部署指南的链接 。

新的“安全性”菜单包括：

- 条件性访问
- 标识保护
- 安全中心
- 标识安全评分
- 身份验证方法
- MFA
- 风险报告 - 风险用户、风险登录、风险检测
- 等等。

有关详细信息，请参阅[安全性 - 入门](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>通过自动续订增强了 Office 365 组过期策略

**类型：** 已更改的功能 **服务类别：** 组管理 **产品功能：** 标识生命周期管理

Office 365 组过期策略已得到增强，可以自动续订其成员正在使用的组。 组将根据所有 Office 365 应用（包括 Outlook、SharePoint 和 Teams）中的用户活动自动续订。

此增强功能有助于减少组过期通知，并有助于确保活动组继续可用。 如果你已拥有 Office 365 组的有效过期策略，则无需执行任何操作即可启用此新功能。

有关详细信息，请参阅[为 Office 365 组配置过期策略](../enterprise-users/groups-lifecycle.md)。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>更新了 Azure AD 域服务 (Azure AD DS) 创建体验

**类型：** 已更改的功能 **服务类别：** Azure AD 域服务 **产品功能：** Azure AD 域服务

我们已更新 Azure AD 域服务 (Azure AD DS)，以包括改进的全新创建体验，只需单击三下即可创建托管域！ 此外，现在可以从模板上传和部署 Azure AD DS。

有关详细信息，请参阅[教程：创建和配置 Azure Active Directory 域服务实例](../../active-directory-domain-services/tutorial-create-instance.md)。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>更改计划：弃用 Power BI 内容包

**类型：** 更改计划 **服务类别：** 报告 **产品功能：** 监视和报告

自 2019 年 10 月 1 日起，Power BI 将开始弃用所有内容包，包括 Azure AD Power BI 内容包。 作为此内容包的替代项，你可以使用 Azure AD 工作簿来深入了解与 Azure AD 相关的服务。 即将推出其他工作簿，包括有关仅限报告模式的条件访问策略、基于应用同意的见解等的工作簿。

有关工作簿的详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报告](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。 有关弃用内容包的详细信息，请参阅[宣布 Power BI 模板应用正式发布](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)博客文章。

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>“我的个人资料”会重新命名，并与 Microsoft Office 帐户页集成

**类型：** 更改计划 **服务类别：** 我的个人资料/帐户 **产品功能：** 协作

从 10 月开始，“我的个人资料”体验将更改为“我的帐户”。 作为更改的一部分，当前所使用的“我的个人资料”都将更改为“我的帐户” 。 除了命名更改和一些设计改进之外，更新后的体验还将提供与 Microsoft Office 帐户页的其他集成。 具体来说，你将能够从“概述帐户”页面访问 Office 安装和订阅，以及从“隐私”页面访问与 Office 相关的联系人首选项 。

有关“我的个人资料”（预览版）体验的详细信息，请参阅[“我的个人资料”（预览版）门户概述](../user-help/my-account-portal-overview.md)。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 门户（公共预览版）中使用 CSV 文件批量管理组和成员

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

我们非常高兴地宣布在 Azure AD 门户中提供批量组管理体验的公共预览版。 现在，可以使用 CSV文件和 Azure AD 门户来管理组和成员列表，包括：

- 添加或删除组中的成员。

- 从目录下载组列表。

- 下载特定组的组成员的列表。

有关详细信息，请参阅[批量添加成员](../enterprise-users/groups-bulk-import-members.md)、[批量删除成员](../enterprise-users/groups-bulk-remove-members.md)、[批量下载成员列表](../enterprise-users/groups-bulk-download-members.md)和[批量下载组列表](../enterprise-users/groups-bulk-download.md)。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>现已通过新的管理员同意终结点支持动态同意

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

我们创建了新的管理员同意终结点以支持动态同意，对于希望在 Microsoft 标识平台上使用动态同意模型的应用，这很有帮助。

有关如何使用这一新终结点的详细信息，请参阅[使用管理员同意终结点](../develop/v2-admin-consent.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD 应用库中推出了新的联合应用 - 2019 年 9 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 9 月将这 29 款支持联合的新应用添加到了应用库：

[ScheduleLook](https://schedulelook.bbsonlineservices.net/)、[MS Azure SSO Access for Ethidex Compliance Office&trade; - Single sign-on](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)、[iServer Portal](../saas-apps/iserver-portal-tutorial.md)、[SKYSITE](../saas-apps/skysite-tutorial.md)、[Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md)、[WorkBoard](../saas-apps/workboard-tutorial.md)、`https://apps.yeeflow.com/`、[ARC Facilities](../saas-apps/arc-facilities-tutorial.md)、[Luware Stratus Team](https://stratus.emea.luware.cloud/login)、[Wide Ideas](https://wideideas.online/wideideas/)、[Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md)、[JDLT Client Hub](https://clients.jdlt.co.uk/login)、[RENRAKU](../saas-apps/renraku-tutorial.md)、[SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)、[Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md)、`https://app.penneo.com/`、`https://app.testhtm.com/settings/email-integration`、[Cintoo Cloud](https://aec.cintoo.com/login)、[Whitesource](../saas-apps/whitesource-tutorial.md)、[Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md)、[IDC](../saas-apps/idc-tutorial.md)、[CakeHR](../saas-apps/cakehr-tutorial.md)、[BIS](../saas-apps/bis-tutorial.md)、[Coo Kai Team Build](https://ms-contacts.coo-kai.jp/)、[Sonarqube](../saas-apps/sonarqube-tutorial.md)、[Adobe Identity Management](../saas-apps/tutorial-list.md)、[Discovery Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md)、[Amelio](https://app.amelio.co/)、`https://itask.yipinapp.com/`

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-azure-ad-global-reader-role"></a>新的 Azure AD 全局读取者角色

**类型：** 新功能 **服务类别：** Azure AD 角色 **产品功能：** 访问控制

自 2019 年 9 月 24 日起，我们将开始推出名为“全局读取者”的新 Azure Active Directory (AD) 角色。 此推出将从生产和全球云客户 (GCC) 开始，并于 10 月在全球范围内完成。

“全局读取者”角色是对应于全局管理员的只读角色。 充当此角色的用户可以跨 Microsoft 365 服务读取设置和管理信息，但无法执行管理操作。 我们创建了“全局读取者”角色，用于帮助减少组织中全局管理员的数量。 由于全局管理员帐户功能强大且容易受到攻击，因此建议拥有的全局管理员不多于五个。 建议使用“全局读取者”角色进行计划、审核或调查。 还建议将“全局读取者”角色与其他受限管理员角色（例如 Exchange 管理员）结合使用，以帮助在无需“全局读取者”角色的情况下完成工作。

“全局读取者”角色可使用新的 Microsoft 365 管理中心、Exchange 管理中心、Teams 管理中心、安全中心、合规中心、Azure AD 管理中心和设备管理管理中心。

>[!NOTE]
> 在公共预览版推出初期，“全局读取者”角色不适用于以下对象：SharePoint、Privileged Access Management、客户密码箱、敏感度标签、Teams 生命周期、Teams 报告和调用分析、Teams IP 电话设备管理以及 Teams 应用目录。

有关详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../roles/permissions-reference.md)。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理从 Power BI 移动应用访问本地报表服务器

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 访问控制

通过 Power BI 移动应用与 Azure AD 应用程序代理之间的新集成，可以安全地登录 Power BI 移动应用并查看本地 Power BI 报表服务器上托管的组织的任何报表。

有关 Power BI 移动应用（包括应用下载位置）的信息，请访问 [Power BI 网站](https://powerbi.microsoft.com/mobile/)。 有关如何使用 Azure AD 应用程序代理设置 Power BI 移动应用的详细信息，请参阅[使用 Azure AD 应用程序代理启用对 Power BI 移动版的远程访问](../app-proxy/application-proxy-integrate-with-power-bi.md)。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>已提供新版本的 AzureADPreview PowerShell 模块

**类型：** 已更改的功能 **服务类别：** 其他 **产品功能：** 目录

新的 cmdlet 已添加到 AzureADPreview 模块，以帮助定义和分配 Azure AD 中的自定义角色，具体的 cmdlet 包括：

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect 的新版本

**类型：** 已更改的功能 **服务类别：** 其他 **产品功能：** 目录

我们已发布用于自动升级客户的 Azure AD Connect 的更新版本。 此新版本包括多项新功能、改进和 bug 修复。 有关此新版本的详细信息，请参阅 [Azure AD Connect：版本发布历史记录](../hybrid/reference-connect-version-history.md#14250)。

---

### <a name="azure-active-directory-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>现已提供 Azure Active Directory 多重身份验证 (MFA) 服务器版本 8.0.2

**类型：** 已修复 **服务类别：** MFA **产品功能：** 标识安全性和保护

如果你是在 2019 年 7 月 1 日之前激活了 MFA 服务器的现有客户，则现在可以下载最新版本的 MFA 服务器（8.0.2 版）。 在此新版本中，我们：

- 修复了一个问题，因此当 Azure AD 同步将某用户从“禁用”更改为“启用”时，系统将向该用户发送电子邮件。

- 修复了一个问题，以便客户可以在成功升级的同时继续使用标记功能。

- 添加了科索沃 (+383) 国家/地区代码。

- 向 MultiFactorAuthSvc.log 添加了免验证一次审核日志记录。

- 改进了 Web 服务 SDK 的性能。

- 修复了其他次要 bug。

自 2019 年 7 月 1 日起，Microsoft 已停止为新部署提供 MFA 服务器。 需要多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 有关详细信息，请参阅[规划一个基于云的 Azure AD 多重身份验证部署](../authentication/howto-mfa-getstarted.md)。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 门户（公共预览版）中提供针对组的增强搜索、筛选和排序

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

我们非常高兴地宣布在 Azure AD 门户提供与组相关的增强体验的公共预览版。 这些增强功能可帮助你更好地管理组和成员列表，具体提供了以下功能：

- 高级搜索功能，例如对组列表的子字符串搜索。
- 成员和所有者列表上的高级筛选和排序选项。
- 针对成员和所有者列表的新搜索功能。
- 针对大型组的更准确的组计数。

有关详细信息，请参阅[在 Azure 门户中管理组](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>新的自定义角色可用于应用注册管理（公共预览版）

**类型：** 新功能 **服务类别：** Azure AD 角色 **产品功能：** 访问控制

自定义角色（与 Azure AD P1 或 P2 订阅一起提供）现在可以通过让你创建具有特定权限的角色定义，然后将这些角色分配给特定资源，从而帮助提供精细访问。 当前，通过使用用于管理应用注册的权限来创建自定义角色，然后将该角色分配给特定的应用。 有关自定义角色的详细信息，请参阅 [Azure Active Directory 中的自定义管理员角色（预览版）](../roles/custom-overview.md)。

如需其他受支持的权限或资源（当前未显示），你可以将反馈发送到我们的 [Azure 反馈网站](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)，然后我们会将请求添加到更新路线图中。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新的预配日志有助于监视应用预配部署并对其进行故障排除（公共预览版）

**类型：** 新功能 **服务类别：** 应用预配 **产品功能：** 标识生命周期管理

新的预配日志可用于帮助监视用户和组预配部署并对其进行故障排除。 这些新的日志文件包含有关以下内容的信息：

- 在 [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 中成功创建的组
- 从 [AWS 单一帐户访问](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)导入的角色
- 不是通过 [Workday](../saas-apps/workday-inbound-tutorial.md) 导入的员工

有关详细信息，请参阅 [Azure Active Directory 门户中的预配报告（预览版）](../reports-monitoring/concept-provisioning-logs.md)。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>适用于所有 Azure AD 管理员的新安全报告（正式发布）

**类型：** 新功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

默认情况下，所有 Azure AD 管理员很快将能够在 Azure AD 中访问新式安全报告。 在 9 月底之前，你将能够使用新式安全报告顶部的横幅返回到旧报告。

新式安全报告将提供较早版本的其他功能，包括：

- 高级筛选和排序
- 批量操作，如解除用户风险
- 确认已泄露的实体或安全实体
- 风险状态，包括：存在风险、已解除、已修正和已确认遭泄露
- 新的风险相关检测（可供 Azure AD Premium 订阅者使用）

有关详细信息，请参阅[风险用户](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)、[风险登录](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)和[风险检测](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>用户分配的托管标识可用于虚拟机和虚拟机规模集（正式发布）

**类型：** 新功能 **服务类别：** Azure 资源托管标识 **产品功能：** 开发人员体验

用户分配的托管标识现在通常可用于虚拟机和虚拟机规模集。 作为此过程的一部分，Azure 可以在 Azure AD 租户中创建一个标识，该标识受在使用中的订阅信任，并且可以分配给一个或多个 Azure 服务实例。 有关用户分配的托管标识的详细信息，请参阅[什么是 Azure 资源托管标识？](../managed-identities-azure-resources/overview.md)。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>用户可以使用移动应用或硬件标志重置其密码（正式发布）

**类型：** 已更改的功能 **服务类别：** 自助式密码重置 **产品功能：** 用户身份验证

在组织中注册了移动应用的用户现在可以重置自己的密码，方式是通过批准 Microsoft Authenticator 应用的通知或通过输入其移动应用或硬件标志中的代码。

有关详细信息，请参阅[工作原理：Azure AD 自助式密码重置](../authentication/concept-sspr-howitworks.md)。 有关用户体验的详细信息，请参阅[重置自己的工作或学校密码概述](https://support.microsoft.com/account-billing/register-the-password-reset-verification-method-for-a-work-or-school-account-47a55d4a-05b0-4f67-9a63-f39a43dbe20a)。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 忽略代表方案的 MSAL.NET 共享缓存

**类型：** 已修复 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

从 Azure AD 身份验证库 (ADAL.NET) 5.0.0 预览版开始，应用开发人员必须[针对 Web 应用和 Web API 序列化每个帐户的一个缓存](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)。 否则，在某些场景中使用 Java [代理流](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java)以及 `UserAssertion` 的一些特定用例，可能会导致特权提升。 为了避免出现此漏洞，ADAL.NET 现在会忽略用于针对代理场景的 dotnet (MSAL.NET) 共享缓存的身份验证库。

有关此问题的详细信息，请参阅 [Azure Active Directory 身份验证库特权提升漏洞](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD 应用库中推出了新的联合应用 - 2019 年 8 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 8 月将这 26 款支持联合的新应用添加到了应用库：

[Civic Platform](../saas-apps/civic-platform-tutorial.md)、[Amazon Business](../saas-apps/amazon-business-tutorial.md)、[ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md)、[Cognidox](../saas-apps/cognidox-tutorial.md)、[Viareport's Inativ Portal (Europe)](../saas-apps/viareports-inativ-portal-europe-tutorial.md)、[Azure Databricks](https://azure.microsoft.com/services/databricks)、[Robin](../saas-apps/robin-tutorial.md)、[Academy Attendance](../saas-apps/academy-attendance-tutorial.md)、[Priority Matrix](https://sync.appfluence.com/pmwebng/)、[Cousto MySpace](https://cousto.platformers.be/account/login)、[Uploadcare](https://uploadcare.com/accounts/signup/)、[Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md)、[CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md)、[Chargebee](../saas-apps/chargebee-tutorial.md)、[deliver.media&trade; Portal](https://portal.deliver.media)、[Frontline Education](../saas-apps/frontline-education-tutorial.md)、[F5](https://www.f5.com/products/security/access-policy-manager)、[stashcat AD connect](https://www.stashcat.com)、[Blink](../saas-apps/blink-tutorial.md)、[Vocoli](../saas-apps/vocoli-tutorial.md)、[ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md)、[Sigstr](../saas-apps/sigstr-tutorial.md)、[Darwinbox](../saas-apps/darwinbox-tutorial.md)、[Watch by Colors](../saas-apps/watch-by-colors-tutorial.md)、[Harness](../saas-apps/harness-tutorial.md)、[EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>提供新版 AzureAD PowerShell 和 AzureADPreview PowerShell 模块

**类型：** 已更改的功能 **服务类别：** 其他 **产品功能：** 目录

AzureAD 和 AzureAD 预览 PowerShell 模块的新更新可用：

- 已将新 `-Filter` 参数添加到 AzureAD 模块中的 `Get-AzureADDirectoryRole` 参数。 此参数可帮助筛选由 cmdlet 返回的目录角色。
- 新的 cmdlet 已添加到 AzureADPreview 模块，以帮助定义和分配 Azure AD 中的自定义角色，具体的 cmdlet 包括：

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>对 Azure 门户中动态组规则生成器的 UI 的改进

**类型：** 已更改的功能 **服务类别：** 组管理 **产品功能：** 协作

我们对 Azure 门户中提供的动态组规则生成器进行了一些 UI 改进，以帮助你更轻松地设置新规则或更改现有规则。 通过此设计改进，你可以创建最多包含五个表达式（而不是只包含一个表达式）的规则。 我们还更新了设备属性列表，以删除弃用的设备属性。

有关详细信息，请参阅[管理动态成员身份规则](../enterprise-users/groups-dynamic-membership.md)。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>新 Microsoft Graph 应用权限可与访问评审一起使用

**类型：** 已更改的功能 **服务类别：** 访问评审 **产品功能：** 标识监管

我们引入了新的 Microsoft Graph 应用权限（即 `AccessReview.ReadWrite.Membership`），该权限允许应用自动创建和检索对组成员身份和应用分配的访问评审。 此权限可以由计划的作业使用，也可以作为自动化的一部分使用，无需登录的用户上下文。

有关详细信息，请参阅[如何通过 PowerShell 并使用 Microsoft Graph 应用权限创建 Azure AD 访问评审示例博客](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD 活动日志现在可用于 Azure Monitor 中的政府云实例

**类型：** 已更改的功能 **服务类别：** 报告 **产品功能：** 监视和报告

我们很高兴地宣布，Azure AD 活动日志现在可用于 Azure Monitor 中的政府云实例。 现可将 Azure AD 日志发送到存储帐户或事件中心，以与 SIEM 工具（例如 [Sumologic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)、[Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md) 和 [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md)）集成。

有关设置 Azure Monitor 的详细信息，请参阅 [Azure Monitor 中的 Azure AD 活动日志](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations)。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>将用户体验更新为新的增强安全信息体验

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

我们将于 2019 年 9 月 25 日关闭用于注册和管理用户安全信息的旧版非增强安全信息体验，而仅启用新的[增强版本](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。 这意味着你的用户将无法再使用旧版体验。

有关增强的安全信息体验的详细信息，请参阅[管理文档](../authentication/concept-registration-mfa-sspr-combined.md)和[用户文档](../user-help/security-info-setup-signin.md)。

#### <a name="to-turn-on-this-new-experience-you-must"></a>要启用这种新体验，你必须：

1. 以全局管理员或用户管理员身份登录到 Azure 门户。

2. 转到“Azure Active Directory”>“用户设置”>管理访问面板预览功能的设置。

3. 在“用户可以使用预览功能注册和管理安全信息 - 增强”区域中，选择“已选中”，然后选择一组用户或选择“全部”，为租户中的所有用户启用此功能  。

4. 在“用户可以使用预览功能注册和管理安全性”信息区域中，选择“无”********。

5. 保存设置。

    保存设置后，将无法再访问旧版安全信息体验。

>[!Important]
>如果在 2019 年 9 月 25 日之前未完成这些步骤，则将自动为 Azure Active Directory 租户启用增强体验。 如有疑问，请通过 registrationpreview@microsoft.com 与我们联系。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>将更严格地验证使用 POST 登录的身份验证请求

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 标准

自 2019 年 9 月 2 日起，将根据 HTTP 标准更严格地验证使用 POST 方法的身份验证请求。 具体来说，将不再从请求窗体值中删除空格和双引号 (")。 预计这些更改不会破坏任何现有客户端，并且将有助于确保每次都能可靠地处理发送到 Azure AD 的请求。

有关详细信息，请参阅 [Azure AD 中断性变更通知](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>更改计划：应用程序代理服务更新以仅支持 TLS 1.2

**类型：** 更改计划 **服务类别：** 应用代理 **产品功能：** 访问控制

为了帮助提供最强加密，我们将开始仅限应用程序代理服务访问 TLS 1.2 协议。 首先会向已使用 TLS 1.2 协议的客户推出此限制，因此对你没有任何影响。 TLS 1.0 和 TLS 1.1 协议将于 2019 年 8 月 31 日完全弃用。 仍在使用 TLS 1.0 和 TLS 1.1 的客户将提前收到通知，以便为此更改做好准备。

若要在整个更改期间保持与应用程序代理服务的连接，建议务必更新客户端-服务器和浏览器-服务器组合以使用 TLS 1.2。 还建议务必包含员工用于访问通过应用程序代理服务发布的应用的任何客户端系统。

有关详细信息，请参阅[在 Azure Active Directory 中添加本地应用程序，以通过应用程序代理进行远程访问](../app-proxy/application-proxy-add-on-premises-application.md)。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>更改计划：应用程序库即将进行设计更新

**类型：** 更改计划 **服务类别：** 企业应用 **产品功能：** SSO

“添加应用程序”边栏选项卡的“从库中添加”区域的设计中即将出现新的用户界面更改 。 这些更改将帮助你更轻松地找到支持自动预配、OpenID Connect、安全断言标记语言 (SAML) 和密码单一登录 (SSO) 的应用。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>更改计划：从 Office 365 IP 地址中删除 MFA 服务器 IP 地址

**类型：** 更改计划 **服务类别：** MFA **产品功能：** 标识安全性和保护

我们将从 [Office 365 IP 地址和 URL Web 服务](/office365/enterprise/office-365-ip-web-service)中删除 MFA 服务器 IP 地址。 如果你当前依靠这些页面来更新防火墙设置，则必须确保你还包含 [Azure Active Directory 多重身份验证服务器入门](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)一文的“Azure Active Directory 多重身份验证服务器防火墙要求”部分中所述的 IP 地址列表。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>仅应用令牌现在要求资源租户中存在客户端应用

**类型：** 已修复 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

我们于 2019 年 7 月 26 日更改了通过[客户端凭据授权](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)提供仅应用令牌的方式。 以前，无论客户端应用是否位于租户中，应用都可以获取令牌来调用其他应用。 我们更新了此行为之后，单一租户资源（有时称为 Web API）只能由资源租户中存在的客户端应用调用。

如果应用不在资源租户中，你将收到一条错误消息，该消息提示 `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`。要解决此问题，必须使用[管理员同意终结点](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)或[通过 PowerShell](../develop/howto-authenticate-service-principal-powershell.md) 在租户中创建客户端应用服务主体，以确保你的租户已获取该应用在租户内操作的权限。

有关详细信息，请参阅[身份验证的新增功能有哪些？](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)。

> [!NOTE]
> 客户端和 API 之间仍然不需要现有同意。 应用仍应执行自己的授权检查。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>使用 FIDO2 安全密钥通过新的无密码方式登录 Azure AD

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

Azure AD 客户现在可以设置策略来管理其组织的用户和组的 FIDO2 安全密钥。 最终用户还可以自行注册安全密钥，并使用这些密钥在支持 FIDO 的设备上登录到网站上的 Microsoft 帐户，以及登录到其已联接 Azure AD 的 Windows 10 设备。

有关详细信息，请参阅[为 Azure AD 启用无密码登录（预览版）](../authentication/concept-authentication-passwordless.md)以获取管理员相关信息；请参阅[设置安全信息以使用安全密钥（预览版）](../user-help/security-info-setup-security-key.md)以获取最终用户相关信息。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD 应用库中推出的全新联合应用 - 2019 年 7 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 7 月将这 18 款支持联合的新应用添加到了应用库中：

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md)、[Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md)、[Clever Nelly](../saas-apps/clever-nelly-tutorial.md)、[AcquireIO](../saas-apps/acquireio-tutorial.md)、[Looop](https://www.looop.co/schedule-a-demo/)、[productboard](../saas-apps/productboard-tutorial.md)、[MS Azure SSO Access for Ethidex Compliance Office&trade;](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)、[Hype](../saas-apps/hype-tutorial.md)、[Abstract](../saas-apps/abstract-tutorial.md)、[Ascentis](../saas-apps/ascentis-tutorial.md)、[Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、[Wandera](../saas-apps/wandera-tutorial.md)、[TwineSocial](https://twinesocial.com/)、[Kallidus](../saas-apps/kallidus-tutorial.md)、[HyperAnna](../saas-apps/hyperanna-tutorial.md)、[PharmID WasteWitness](https://pharmid.com/)、[i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、[JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动为这些新支持的 SaaS 应用执行用户帐户预配

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 监视和报告

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>网络安全组的新 Azure AD 域服务的服务标记

**类型：** 新功能 **服务类别：** Azure AD 域服务 **产品功能：** Azure AD 域服务

如果厌倦了管理 IP 地址和范围的长列表，则可以在 Azure 网络安全组中使用新的 AzureActiveDirectoryDomainServices 网络服务标记来帮助保护到 Azure AD 域服务虚拟网络子网的入站流量。

有关此新服务标记的详细信息，请参阅 [Azure AD 域服务的网络安全组](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核（公共预览版）

**类型：** 新功能 **服务类别：** Azure AD 域服务 **产品功能：** Azure AD 域服务

我们很高兴地宣布发布了 Azure AD 域服务安全审核的公共预览版。 安全审核通过使用 Azure AD 域服务门户将安全审核事件流式传输到目标资源（包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心），从而帮助你深入了解身份验证服务。

有关详细信息，请参阅[为 Azure AD 域服务启用安全审核（预览版）](../../active-directory-domain-services/security-audit-events.md)。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新身份验证方法使用情况和见解（公共预览版）

**类型：** 新功能 **服务类别：** 自助式密码重置 **产品功能：** 监视和报告

新的身份验证方法使用情况和见解报告可帮助你了解 Azure AD 多重身份验证和自助式密码重置等功能在组织中的注册和使用方式，包括每种功能的注册用户数、自助式密码重置用于重置密码的频率以及重置方法。

有关详细信息，请参阅[身份验证方法使用情况和见解（预览版）](../authentication/howto-authentication-methods-activity.md)。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>所有 Azure AD 管理员都可以使用新的安全报告（公共预览版）

**类型：** 新功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

现在，所有 Azure AD 管理员都可以选择现有安全报告（例如“标记为存在风险的用户”报告）顶部的横幅，以开始使用“风险用户”和“风险登录”报告中显示的新安全体验  。 随着时间的推移，所有安全报告都将从旧版本升级到新版本，新报告提供了以下附加功能：

- 高级筛选和排序

- 批量操作，如解除用户风险

- 确认已泄露的实体或安全实体

- 风险状态，包括：存在风险、已解除、已修正和已确认遭泄露

有关详细信息，请参阅[风险用户报告](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)和[风险登录报告](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核（公共预览版）

**类型：** 新功能 **服务类别：** Azure AD 域服务 **产品功能：** Azure AD 域服务

我们很高兴地宣布发布了 Azure AD 域服务安全审核的公共预览版。 安全审核通过使用 Azure AD 域服务门户将安全审核事件流式传输到目标资源（包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心），从而帮助你深入了解身份验证服务。

有关详细信息，请参阅[为 Azure AD 域服务启用安全审核（预览版）](../../active-directory-domain-services/security-audit-events.md)。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>使用 SAML/WS-Fed 的新 B2B 直接联合（公共预览版）

**类型：** 新功能 **服务类别：** B2B **产品功能：** B2B/B2C

直接联合通过使用支持 SAML 或 WS-Fed 标准的标识系统，可帮助你更轻松地与其 IT 托管标识解决方案而不是 Azure AD 的合作伙伴进行协作。 设置与合作伙伴的直接联合关系后，从该域邀请的任何新来宾用户都可以使用其现有组织帐户与你进行协作，从而使来宾的用户体验更顺畅。

有关详细信息，请参阅[与面向来宾用户的 AD FS 和第三方提供商的直接联合（预览版）](../external-identities/direct-federation.md)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动为这些新支持的 SaaS 应用执行用户帐户预配

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 监视和报告

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD 门户中对重复的组名的全新检查

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

现在，当你从 Azure AD 门户创建或更新组名时，我们将执行检查以查看是否在资源中重复使用现有组名。 如果我们确定其他组已使用该名称，系统将要求你修改名称。

有关详细信息，请参阅[在 Azure AD 门户中管理组](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD 现在支持回复（重定向）URI 中的静态查询参数

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

现在，Azure AD 应用可以注册和使用带有静态查询参数（例如 `https://contoso.com/oauth2?idp=microsoft`）的回复（重定向）URI 来处理 OAuth 2.0 请求。 静态查询参数遵循回复 URI 的字符串匹配，就像回复 URI 的任何其他部分一样。 如果没有已注册的字符串与 URL 解码的重定向 URL 匹配，则该请求会遭到拒绝。 如果找到了回复 URI，则使用整个字符串重定向用户，包括静态查询参数。

仍然禁止使用动态回复 URI，因为它们存在安全风险，不能用于保留身份验证请求中的状态信息。 为此，请使用 `state` 参数。

当前，Azure 门户的应用注册屏幕仍会阻止查询参数。 但是，可以手动编辑应用部件清单 (manifest)，以在应用中添加和测试查询参数。 有关详细信息，请参阅[身份验证的新增功能有哪些？](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters)。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>现在可以通过 PowerShell Cmdlet 使用 Azure AD 的活动日志 (MS Graph API)

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

我们很高兴地宣布，Azure AD 活动日志（审核和登录报告）现已通过 Azure AD PowerShell 模块提供。 以前，你可以使用 MS Graph API 终结点创建自己的脚本，现在我们已将该功能扩展到 PowerShell cmdlet。

有关如何使用这些 cmdlet 的详细信息，请参阅[用于报告的 Azure AD PowerShell cmdlet](../reports-monitoring/reference-powershell-reporting.md)。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>更新了 Azure AD 中审核和登录日志的筛选器控件

**类型：** 已更改的功能 **服务类别：** 报告 **产品功能：** 监视和报告

我们已更新审核和登录日志报告，因此你现在可以应用各种筛选器，而无需将其作为列添加到报告屏幕上。 此外，现在可以决定要在屏幕上显示的筛选器数量。 这些更新协同工作，使报告更易于阅读，并且可以更好地满足你的需求。

有关这些更新的详细信息，请参阅[筛选审核日志](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs)和[登录活动](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>用于 Microsoft Graph 的新 riskDetections API（公共预览版）

**类型：** 新功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

我们很高兴地宣布，用于 Microsoft Graph 的新 riskDetections API 现提供公共预览版。 可以使用此新 API 查看与组织的标识保护相关的用户和登录风险检测的列表。 还可以使用此 API 更有效地查询风险检测，包括有关检测类型、状态、级别等的详细信息。

有关详细信息，请参阅[风险检测 API 参考文档](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 应用库中推出的全新联合应用 - 2019 年 6 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 6 月将这 22 款支持联合的新应用添加到了应用库中：

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md)、[Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md)、[ANAQUA](../saas-apps/anaqua-tutorial.md)、[Azure VPN Client](https://portal.azure.com/)、[ExpenseIn](../saas-apps/expensein-tutorial.md)、[Helper Helper](../saas-apps/helper-helper-tutorial.md)、[Costpoint](../saas-apps/costpoint-tutorial.md)、[GlobalOne](../saas-apps/globalone-tutorial.md)、[Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/)、[Skore](https://app.justskore.it/)、[Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md)、[CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md)、[Scrible Edu](https://www.scrible.com/sign-in/#/create-account)、[PandaDoc](../saas-apps/pandadoc-tutorial.md)、[Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial)、[Proptimise OS](https://proptimise.co.uk/)、[Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md)、Oracle Access Manager for Oracle Retail Merchandising、Oracle Access Manager for Oracle E-Business Suite、Oracle IDCS for E-Business Suite、Oracle IDCS for PeopleSoft、Oracle IDCS for JD Edwards

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动为这些新支持的 SaaS 应用执行用户帐户预配

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 监视和报告

现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>查看 Azure AD 预配服务的实时进度

**类型：** 已更改的功能 **服务类别：** 应用预配 **产品功能：** 标识生命周期管理

我们已更新 Azure AD 预配体验，新增了一个进度栏，显示你在用户预配进度中的完成进度。 此更新体验还提供了有关当前周期内预配的用户数以及目前已预配的用户数的信息。

有关详细信息，请参阅[检查用户预配状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>公司品牌现在显示在注销和错误屏幕上

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

我们已更新 Azure AD，现在你的公司品牌显示在注销和错误屏幕以及登录页面上。 无需执行任何操作即可启用此功能，Azure AD 仅使用你已在 Azure 门户的“公司品牌”区域中设置的资产。

有关如何设置公司品牌的详细信息，请参阅[将品牌添加到组织的 Azure Active Directory 页面](./customize-branding.md)。

---

### <a name="azure-active-directory-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Active Directory 多重身份验证 (MFA) 服务器不再适用于新部署

**类型：** 弃用 **服务类别：** MFA **产品功能：** 标识安全性和保护

从 2019 年 7 月 1 日开始，Microsoft 将不再为新部署提供 MFA 服务器。 希望在其组织中要求进行多重身份验证的新客户现在必须使用基于云的 Azure AD 多重身份验证。 在 7 月 1 日之前已激活 MFA 服务器的客户不会看到任何更改。 你仍可下载最新版本、获取将来的更新，以及生成激活凭据。

有关详细信息，请参阅 [Azure Active Directory 多重身份验证服务器入门](../authentication/howto-mfaserver-deploy.md)。 有关基于云的 Azure AD 多重身份验证的详细信息，请参阅[规划一个基于云的 Azure AD 多重身份验证部署](../authentication/howto-mfa-getstarted.md)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服务更改：未来仅支持在应用程序代理服务上访问 TLS 1.2 协议

**类型：** 更改计划 **服务类别：** 应用代理 **产品功能：** 访问控制

为了帮助向客户提供一流的加密，我们将对应用程序代理服务上的访问限制为只能访问 TLS 1.2 协议。 我们正在逐步向已仅使用 TLS 1.2 协议的客户推出此更改，因此你应该不会看到任何更改。

我们将于 2019 年 8 月 31 日弃用 TLS 1.0 和 TLS 1.1，但我们将发出其他提前通知，让你有时间为此更改做好准备。 要为此更改做准备，请确保将客户端-服务器和浏览器-服务器组合（包括用户用于通过应用程序代理访问已发布的应用的所有客户端）更新为使用 TLS 1.2 协议，以维持与应用程序代理服务的连接。 有关详细信息，请参阅[在 Azure Active Directory 中添加本地应用程序，以通过应用程序代理进行远程访问](../app-proxy/application-proxy-add-on-premises-application.md#prerequisites)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用使用情况和见解报告来查看与应用相关的登录数据

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 监视和报告

现在，可以使用 Azure 门户的“企业应用程序”区域中的使用情况和见解报告来获取以应用程序为中心的登录数据，包括以下信息：

- 组织中最常用的应用

- 登录失败次数最多的应用

- 每个应用的最常出现的登录错误

有关此功能的详细信息，请参阅 [Azure Active Directory 门户中的使用情况和见解报告](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>使用 Azure AD 自动将用户预配到云应用

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 监视和报告

请按照以下新教程操作，以使用 Azure AD 预配服务自动为以下基于云的应用创建、删除和更新用户帐户：

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

还可以按照此新 [Dropbox 教程](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)操作，其中提供有关如何预配组对象的信息。

有关如何通过自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Azure AD 中现已提供标识安全分数（正式发布）

**类型：** 新功能 **服务类别：** 不适用 **产品功能：** 标识安全性和保护

现在，可以使用 Azure AD 中的标识安全分数功能监视和改进标识安全状况。 标识安全分数功能使用单个仪表板，以帮助你：

- 根据介于 1 到 223 之间的分数，客观地衡量你的标识安全状况。

- 规划你的标识安全改进

- 查看安全改进是否成功

有关标识安全分数功能的详细信息，请参阅[什么是 Azure Active Directory 中的标识安全分数？](./identity-secure-score.md)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>现已推出新的应用注册体验（正式发布）

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 开发人员体验

新[应用注册](https://aka.ms/appregistrations)体验现已正式发布。 这种新体验包括你熟悉的 Azure 门户和应用程序注册门户中的所有关键功能，并通过以下方式对这些功能进行了改进：

- **改进了管理应用。** 现在，可以在一个位置集中查看所有应用，而不必跨不同的门户查看应用。

- **简化了应用注册。** 从改进的导航体验到全新的权限选择体验，现在可以更轻松地注册和管理应用。

- **添加了详细信息。** 可以查找有关应用的更多详细信息，包括快速入门指南等。

有关详细信息，请参阅 [Microsoft 标识平台](../develop/index.yml)和[应用注册体验现已正式发布！](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 博客公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>风险用户 API 中新增的用于标识保护的功能

**类型：** 新功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

我们很高兴地宣布，你现在可以使用“风险用户 API”来检索用户的风险历史记录、解除风险用户，并确认用户是否遭到攻击。 此更改可帮助你更高效地更新用户的风险状态以及了解他们的风险历史记录。

有关详细信息，请参阅[风险用户 API 参考文档](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 应用库中推出的全新联合应用 - 2019 年 5 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 5 月将这 21 款支持联合的新应用添加到了应用库中：

[Freedcamp](../saas-apps/freedcamp-tutorial.md)、[Real Links](../saas-apps/real-links-tutorial.md)、[Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)、[Simple Sign](../saas-apps/simple-sign-tutorial.md)、[Braze](../saas-apps/braze-tutorial.md)、[Displayr](../saas-apps/displayr-tutorial.md)、[Templafy](../saas-apps/templafy-tutorial.md)、[Marketo Sales Engage](https://toutapp.com/login)、[ACLP](../saas-apps/aclp-tutorial.md)、[OutSystems](../saas-apps/outsystems-tutorial.md)、[Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md)、[Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md)、[Cobalt](../saas-apps/cobalt-tutorial.md)、[webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md)、[RedFlag](https://pocketstop.com/redflag/)、[Whatfix](../saas-apps/whatfix-tutorial.md)、[Control](../saas-apps/control-tutorial.md)、[JOBHUB](../saas-apps/jobhub-tutorial.md)、[NEOGOV](../saas-apps/neogov-tutorial.md)、[Foodee](../saas-apps/foodee-tutorial.md)、[MyVR](../saas-apps/myvr-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD 门户中改进的组创建和管理体验

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

我们已改进了 Azure AD 门户中与组相关的体验。 借助这些改进，管理员可以更好地管理组列表、成员列表，并提供其他创建选项。

改进包括：

- 按成员身份类型和组类型进行基本筛选。

- 添加新列，例如“源”和“电子邮件地址”。

- 可以选择多个组、成员和所有者列表，以便轻松删除。

- 可以选择电子邮件地址并在组创建过程中添加所有者。

有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](./active-directory-groups-create-azure-portal.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>在 Azure AD 门户中为 Office 365 组配置命名策略（正式发布）

**类型：** 已更改的功能 **服务类别：** 组管理 **产品功能：** 协作

管理员现在可以使用 Azure AD 门户为 Office 365 组配置命名策略。 此更改有助于对组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略：

- 定义自动添加到组名中的前缀或后缀。

- 为组织上传一组自定义的屏蔽字词，即不允许在组名中使用的字词（例如“CEO、工资单、HR”）。

有关详细信息，请参阅[对 Office 365 组强制实施命名策略](../enterprise-users/groups-naming-policy.md)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 终结点现在可用于 Azure AD 活动日志（正式发布）

**类型：** 已更改的功能 **服务类别：** 报告 **产品功能：** 监视和报告

我们非常高兴地宣布 Microsoft Graph API 终结点支持 Azure AD 活动日志已正式发布。 在此版本中，现在可以同时使用 Azure AD 审核日志和登录日志 API 的 1.0 版。

有关详细信息，请参阅 [Azure AD 审核日志 API 概述](/graph/api/resources/azure-ad-auditlog-overview)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理员现在可以将条件访问用于合并的注册过程（公共预览版）

**类型：** 新功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

管理员现在可以创建条件访问策略，以供合并的注册页面使用。 这包括在以下情况下应用允许注册的策略：

- 用户处于受信任的网络中。

- 用户的登录风险较低。

- 用户位于受管理设备上。

- 用户同意组织的使用条款 (TOU)。

有关条件访问和密码重置的详细信息，可以参阅 [Azure AD 的条件访问结合了 MFA 和密码重置注册体验博客文章](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 有关组合注册过程的条件访问策略的详细信息，请参阅[组合注册的条件访问策略](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration)。 有关 Azure AD 使用条款功能的详细信息，请参阅 [Azure Active Directory 使用条款功能](../conditional-access/terms-of-use.md)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新 Azure AD 威胁智能检测现在作为 Azure AD 标识保护的一部分提供

**类型：** 新功能 **服务类别：** Azure AD 标识保护 **产品功能：** 标识安全性和保护

Azure AD 威胁智能检测现在作为更新后的 Azure AD 标识保护功能的一部分提供。 此新功能有助于为特定用户或活动（该活动与基于 Microsoft 内部和外部威胁智能源的已知攻击模式一致）指出异常用户活动。

有关更新版本的 Azure AD 标识保护的详细信息，请参阅[四个主要的 Azure AD 标识保护增强功能现提供公共预览版](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)博客和[什么是 Azure Active Directory 标识保护（已刷新）？](../identity-protection/overview-identity-protection.md) 一文。 有关 Azure AD 威胁智能检测的详细信息，请参阅 [Azure Active Directory 标识保护风险检测](../identity-protection/concept-identity-protection-risks.md)一文。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 权利管理现已可用（公共预览版）

**类型：** 新功能 **服务类别：** Identity Governance **产品功能：** 标识监管

Azure AD 权利管理现已提供公共预览版，它可帮助客户委托访问包的管理，而该访问包定义了员工和业务合作伙伴请求访问的方式、必须批准的对象以及访问时长。 访问包可以管理 Azure AD 和 Office 365 组中的成员身份、企业应用程序中的角色分配以及 SharePoint Online 站点的角色分配。 有关权利管理的详细信息，请参阅 [Azure AD 权利管理概述](../governance/entitlement-management-overview.md)。 要详细了解 Azure AD Identity Governance 功能的范围（包括 Privileged Identity Management、访问评审和使用条款），请参阅[什么是 Azure AD Identity Governance？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 门户中为 Office 365 组配置命名策略（公共预览版）

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

管理员现在可以使用 Azure AD 门户为 Office 365 组配置命名策略。 此更改有助于对组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略：

- 定义自动添加到组名中的前缀或后缀。

- 为组织上传一组自定义的屏蔽字词，即不允许在组名中使用的字词（例如“CEO、工资单、HR”）。

有关详细信息，请参阅[对 Office 365 组强制实施命名策略](../enterprise-users/groups-naming-policy.md)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure Monitor 中现已提供 Azure AD 活动日志（正式发布）

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

为了帮助处理有关 Azure AD 活动日志的可视化效果的反馈，我们在 Log Analytics 中引入了新的见解功能。 此功能可帮助使用交互式模板（称为“工作簿”）获取有关 Azure AD 资源的见解。 这些预构建的工作簿可以提供有关应用或用户的详细信息，其中包括：

- **登录。** 提供有关应用和用户的详细信息，其中包括登录位置、正在使用的操作系统或浏览器客户端和版本，以及成功或失败的登录次数。

- **旧式身份验证和条件访问。** 提供有关使用旧式身份验证的应用和用户的详细信息，包括由条件访问策略触发的多重身份验证使用情况、使用条件访问策略的应用等。

- **登录失败分析。** 帮助确定登录错误是由于用户操作、策略问题还是基础结构而导致的。

- **自定义报表。** 可以新建工作簿或编辑现有工作簿，从而为组织自定义见解功能。

有关详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 应用库中推出的全新联合应用 - 2019 年 4 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019年 4 月将这 21 款支持联合的新应用添加到了应用库中：

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md)、[HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md)、[Percolate](../saas-apps/percolate-tutorial.md)、[MobiControl](../saas-apps/mobicontrol-tutorial.md)、[Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md)、[Shibumi](../saas-apps/shibumi-tutorial.md)、[Benchling](../saas-apps/benchling-tutorial.md)、[MileIQ](https://mileiq.onelink.me/991934284/7e980085)、[PageDNA](../saas-apps/pagedna-tutorial.md)、[EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md)、[RStudio Connect](../saas-apps/rstudio-connect-tutorial.md)、[AMMS](../saas-apps/amms-tutorial.md)、[Mitel Connect](../saas-apps/mitel-connect-tutorial.md)、[Alibaba Cloud (Role-based SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md)、[Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md)、[Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md)、[GreenOrbit](../saas-apps/greenorbit-tutorial.md)、[Workgrid](../saas-apps/workgrid-tutorial.md)、[monday.com](../saas-apps/mondaycom-tutorial.md)、[SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md)、[Indiggo](https://indiggolead.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新访问评审频率选项和多个角色选择

**类型：** 新功能 **服务类别：** 访问评审 **产品功能：** 标识监管

借助 Azure AD 访问评审中的新更新，你可以：

- 除了之前已有的“每周一次”、“每月一次”、“每季度一次”和“每年一次”选项之外，将访问评审的频率更改为“每半年一次”。

- 创建单个访问评审时，选择多个 Azure AD 和 Azure 资源角色。 在这种情况下，所有角色都是使用相同设置进行设置的，并且所有审阅者会同时收到通知。

有关如何创建访问评审的详细信息，请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](../governance/create-access-review.md)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 正在转换电子邮件警报系统，为一些客户发送新的电子邮件发件人信息

**类型：** 已更改的功能 **服务类别：** AD Sync **产品功能：** 平台

Azure AD Connect 正在转换电子邮件警报系统，系统可能会向某些客户显示新的电子邮件发件人。 要解决此问题，必须将 `azure-noreply@microsoft.com` 添加到组织的允许列表中，否则将无法继续接收来自 Office 365、Azure 或同步服务的重要警报。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>现在可以在 Azure AD Connect 中成功地将 UPN 后缀从一个联盟域更改为另一个联盟域

**类型：** 已修复 **服务类别：** AD Sync **产品功能：** 平台

现在，可以在 Azure AD Connect 中成功将用户的 UPN 后缀从一个联盟域更改为另一个联盟域。 此修复意味着在同步过程中，你将不会再遇到 FederatedDomainChangeError 错误消息，也不会收到指出“由于属性 [FederatedUser.UserPrincipalName] 无效，因此无法在 Azure Active Directory 中更新此对象。 请更新本地目录服务中的值”的通知电子邮件。


---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>使用 Azure AD 中基于应用保护的条件访问策略提高安全性（公共预览版）

**类型：** 新功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

现在，可以通过“要求应用保护”策略来使用基于应用保护的条件访问。 此新策略有助于防止出现以下情况，从而提高组织的安全性：

- 用户在没有 Microsoft Intune 许可证的情况下访问应用。

- 用户无法获取 Microsoft Intune 应用保护策略。

- 用户在未配置 Microsoft Intune 应用保护策略的情况下访问应用。

有关详细信息，请参阅[如何要求应用保护策略通过条件访问来访问云应用](../conditional-access/app-protection-based-conditional-access.md)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 中对 Azure AD 单一登录和条件访问的新支持（公共预览版）

**类型：** 新功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

我们增强了对 Microsoft Edge 的 Azure AD 支持，包括提供对 Azure AD 单一登录和条件访问的新支持。 如果以前使用过 Microsoft Intune Managed Browser，则现在可以改用 Microsoft Edge。

有关使用条件访问设置和管理设备和应用的详细信息，请参阅[要求受管理设备通过条件访问来访问云应用](../conditional-access/require-managed-devices.md)和[要求批准的客户端应用通过条件访问来访问云应用](../conditional-access/app-based-conditional-access.md)。 若要详细了解如何使用 Microsoft Edge 和 Microsoft Intune 策略来管理访问权限，请参阅[使用受 Microsoft Intune 策略保护的浏览器管理 Internet 访问权限](/intune/app-configuration-managed-browser)。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C 中的 Identity Experience Framework 和自定义策略支持现已推出 (GA)

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

现在，可以在 Azure AD B2C 中创建自定义策略（包括以下受 Azure SLA 大规模支持的任务）：

- 使用自定义策略创建和上传自定义身份验证用户旅程。

- 将用户旅程逐步描述为声明提供程序之间的交换。

- 在用户旅程中定义条件分支。

- 转换和映射声明以用于实时决策和通信。

- 在自定义身份验证用户旅程中使用支持 REST API 的服务。 例如使用电子邮件提供程序、CRM 和专有授权系统。

- 与符合 OpenIDConnect 协议的标识提供者联合。 例如与多租户 Azure AD、社交帐户提供者或双重验证提供者联合。

有关创建自定义策略的详细信息，请参阅 [Azure Active Directory B2C 中自定义策略的开发人员说明](../../active-directory-b2c/custom-policy-developer-notes.md)并阅读 [Alex Simon 的博客文章（包括案例研究）](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD 应用库中推出的全新联合应用 - 2019 年 3 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 3 月将这 14 款支持联合的新应用添加到了应用库中：

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/)、[MediusFlow](https://office365.cloudapp.mediusflow.com/)、[ePlatform](../saas-apps/eplatform-tutorial.md)、[Fulcrum](../saas-apps/fulcrum-tutorial.md)、[ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md)、[Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md)、[Lean](../saas-apps/lean-tutorial.md)、[Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md)、[Cinode](https://cinode.com/)、[Iris Intranet](../saas-apps/iris-intranet-tutorial.md)、[Empactis](../saas-apps/empactis-tutorial.md)、[SmartDraw](../saas-apps/smartdraw-tutorial.md)、[Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md)、[TAS](../saas-apps/tas-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD 库中的新 Zscaler 和 Atlassian 预配连接器 - 2019 年 3 月

**类型：** 新功能 **服务类别：** 应用预配 **产品功能：** 第三方集成

自动创建、更新和删除以下应用的用户帐户：

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md)、[Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md)、[Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md)、[Zscaler Two](../saas-apps/zscaler-two-provisioning-tutorial.md)、[Zscaler Three](../saas-apps/zscaler-three-provisioning-tutorial.md)、[Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md)、[Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

有关如何通过自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>在 Azure AD 门户中还原和管理已删除的 Office 365 组

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

现在，可以通过 Azure AD 门户查看和管理已删除的 Office 365 组。 此更改有助于查看可还原的组，并让你可以永久删除组织不需要的任何组。

有关详细信息，请参阅[还原已过期或已删除的组](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Azure AD SAML 保护的本地应用现可通过应用程序代理使用单一登录（公共预览版）

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 访问控制

现在，可以为通过 SAML 身份验证的本地应用提供单一登录 (SSO) 体验，并且可以通过应用程序代理对这些应用进行远程访问。 有关如何为本地应用设置 SAML SSO 的详细信息，请参阅[具有应用程序代理的本地应用的 SAML 单一登录（预览版）](../app-proxy/application-proxy-configure-single-sign-on-on-premises-apps.md)。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>请求循环中的客户端应用将中断，以提高可靠性和用户体验

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

客户端应用可能会在短时间内错误地发出数百个相同的登录请求。 这些请求（无论是否成功）都会导致用户体验不佳且增加 IDP 的工作负载，同时延长所有用户的延迟以及降低 IDP 的可用性。

此更新向客户端应用发送 `invalid_grant` 错误（即 `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`），该客户端应用在短时间内多次发出重复请求，这超出了正常操作范围。 遇到此问题的客户端应用应显示交互式提示，要求用户再次登录。 有关此更改以及在遇到此错误时如何修复应用的详细信息，请参阅[身份验证的新增功能有哪些？](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted)。

---

### <a name="new-audit-logs-user-experience-now-available"></a>新的审核日志用户体验现已推出

**类型：** 已更改的功能 **服务类别：** 报告 **产品功能：** 监视和报告

我们已创建新的 Azure AD 审核日志页面，以帮助提高可读性和改进信息搜索方式。 若要查看新的“审核日志”页面，请在 Azure AD 的“活动”部分中选择“审核日志”  。

![新的审核日志页面，其中包含示例信息](media/whats-new/audit-logs-page.png)

有关新的“审核日志”页面的详细信息，请参阅 [Azure Active Directory 门户中的审核活动报告](../reports-monitoring/concept-audit-logs.md)。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>新的警告和指南，用于帮助防止因配置错误的条件访问策略而导致管理员锁定

**类型：** 已更改的功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

为了防止管理员因错误配置条件访问策略而意外地将自己锁定在租户之外，我们在 Azure 门户中创建了新的警告和更新指南。 有关新指南的详细信息，请参阅 [Azure Active Directory 条件访问中的服务依赖关系的定义](../conditional-access/service-dependencies.md)。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>改进了移动设备上的最终用户使用条款体验

**类型：** 已更改的功能 **服务类别：** 使用条款 **产品功能：** 调控

我们已更新现有的使用条款体验，以帮助改进在移动设备上查看和同意使用条款的方式。 现在，你可以放大和缩小、返回、下载信息，以及选择超链接。 有关更新后的使用条款的详细信息，请参阅 [Azure Active Directory 使用条款功能](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users)。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>新的 Azure AD 活动日志下载体验已推出

**类型：** 已更改的功能 **服务类别：** 报告 **产品功能：** 监视和报告

现在可以直接从 Azure 门户下载大量活动日志。 通过此更新，你可以执行以下操作：

- 下载最多 250,000 个行。

- 下载完成后收到通知。

- 自定义文件名。

- 确定输出格式（可以是 JSON 或 CSV）。

有关此功能的详细信息，请参阅[快速入门：使用 Azure 门户下载审核报告](../reports-monitoring/howto-download-logs.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大更改：更新 Exchange ActiveSync (EAS) 评估条件的方式

**类型：** 更改计划 **服务类别：** 条件访问 **产品功能：** 访问控制

我们正在更新 Exchange ActiveSync (EAS) 评估以下条件的方式：

- 用户位置（基于国家/地区、区域或 IP 地址）

- 登录风险

- 设备平台

如果以前在条件访问策略中使用了这些条件，请注意条件行为可能会更改。 例如，如果以前在策略中使用了用户位置条件，则可能会发现当前根据用户位置跳过了该策略。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>可配置的 Azure AD SAML 令牌加密（公共预览版）

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

现在，可以配置任何受支持的 SAML 应用以接收加密的 SAML 令牌。 配置完成并应用于应用时，Azure AD 使用从存储在 Azure AD 中的证书获得的公钥来加密发出的 SAML 断言。

有关配置 SAML 令牌加密的详细信息，请参阅[配置 Azure AD SAML 令牌加密](../manage-apps/howto-saml-token-encryption.md)。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>使用 Azure AD 访问评审为组或应用创建访问评审

**类型：** 新功能 **服务类别：** 访问评审 **产品功能：** 调控

现在可以将多个组或应用添加到组成员身份或应用分配的单个 Azure AD 访问评审中。 具有多个组或应用的访问评审使用同一设置进行设置，同时通知所有包含的审阅者。

有关如何使用 Azure AD 访问评审创建访问评审的详细信息，请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 应用库中已推出新的联合应用 - 2019 年 2 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 2 月将这 27 款支持联合的新应用添加到了应用库中：

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md)、[MindTickle](../saas-apps/mindtickle-tutorial.md)、[FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)、[AirStack](../saas-apps/airstack-tutorial.md)、[Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md)、[IDrive](../saas-apps/idrive-tutorial.md)、[Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md)、[Brightidea](../saas-apps/brightidea-tutorial.md)、[AlertOps](../saas-apps/alertops-tutorial.md)、[Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md)、Permission Click、[Brandfolder](../saas-apps/brandfolder-tutorial.md)、[StoregateSmartFile](../saas-apps/smartfile-tutorial.md)、[Pexip](../saas-apps/pexip-tutorial.md)、[Stormboard](../saas-apps/stormboard-tutorial.md)、[Seismic](../saas-apps/seismic-tutorial.md)、[Share A Dream](https://www.shareadream.org/how-it-works)、[Bugsnag](../saas-apps/bugsnag-tutorial.md)、[webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md)、[Knowledge Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md)、[OU Campus](../saas-apps/ou-campus-tutorial.md)[Periscope Data](../saas-apps/periscope-data-tutorial.md)、[Netop Portal](../saas-apps/netop-portal-tutorial.md)、[smartvid.io](../saas-apps/smartvid.io-tutorial.md)、[PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md)、[ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="enhanced-combined-mfasspr-registration"></a>增强了组合 MFA/SSPR 注册

**类型：** 已更改的功能 **服务类别：** 自助式密码重置 **产品功能：** 用户身份验证

为响应客户的反馈，我们增强了组合 MFA/SSPR 注册预览体验，可帮助用户更快速地注册其 MFA 和 SSPR 的安全信息。

**若要立即为用户启用增强型体验，请按照以下步骤操作：**

1. 以全局管理员或用户管理员身份登录到 Azure 门户，并依次转到“Azure Active Directory”>“用户设置”>“管理访问面板预览功能的设置”。

2. 在“可以使用预览功能注册和管理安全信息的用户 - 刷新”选项中，选择为“选定用户组”或“所有用户”启用此功能  。

在接下来的几周内，我们将禁用为尚未启用旧版合并 MFA/SSPR 注册预览体验的租户启用此功能的功能。

**若要查看是否将对租户删除此控件，请按照以下步骤操作：**

1. 以全局管理员或用户管理员身份登录到 Azure 门户，并依次转到“Azure Active Directory”>“用户设置”>“管理访问面板预览功能的设置”。

2. 如果将“可以使用预览功能注册和管理安全信息的用户”选项设置为“无”，则该选项将从租户中删除 。

无论你以前是否为用户启用了旧版合并 MFA/SSPR 注册预览体验，旧版体验都将在以后禁用。 因此，强烈建议尽快迁移到新版增强型体验。

有关增强型注册体验的详细信息，请参阅 [Azure AD 合并 MFA 和密码重置注册体验的炫酷增强功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>更新了用户流的策略管理体验

**类型：** 已更改的功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

我们已更新用户流（以前称为内置策略）的策略创建和管理过程，使这一过程更加轻松。 现在所有 Azure AD 租户均默认使用此新体验。

可以使用门户屏幕顶部的“向我们发送反馈”区域中的笑脸或哭脸图标来提供其他反馈和建议。

有关新策略管理体验的详细信息，请参阅 [Azure AD B2C 现在具有 JavaScript 自定义功能和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>选择 Azure AD B2C 提供的特定页面元素版本

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

现在可以选择 Azure AD B2C 提供的页面元素的特定版本。 通过选择特定版本，可以在更新显示在页面上之前对其进行测试，并获得可预测行为。 此外，现在可以选择强制执行特定的页面版本以允许 JavaScript 自定义。 若要启用此功能，请转到用户流中的“属性”页面。

若要详细了解如何选择页面元素的特定版本，请参阅 [Azure AD B2C 现在具有 JavaScript 自定义功能和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C (GA) 的可配置最终用户密码要求

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

现在可为最终用户设置组织的密码复杂性，而不必使用本机 Azure AD 密码策略。 从用户流（以前称为内置策略）的“属性”边栏选项卡中，对于密码复杂性，可以选择“简单”或“强”，也可以创建一组“自定义”要求   。

有关密码复杂性要求配置的详细信息，请参阅[在 Azure Active Directory B2C 中配置密码的复杂性要求](../../active-directory-b2c/password-complexity.md)。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>自定义品牌身份验证体验的新默认模板

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

可以使用位于用户流（以前称为内置策略）的“页面布局”上的新的默认模板为用户创建自定义品牌身份验证体验。

有关使用模板的详细信息，请参阅 [Azure AD B2C 现在具有 JavaScript 自定义功能和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)。

---

## <a name="january-2019"></a>2019 年 1 月

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>使用一次性密码身份验证（公共预览版）的 Active Directory B2B 协作

**类型：** 新功能 **服务类别：** B2B **产品功能：** B2B/B2C

已为无法通过 Azure AD、Microsoft 帐户 (MSA) 或 Google 联合身份验证等其他方式进行身份验证的 B2B 来宾用户引入了一次性密码认证 (OTP)。 这种新的身份验证方法意味着来宾用户无需创建新的 Microsoft 帐户。 相反，在兑换邀请或访问共享资源时，来宾用户可以请求将临时代码发送到电子邮件地址。 使用此临时代码，访客便可以继续登录。

有关详细信息，请参阅[电子邮件一次性密码身份验证（预览）](../external-identities/one-time-passcode.md)和博客 [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)（Azure AD 使拥有任意帐户的任何用户都能无缝地共享和协作）。

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>新的 Azure AD 应用程序代理 Cookie 设置

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 访问控制

我们引入了三种新的 Cookie 设置，这些设置可以供通过应用程序代理发布的应用使用：

- **使用仅限 HTTP 的 Cookie。** 在应用程序代理访问权限和会话 Cookie 上设置 **HTTPOnly** 标志。 启用此设置会带来更多的安全好处，例如有助于防止用户通过客户端脚本复制或修改 Cookie。 建议启用此标志（选择“是”）以享受增加的安全好处。

- **使用安全 Cookie。** 在应用程序代理访问权限和会话 Cookie 上设置 **Secure** 标志。 启用此设置可确保 Cookie 仅通过 TLS 安全通道（例如 HTTPS）传输，因此会带来更多的安全好处，。 建议启用此标志（选择“是”）以享受增加的安全好处。

- **使用永久性 Cookie。** 防止访问 Cookie 在关闭 Web 浏览器时过期。 这些 Cookie 可以持续到访问令牌的生存期结束。 不过，如果过期时间已到，或者用户手动删除了 Cookie，则 Cookie 会重置。 建议保持默认设置“否”，只有在应用较旧且不需在进程之间共享 Cookie 时，才启用该设置。

有关新 Cookie 的详细信息，请参阅[用于在 Azure Active Directory 中访问本地应用程序的 Cookie 设置](../app-proxy/application-proxy-configure-cookie-settings.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD 应用库中已推出新的联合应用 - 2019 年 1 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2019 年 1 月将这 35 款支持联合的新应用添加到了应用库中：

[Firstbird](../saas-apps/firstbird-tutorial.md)、[Folloze](../saas-apps/folloze-tutorial.md)、[Talent Palette](../saas-apps/talent-palette-tutorial.md)、[Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md)、[Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md)、[Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md)、[Expiration Reminder](../saas-apps/expiration-reminder-tutorial.md)、[InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md)、[CorpTax](../saas-apps/corptax-tutorial.md)、[Verb](https://app.verb.net/login)、[OpenLattice](https://openlattice.com/agora)、[TheOrgWiki](https://www.theorgwiki.com/signup)、[Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md)、[GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md)、[Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md)、[AuditBoard](../saas-apps/auditboard-tutorial.md)、[iProva](../saas-apps/iprova-tutorial.md)、[Workable](../saas-apps/workable-tutorial.md)、[CallPlease](https://webapp.callplease.com/create-account/create-account.html)、[GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md)、[CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md)、[Deskradar](../saas-apps/deskradar-tutorial.md)、[Coralogixv](../saas-apps/coralogix-tutorial.md)、[Signagelive](../saas-apps/signagelive-tutorial.md)、[ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md)、[K2 for Office 365](https://www.k2.com/O365)、[Xledger](https://www.xledger.net/)、[iDiD Manager](../saas-apps/idid-manager-tutorial.md)、[HighGear](../saas-apps/highgear-tutorial.md)、[Visitly](../saas-apps/visitly-tutorial.md)、[Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md)、[Acadia](../saas-apps/acadia-tutorial.md)、[Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>新的“Azure AD 标识保护”增强功能（公共预览版）

**类型：** 已更改的功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

我们很高兴地宣布，我们已为“Azure AD 标识保护”公开预览版套餐添加以下增强功能，其中包括：

- 经过更新后集成度更高的用户界面

- 其他 API

- 通过机器学习改进风险评估

- 针对风险用户和风险登录实现产品范围的符合性

有关增强功能的详细信息，请参阅[什么是 Azure Active Directory 标识保护（已刷新）？](../identity-protection/overview-identity-protection.md) 以进行详细的了解并通过产品内提示共享自己的想法。

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>iOS 和 Android 设备上的 Microsoft Authenticator 应用的新应用锁定功能

**类型：** 新功能 **服务类别：** Microsoft Authenticator 应用 **产品功能：** 标识安全性和保护

若要使你的一次性密码、应用信息和应用设置更加安全，可以在 Microsoft Authenticator 应用中开启应用锁定功能。 开启应用锁定意味着你每次打开 Microsoft Authenticator 应用时都会要求你使用 PIN 或生物识别进行身份验证。

有关详细信息，请参阅 [Microsoft Authenticator app FAQ](../user-help/user-help-auth-app-faq.md)（Microsoft Authenticator 应用常见问题解答）。

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>增强的 Azure AD Privileged Identity Management (PIM) 导出功能

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

Privileged Identity Management (PIM) 管理员现在可以为特定资源导出所有活动的和符合资格的角色分配，其中包括针对所有子资源的角色分配。 以前，管理员很难获取某个订阅的角色分配完整列表，他们必须导出每个特定资源的角色分配。

有关详细信息，请参阅[在 PIM 中查看 Azure 资源角色的活动和审核历史记录](../privileged-identity-management/azure-pim-resource-rbac.md)。

---

## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>从同步范围删除的用户不再切换到仅限云的帐户

**类型：** 已修复 **服务类别：** 用户管理 **产品功能：** 目录

>[!Important]
>我们已听说并理解你因为此修复而遭受到的挫折感。 因此，我们已将此更改还原，直到我们可以让你更轻松地在组织中实施此修复。

我们修复了一个 bug。该 bug 表现为：将 Active Directory 域服务 (AD DS) 对象从同步范围中排除后，如果接着在下一个同步周期中将其移到 Azure AD 中的回收站，则用户的 DirSyncEnabled 标志会被错误地切换为 False。 进行此修复以后，如果将用户从同步范围中排除，然后又将其从 Azure AD 回收站还原，则用户帐户会保持预期的从本地 AD 同步的状态，不能在云中托管，因为其授权源 (SoA) 仍旧为本地 AD。

在进行此修复之前，将 DirSyncEnabled 标志切换为 False 会出现问题。 它给人一个错误印象：这些帐户已转换为仅限云的对象，并且可以在云中托管。 但是，这些帐户仍将其 SoA 作为本地项保留，并保留所有来自本地 AD 的已同步属性（影子属性）。 这种情况在 Azure AD 和其他特定的云工作负荷（例如 Exchange Online）中导致多个问题，这些工作负荷预期将这些帐户作为从 AD 同步的帐户处理，但现在这些帐户的表现就像是仅限云的帐户一样。

目前，若要将从 AD 同步的帐户真正地转换为仅限云的帐户，唯一的方法是在租户级别禁用 DirSync，以便触发一项传输 SoA 的后端操作。 此类 SoA 更改要求（但不限于）清除所有本地的相关属性（例如 LastDirSyncTime 属性和影子属性）并将一个信号发送到其他云工作负荷，以便将相应的对象也转换为仅限云的帐户。

因此，此修复可以防止对从 AD 同步的用户的 ImmutableID 属性进行直接更新，这种更新在过去的某些情况下是必需的。 顾名思义，根据设计，Azure AD 中对象的 ImmutableID 是不可更改的。 在 Azure AD Connect Health 和 Azure AD Connect Synchronization 客户端中实现的新功能用于解决以下方案的问题：

- **分阶段对多个用户进行大规模 ImmutableID 更新**

  例如，需要进行耗时很长的 AD DS 林间迁移。 解决方案：使用 Azure AD Connect 配置源定位点，并在用户迁移时将现有的 ImmutableID 值从 Azure AD 复制到本地 AD DS 用户的新林的 ms-DS-Consistency-Guid 属性中。 有关详细信息，请参阅[将 msDS-ConsistencyGuid 用作 sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)。

- **一次性对多个用户进行大规模 ImmutableID 更新**

  例如，在实现 Azure AD Connect 时犯错，现在需要更改 SourceAnchor 属性。 解决方案：在租户级别禁用 DirSync，并清除所有无效的 ImmutableID 值。 有关详细信息，请参阅[禁用 Office 365 的目录同步](/office365/enterprise/turn-off-directory-synchronization)。

- **将本地用户与 Azure AD 中的现有用户重新匹配** 例如，在 AD DS 中重新创建的用户会在 Azure AD 帐户中生成一个重复的用户，系统不会将其与现有的 Azure AD 帐户（孤立对象）重新匹配。 解决方案：在 Azure 门户中使用 Azure AD Connect Health 重新映射源定位点/ImmutableID。 有关详细信息，请参阅[孤立对象场景](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario)。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重大更改：更新了通过 Azure Monitor 提供的审核和登录日志的架构

**类型：** 已更改的功能 **服务类别：** 报告 **产品功能：** 监视和报告

目前，我们正在通过 Azure Monitor 发布审核和登录日志流，使你能够将日志文件与 SIEM 工具或 Log Analytics 无缝集成。 根据客户的反馈，同时为此功能的正式版通告做好准备，我们正在对架构进行以下更改。 在 1 月份的第一周，我们将完成这些架构更改并更新其相关的文档。

#### <a name="new-fields-in-the-audit-schema"></a>审核架构中的新字段
我们将添加一个新的“操作类型”字段，以提供针对资源执行的操作类型。 例如“添加”、“更新”或“删除”。  

#### <a name="changed-fields-in-the-audit-schema"></a>审核架构中已更改的字段
审核架构中的以下字段即将发生变化：

|字段名|更改内容|旧值|新值|
|----------|------------|----------|----------|
|类别|以前它是“服务名称”字段， 现在是“审核类别”字段。 “服务名称”已重命名为“loggedByService”字段。 |<ul><li>帐户预配</li><li>核心目录</li><li>自助密码重置</li></ul>|<ul><li>用户管理</li><li>组管理</li><li>应用管理</li></ul>|
|targetResources|包括顶层的 **TargetResourceType**。|&nbsp;|<ul><li>策略</li><li>应用</li><li>用户</li><li>组</li></ul>|
|loggedByService|提供生成审核日志的服务的名称。|Null|<ul><li>帐户预配</li><li>核心目录</li><li>自助式密码重置</li></ul>|
|结果|提供审核日志的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>失败</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>登录架构中已更改的字段
登录架构中的以下字段即将发生变化：

|字段名|更改内容|旧值|新值|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|以前它是“conditionalaccessPolicies”字段， 现在是“appliedConditionalAccessPolicies”字段。|没有变化|没有变化|
|conditionalAccessStatus|在登录时提供条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>已禁用</li></ul>|
|appliedConditionalAccessPolicies: result|在登录时提供单个条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>已禁用</li></ul>|

有关架构的详细信息，请参阅[解释 Azure Monitor（预览版）中的 Azure AD 审核日志架构](../reports-monitoring/overview-reports.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>对监督式机器学习模型和风险评分引擎做出的“标识保护”改进

**类型：** 已更改的功能 **服务类别：** 标识保护 **产品功能：** 风险评分

对“标识保护”相关的用户和登录风险评估引擎所做的改进有助于提高用户风险评估的准确度和覆盖度。 管理员可能会注意到，用户风险级别不再与特定检测的风险级别直接相关，并且有风险登录事件的数量和级别都已增加。

风险检测现在由监督式机器学习模型评估。该模型使用附加的用户登录和检测模式功能来计算用户风险。 基于此模型，管理员可以发现风险评分较高的用户，即使与该用户关联的检测具有中低型的风险。

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理员可以使用 Microsoft Authenticator 应用（公共预览版）重置自己的密码

**类型：** 已更改的功能 **服务类别：** 自助式密码重置 **产品功能：** 用户身份验证

现在，Azure AD 管理员可以使用 Microsoft Authenticator 应用通知或者任何移动 Authenticator 应用或硬件令牌提供的代码重置自己的密码。 若要重置自己的密码，管理员现在可以使用以下两种方法：

- Microsoft Authenticator 应用通知

- 其他移动 Authenticator 应用/硬件令牌代码

- 电子邮件

- 电话呼叫

- 短信

有关使用 Microsoft Authenticator 应用重置密码的详细信息，请参阅 [Azure AD 自助密码重置 - 移动应用和 SSPR（预览版）](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新的 Azure AD 云设备管理员角色（公共预览版）

**类型：** 新功能 **服务类别：** 设备注册和管理 **产品功能：** 访问控制

管理员可将用户分配到新的云设备管理员角色，以执行云设备管理员任务。 分配有“云设备管理员”角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。

有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>在 Azure AD 中使用新的活动时间戳管理设备（公共预览版）

**类型：** 新功能 **服务类别：** 设备注册和管理 **产品功能：** 设备生命周期管理

我们已认识到，在一段时间后，你必须在 Azure AD 中更新和淘汰组织的设备，以避免环境中存在陈旧设备。 为了帮助完成此过程，Azure AD 现在会使用新的活动时间戳更新设备，以帮助管理设备生命周期。

有关如何获取和使用此时间戳的详细信息，请参阅[如何：在 Azure AD 中管理陈旧的设备](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理员可以要求用户在每台设备上接受使用条款

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 调控

现在，管理员可以启用“要求用户在每台设备上同意”选项，以要求用户在租户中所用的每台设备上接受使用条款。

有关详细信息，请参阅[“Azure Active Directory 使用条款功能”的“按设备实施的使用条款”部分](../conditional-access/terms-of-use.md#per-device-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理员可将使用条款配置为按重复性计划过期

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 调控


现在，管理员可以启用“使同意状态过期”选项，以根据指定的重复性计划，使所有用户对使用条款的同意状态过期。 可以实施每年、半年、每季或每月计划。 使用条款过期后，用户必须重新接受。

有关详细信息，请参阅[“Azure Active Directory 使用条款功能”的“添加使用条款”部分](../conditional-access/terms-of-use.md#add-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理员可将使用条款配置为按每个用户的计划过期

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 调控

现在，管理员可以指定用户必须接受使用条款的持续时间。 例如，管理员可以指定用户必须每隔 90 天重新接受一次使用条款。

有关详细信息，请参阅[“Azure Active Directory 使用条款功能”的“添加使用条款”部分](../conditional-access/terms-of-use.md#add-terms-of-use)。

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 角色的新 Azure AD Privileged Identity Management (PIM) 电子邮件

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

使用 Azure AD Privileged Identity Management (PIM) 的客户现在可以接收每周摘要电子邮件，其中包括过去七天的以下信息：

- 最符合条件的和永久性的角色分配概述

- 激活角色的用户数

- 已分配到 PIM 中的角色的用户数

- 已分配到 PIM 外部的角色的用户数

- PIM 中“永久”的用户数

有关 PIM 和可用电子邮件通知的详细信息，请参阅 [PIM 中的电子邮件通知](../privileged-identity-management/pim-email-notifications.md)。

---

### <a name="group-based-licensing-is-now-generally-available"></a>基于组的许可现已推出正式版

**类型：** 已更改的功能 **服务类别：** 其他 **产品功能：** 目录

基于组的许可已过公共预览期，现已推出正式版。 正式版提高了此功能的可伸缩性，并添加了为单个用户重新处理基于组的许可分配的功能，以及结合 Office 365 E3/A3 许可证使用基于组的许可的功能。

有关基于组的许可的详细信息，请参阅 [Azure Active Directory 中基于组的许可是什么？](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 11 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已在 2018 年 11 月将这 26 款支持联合的新应用添加到了应用库：

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](../saas-apps/hubspot-tutorial.md)、[GetThere](../saas-apps/getthere-tutorial.md)、[Gra-Pe](../saas-apps/grape-tutorial.md)、[eHour](https://getehour.com/try-now)、[Consent2Go](../saas-apps/consent2go-tutorial.md)、[Appinux](../saas-apps/appinux-tutorial.md)、[DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview)、[Useall](../saas-apps/useall-tutorial.md)、[Infinite Campus](../saas-apps/infinitecampus-tutorial.md)、[Alaya](https://alayagood.com)、[HeyBuddy](../saas-apps/heybuddy-tutorial.md)、[Wrike SAML](../saas-apps/wrike-tutorial.md)、[Drift](../saas-apps/drift-tutorial.md)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](../saas-apps/everbridge-tutorial.md)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md)、[Peakon](../saas-apps/peakon-tutorial.md)、[Allbound SSO](../saas-apps/allbound-sso-tutorial.md)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD 日志现在可与 Azure Log Analytics（公共预览版）配合使用

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

我们很高兴地宣布，现在可将 Azure AD 日志转发到 Azure Log Analytics！ 这项呼声最高的功能有助于更好地访问业务、运营和安全分析数据，以及监视基础结构。 有关详细信息，请参阅博客文章 [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)（Azure Log Analytics 中的 Azure Active Directory 活动日志现已提供）。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 10 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已在 2018 年 10 月将这 14 款支持联合的新应用添加到了应用库：

[My Award Points](../saas-apps/myawardpoints-tutorial.md)、[Vibe HCM](../saas-apps/vibehcm-tutorial.md)、ambyint、[MyWorkDrive](../saas-apps/myworkdrive-tutorial.md)、[BorrowBox](../saas-apps/borrowbox-tutorial.md)、Dialpad、[ON24 Virtual Environment](../saas-apps/on24-tutorial.md)、[RingCentral](../saas-apps/ringcentral-tutorial.md)、[Zscaler Three](../saas-apps/zscaler-three-tutorial.md)、[Phraseanet](../saas-apps/phraseanet-tutorial.md)、[Appraisd](../saas-apps/appraisd-tutorial.md)、[Workspot Control](../saas-apps/workspotcontrol-tutorial.md)、[Shuccho Navi](../saas-apps/shucchonavi-tutorial.md)、[Glassfrog](../saas-apps/glassfrog-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD 域服务电子邮件通知

**类型：** 新功能 **服务类别：** Azure AD 域服务 **产品功能：** Azure AD 域服务

Azure AD 域服务在 Azure 门户中提供有关托管域配置错误或问题的警报。 这些警报包含分步引导，使你能够在不联系支持人员的情况下尝试解决问题。

从 10 月份开始，可以自定义托管域的通知设置，以便在发生新警报时，向指定的一组人员发送电子邮件，而无需频繁地在门户中检查最新信息。

有关详细信息，请参阅 [Azure AD 域服务中的通知设置](../../active-directory-domain-services/notifications.md)。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD 门户支持使用 ForceDelete 域 API 删除自定义域

**类型：** 已更改的功能 **服务类别：** 目录管理 **产品功能：** 目录

我们很高兴地宣布，现在可以使用 ForceDelete 域 API，通过将用户、组和应用等引用从自定义域名 (contoso.com) 异步重命名回到初始默认域名 (contoso.onmicrosoft.com)，来删除自定义域名。

如果组织不再使用自定义域名，或者你需要使用其他 Azure AD 的域名，此项更改可帮助你更快地删除自定义域名。

有关详细信息，请参阅[删除自定义域名](../enterprise-users/domains-manage.md#delete-a-custom-domain-name)。

---

## <a name="september-2018"></a>2018 年 9 月

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>已更新动态组的管理员角色权限

**类型：** 已修复 **服务类别：** 组管理 **产品功能：** 协作

我们修复了相关的问题，使特定的管理员角色无需成为组的所有者，即可创建和更新动态成员身份规则。

这些角色为：

- 全局管理员

- Intune 管理员

- 用户管理员

有关详细信息，请参阅[创建动态组和检查状态](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>简化了某些第三方应用的单一登录 (SSO) 配置设置

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

我们已认识到，由于每种应用配置的独特性，为软件即服务 (SaaS) 应用设置单一登录 (SSO) 可能有一定的难度。 我们构建了一个简化的配置体验，可以自动填充以下第三方 SaaS 应用的 SSO 配置设置：

- Zendesk

- ArcGis Online

- Jamf Pro

若要开始使用这种单键式的体验，请转到应用的“Azure 门户” > “SSO 配置”页。  有关详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>“Azure Active Directory - 数据位于何处?”页

**类型：** 新功能 **服务类别：** 其他 **产品功能：** GoLocal

在“Azure Active Directory - 数据位于何处”页中选择公司所在的区域，查看哪个 Azure 数据中心托管了所有 Azure AD 服务的 Azure AD 静态数据。 可以根据公司所在区域的特定 Azure AD 服务来筛选信息。

若要访问此功能和详细信息，请参阅 [Azure Active Directory - 数据位于何处](https://aka.ms/AADDataMap)。

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>适用于“我的应用”访问面板的新部署计划

**类型：** 新功能 **服务类别：** 我的应用 **产品功能：** SSO

查看适用于“我的应用”访问面板的新部署计划 (https://aka.ms/deploymentplans) 。
“我的应用”访问面板为用户提供查找和访问其应用的单一位置。 此门户还为用户提供自助服务功能，例如，请求访问应用和组，或代表他人管理对这些资源的访问。

有关详细信息，请参阅[什么是“我的应用”门户？](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure 门户“登录日志”页上的新“故障排除和支持”选项卡

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

Azure 门户“登录”页上的新“故障排除和支持”选项卡旨在帮助管理员和支持工程师排查 Azure AD 登录相关的问题。 此新选项卡提供错误代码、错误消息和建议的补救措施（如果有）来帮助解决问题。 如果无法解决问题，我们还提供使用“复制到剪贴板”体验创建支持票证的新方法，该体验可在支持票证中填充日志文件的“请求 ID”和“日期(UTC)”字段。  

![显示“新建”选项卡的登录日志](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>增强了用于创建动态成员身份规则的自定义扩展属性的支持

**类型：** 已更改的功能 **服务类别：** 组管理 **产品功能：** 协作

借助此项更新，在为用户创建动态成员身份规则时，现在在动态用户组规则生成器中单击“获取自定义扩展属性”链接，输入唯一的应用 ID，然后即可收到要使用的完整自定义扩展属性列表。 还可以刷新此列表，以获取该应用的任何新自定义扩展属性。

有关为动态成员身份规则使用自定义扩展属性的详细信息，请参阅[扩展属性和自定义扩展属性](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

**类型：** 更改计划 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

以下应用包含在[批准的客户端应用](../conditional-access/concept-conditional-access-conditions.md#client-apps)列表中：

- 微软待办

- Microsoft Stream

有关详细信息，请参阅：

- [基于 Azure AD 应用的条件访问](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 锁屏界面中新的自助密码重置支持

**类型：** 新功能 **服务类别：** SSPR **产品功能：** 用户身份验证

设置此新功能后，运行 Windows 7、8 或 Windows 8.1 的设备的 **锁屏** 界面中会向用户显示一个用于重置密码的链接。 单击该链接时，系统会引导用户完成与 Web 浏览器中相同的密码重置流程。

有关详细信息，请参阅[如何在 Windows 7、8 和 8.1 中启用密码重置](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>更改通知：授权代码不再可重复使用

**类型：** 更改计划 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何应用都将收到 invalid_grant 错误。

有关此项更改和其他与协议相关的更改，请参阅[身份验证新增功能的完整列表](../develop/reference-breaking-changes.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 9 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已在 2018 年 9 月将这 16 款支持联合的新应用添加到了应用库：

[Uberflip](../saas-apps/uberflip-tutorial.md)、[Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md)、[Workteam](../saas-apps/workteam-tutorial.md)、[ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md)、[Nuclino](../saas-apps/nuclino-tutorial.md)、[JDA Cloud](../saas-apps/jdacloud-tutorial.md)、[Snowflake](../saas-apps/snowflake-tutorial.md)、NavigoCloud、[Figma](../saas-apps/figma-tutorial.md)、join.me、[ZephyrSSO](../saas-apps/zephyrsso-tutorial.md)、[Silverback](../saas-apps/silverback-tutorial.md)、Riverbed Xirrus EasyPass、[Rackspace SSO](../saas-apps/rackspacesso-tutorial.md)、Enlyft SSO for Azure、SurveyMonkey、[Convene](../saas-apps/convene-tutorial.md)、[dmarcian](../saas-apps/dmarcian-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="support-for-additional-claims-transformations-methods"></a>对其他声明转换方法的支持

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

我们引入了新的声明转换方法 ToLower() 和 ToUpper()，可以在基于 SAML 的“单一登录配置”页中将这些方法应用到 SAML 令牌。

有关详细信息，请参阅[如何为 Azure AD 中的企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>更新了基于 SAML 的应用配置 UI（预览版）

**类型：** 已更改的功能 **服务类别：** 企业应用 **产品功能：** SSO

已更新的基于 SAML 的应用配置 UI 提供：

- 用于配置基于 SAML 的应用的已更新演练体验。

- 为配置中缺失或错误的设置提供更直观的显示。

- 为证书过期通知添加多个电子邮件地址的功能。

- 新的声明转换方法 ToLower() 和 ToUpper() 等。

- 为企业应用上传你自己的令牌签名证书的方法。

- 为 SAML 应用设置 NameID 格式的方法，以及将 NameID 值设置为目录扩展的方法。

若要启用此更新视图，请在“单一登录”页的顶部单击“尝试新体验”链接。  有关详细信息，请参阅[教程：通过 Azure Active Directory 为应用程序配置基于 SAML 的单一登录](../manage-apps/view-applications-portal.md)。

---

## <a name="august-2018"></a>2018 年 8 月

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>对 Azure Active Directory IP 地址范围的更改

**类型：** 更改计划 **服务类别：** 其他 **产品功能：** 平台

我们正在为 Azure AD 引入更大的 IP 范围，这意味着如果你已为防火墙、路由器或网络安全组配置了 Azure AD IP 地址范围，则需要更新它们。 我们正在进行此更新，因此，在 Azure AD 添加新的终结点时，你不必再次更改防火墙、路由器或网络安全组 IP 范围配置。

在接下来的两个月中，网络流量将迁移到这些新范围。 若要保持不间断地提供服务，必须在 2018 年 9 月 10 日之前将这些更新的值添加到你的 IP 地址：

- 20.190.128.0/18

- 40.126.0.0/18

我们强烈建议不要删除旧的 IP 地址范围，直到你的所有网络流量都已迁移到新范围。 若要了解有关迁移的更新并了解何时可以删除旧范围，请参阅 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>更改通知：授权代码不再可重复使用

**类型：** 更改计划 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何应用都将收到 invalid_grant 错误。

有关此项更改和其他与协议相关的更改，请参阅[身份验证新增功能的完整列表](../develop/reference-breaking-changes.md)。

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>为自助密码重置 (SSPR) 和多重身份验证 (MFA) 融合了安全信息管理

**类型：** 新功能 **服务类别：** SSPR **产品功能：** 用户身份验证

此新功能可帮助用户在单个位置和体验中管理 SSPR 和 MFA 的安全信息（例如，电话号码、移动应用等），而以前必须在两个不同的位置进行管理。

此融合体验也适用于使用 SSPR 或 MFA 的用户。 此外，如果组织未强制实施 MFA 或 SSPR 注册，则用户仍可通过“我的应用”门户注册组织允许的任何 MFA 或 SSPR 安全信息方法。

这是一个选用的公共预览版。 管理员可以针对选定的一组用户或者租户中的所有用户启用新体验（如果需要）。 有关融合体验的详细信息，请参阅[融合体验博客](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD 应用程序代理应用中的新 HTTP-Only Cookie 设置

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 访问控制

应用程序代理应用中有一个名为“HTTP-Only Cookie”的新设置。 此设置在应用程序代理的访问 Cookie 和会话 Cookie 的 HTTP 响应标头中包含 HTTPOnly 标志，阻止从客户端侧脚本访问 Cookie，并进一步阻止复制或修改 Cookie 等操作，以此提供更高的安全性。 尽管以前未使用此标志，但 Cookie 始终经过加密并通过 TLS 连接传输，以帮助防范不当的修改。

此项设置与使用 ActiveX 控件的应用（例如远程桌面）不兼容。 如果遇到这种情况，我们建议禁用此设置。

有关 HTTP-Only Cookie 设置的详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](../app-proxy/application-proxy-add-on-premises-application.md)。

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure 资源的 Privileged Identity Management (PIM) 支持管理组资源类型

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

现在，可将实时激活和分配设置应用到管理组资源类型，就像应用到订阅、资源组和资源（例如 VM、应用服务等）一样。 此外，对管理组拥有管理员访问权限的任何人都可以在 PIM 中发现和管理该资源。

有关 PIM 和 Azure 资源的详细信息，请参阅[使用 Privileged Identity Management 发现和管理 Azure 资源](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>使用“应用程序访问”（预览版）可以更快地访问 Azure AD 门户

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

目前，在使用 PIM 激活某个角色时，可能需要 10 分钟以上才能让权限生效。 如果选择使用“应用程序访问”（目前以公共预览版提供），则管理员可以在激活请求完成后立即访问 Azure AD 门户。

目前，“应用程序访问”仅支持 Azure AD 门户体验和 Azure 资源。 有关 PIM 和“应用程序访问”的详细信息，请参阅 [Azure AD Privileged Identity Management 是什么？](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 8 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已在 2018 年 8 月将这 16 款支持联合的新应用添加到了应用库：

[Hornbill](../saas-apps/hornbill-tutorial.md)、[Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md)、[Sauce Labs - Mobile and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md)、[Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md)、[Way We Do](../saas-apps/waywedo-tutorial.md)、[Spotinst](../saas-apps/spotinst-tutorial.md)、[ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md)、SchoolBooking、[4me](../saas-apps/4me-tutorial.md)、[Dossier](../saas-apps/dossier-tutorial.md)、[N2F - Expense reports](../saas-apps/n2f-expensereports-tutorial.md)、[Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md)、[SafeConnect](../saas-apps/safeconnect-tutorial.md)、[ZenQMS](../saas-apps/zenqms-tutorial.md)、[eLuminate](../saas-apps/eluminate-tutorial.md)、[Dovetale](../saas-apps/dovetale-tutorial.md)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Azure AD 应用程序代理现已提供本机 Tableau 支持

**类型：** 已更改的功能 **服务类别：** 应用代理 **产品功能：** 访问控制

随着预身份验证协议已从 OpenID Connect 更新为 OAuth 2.0 代码授予协议，不再需要进行任何附加的配置就能在应用程序代理中使用 Tableau。 此项协议变更还有助于应用程序代理使用仅限 HTTP 的重定向（通常在 JavaScript 和 HTML 标记中受支持）来更好地支持更多新式应用。

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>将 Google 添加为 Azure Active Directory 中 B2B 来宾用户的标识提供者的新支持（预览版）

**类型：** 新功能 **服务类别：** B2B **产品功能：** B2B/B2C

在组织中设置 Google 联合时，可让受邀的 Gmail 用户使用其现有 Google 帐户登录到共享的应用和资源，而无需创建个人 Microsoft 帐户 (MSA) 或 Azure AD 帐户。

这是一个选用的公共预览版。 有关 Google 联合的详细信息，请参阅[将 Google 添加为 B2B 来宾用户的标识提供者](../external-identities/google-federation.md)。

---

## <a name="july-2018"></a>2018 年 7 月

### <a name="improvements-to-azure-active-directory-email-notifications"></a>对 Azure Active Directory 电子邮件通知的改进

**类型：** 已更改的功能 **服务类别：** 其他 **产品功能：** 标识生命周期管理

通过以下服务发送的 Azure Active Directory (Azure AD) 电子邮件现在采用更新的设计，并应用对发件人电子邮件地址和发件人显示名称的更改：

- Azure AD 访问评审
- Azure AD Connect Health
- Azure AD 标识保护
- Azure AD 特权标识管理
- 企业应用到期证书通知
- 企业应用预配服务通知

电子邮件通知将从以下电子邮件地址和显示名称发送：

- 电子邮件地址：azure-noreply@microsoft.com
- 显示名称：Microsoft Azure

有关一些新电子邮件设计的示例以及详细信息，请参阅 [Azure AD PIM 中的电子邮件通知](../privileged-identity-management/pim-email-notifications.md)。

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD 活动日志现在通过 Azure Monitor 提供

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

Azure AD 活动日志现已推出适用于 Azure Monitor（Azure 的平台级监视服务）的公共预览版。 Azure Monitor 提供长期保留和无缝集成，此外还做出了以下方面的改进：

- 通过将日志文件路由到 Azure 存储帐户来实现长期保留。

- 无缝 SIEM 集成，无需编写或维护自定义脚本。

- 与自己的自定义解决方案、分析工具或事件管理解决方案无缝集成。

有关这些新功能的详细信息，请参阅博客文章 [Azure AD activity logs in Azure Monitor diagnostics is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/)（Azure Monitor 诊断中的 Azure AD 活动日志现已推出公共预览版）和文档 [Azure Monitor 中的 Azure Active Directory 活动日志（预览版）](../reports-monitoring/concept-activity-logs-azure-monitor.md)。

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>已将条件访问信息添加到 Azure AD 登录报告

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 标识安全性和保护

通过此项更新，可以查看用户登录时会评估哪些策略以及策略结果。 此外，报告现在包括用户使用的客户端应用类型，以便可以识别旧式协议流量。 现在还可以在报告条目中搜索关联 ID，这可以在面向用户的错误消息中找到。此 ID 可用于识别匹配的登录请求及排查其问题。

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>通过登录活动日志查看旧式身份验证

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

由于在登录活动日志中引入了“客户端应用”字段，客户现在可以查看使用旧式身份验证的用户。 客户将能够在 Azure AD 门户中通过登录 Microsoft Graph API 或登录活动日志访问此信息。在该门户中，你可以使用“客户端应用”控件对旧式身份验证进行筛选。 查看文档可了解更多详细信息。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 7 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2018 年 7 月将这 16 款支持联合的新应用添加到了应用库中：

[Innovation Hub](../saas-apps/innovationhub-tutorial.md)、[Leapsome](../saas-apps/leapsome-tutorial.md)、[Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md)、PSUC Staging、[iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md)、[Screencast-O-Matic](../saas-apps/screencast-tutorial.md)、PowerSchool Unified Classroom、[Eli Onboarding](../saas-apps/elionboarding-tutorial.md)、[Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md)、[Nimblex](../saas-apps/nimblex-tutorial.md)、[Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md)、[Insight4GRC](../saas-apps/insight4grc-tutorial.md)、[SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md)、[Kanbanize](../saas-apps/kanbanize-tutorial.md)、[SmartLPA](../saas-apps/smartlpa-tutorial.md)、[Skills Base](../saas-apps/skillsbase-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>新用户预配 SaaS 应用集成 - 2018 年 7 月

**类型：** 新功能 **服务类别：** 应用预配 **产品功能：** 第三方集成

可以通过 Azure AD 自动创建、维护和删除 SaaS 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 对于 2018 年 7 月版本，我们为 Azure AD 应用库中的以下应用程序添加了用户预配支持：

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

有关 Azure AD 库中支持用户预配的所有应用程序的列表，请参阅 [SaaS 应用程序与 Azure Active Directory 的集成](../saas-apps/tutorial-list.md)。

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - 解决孤立和重复属性同步错误的更简单方法

**类型：** 新功能 **服务类别：** AD Connect **产品功能：** 监视和报告

Azure AD Connect Health 引入了自助补救，以帮助突出显示和解决同步错误。 此功能可以排查重复属性同步错误，并修复从 Azure AD 孤立的对象。 此诊断功能具有以下优点：

- 缩小重复属性同步错误的范围，并提供具体的修复方法

- 对专用 Azure AD 方案应用修复，通过一个步骤解决错误

- 无需升级或配置即可启用此功能

有关详细信息，请参阅[诊断并修正重复的属性同步错误](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>对 Azure AD 和 MSA 登录体验做了视觉更新

**类型：** 已更改的功能 **服务类别：** Azure AD **产品功能：** 用户身份验证

我们已更新 Office 365 和 Azure 等 Microsoft 联机服务登录体验的 UI。 此项更改使得屏幕更简洁、更直观。 有关此项更改的详细信息，请参阅博客文章 [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)（即将对 Azure AD 登录体验的改进）。

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect 新版本 - 2018 年 7 月

**类型：** 已更改的功能 **服务类别：** 应用预配 **产品功能：** 标识生命周期管理

Azure AD Connect 的最新版本包括：

- Bug 修复和支持性更新

- Ping-Federate 集成正式版

- 对最新版 SQL 2012 客户端的更新

有关此项更新的详细信息，请参阅 [Azure AD Connect：版本发行历史记录](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>对使用条款最终用户 UI 的更新

**类型：** 已更改的功能 **服务类别：** 使用条款 **产品功能：** 调控

我们正在更新 TOU 最终用户 UI 中的“接受”字符串。

**当前文本。** 若要访问 [tenantName] 资源，必须接受使用条款。<br>**新文本。** 若要访问 [tenantName] 资源，必须阅读使用条款。

**当前文本：** 选择接受表示你同意上述所有使用条款。<br>**新文本：** 请单击“接受”，确认已阅读并理解使用条款。

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>直通身份验证支持旧式协议和应用程序

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

直通身份验证现在支持旧式协议和应用。 现在完全支持以下限制：

- 用户无需完成新式新式身份验证，即可登录到旧式 Office 客户端应用程序 Office 2010 和 Office 2013。

- 仅在 Office 2010 上可以访问 Exchange 混合环境中的日历共享功能和闲/忙信息。

- 用户无需完成新式身份验证，即可登录到 Skype for Business 客户端应用程序。

- 用户登录到 PowerShell 版本 1.0。

- 使用 iOS 设置助手的 Apple 设备注册计划 (Apple DEP)。

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>为自助密码重置和多重身份验证融合了安全信息管理

**类型：** 新功能 **服务类别：** SSPR **产品功能：** 用户身份验证

此新功能可让用户在单个体验中管理自助密码重置 (SSPR) 和多重身份验证 (MFA) 的安全信息（例如，电话号码、电子邮件地址、移动应用等）。 用户不再需要在两个不同的体验中为 SSPR 和 MFA 注册相同的安全信息。 此新体验也适用于具有 SSPR 或 MFA 的用户。

如果组织未强制实施 MFA 或 SSPR 注册，则用户可以通过“我的应用”门户注册安全信息。 在此门户中，用户可以注册针对 MFA 或 SSPR 启用的任何方法。

这是一个选用的公共预览版。 管理员可为租户中选定的一组用户或所有用户启用新体验（如果需要）。

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>重置密码时使用 Microsoft Authenticator 应用验证身份

**类型：** 已更改的功能 **服务类别：** SSPR **产品功能：** 用户身份验证

此功能可让非管理员使用 Microsoft Authenticator（或其他任何验证器应用）提供的通知或代码验证其身份。 管理员启用此自助密码重置方法后，已通过 aka.ms/mfasetup 或 aka.ms/setupsecurityinfo 注册移动应用的用户可以在重置密码时，使用其移动应用作为验证方法。

只能在要求使用两种方法重置密码的策略中启用移动应用通知。

---

## <a name="june-2018"></a>2018 年 6 月

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>更改通知：对使用 Azure AD 活动日志 API 的应用程序的委派授权流的安全修补

**类型：** 更改计划 **服务类别：** 报告 **产品功能：** 监视和报告

由于我们实施了更强的安全性，我们已对使用委派的授权流访问 [Azure AD 活动日志 API](../reports-monitoring/concept-reporting-api.md) 的应用进行了权限更改。 此更改在 **2018 年 6 月 26 日** 前生效。

如果你的任何应用都使用 Azure AD 活动日志 API，请按照以下步骤操作，以确保更改发生后应用不会中断。

**更新应用权限**

1. 登录 Azure 门户，选择“Azure Active Directory”，然后选择“应用注册” 。
2. 选择使用 Azure AD 活动日志 API 的应用，依次选择“设置”、“所需权限”和“Windows Azure Active Directory”API  。
3. 在“启用访问权限”边栏选项卡的“委派的权限” 区域中，选中“读取目录数据”旁边的框，然后选择“保存”。
4. 选择“授予权限”，然后选择“是” 。

    >[!Note]
    >你必须是全局管理员才能向应用授予权限。

有关详细信息，请参阅访问 Azure AD 报告 API 的先决条件文章的[授予权限](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions)部分。

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>配置 TLS 设置以连接到 Azure AD 服务从而实现 PCI DSS 符合性

**类型：** 新功能 **服务类别：** 不适用 **产品功能：** 平台

传输层安全性 (TLS) 是一种在两个通信应用程序间提供隐私和数据完整性的协议，是目前使用最广泛的安全协议。

[PCI 安全标准委员会](https://www.pcisecuritystandards.org/)已决定禁用早期版本的 TLS 和安全套接字层 (SSL)，以此支持启用新的和更安全的应用协议，该决定于 2018 年 6 月 30 日开始生效。 这一改变意味着如果连接到 Azure AD 服务并要求符合 PCI DSS，则必须禁用 TLS 1.0。 有多个 TLS 版本可供使用，但 TLS 1.2 是可供 Azure Active Directory 服务使用的最新版本。 强烈建议直接为客户端/服务器和浏览器/服务器组合使用 TLS 1.2。

过时的浏览器可能不支持较新的 TLS 版本，例如 TLS 1.2。 若要查看浏览器支持哪些版本的 TLS，请转到 [Qualys SSL 实验室](https://www.ssllabs.com/)网站，然后单击“Test your browser”（测试浏览器）。 建议将 Web 浏览器升级到最新版本，并且最好只启用 TLS 1.2。

**在各种浏览器中启用 TLS 1.2**

- **Microsoft Edge和 Internet Explorer（均使用 Internet Explorer 设置）**

    1. 打开 Internet Explorer，选择“工具” > “Internet 选项” > “高级”  。
    2. 在“安全”部分，选择“使用 TLS 1.2”，然后选择“确定”  。
    3. 关闭所有浏览器窗口并重启 Internet Explorer。

- **Google Chrome**

    1. 打开 Google Chrome，在地址栏键入“chrome://settings/”，然后按 Enter。
    2. 展开“高级”选项，转到“系统”部分，然后选择“打开代理设置”  。
    3. 在“Internet 属性”框中，选择“高级”选项卡，转到“安全”部分，选择“使用 TLS 1.2”，然后选择“确定”    。
    4. 关闭所有浏览器窗口并重启 Google Chrome。

- **Mozilla Firefox**

    1. 打开 Firefox，在地址栏键入“about:config”，然后按 Enter。
    2. 搜索 TLS 一词，然后选择“security.tls.version.max”条目。
    3. 将值设置为 3，强制浏览器使用最高 TLS 1.2 的版本，然后选择“确定” 。

        >[!NOTE]
        >Firefox 版本 60.0 支持 TLS 1.3，因此还可将 security.tls.version.max 值设置为 4。

    4. 关闭所有浏览器窗口并重启 Mozilla Firefox。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 6 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2018 年 6 月将这 15 款支持联合的新应用添加到了应用库中：

[Skytap](../saas-apps/skytap-tutorial.md)[Settling music](../saas-apps/settlingmusic-tutorial.md)[SAML 1.1 Token enabled LOB App](../saas-apps/saml-tutorial.md)[Supermood](../saas-apps/supermood-tutorial.md)[Autotask](../saas-apps/autotaskendpointbackup-tutorial.md)[Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md)[Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md)Smartway2、[TonicDM](../saas-apps/tonicdm-tutorial.md)[Moconavi](../saas-apps/moconavi-tutorial.md)[Zoho One](../saas-apps/zohoone-tutorial.md)[SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md)[ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md)[Vidyard](../saas-apps/vidyard-tutorial.md)[ChronicX](../saas-apps/chronicx-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>公共预览版中提供 Azure AD 密码保护功能

**类型：** 新功能 **服务类别：** 标识保护 **产品功能：** 用户身份验证

使用 Azure AD 密码保护有助于杜绝环境中出现易于猜到的密码。 消除这些密码有助于降低遭受密码喷射型攻击时密码泄露的风险。

具体来说，Azure AD 密码保护有助于：

- 保护 Azure AD 和 Windows Server Active Directory (AD) 中的组织帐户。
- 阻止用户使用最常用密码列表上的密码，该列表包含 500 多个密码，以及这些密码的 100 多万个字符替换变体。
- 从 Azure AD 门户中的单一位置管理 Azure AD 密码保护，既适用于 Azure AD，也适用于本地 Windows Server AD。

有关 Azure AD 密码保护的详细信息，请参阅[消除组织中的劣质密码](../authentication/concept-password-ban-bad.md)。

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>创建使用条款期间创建新的“所有来宾”条件访问策略模板

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 调控

在创建使用条款期间，还会为“所有来宾”和“所有应用”创建新的条件访问策略模板。 此全新的策略模板采用新创建的 ToU，简化了来宾的创建和执行过程。

有关详细信息，请参阅 [Azure Active Directory 使用条款功能](../conditional-access/terms-of-use.md)。

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>创建使用条款期间创建新的“自定义”条件访问策略模板

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 调控

在创建使用条款期间，还会创建新的“自定义”条件访问策略模板。 通过此新策略模板可创建 ToU，然后立即转到条件访问策略创建边栏选项卡，无需手动浏览门户。

有关详细信息，请参阅 [Azure Active Directory 使用条款功能](../conditional-access/terms-of-use.md)。

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>有关部署 Azure AD 多重身份验证的全新详尽指南

**类型：** 新功能 **服务类别：** 其他 **产品功能：** 标识安全性和保护

我们发布了有关如何在组织中部署 Azure AD 多重身份验证 (MFA) 的新的分步指南。

若要查看 MFA 部署指南，请转到 GitHub 上的[身份部署指南](./active-directory-deployment-plans.md)存储库。 若要提供有关部署指南的反馈，请使用[部署计划反馈表](https://aka.ms/deploymentplanfeedback)。 如对部署指南有任何疑问，请通过 [IDGitDeploy](mailto:idgitdeploy@microsoft.com) 与我们联系。

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD 委派的应用管理角色处于公共预览状态

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 访问控制

管理员现可委派应用管理任务，无需分配全局管理员角色。 新的角色和功能有：

- **新的标准 Azure AD 管理员角色：**

    - **应用程序管理员。** 授予管理所有应用各个方面的权限，包括注册、SSO 设置、应用分配和许可、应用代理设置和同意（Azure AD 资源除外）。

    - **云应用程序管理员。** 授予所有应用程序管理员权限，但应用代理除外，因为它不提供本地访问权限。

    - **应用程序开发人员。** 即使“允许用户注册应用”选项已关闭，该角色也可授予创建应用注册的权限。

- **所有权（设置每个应用注册和每个企业应用，类似于群组所有权流程：**

    - **应用注册所有者。** 授予管理自有应用注册各个方面的权限，包括应用清单和添加其他所有者。

    - **企业应用所有者。** 授予管理自有企业应用许多方面的权限，包括 SSO 设置、应用分配和同意（Azure AD 资源除外）。

有关公共预览版的详细信息，请参阅 [Azure AD 委派的应用程序管理角色处于公共预览状态！](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) 博客。 有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)。

---

## <a name="may-2018"></a>2018 年 5 月

### <a name="expressroute-support-changes"></a>ExpressRoute 支持更改

**类型：** 更改计划 **服务类别：** 身份验证（登录）**产品功能：** 平台

软件即服务产品/服务，例如 Azure Active Directory (Azure AD)，设计为直接通过 Internet 时工作性能最好，不需要使用 ExpressRoute 或任何其他专用 VPN 隧道。 因此，在 **2018 年 8 月 1 日**，我们将停止支持将 ExpressRoute 用于使用 Azure 公共对等互连的 Azure AD 服务和 Microsoft 对等互连中的 Azure 社区。 受此更改影响的所有服务可能会注意到 Azure AD 流量逐步从 ExpressRoute 转移到 Internet。

同时我们正在改变我们的支持，我们也知道，在某些情况下，你可能仍然需要为身份验证流量使用专用的线路集。 因此，Azure AD 将继续使用 ExpressRoute 支持每租户 IP 范围限制，并继续通过“其他 Office 365 联机服务”社区支持 Microsoft 对等互连上已有的服务。 如果服务受到影响，但你需要 ExpressRoute，则必须执行以下操作：

- **如果当前在使用 Azure 公共对等互连。** 迁移到 Microsoft 对等互连并注册其他 Office 365 联机服务 (12076:5100) 社区。 有关如何从 Azure 公共对等互连迁移到 Microsoft 对等互连的详细信息，请参阅[将公共对等互连迁移到 Microsoft 对等互连](../../expressroute/how-to-move-peering.md)一文。

- **如果当前在使用 Microsoft 对等互连。** 注册其他 Office 365 联机服务 (12076:5100) 社区。 有关路由要求的详细信息，请参阅 ExpressRoute 路由要求一文中的[对 BGP 社区的支持](../../expressroute/expressroute-routing.md#bgp)部分。

如果必须继续使用专用线路，则需要与你的 Microsoft 帐户团队沟通如何获得授权来使用 **其他 Office 365 联机服务 (12076:5100)** 社区。 MS Office 托管的评审委员会将验证你是否需要那些线路并确保你理解保留它们的技术影响。 尝试为 Office 365 创建路由筛选器的未经授权订阅将收到错误消息。

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>使用 Microsoft Graph API 实现 TOU 管理方案

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 开发人员体验

已添加 Microsoft Graph API 以实现 Azure AD 使用条款的管理性运行。 可创建、更新、删除使用条款对象。

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>在 Azure AD B2C 中将 Azure AD 多租户终结点添加为标识提供者

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

现在可以使用自定义策略在 Azure AD B2C 中将 Azure AD 常用终结点添加为标识提供者。 这样就可以为登录到应用程序的所有 Azure AD 用户提供单个入口点。 有关详细信息，请参阅 [Azure Active Directory B2C：让用户使用自定义策略登录到多租户 Azure AD 标识提供者](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)。

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>通过“我的应用登录扩展”和 Azure AD 应用程序代理，使用内部 URL 从任何位置访问应用

**类型：** 新功能 **服务类别：** 我的应用 **产品功能：** SSO

用户现在可以使用适用于 Azure AD 的“我的应用”安全登录扩展通过内部 URL 访问应用程序，即使在公司网络之外也是如此。 这适用于使用 Azure AD 应用程序代理发布的任何应用程序，适用于任何也安装了访问面板浏览器扩展的浏览器。 URL 重定向功能在用户登录此扩展后自动启用。 此扩展可以在 [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)、[Chrome](https://go.microsoft.com/fwlink/?linkid=866367) 上下载。

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - 将欧洲客户的数据保留在欧洲

**类型：** 新功能 **服务类别：** 其他 **产品功能：** GoLocal

根据隐私法和欧洲法律，欧洲客户的数据需保留在欧洲，不得复制到欧洲数据中心以外的区域。 此[文章](./active-directory-data-storage-eu.md)详述了哪些标识信息会存储在欧洲数据中心内，哪些标识信息会存储在欧洲数据中心外。

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>新用户预配 SaaS 应用集成 - 2018 年 5 月

**类型：** 新功能 **服务类别：** 应用预配 **产品功能：** 第三方集成

可以通过 Azure AD 自动创建、维护和删除 SaaS 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 对于 2018 年 5 月版本，我们为 Azure AD 应用库中的以下应用程序添加了用户预配支持：

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

若需 Azure AD 库中支持用户预配的所有应用程序的列表，请参阅 [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md)。

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>针对组和应用访问的 Azure AD 访问评审功能现在提供定期评审

**类型：** 新功能 **服务类别：** 访问评审 **产品功能：** 调控

现在可以正式通过 Azure AD Premium P2 对组和应用进行访问评审。  管理员可以对组成员身份和应用程序分配的访问评审进行配置，使之自动按固定时间间隔（例如按月或按季）定期进行。

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>现在可通过 MS Graph 获取 Azure AD 活动日志（登录和审核）

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

现在可通过 Microsoft Graph API 获取 Azure AD 活动日志（包括登录和审核日志）。 我们已通过 Microsoft Graph API 公开 2 个用于访问这些日志的终结点。 请查看我们的[文档](../reports-monitoring/concept-reporting-api.md)，了解如何以编程方式访问入门所需的 Azure AD 报告 API。

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>对 B2B 兑换体验和离开组织的体验的改进

**类型：** 新功能 **服务类别：** B2B **产品功能：** B2B/B2C

**及时兑换：** 使用 B2B API 与来宾用户共享某个资源以后，不需发送专门的邀请电子邮件。 大多数情况下，来宾用户可以访问资源，并可及时获得兑换体验。 再也不会因错过电子邮件而受到影响。 再也不用询问来宾用户：“你单击了系统发送给你的那个兑换链接了吗？”。 这意味着，在 SPO 使用邀请管理器以后，云附件可以对所有用户（包括内部用户和外部用户）使用同一个规范的 URL，不管兑换状态如何。

**新式兑换体验：** 再也不会有拆分的屏幕兑换登陆页。 用户会看到包含邀请组织的隐私声明的新式许可体验，就像使用第三方应用一样。

**来宾用户可以离开组织：** 用户在其与组织的关系结束以后，可以自行离开该组织。 再也不用给邀请组织的管理员打电话以“要求离开组织”，再也不用开具支持票证。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 5 月

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2018 年 5 月将这 18 款支持联合的新应用添加到了我们的应用库中：

[AwardSpring](../saas-apps/awardspring-tutorial.md)、Infogix Data3Sixty Govern、[Yodeck](../saas-apps/infogix-tutorial.md)、[Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md)、[KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md)、[Envi MMIS](../saas-apps/envimmis-tutorial.md)、[LaunchDarkly](../saas-apps/launchdarkly-tutorial.md)、[Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md)、[Montage Online](../saas-apps/montageonline-tutorial.md)、[まなびポケット](../saas-apps/manabipocket-tutorial.md)、OpenReel、[Arc Publishing - SSO](../saas-apps/arc-tutorial.md)、[PlanGrid](../saas-apps/plangrid-tutorial.md)、[iWellnessNow](../saas-apps/iwellnessnow-tutorial.md)、[Proxyclick](../saas-apps/proxyclick-tutorial.md)、[Riskware](../saas-apps/riskware-tutorial.md)、[Flock](../saas-apps/flock-tutorial.md)、[Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory 的新分步部署指南

**类型：** 新功能 **服务类别：** 其他 **产品功能：** 目录

有关如何部署 Azure Active Directory (Azure AD) 的全新分步指南，包括自助式密码重置 (SSPR)、单一登录 (SSO)、条件访问、应用代理、用户预配、Active Directory 联合身份验证服务 (ADFS) 以及传递身份验证 (PTA)、ADFS 和密码哈希同步 (PHS)。

若要查看部署指南，请转到 GitHub 上的[身份部署指南](./active-directory-deployment-plans.md)存储库。 若要提供有关部署指南的反馈，请使用[部署计划反馈表](https://aka.ms/deploymentplanfeedback)。 如对部署指南有任何疑问，请通过 [IDGitDeploy](mailto:idgitdeploy@microsoft.com) 与我们联系。

---

### <a name="enterprise-applications-search---load-more-apps"></a>企业应用程序搜索 - 加载更多应用

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

找不到应用程序/服务主体？ 我们增加了在“企业应用程序”下的“所有应用程序”列表中加载更多应用程序的功能。 默认显示 20 个应用程序。 现在可以通过单击“加载更多”来查看更多应用程序。

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>AADConnect 的 5 月发布内容包括与 PingFederate 的集成、重要安全更新、许多 Bug 修复和功能强大的全新故障排除工具的公共预览版。

**类型：** 已更改的功能 **服务类别：** AD Connect **产品功能：** 标识生命周期管理

AADConnect 的 5 月发布内容包括与 PingFederate 的集成、重要安全更新、许多 Bug 修复和功能强大的全新故障排除工具的公共预览版。 [此处](../hybrid/reference-connect-version-history.md)提供发行说明。

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD 访问评审：自动应用

**类型：** 已更改的功能 **服务类别：** 访问评审 **产品功能：** 调控

现在可以正式通过 Azure AD Premium P2 对组和应用进行访问评审。 管理员可以进行配置，以便访问评审完成后自动应用评审者对该组或应用所做的更改。 管理员还可以指定在评审者未响应、删除访问权限、保留访问权限或采用系统建议的情况下，用户进行后续访问时会发生什么。

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>对于新应用，无法再使用 query response_mode 返回 ID 令牌。

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

在 2018 年 4 月 25 日当天或以后创建的应用将不再能够使用 query response_mode 来请求 id_token 。  这样可以为 Azure AD 提供内联的 OIDC 规范，有助于减少应用的受攻击面。  在 2018 年 4 月 25 之前创建的应用可以将 query response_mode 与值为 id_token 的 response_type 配合使用 。  从 Azure AD 请求 id_token 时，返回的错误为“AADSTS70007: 在请求令牌时，'query' 是 'response_mode' 的不受支持的值”。

fragment 和 form_post response_mode 继续有效 - 创建新的具有特定用途（例如，供应用代理使用）的应用程序对象时，请确保使用这两个 response_mode 中的一个，然后才能创建新应用程序 。

---

## <a name="april-2018"></a>2018 年 4 月

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C 访问令牌已发布正式版

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

现在可以使用访问令牌访问受 Azure AD B2C 保护的 Web API。 该功能将从公共预览版过渡到正式版。 改进了配置 Azure AD B2C 应用程序和 Web API 的 UI 体验，并进行了其他微小改进。

有关详细信息，请参阅 [Azure AD B2C：请求访问令牌](../../active-directory-b2c/access-tokens.md)。

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>测试基于 SAML 的应用程序的单一登录配置

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

配置基于 SAML 的 SSO 应用程序时，能够在配置页上测试集成。 如果在登录期间遇到错误，可以在测试体验中提供错误，Azure AD 将提供用于解决特定问题的解决方法步骤。

有关详细信息，请参阅：

- [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](../manage-apps/view-applications-portal.md)
- [如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD 使用条款现在具有每个用户的报告

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 合规性

管理员现在可以选择给定 ToU，查看已同意该 ToU 的所有用户以及同意发生的日期/时间。

有关详细信息，请参阅 [Azure AD 使用条款功能](../conditional-access/terms-of-use.md)。

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health：AD FS Extranet 锁定保护的风险 IP

**类型：** 新功能 **服务类别：** 其他 **产品功能：** 监视和报告

Connect Health 现在支持按小时或按天检测超过失败 U/P 登录次数阈值的 IP 地址的功能。 该功能提供的功能包括：

- 使用可自定义阈值按小时/按天生成的综合报告，显示失败登录的 IP 地址和次数。
- 基于电子邮件的警报，当特定 IP 地址按小时/按天超出了失败 U/P 登录次数阈值时显示。
- 用于对数据进行详细分析的下载选项

有关详细信息，请参阅[风险 IP 报表](../hybrid/how-to-connect-health-adfs.md)。

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>包含元数据文件或 URL 的简单应用配置

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

在“企业应用程序”页上，管理员可以上传 SAML 元数据文件，以便为 Azure AD 库和非库应用程序配置基于 SAML 的登录。

此外，还可以使用 Azure AD 应用程序联合元数据 URL 为目标应用程序配置 SSO。

有关详细信息，请参阅[针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](../manage-apps/view-applications-portal.md)。

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD 使用条款现已正式发布

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 合规性


Azure AD 使用条款已从公共预览版过渡到正式版。

有关详细信息，请参阅 [Azure AD 使用条款功能](../conditional-access/terms-of-use.md)。

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>允许或阻止向特定组织中的 B2B 用户发送邀请

**类型：** 新功能 **服务类别：** B2B **产品功能：** B2B/B2C


现在可以在 Azure AD B2B 协作中指定要与之共享和协作的合作伙伴组织。 为此，可以选择创建具体允许或拒绝域的列表。 使用这些功能阻止某个域时，员工可以不再向该域中的人员发送邀请。

这可帮助你控制对资源的访问，同时可使获得批准的用户的体验更流畅。

此 B2B 协作功能可供所有 Azure Active Directory 客户使用，并可与 Azure AD Premium 功能（如条件访问和标识保护）结合使用，以更精细地控制外部业务用户登录和获得访问权限的时间和方式。

有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../external-identities/allow-deny-list.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2018 年 4 月将这 13 款支持联合的新应用添加到了我们的应用库中：

Criterion HCM、[FiscalNote](../saas-apps/fiscalnote-tutorial.md)、[Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md)、[Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md)、[mindWireless](../saas-apps/mindwireless-tutorial.md)、[OrgChart Now](../saas-apps/orgchartnow-tutorial.md)、[Ziflow](../saas-apps/ziflow-tutorial.md)、[AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md)、[Elium](../saas-apps/elium-tutorial.md)、[Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md)、[Cisco Cloud](../saas-apps/ciscocloud-tutorial.md)、Shelf、[SafetyNet](../saas-apps/safetynet-tutorial.md)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>向 Azure AD 中 B2B 用户授予对本地应用程序（公共预览版）的访问权限

**类型：** 新功能 **服务类别：** B2B **产品功能：** B2B/B2C

使用 Azure Active Directory (Azure AD) B2B 协作功能邀请合作伙伴组织中的来宾用户加入 Azure AD 的组织现在可以向这些 B2B 用户提供本地应用的访问权限。 这些本地应用可以结合 Kerberos 约束委派 (KCD) 使用基于 SAML 的身份验证或 Windows 集成身份验证 (IWA)。

有关详细信息，请参阅[向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限](../external-identities/hybrid-cloud-to-on-premises.md)。

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>从 Azure 市场获取 SSO 集成教程

**类型：** 已更改的功能 **服务类别：** 其他 **产品功能：** 第三方集成

如果 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1)中列出的应用程序支持基于 SAML 的单一登录，则单击“立即获取”会为你提供与该应用程序关联的集成教程。

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>加快 Azure AD 对 SaaS 应用程序的自动用户预配性能

**类型：** 已更改的功能 **服务类别：** 应用预配 **产品功能：** 第三方集成

以前，在以下情况下，将 Azure Active Directory 用户预配连接器用于 SaaS 应用程序（例如，Salesforce、ServiceNow 和 Box）的客户可能会遇到性能缓慢的情况：其 Azure AD 租户包含的组合用户和组超过 100,000 个，并且他们使用用户和组分配来确定应预配哪些用户。

在 2018 年 4 月 2 日，非常显著的性能增强功能已部署到 Azure AD 预配服务，以大幅减少执行 Azure Active Directory 和目标 SaaS 应用程序之间的初始同步所需的时间。

因此，与应用的初始同步需要多天或从未完成的许多客户现在可以在大约几分钟或几小时内完成。

有关详细信息，请参阅[预配期间会发生什么情况？](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>从已加入混合 Azure AD 的计算机的 Windows 10 锁定屏幕进行自助服务密码重置

**类型：** 已更改的功能 **服务类别：** 自助式密码重置 **产品功能：** 用户身份验证

我们已更新 Windows 10 SSPR 功能，以支持已加入混合 Azure AD 的计算机。 此功能在 Windows 10 RS4 中提供，允许用户从 Windows 10 计算机的锁屏界面重置其密码。 已启用并已注册自助服务密码重置的用户可以利用此功能。

有关详细信息，请参阅[从登录屏幕进行 Azure AD 密码重置](../authentication/howto-sspr-windows.md)。

---

## <a name="march-2018"></a>2018 年 3月

### <a name="certificate-expire-notification"></a>证书过期通知

**类型：** 已修复 **服务类别：** 企业应用 **产品功能：** SSO

当库或非库应用程序的证书即将过期时，Azure AD 会发送通知。

对于配置了基于 SAML 的单一登录的企业应用程序，某些用户无法收到通知。 此问题已解决。 如果证书在 7、30 和 60 天内过期，Azure AD 会发送通知。 可以在审核日志中查看此事件。

有关详细信息，请参阅：

- [在 Azure Active Directory 中管理用于联合单一登录的证书](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Azure Active Directory 门户中的“审核活动”报表](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C 中的 Twitter 和 GitHub 标识提供者

**类型：** 新功能 **服务类别：** B2C - 使用者标识管理 **产品功能：** B2B/B2C

现在能以标识提供者的身份在 Azure AD B2C 中添加 Twitter 或 GitHub。 Twitter 将从公共预览版过渡到正式版。 GitHub 即将发布公共预览版。

有关详细信息，请参阅[什么是 Azure AD B2B 协作？](../external-identities/what-is-b2b.md)

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>将 Intune Managed Browser 与基于 Azure AD 应用程序的条件访问配合使用来限制 iOS 和 Android 的浏览器访问

**类型：** 新功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

**现在处于公开预览状态！**

**Intune Managed Browser SSO：** 员工可以将跨本机客户端（如 Microsoft Outlook）的单一登录和 Intune Managed Browser 用于 Azure AD 连接的所有应用。

**Intune Managed Browser 条件访问支持：** 现在可以要求员工通过基于应用程序的条件访问策略使用 Intune Managed Browser。

有关详细信息，请阅读我们的[博客文章](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)。

有关详细信息，请参阅：

- [设置基于应用程序的条件访问](../conditional-access/app-based-conditional-access.md)

- [配置 Managed browser 策略](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>PowerShell GA 模块中的应用代理 Cmdlet

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 访问控制

PowerShell GA 模块现已提供对应用程序代理 cmdlet 的支持！ 这需要随时更新 PowerShell 模块 - 如果超过一年未更新，某些 cmdlet 可能会停止工作。

有关详细信息，请参阅 [AzureAD](/powershell/module/Azuread/)。

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>使用非交互式协议的无缝 SSO 支持 Office 365 本机客户端

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

使用 Office 365 本机客户端（16.0.8730.xxxx 和更高版本）的用户在使用无缝 SSO 时会获得静默登录体验。 这项支持是通过在 Azure AD 中添加非交互式协议 (WS-Trust) 提供的。

有关详细信息，请参阅[如何使用无缝 SSO 在本地客户端上进行登录？](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>如果应用程序将登录请求发送到 Azure AD 的租用终结点，则用户在使用无缝 SSO 时会获得静默登录体验

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

如果应用程序（例如 `https://contoso.sharepoint.com`）向 Azure AD 的租户终结点（即 `https://login.microsoftonline.com/contoso.com/<..>` 或 `https://login.microsoftonline.com/<tenant_ID>/<..>`）而不是 Azure AD 的普通终结点 (`https://login.microsoftonline.com/common/<...>`) 发送登录请求，用户在使用无缝 SSO 时可以获得静默登录体验。

有关详细信息，请参阅 [Azure Active Directory 无缝单一登录](../hybrid/how-to-connect-sso.md)。

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>只需将一个 Azure AD URL（以前为两个 URL）添加到用户的 Intranet 区域设置，即可实施无缝 SSO

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

若要向用户实施无缝 SSO，只需使用 Active Directory 中的组策略将一个 Azure AD URL 添加到用户的 Intranet 区域设置：`https://autologon.microsoftazuread-sso.com`。 以前，客户需要添加两个 URL。

有关详细信息，请参阅 [Azure Active Directory 无缝单一登录](../hybrid/how-to-connect-sso.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2018 年 3 月将这 15 款支持联合的新应用添加到了我们的应用库中：

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md)、[CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md)、Wrike、[SignalFx](../saas-apps/signalfx-tutorial.md)、Assistant by FirstAgenda、[YardiOne](../saas-apps/yardione-tutorial.md)、Vtiger CRM、inwink、[Amplitude](../saas-apps/amplitude-tutorial.md)、[Spacio](../saas-apps/spacio-tutorial.md)、[ContractWorks](../saas-apps/contractworks-tutorial.md)、[Bersin](../saas-apps/bersin-tutorial.md)、[Mercell](../saas-apps/mercell-tutorial.md)、[Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md)、[Qumu Cloud](../saas-apps/qumucloud-tutorial.md)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Azure 资源的 PIM 已发布正式版

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

如果将 Azure AD Privileged Identity Management 用于目录角色，现在可将 PIM 的时限访问和分配功能用于 Azure 资源角色，例如订阅、资源组、虚拟机和 Azure 资源管理器支持的其他任何资源。 实时激活角色时强制实施多重身份验证，并根据批准的更改时间范围计划激活。 此外，此版本添加了公共预览版中未提供的增强功能，包括更新的 UI、审批工作流，并且能够延长即将过期的角色，以及续订过期的角色。

有关详细信息，请参阅 [Azure 资源的 PIM（预览）](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>将可选声明添加到应用令牌（公共预览版）

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

Azure AD 应用现在可以在 JWT 或 SAML 令牌中请求自定义或可选声明。  因为大小或适用性方面的约束，这些有关用户或租户的声明默认不会包含在令牌中。  此功能目前已在 v1.0 和 v2.0 终结点上的 Azure AD 应用公共预览版中提供。  请参阅文档，以了解可添加的声明，以及如何编辑应用程序清单来请求这些声明。

有关详细信息，请参阅 [Azure AD 中的可选声明](../develop/active-directory-optional-claims.md)。

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD 支持使用 PKCE 来提高 OAuth 流的安全性

**类型：** 新功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

已更新 Azure AD 文档来指明对 PKCE 的支持。使用 PKCE 可以在执行 OAuth 2.0 授权代码授予流期间提高通信的安全性。  v1.0 和 v2.0 终结点上同时支持 S256 和纯文本 code_challenges。

有关详细信息，请参阅[请求授权代码](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)。

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Workday Get_Workers API 中提供预配所有用户属性值的支持

**类型：** 新功能 **服务类别：** 应用预配 **产品功能：** 第三方集成

从 Workday 到 Active Directory 和 Azure AD 的入站预配公共预览版现在支持提取和预配 Workday Get_Workers API 中可用的所有属性值。 除 Workday 入站预配连接器初始版本随附的属性以外，还支持数百个其他标准和自定义属性。

有关详细信息，请参阅：[自定义 Workday 用户属性列表](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>将组成员身份从动态更改为静态，或反之

**类型：** 新功能 **服务类别：** 组管理 **产品功能：** 协作

可更改在组中管理成员身份的方式。 想要在系统中保留相同的组名称和 ID，使针对组的任何现有引用仍然有效时，这很有用；创建新组需要更新这些引用。
我们已更新 Azure AD 管理中心，以支持此功能。 现在，客户可将现有组从动态成员身份转换为分配的成员身份，或反之。 现有的 PowerShell cmdlet 仍可用。

有关详细信息，请参阅 [Azure Active Directory 中的动态组成员身份规则](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>改进了无缝 SSO 的注销行为

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

以前，即使用户显式注销受 Azure AD 保护的应用程序，但如果他们尝试在其企业网络中从已加入域的设备再次访问 Azure AD 应用程序，系统也仍会使用无缝 SSO 自动将其登录。 实施此项更改后，将会支持注销。  这可以让用户选择使用相同或不同的 Azure AD 帐户登录，而不是使用无缝 SSO 自动登录。

有关详细信息，请参阅 [Azure Active Directory 无缝单一登录](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>应用程序代理连接器 1.5.402.0 版已发布

**类型：** 已更改的功能 **服务类别：** 应用代理 **产品功能：** 标识安全性和保护

此连接器版本将在 11 月之前逐步推出。 此新连接器版本包含以下更改：

- 连接器现在会设置域级别而不是子域级别的 Cookie。 这可以确保更顺畅的 SSO 体验，并避免多余的身份验证提示。
- 支持区块编码请求
- 改进了连接器运行状况监视
- 多项 bug 修复和稳定性改进

有关详细信息，请参阅[了解 Azure AD 应用程序代理连接器](../app-proxy/application-proxy-connectors.md)。

---

## <a name="february-2018"></a>2018 年 2 月

### <a name="improved-navigation-for-managing-users-and-groups"></a>改进了用于管理用户和组的导航界面

**类型：** 更改计划 **服务类别：** 目录管理 **产品功能：** 目录

已简化用于管理用户和组的导航体验。 现在，可以从目录概述直接导航到所有用户的列表，并更轻松地访问已删除用户的列表。 还可以从目录概述直接导航到所有组的列表，并更轻松地访问组管理设置。 在目录概述页中，还可以搜索用户、组、企业应用程序或应用注册。

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>世纪互联运营的 Microsoft Azure（Azure 中国区世纪互联）提供登录和审核报告

**类型：** 新功能 **服务类别：** Azure Stack **产品功能：** 监视和报告

世纪互联运营的 Microsoft Azure（Azure 中国区世纪互联）实例现在提供 Azure AD 活动日志报告。 包括以下日志：

- **登录活动日志** - 包括与租户关联的所有登录日志。

- **自助密码审核日志** - 包括所有 SSPR 审核日志。

- **目录管理审核日志** - 包括目录管理（例如用户管理、应用管理等）相关的所有审核日志。

使用这些日志，可以洞察环境的工作情况。 可以将提供的数据用于：

- 确定用户如何使用你的应用和服务。

- 排查妨碍用户完成其工作的问题。

有关如何使用这些报告的详细信息，请参阅 [Azure Active Directory 报告](../reports-monitoring/overview-reports.md)。

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>使用“报告读取者”角色（非管理员角色）查看 Azure AD 活动报告

**类型：** 新功能 **服务类别：** 报告 **产品功能：** 监视和报告

由于某些客户反映他们想要启用非管理员角色来访问 Azure AD 活动日志，我们为充当“报表读者”角色的用户启用了该功能，让他们使用 Azure 门户或 Microsoft Graph API 访问登录和审核活动。

有关如何使用这些报告的详细信息，请参阅 [Azure Active Directory 报告](../reports-monitoring/overview-reports.md)。

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>以用户属性和用户标识符的形式提供 EmployeeID 声明

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** SSO

可以通过企业应用程序 UI，将基于 SAML 登录的应用程序中的成员用户和 B2B 来宾的 **EmployeeID** 配置为用户标识符和用户属性。

有关详细信息，请参阅[在 Azure Active Directory 中为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>在 Azure AD 应用程序代理中使用通配符简化了应用程序管理

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 用户身份验证

为了简化应用程序部署并减少管理开销，我们现在支持使用通配符发布应用程序。 若要发布通配符应用程序，可以遵循标准的应用程序发布流，但需要在内部和外部 URL 中使用通配符。

有关详细信息，请参阅 [Azure Active Directory 应用程序代理中的通配符应用程序](../app-proxy/application-proxy-wildcard.md)。

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>开发了新的 cmdlet 用于支持应用程序代理配置

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 平台

最新版本的 AzureAD PowerShell 预览版模块包含新的 cmdlet，可让客户使用 PowerShell 来配置应用程序代理应用程序。

新 cmdlet 如下：

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>开发了新的 cmdlet 用于支持组配置

**类型：** 新功能 **服务类别：** 应用代理 **产品功能：** 平台

最新版本的 AzureAD PowerShell 模块包含用于在 Azure AD 中管理组的 cmdlet。 这些 cmdlet 以前在 AzureADPreview 模块中提供，现已添加到 AzureAD 模块

现已推出正式版的 Goup cmdlet 为：

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>推出了新版 Azure AD Connect

**类型：** 新功能 **服务类别：** AD Sync **产品功能：** 平台

Azure AD Connect 是在 Azure AD 与本地数据源（包括 Windows Server Active Directory 和 LDAP）之间同步数据的首选的工具。

>[!Important]
>此版本引入了架构和同步规则更改。 Azure AD Connect 同步服务在升级后将触发完全导入和完全同步步骤。 有关如何更改此行为的信息，请参阅[如何在升级后推迟完全同步](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)。

此版本提供以下更新和更改：

**已修复的问题**

- 修复了在切换到下一页时，“分区筛选”页的后台任务的计时窗口问题。

- 修复了在 ConfigDB 自定义操作过程中导致访问冲突的 Bug。

- 修复了 Bug，因此可以从 SQL 连接超时恢复。

- 修复了带 SAN 通配符的证书无法通过先决条件检查的 Bug。

- 修复了在 Azure AD 连接器导出过程中导致 miiserver.exe 崩溃的 Bug。

- 修复了当运行导致 Azure AD 连接向导更改配置时错误密码会尝试登录 DC 的 Bug

**新增功能和改进**

- 应用程序遥测 - 管理员可以切换此类数据的开/关设置。

- Azure AD 运行状况数据 - 管理员必须访问运行状况门户才能控制其运行状况设置。 等到服务策略更改以后，代理就会读取并强制实施它。

- 添加了设备写回配置操作以及用于页面初始化的进度栏。

- 改进了 HTML 报表的常规诊断功能以及 ZIP-Text/HTML 报表的完整数据收集功能。

- 提高了自动升级的可靠性并增加了更多的遥测，确保可以确定服务器的运行状况。

- 限制提供给以 AD 连接器帐户为基础的特权帐户的权限。 进行全新安装时，向导会限制特权帐户拥有的针对 MSOL 帐户的权限（前提是 MSOL 帐户已创建）。 这些更改会影响使用 Auto-Create 帐户执行的快速安装和自定义安装。

- 更改了安装程序。在进行 AADConnect 全新安装时，不需要 SA 特权。

- 添加了新的实用工具用于排查特定对象的同步问题。 目前，该实用程序用于检查以下问题：

    - Azure AD 租户中的已同步用户对象和用户帐户之间出现 UserPrincipalName 不匹配的情况。

    - 是否已通过域筛选将对象从同步中筛选出来

    - 是否已通过组织单位 (OU) 筛选将对象从同步中筛选出来

- 添加了新的实用工具，用于同步当前的密码哈希，该哈希存储在针对特定用户帐户的本地 Active Directory 中。 该实用程序不需要更改密码。

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>添加了支持 Intune 应用保护策略的应用程序以便在基于 Azure AD 应用程序的条件访问中使用

**类型：** 已更改的功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

我们添加了更多支持基于应用程序的条件访问的应用程序。 现在，可以使用这些批准的客户端应用来访问 Office 365 和其他已连接到 Azure AD 的云应用。

在 2 月底之前将添加以下应用程序：

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

有关详细信息，请参阅：

- [批准的客户端应用要求](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [基于 Azure AD 应用的条件访问](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>移动体验的使用条款更新

**类型：** 已更改的功能 **服务类别：** 使用条款 **产品功能：** 合规性

显示使用条款时，现在可以单击“浏览时遇到问题？请单击此处”。 单击此链接会在设备本地打开使用条款。 不管文档中的字体大小或设备屏幕大小如何，都可以根据需要进行缩放，以方便阅读文档。

---

## <a name="january-2018"></a>2018 年 1 月

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

2018 年 1 月，应用库中添加了支持联合的以下新应用：

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md)、[OneTrust 隐私管理软件](../saas-apps/onetrust-tutorial.md)、[Dealpath](../saas-apps/dealpath-tutorial.md)IriusRisk Federated Directory 和 [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="sign-in-with-additional-risk-detected"></a>登录时检测到其他风险

**类型：** 新功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

从检测到的风险检测获得的见解会绑定到 Azure AD 订阅。 使用 Azure AD Premium P2 版本时，可以获取有关所有基础检测的最详细的信息。

使用 Azure AD Premium P1 版本时，许可证未涵盖的检测项会显示为风险检测“登录时检测到其他风险”。

有关详细信息，请参阅 [Azure Active Directory 风险检测](../identity-protection/overview-identity-protection.md)。

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>在最终用户的访问面板中隐藏 Office 365 应用程序

**类型：** 新功能 **服务类别：** 我的应用 **产品功能：** SSO

现在，可以通过新的用户设置来更好地管理 Office 365 应用程序显示用户访问面板的方式。 如果只想在 Office 门户中显示 Office 应用，可以借助此选项来减少用户访问面板中的应用数量。 该设置位于“用户设置”中，带有“用户只能在 Office 365 门户中查看 Office 365 应用”标签 。

有关详细信息，请参阅[在 Azure Active Directory 的用户体验中隐藏应用程序](../manage-apps/hide-application-from-user-portal.md)。

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>直接从应用的 URL 无缝登录到启用了密码 SSO 的应用

**类型：** 新功能 **服务类别：** 我的应用 **产品功能：** SSO

现已通过一个便捷工具提供“我的应用”浏览器扩展。该工具以快捷方式的形式显示在浏览器中，可实现“我的应用”单一登录功能。 安装后，用户将在浏览器中看到一个堆积圆点图标，单击该图标可快速访问应用。 用户现在可以：

- 从应用登录页面直接登录到基于密码 SSO 的应用
- 使用快速搜索功能启动任何应用
- 在扩展中使用快捷方式访问最近使用的应用
- 此扩展适用于 Microsoft Edge、Chrome 和 Firefox。

有关详细信息，请参阅[我的应用安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 经典门户中的 Azure AD 管理体验已停用

**类型：** 弃用 **服务类别：** Azure AD **产品功能：** 目录

从 2018 年 1 月 8 日起，Azure 经典门户中的 Azure AD 管理体验已停用。 Azure 经典门户本身在同一时间也已停用。 今后，应使用 [Azure AD 管理员中心](https://aad.portal.azure.com)来执行所有基于门户的 Azure AD 管理。

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor Web 门户已停用

**类型：** 弃用 **服务类别：** Azure AD **产品功能：** 目录

从 2018 年 1 月 8 日起，PhoneFactor Web 门户已停用。 此门户用于管理 MFA 服务器，但这些功能已移至 Azure 门户 (portal.azure.com)。

MFA 配置位于：“Azure Active Directory”\>“MFA 服务器”

---

### <a name="deprecate-azure-ad-reports"></a>弃用 Azure AD 报告

**类型：** 弃用 **服务类别：** 报告 **产品功能：** 标识生命周期管理


随着新 Azure Active Directory 管理控制台的正式发布以及用于活动和安全报告的新 API 的推出，“/reports”终结点下面的报告 API 从 2017 年 12 月 31 日开始已停用。

**可用功能**

在过渡到新管理控制台的过程中，有 2 个新 API 可用于检索 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的标识保护风险检测 API 来访问以前通过安全报告提供的数据。

有关详细信息，请参阅：

- [Azure Active Directory 报告 API 入门](../reports-monitoring/concept-reporting-api.md)

- [Azure Active Directory 标识保护和 Microsoft Graph 入门](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>2017 年 12 月

### <a name="terms-of-use-in-the-access-panel"></a>访问面板中的使用条款

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 合规性

现在，可以转到访问面板并查看以前接受的使用条款。

执行以下步骤:

1. 转到 [MyApps 门户](https://myapps.microsoft.com)并登录。

2. 在右上角选择自己的姓名，然后从列表中选择“个人资料”。

3. 在“个人资料”中选择“查看使用条款”。 

4. 现在，可以查看已接受的使用条款。

有关详细信息，请参阅 [Azure AD 使用条款功能（预览版）](../conditional-access/terms-of-use.md)。

---

### <a name="new-azure-ad-sign-in-experience"></a>新 Azure AD 登录体验

**类型：** 新功能 **服务类别：** Azure AD **产品功能：** 用户身份验证

Azure AD 和 Microsoft 帐户标识系统 UI 经过重新设计，现在拥有一致的外观。 此外，Azure AD 登录页会先收集用户名，然后在第二个屏幕上收集凭据。

有关详细信息，请参阅[现已推出新 Azure AD 登录体验的公共预览版](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)。

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>登录提示减少：Azure AD 登录的新的“使我保持登录状态”体验

**类型：** 新功能 **服务类别：** Azure AD **产品功能：** 用户身份验证

Azure AD 登录页上的“使我保持登录状态”复选框已被替换为新提示，成功通过身份验证后会显示该提示。

如果对此提示回答“是”，则服务会提供永久刷新令牌。 此行为与在旧体验中选中“使我保持登录状态”复选框时相同。 对于联合租户，此提示在使用联合服务成功完成身份验证后显示。

有关详细信息，请参阅 [Fewer sign-in prompts:The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)（登录提示减少：现已推出 Azure AD 新的“使我保持登录状态”体验的预览版）。

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>添加配置以要求在接受使用条款之前先将其展开。

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 合规性

为管理员添加了一个选项，要求其用户在接受使用条款之前先将其展开。

对于要求用户展开使用条款这一选项，可选择“打开”或“关闭”。  “打开”设置要求用户在接受使用条款之前先查看条款。

有关详细信息，请参阅 [Azure AD 使用条款功能（预览版）](../conditional-access/terms-of-use.md)。

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>符合条件的角色分配的作用域激活

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

可以使用范围激活功能来激活符合条件的 Azure 资源角色分配，其自治性比原始分配的默认值小。 例如，你被分配为租户中某个订阅的所有者。 使用范围激活可以激活订阅中包含的最多五个资源（例如资源组和虚拟机）的“所有者”角色。 划分激活范围可能会降低对关键 Azure 资源执行不必要更改的可能性。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)。

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**类型：** 新功能 **服务类别：** 企业应用 **产品功能：** 第三方集成

我们已于 2017 年 12 月将这些支持联合的新应用添加到了我们的应用库中：

[Accredible](../saas-apps/accredible-tutorial.md)、Adobe Experience Manager、[EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md)、[Communifire](../saas-apps/communifire-tutorial.md) CybSafe、[FactSet](../saas-apps/factset-tutorial.md)、[IMAGE WORKS](../saas-apps/imageworks-tutorial.md)、[MOBI](../saas-apps/mobi-tutorial.md)、[MobileIron Azure AD 集成](../saas-apps/mobileiron-tutorial.md)、[Reflektive](../saas-apps/reflektive-tutorial.md)、[SAML SSO for Bamboo by resolution GmbH](../saas-apps/bamboo-tutorial.md)、[SAML SSO for Bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md)、[Vodeclic](../saas-apps/vodeclic-tutorial.md)、WebHR、Zenegy Azure AD 集成。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](../saas-apps/tutorial-list.md)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](../develop/v2-howto-app-gallery-listing.md)。

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 目录角色的审批工作流

**类型：** 已更改的功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

Azure AD 目录角色的审批工作流程已正式发布。

通过审批工作流程，特权角色管理员可要求符合条件的角色成员先请求角色激活才能使用特权角色。 可向多个用户和组委派审批职责。 符合条件的角色成员会在审批完成且角色激活时收到通知。

---

### <a name="pass-through-authentication-skype-for-business-support"></a>传递身份验证：Skype For Business 支持

**类型：** 已更改的功能 **服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

传递身份验证现在支持用户登录到支持新式身份验证的 Skype for Business 客户端应用程序，包括联机和混合拓扑。

有关详细信息，请参阅[支持新式身份验证的 Skype for Business 拓扑](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)。

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>更新到 Azure RBAC 的 Azure AD Privileged Identity Management（预览版）

**类型：** 已更改的功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

通过 Azure 基于角色的访问控制 (RBAC) 的 Azure AD Privileged Identity Management (PIM) 公共预览版更新，现在可以：

* 使用 Just Enough Administration。
* 需要批准才能激活资源角色。
* 安排将来激活需要批准 Azure AD 和 Azure 角色的角色。

有关详细信息，请参阅 [Azure 资源的 Privileged Identity Management（预览版）](../privileged-identity-management/azure-pim-resource-rbac.md)。

---

## <a name="november-2017"></a>2017 年 11 月

### <a name="access-control-service-retirement"></a>访问控制服务停用

**类型：** 更改计划 **服务类别：** 访问控制服务 **产品功能：** 访问控制服务

Azure Active Directory 访问控制（也称作访问控制服务）将在 2018 年底停用。 接下来的几周内会提供更多信息，包括详细的计划和高级迁移指南。 有关访问控制服务的任何问题，请在本页面留言，团队成员将予以解答。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>限制对 Intune 托管浏览器的浏览器访问

**类型：** 更改计划 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

可以使用 Intune 托管浏览器作为批准的应用，限制对 Office 365 以及其他已连接 Azure AD 的云应用的浏览器访问。

现在，可为基于应用程序的条件访问配置以下条件：

**客户端应用：** 浏览者

**此更改会产生什么影响？**

目前，使用此条件时会阻止访问。 推出预览版后，所有访问都需要使用托管的浏览器应用程序。

可在即将发布的博客和发行说明中了解此功能和详细信息。

有关详细信息，请参阅 [Azure AD 中的条件访问](../conditional-access/overview.md)。

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

**类型：** 更改计划 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

以下应用包含在[批准的客户端应用](../conditional-access/concept-conditional-access-conditions.md#client-apps)列表中：

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

有关详细信息，请参阅：

- [批准的客户端应用要求](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [基于 Azure AD 应用的条件访问](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条款支持多种语言

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 合规性

管理员现在可以创建包含多个 PDF 文档的新使用条款。 可以使用相应语言来标记这些 PDF 文档。 将会根据用户的偏好，以匹配的语言显示 PDF。 如果没有匹配的语言，则以默认语言显示。

---

### <a name="real-time-password-writeback-client-status"></a>实时密码写回客户端状态

**类型：** 新功能 **服务类别：** 自助式密码重置 **产品功能：** 用户身份验证

现在可以查看本地密码写回客户端的状态。 此选项位于[密码重置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)页的“本地集成”部分。

如果与本地写回客户端的连接出现问题，将会显示一条错误消息，其中包含：

- 有关无法连接到本地写回客户端的原因的信息。
- 可帮助解决此问题的文档链接。

有关详细信息，请参阅[本地集成](../authentication/concept-sspr-howitworks.md#on-premises-integration)。

---

### <a name="azure-ad-app-based-conditional-access"></a>基于 Azure AD 应用的条件访问

**类型：** 新功能 **服务类别：** Azure AD **产品功能：** 标识安全性和保护

现在可将对 Office 365 和其他已连接 Azure AD 的云应用的访问权限限于[批准的客户端应用](../conditional-access/concept-conditional-access-conditions.md#client-apps)，这些应用支持使用[基于 Azure AD 应用的条件访问](../conditional-access/app-based-conditional-access.md)的 Intune 应用保护策略。 Intune 应用保护策略用于配置和保护这些客户端应用程序中的公司数据。

通过组合[基于应用](../conditional-access/app-based-conditional-access.md)的条件访问策略和[基于设备](../conditional-access/require-managed-devices.md)的条件访问策略，可以灵活地保护个人和公司设备的数据。

以下条件和控制现可用于基于应用的条件访问：

**支持的平台条件**

- iOS
- Android

**客户端应用条件**

- 移动应用和桌面客户端

**访问控制**

- 需要批准的客户端应用

有关详细信息，请参阅[基于 Azure AD 应用的条件访问](../conditional-access/app-based-conditional-access.md)。

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>在 Azure 门户中管理 Azure AD 设备

**类型：** 新功能 **服务类别：** 设备注册和管理 **产品功能：** 标识安全性和保护

现在可在一个位置找到连接到 Azure AD 的所有设备以及与设备相关的活动。 在 Azure 门户中管理所有设备标识和设置有新的管理体验。 在此版本中，可以：

- 查看可用于 Azure AD 中的条件访问的所有设备。
- 查看属性，包括已加入混合 Azure AD 的设备。
- 查找已加入 Azure AD 的设备的 BitLocker 密钥、使用 Intune 管理设备，等等。
- 管理与 Azure AD 设备相关的设置。

有关详细信息，请参阅[使用 Azure 门户管理设备](../devices/device-management-azure-portal.md)。

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>支持将 macOS 作为 Azure AD 条件访问的设备平台

**类型：** 新功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

现在可以包含（或排除）macOS 作为 Azure AD 条件访问策略中的设备平台条件。 通过将 macOS 添加到支持的设备平台，可以：

- **使用 Intune 登记和管理 macOS 设备。** 类似于 iOS 和 Android 等其他平台，macOS 可通过公司门户应用程序执行统一注册。 可以使用适用于 macOS 的新公司门户应用，通过 Intune 登记设备并将其注册到 Azure AD。
- **确保 macOS 设备符合组织在 Intune 中定义的符合性策略。** 可以在 Azure 门户上的 Intune 中设置 macOS 设备的符合性策略。
- **只允许符合条件的 macOS 设备访问 Azure AD 中的应用程序。** 条件访问策略创作将 macOS 用作单独的设备平台选项。 现在，可以针对 Azure 中设置的目标应用程序创作特定于 macOS 的条件访问策略。

有关详细信息，请参阅：

- [使用 Intune 创建适用于 macOS 设备的设备符合性策略](/mem/intune/protect/compliance-policy-create-mac-os)
- [Azure AD 中的条件访问](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>适用于 Azure AD 多重身份验证的网络策略服务器扩展

类型：新功能 服务类别：多重身份验证 产品功能：用户身份验证

适用于 Azure Active Directory (Azure AD) 多重身份验证的网络策略服务器扩展使用现有服务器在身份验证基础结构中添加基于云的多重身份验证功能。 使用网络策略服务器扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流。 无需安装、配置和维护新服务器。

此扩展是为想要保护虚拟专用网络连接，但不部署 Azure Active Directory 多重身份验证服务器的组织创建的。 网络策略服务器扩展充当 RADIUS 与基于云的 Azure AD 多重身份验证之间的适配器，以便为联合用户或已同步用户提供身份验证的第二个因素。

有关详细信息，请参阅[将现有网络策略服务器基础结构与 Azure AD 多重身份验证集成](../authentication/howto-mfa-nps-extension.md)。

---

### <a name="restore-or-permanently-remove-deleted-users"></a>还原或永久删除已删除的用户

**类型：** 新功能 **服务类别：** 用户管理 **产品功能：** 目录

在 Azure AD 管理中心，可以：

- 还原已删除的用户。
- 永久删除用户。

**试试看：**

1. 在 Azure AD 管理中心，选择“管理”部分中的“[所有用户](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All)”。

2. 从“显示”列表中，选择“最近删除的用户” 。

3. 选择一个或多个最近删除的用户，然后将其还原或永久删除。

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

**类型：** 已更改的功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

以下应用已添加到[批准的客户端应用](../conditional-access/concept-conditional-access-conditions.md#client-apps)列表：

- Microsoft Planner
- Azure 信息保护

有关详细信息，请参阅：

- [批准的客户端应用要求](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [基于 Azure AD 应用的条件访问](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>在条件访问策略中的控制条件之间使用“OR”

**类型：** 已更改的功能 **服务类别：** 条件访问 **产品功能：** 标识安全性和保护

现在，可对条件访问控制使用“OR”（需要一个选定的控制条件）。 可以使用此功能创建在访问控制条件之间包含“OR”的策略。 例如，可以使用此功能创建一个策略，要求用户使用多重身份验证登录，或要求用户在符合条件的设备上操作。

有关详细信息，请参阅 [Azure AD 条件访问中的控制](../conditional-access/controls.md)。

---

### <a name="aggregation-of-real-time-risk-detections"></a>实时风险检测的聚合

**类型：** 已更改的功能 **服务类别：** 标识保护 **产品功能：** 标识安全性和保护

在 Azure AD 标识保护中，某一天源自同一 IP 地址的所有实时风险检测现按照每种风险检测类型聚合在一起。 此更改限制了显示的风险检测数量，但不会对用户安全性造成任何影响。

每当用户登录时，基础实时检测都会运行。 如果针对多重身份验证或阻止访问设置了登录风险安全策略，则每次存在风险的登录期间都会触发该设置。

---

## <a name="october-2017"></a>2017 年 10 月

### <a name="deprecate-azure-ad-reports"></a>弃用 Azure AD 报告

**类型：** 更改计划 **服务类别：** 报告 **产品功能：** 标识生命周期管理

Azure 门户提供：

- 新的 Azure AD 管理控制台。
- 用于活动和安全报告的新 API。

由于推出了这些新功能，/reports 终结点下的报告 API 将在 2017 年 12 月 10 日停用。

---

### <a name="automatic-sign-in-field-detection"></a>自动登录字段检测

**类型：** 已修复 **服务类别：** 我的应用 **产品功能：** 单一登录

对于显示 HTML 用户名和密码字段的应用程序，Azure AD 支持自动登录字段检测。 [如何自动捕获应用程序的登录字段](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app)中介绍了这些步骤。 在 [Azure 门户](https://aad.portal.azure.com)中的“企业应用程序”页面上添加一个“非库”应用程序，即可找到此功能。 此外，可在此新应用程序中将“单一登录”模式配置为“基于密码的单一登录”，输入 Web URL，然后保存页面。 

由于某个服务问题，此功能曾经暂时禁用过。 该问题现已得到解决，自动登录字段检测功能再次可用。

---

### <a name="new-multi-factor-authentication-features"></a>新的多重身份验证功能

类型：新功能 服务类别：多重身份验证 产品功能：标识安全性和保护

Azure Active Directory (Azure AD) 多重身份验证 (MFA) 是保护组织的不可或缺的组成部分。 为使凭证的适应能力更强，体验更顺畅，添加了以下功能：

- 将多重身份验证质询结果直接集成到 Azure AD 登录报告，包括以编程方式访问 MFA 结果。
- 将 MFA 配置更深入地集成到 Azure 门户中的 Azure AD 配置体验。

在此公共预览版中，MFA 管理和报告集成到核心 Azure AD 配置体验中。 现在，可以在 Azure AD 体验中管理 MFA 管理门户功能。

有关详细信息，请参阅 [Azure 门户中的 MFA 报告参考](../authentication/howto-mfa-reporting.md)。

---

### <a name="terms-of-use"></a>使用条款

**类型：** 新功能 **服务类别：** 使用条款 **产品功能：** 合规性

可以使用 Azure AD 的使用条款功能向用户显示法律要求或符合性要求的相关免责声明。

可在以下情况下使用 Azure AD 使用条款：

- 针对组织中所有用户的常规使用条款
- 基于用户属性（例如，动态组中的医生与护士或国内员工和国际员工）的具体使用条款
- 有关访问业务影响性较高的应用（例如 Salesforce）的具体使用条款

有关详细信息，请参阅 [Azure AD 使用条款](../conditional-access/terms-of-use.md)。

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management 的增强

**类型：** 新功能 **服务类别：** Privileged Identity Management **产品功能：** Privileged Identity Management

使用 Azure AD Privileged Identity Management，可以管理、控制和监视对组织中 Azure 资源（预览版）的访问：

- 订阅
- 资源组
- 虚拟机

Azure 门户中使用 Azure RBAC 功能的所有资源都可以利用 Azure AD Privileged Identity Management 提供的所有安全和生命周期管理功能。

有关详细信息，请参阅 [Azure 资源的 Privileged Identity Management](../privileged-identity-management/azure-pim-resource-rbac.md)。

---

### <a name="access-reviews"></a>访问评审

**类型：** 新功能 **服务类别：** 访问评审 **产品功能：** 合规性

组织可以使用访问评审（预览版）有效管理组成员身份以及对企业应用程序的访问权限：

- 可以使用访问评审鉴定来宾用户对应用程序的访问权限及其组成员身份。 评审者可以根据访问评审提供的见解，有效决定是否允许来宾继续访问。
- 可以使用访问评审再次验证员工对应用程序和组成员身份的访问权限。

可以收集与组织相关的程序中的访问评审控件，跟踪符合性或风险敏感应用程序的审阅。

有关详细信息，请参阅 [Azure AD 访问评审](../governance/access-reviews-overview.md)。

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>在“我的应用”和 Office 365 应用启动器中隐藏第三方应用程序

**类型：** 新功能 **服务类别：** 我的应用 **产品功能：** 单一登录

现在，可以通过“隐藏应用”属性更好地管理用户门户中显示的应用。 如果为后端服务显示的应用磁贴或重复的磁贴导致用户的应用启动器变得混杂，隐藏应用可帮助解决问题。 切换开关位于第三方应用的“属性”部分中，带有“对用户可见?”标签  还可以通过 PowerShell 以编程方式隐藏应用。

有关详细信息，请参阅[在 Azure AD 的用户体验中隐藏第三方应用程序](../manage-apps/hide-application-from-user-portal.md)。


**可用功能**

 在过渡到新管理控制台的过程中，有两个新 API 可用于检索 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的标识保护风险检测 API 来访问以前通过安全报告提供的数据。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>适用于 Identity Manager 的修补程序

**类型：** 已更改的功能 **服务类别：** Identity Manager **产品功能：** 标识生命周期管理

现已推出 Identity Manager 2016 Service Pack 1 截止 2017 年 9 月 25 日的修补程序汇总包（内部版本 4.4.1642.0）。 此汇总包：

- 解决了一些问题并添加了改进项。
- 是一项累积更新，可取代 Identity Manager 2016 内部版本 4.4.1459.0 及其之前的所有 Identity Manager 2016 Service Pack 1 更新。
- 要求安装 Identity Manager 2016 内部版本 4.4.1302.0。

有关详细信息，请参阅[为 Identity Manager 2016 Service Pack 1 推出了修补程序汇总包（内部版本 4.4.1642.0）](https://support.microsoft.com/help/4021562)。

---
