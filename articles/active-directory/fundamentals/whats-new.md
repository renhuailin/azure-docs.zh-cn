---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 的新增功能;例如最新的发行说明、已知问题、bug 修复、已弃用的功能以及即将发生的更改。
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
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c835ddcee332499ef7853d0d2fa4fcdbcf8a1cf
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988872"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`) 并粘贴到 ![RSS 源阅读器图标](./media/whats-new/feed-icon-16x16.png) 订阅源阅读器中，获取有关何时重新访问此页以获得更新的通知。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果你要查找的项超过六个月，则可以在存档中查找 [Azure Active Directory 中的新增功能](whats-new-archive.md)。

---
## <a name="january-2021"></a>2021 年 1 月

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>配置预配时，机密令牌将为必填字段

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

过去，在自定义/BYOA 应用程序上设置设置时，"机密令牌" 字段可能会保留为空。 此函数旨在仅用于测试。 我们将更新 UI，以使字段成为必填字段。 

客户可通过使用浏览器 URL 中的功能标志解决这一要求，以便进行测试。 [了解详细信息](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)。
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>公共预览版-大规模自定义和配置适用于 Firstline 工作人员的 Android 共享设备

**类型：** 新功能  
**服务类别：** 设备注册和管理  
**产品功能：** 标识安全和保护
 
Azure AD 和 Microsoft 终结点管理器团队组合在一起，可自定义、缩放和保护 Firstline 工作线程设备。

以下预览功能将允许你：
- 通过 Microsoft 终结点管理器大规模预配 Android 共享设备
- 使用基于设备的条件访问保护对倒班辅助角色的访问
- 通过托管主屏幕为倒班 worker 自定义登录体验

若要了解详细信息，请参阅 [自定义和配置规模为 Firstline 的辅助角色的共享设备](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)。

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>公共预览版-预配日志现在可以下载为 CSV 或 JSON

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

客户可以通过 UI 和通过图形 API 将预配日志作为 CSV 或 JSON 文件下载。 若要了解详细信息，请参阅 [在 Azure Active Directory 门户中预配报表](../reports-monitoring/concept-provisioning-logs.md)。

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>公共预览-将云组分配到 Azure AD 自定义角色和管理单元范围的角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
客户可将云组分配到 Azure AD 自定义角色或管理单元范围的角色。 若要了解如何使用此功能，请参阅 [使用云组管理 Azure Active Directory 中的角色分配](../roles/groups-concept.md)。

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>公开上市-Azure AD Connect 云同步 (之前称为云预配) 

**类型：** 新功能  
**服务类别：** Azure AD Connect 云同步  
**产品功能：** 标识生命周期管理
 
Azure AD Connect 云同步现已正式提供给所有客户。

Azure AD Connect 云将转换逻辑的重提升量转移到了云，从而减少了本地占用量。 此外，有多个轻型代理部署可用于提高同步可用性。 [了解详细信息](https://aka.ms/cloudsyncGA)。
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>公开发布-攻击模拟管理员和攻击负载作者内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
Role-Based 访问控制中的两个新角色可分配给用户、攻击模拟管理员和攻击负载作者。 

[攻击模拟管理员](../roles/permissions-reference.md#attack-simulation-administrator)角色中的用户可以访问租户中的所有模拟，并可以：
- 创建和管理攻击模拟创建的所有方面
- 启动/计划模拟
-  查看模拟结果。 

[攻击负载作者](../roles/permissions-reference.md#attack-payload-author)角色中的用户可以创建攻击负载，但实际上不会启动或计划它们。 然后，租户中的所有管理员都可以使用攻击有效负载创建模拟。

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>公开上市-使用情况摘要报表读者内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
使用 "使用情况摘要报表读者" 角色的用户可以访问租户级别的聚合数据，并 Microsoft 365 管理中心中的相关见解来实现使用情况和效率评分。 但是，他们无法访问任何用户级别的详细信息或见解。 

对于这两个报表 Microsoft 365 管理中心，我们区分租户级别的聚合数据和用户级别的详细信息。 此角色向单独的用户可识别数据添加了额外的保护层。 [了解详细信息](../roles/permissions-reference.md#usage-summary-reports-reader)。

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>公开上市-需要 Azure AD 条件访问中的应用保护策略授予

**键入：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
现已为 "需要应用保护策略" Azure AD 条件访问授予。 

该策略提供了以下功能：
- 仅在使用支持 Intune 应用保护的移动应用程序时才允许访问
- 仅当用户将 Intune 应用保护策略传递到移动应用程序时才允许访问

在 [此处](../conditional-access/app-protection-based-conditional-access.md)了解有关如何为应用保护设置条件访问策略的详细信息。
 
---

### <a name="general-availability---email-one-time-passcode"></a>公开发布-电子邮件 One-Time 密码

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
通过电子邮件 OTP，世界各地的组织可以通过电子邮件发送链接或邀请来与他人进行协作。 受邀用户可以使用发送到其电子邮件的一次性密码来验证其身份，以便访问其合作伙伴的资源。 [了解详细信息](../external-identities/one-time-passcode.md)。 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Azure AD 应用程序库中的新预配连接器-2021 年1月

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

有关详细信息，请参阅 [什么是 Azure AD 中的自动 SaaS 应用用户预配？](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Azure AD 应用程序库中提供了新的联合应用程序-2021 年1月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2021年1月，我们已在应用程序库中添加了以下29个新应用程序，并提供联合支持：

[mySCView](https://dev.myscview.com/)、 [Talentech](https://talentech.com/contact/)、 [Bipsync](https://www.bipsync.com/)、 [OroTimesheet](https://app.orotimesheet.com/login.php)、 [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub)、 [SOVELTO](https://login.soveltoeasy.fi/) [Supportbench](https://account.supportbench.net/agent/login/)，[Bienvenue 构造](https://formation.bienvenue.pro/login)， [AIDA 医疗保健 SSO](https://aidaforparents.com/login/organizations)，[国际 SOS 援助产品](../saas-apps/international-sos-assistance-products-tutorial.md)， [NAVEX One](../saas-apps/navex-one-tutorial.md)， [LabLog](../saas-apps/lablog-tutorial.md)，Oktopost [SAML](../saas-apps/oktopost-saml-tutorial.md)，EPHOTO [DAM](../saas-apps/ephoto-dam-tutorial.md)，[观念](../saas-apps/notion-tutorial.md)， [Syndio，](../saas-apps/syndio-tutorial.md) [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md)， [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md)， [Nalco E-数据](https://www.ecolab.com/)，[空缺 Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)，Synerise [AI 增长生态系统](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md)， [Imperva 数据安全](../saas-apps/imperva-data-security-tutorial.md)， [Illusive 网络](../saas-apps/illusive-networks-tutorial.md)， [Proware](../saas-apps/proware-tutorial.md) [，](../saas-apps/perimeter-81-tutorial.md)Splan[访问者，](../saas-apps/splan-visitor-tutorial.md) [Aruba 用户](https://docs.microsoft.com/azure/active-directory/saas-apps/burp-suite-enterprise-edition-tutorial)[体验见解，](../saas-apps/aruba-user-experience-insight-tutorial.md) [Contentsquare 81](../saas-apps/contentsquare-sso-tutorial.md)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>公共预览版-第二级经理可以设置为备用审批者

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
当你选择 "审批者" 时，可以使用其他选项。 如果为第一个审批者选择了 "经理作为审批者"，则可以选择另一个选项，即 "作为备用审批者的第二级经理"，可以在 "备用审批者" 字段中进行选择。 如果选择此选项，则需要添加后备审批者，以便在系统找不到二级管理员的情况下将请求转发给该审批者。 [了解详细信息](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>公开上市-直接从我的访问门户导航到团队

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
你现在可以直接从 "我的 Access" 门户启动团队。 

为此，请登录到 "我的 Access (https://myaccess.microsoft.com/) ，导航到" 访问包 "，然后转到" 活动 "选项卡以查看您有权访问的所有访问包。 展开所选访问包并将其悬停在团队中时，可以通过单击 "打开" 按钮启动它。 [了解详细信息](../governance/entitlement-management-request-access.md)。
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>改善了日志记录 & End-User 为有风险的来宾用户提供提示

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 

已更新有风险的来宾用户的日志记录和 End-User 提示。 详细了解 [Identity Protection 和 B2B 用户](../identity-protection/concept-identity-protection-b2b.md)。
 
---
 
## <a name="december-2020"></a>2020 年 12 月

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>公共预览版-使用内置策略 Azure AD B2C 手机注册和登录

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
使用内置策略的 B2C 手机注册和登录可让组织的 IT 管理员和开发人员允许最终用户登录并使用用户流中的电话号码进行注册。 有关详细信息，请参阅为 [用户流 (预览设置电话注册和登录) ](../../active-directory-b2c/phone-authentication-user-flows.md) 。

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>常规可用性-默认情况下，为所有新租户启用安全默认值

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 标识安全和保护
 
为了保护用户帐户，在2020年11月12日或之后创建的所有新租户都将附带启用安全默认设置。 安全默认值强制实施多个策略，包括：
- 要求所有用户和管理员使用 Microsoft Authenticator 应用注册 MFA
- 需要关键管理员角色才能在每次登录时使用 MFA。 必要时，将提示所有其他用户进行 MFA。 
- 旧身份验证将在租户范围内被阻止。 

有关详细信息，请参阅 [什么是安全默认值？](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>公开上市-支持 AADConnect 中具有最多250K 成员的组

**类型：** 已更改的功能  
**服务类别：** AD Connect  
**产品功能：** 标识生命周期管理
 
Microsoft 已部署新的 Azure AD Connect 终结点 (API)，可提高 Azure Active Directory 的同步服务操作性能。 当你使用新的 [V2 终结点](../hybrid/how-to-connect-sync-endpoint-api-v2.md)时，你将会遇到 Azure AD 的导出和导入的显著性能改进。 此新终结点支持以下方案：

- 与最多250k 个成员同步组
- 导出和导入到 Azure AD 的性能提高

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>公开上市-适用于 Azure 中国云中租户的授权管理

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 

授权管理功能现已适用于 Azure 中国云中的所有租户。 有关信息，请访问我们的 [标识调控文档](https://docs.azure.cn/zh-cn/active-directory/governance/) 网站。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年12月

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

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Azure AD 应用程序库中提供了新的联合应用程序-2020 年12月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年12月，我们已在应用程序库中添加了以下18个新应用程序，并提供联合支持：

[AwareGo](../saas-apps/awarego-tutorial.md)， [HowNow SSO](https://gethownow.com/)， [ZyLAB ONE 法定候](https://www.zylab.com/en/product/legal-hold)， [Guider](http://www.guider-ai.com/)， [Softcrisis](https://www.softcrisis.se/sv/)， [Pims 365](http://www.omega365.com/pims)， [InformaCast](../saas-apps/informacast-tutorial.md)， [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md)， [VONAGE](../saas-apps/vonage-tutorial.md)， [统计我的操作仪表板](../saas-apps/count-me-in-operations-dashboard-tutorial.md)， [ProProfs 知识库](../saas-apps/proprofs-knowledge-base-tutorial.md)， [RightCrowd 劳动力管理](../saas-apps/rightcrowd-workforce-management-tutorial.md)， [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md)， [Shutterstock](../saas-apps/shutterstock-tutorial.md)， [FortiWeb Web 应用程序防火墙](../saas-apps/linkedin-talent-solutions-tutorial.md)， [LinkedIn 人才解决方案](../saas-apps/linkedin-talent-solutions-tutorial.md)，Equinix [联合应用](../saas-apps/equinix-federation-app-tutorial.md)， [KFAdvance](../saas-apps/kfadvance-tutorial.md)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>直接从我的访问门户导航到团队

**类型：** 已更改的功能  
**服务类别：** 用户访问管理 **产品功能：** 权利管理

你现在可以直接从我的访问门户启动团队。 为此，请登录到 ["我的访问](https://myaccess.microsoft.com/)"，导航到 " **访问包**"，然后转到 " **活动** " 选项卡以查看你有权访问的所有访问包。 展开访问包并将鼠标悬停在团队中时，可以通过单击 " **打开** " 按钮启动。 

若要详细了解如何使用 "我的访问门户"，请访问 Azure AD 权限管理 "中的" [请求访问访问包](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)"。

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>公共预览版-第二级经理可以设置为备用审批者

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理

在授权管理的审批过程中，现在可以使用额外的选项。 如果为第一个审批者选择了 "经理" 作为审批者，则可以选择另一个选项，第二级经理作为备用审批者，可在备用审批者字段中进行选择。 如果选择此选项，则需要添加后备审批者，以便在系统无法找到第二层管理器的情况下将请求转发到。

有关详细信息，请转到 [Azure AD 授权管理中的访问包的 "更改批准设置](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)"。

--- 

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1.0、TLS 1.1 和3DES 弃用

**类型：** 更改计划  
**服务类别：** 所有 Azure AD 应用程序  
**产品功能：** 标准

Azure Active Directory 将在2021年6月30日之前 Azure Active Directory 全球区域中弃用以下协议：

- TLS 1.0
- TLS 1.1
- TLS_RSA_WITH_3DES_EDE_CBC_SHA 的3DES 密码套件 () 

受影响的环境包括：
- Azure 商业云
- Office 365 GCC 和 WW

相关公告所有客户端-服务器和浏览器-服务器组合应使用 TLS 1.2 和新式密码套件来维护与 Azure Active Directory 的 Azure、Office 365 和 Microsoft 365 服务的安全连接。 这种更改与 [US Gov 云中 AZURE ACTIVE DIRECTORY TLS 1.0 & 1.1 和3Des 密码套件弃用](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)相关。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD 应用程序库中提供了新的联合应用-2020 年11月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020年11月，我们已在应用程序库中添加了以下52新应用程序，并提供联合身份验证支持：

[旅游 & 支出管理](https://app.expenseonce.com/Account/Login)、 [Tribeloo](../saas-apps/tribeloo-tutorial.md)、 [Itslearning 文件选取器](https://pmteam.itslearning.com/)、[危机控制](../saas-apps/crises-control-tutorial.md) [CourtAlert](https://www.courtalert.com/)， [StealthMail](https://stealthmail.com/)， [Edmentum-研究岛](https://app.studyisland.com/cfw/login/)，[虚拟风险管理器](../saas-apps/virtual-risk-manager-tutorial.md)， [TIMU](../saas-apps/timu-tutorial.md)， [Looker 分析平台](../saas-apps/looker-analytics-platform-tutorial.md)， [Talview](https://recruit.talview.com/login)，实时翻译器，Klaxoon， [](https://access.klaxoon.com/login) [Podbean](../saas-apps/podbean-tutorial.md)， [zcal](https://zcal.co/signup)， [expensemanager](https://api.expense-manager.com/) [，Netsparker，trak](../saas-apps/netsparker-enterprise-tutorial.md) [，Appian](../saas-apps/panorays-tutorial.md) [，](https://portal.builterra.com/) [EVA 签入](https://my.evacheckin.com/organization) [，Panorays](https://portal.en-trak.app/)Builterra [，HowNow](../saas-apps/appian-tutorial.md) [，WebApp，](../saas-apps/coupa-risk-assess-tutorial.md)Coupa，Lucid imagination [ (所有产品) ](../saas-apps/lucid-tutorial.md)， [GoBright](https://portal.brightbooking.eu/)， [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md)，[Resource Central](../saas-apps/resource-central-tutorial.md)， [UiPathStudioO365App](https://www.uipath.com/product/platform)， [Jedox](../saas-apps/jedox-tutorial.md)， [Cequence 应用程序安全性](../saas-apps/cequence-application-security-tutorial.md)， [PerimeterX](../saas-apps/perimeterx-tutorial.md)， [TrendMiner](../saas-apps/trendminer-tutorial.md)， [Lexion](../saas-apps/lexion-tutorial.md)， [WorkWare](../saas-apps/workware-tutorial.md)， [ProdPad](../saas-apps/prodpad-tutorial.md)， [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md)， [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md)， [Luum](../saas-apps/luum-tutorial.md)，[运费度量值](https://www.gpcsl.com/freight.html)，Terraform[云](../saas-apps/terraform-cloud-tutorial.md)，[性质研究](../saas-apps/nature-research-tutorial.md)，[播放数字告示](https://login.playsignage.com/login) [，RemotePC，Prolorus](../saas-apps/remotepc-tutorial.md) [，Hirebridge](../saas-apps/prolorus-tutorial.md) [ATS](../saas-apps/hirebridge-ats-tutorial.md)， [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure)， [Roadmunk](../saas-apps/roadmunk-tutorial.md)，[日出 Software 关系 CRM](https://cloud.relations-crm.com/) [，Procaire，](../saas-apps/procaire-tutorial.md)[导师®](https://www.edriving.com/) [](../saas-apps/hownow-webapp-sso-tutorial.md) [](https://gradle.com/)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>公共预览版-适用于企业应用的自定义角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
 [用于委派的企业应用程序管理的自定义 RBAC 角色现提供](../roles/custom-available-permissions.md) 公共预览版。 这些新权限建立在应用注册管理的自定义角色之上，这允许对管理员拥有的访问权限进行精细控制。 随着时间的推移，将发布对 Azure AD 的委派管理的其他权限。

一些常见委托方案：
- 分配可访问基于 SAML 的单一登录应用程序的用户和组
- Azure AD 库应用程序的创建
- 更新和读取基于 SAML 的单一登录应用程序的基本 SAML 配置
- 管理基于 SAML 的单一登录应用程序的签名证书
- 更新基于 SAML 的单一登录应用程序的过期登录证书通知电子邮件地址
- 为基于 SAML 的单一登录应用程序更新 SAML 令牌签名和登录算法
- 为基于 SAML 的单一登录应用程序创建、删除和更新用户属性和声明
- 能够打开、关闭和重新启动预配作业
- 属性映射的更新
- 能够读取与对象关联的预配设置
- 能够读取与服务主体关联的预配设置
- 能够授权应用程序访问以进行预配

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>公共预览版-Azure AD 应用程序代理本身支持对使用标头进行身份验证的应用程序进行单一登录访问

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制
 
Azure Active Directory (Azure AD) 应用程序代理在本地支持对使用标头进行身份验证的应用程序进行单一登录访问。 你可以在 Azure AD 中配置应用程序所需的标头值。 标头值将通过应用程序代理发送到应用程序。 若要了解详细信息，请参阅 [具有 Azure AD 应用 Proxy 的本地应用的基于标头的单一登录](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>公开上市-Azure AD B2C 使用自定义策略进行电话注册和登录

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

通过电话号码注册和登录，开发人员和企业可以允许其客户使用通过短信发送到用户的电话号码的一次性密码进行注册和登录。 此功能还允许客户在电话号码失去访问权限时更改其电话号码。 通过自定义策略的强大功能，开发人员和企业可以通过页面自定义来传达其品牌。 了解如何 [在 Azure AD B2C 中设置自定义策略的手机注册和登录](../../active-directory-b2c/phone-authentication.md)。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年11月

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
- [票-Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

有关详细信息，请参阅 [通过 Azure AD 自动完成用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>公共预览版-带有 ProxyAddresses 的电子邮件 Sign-In 现在可通过分步推出进行部署

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
租户管理员现在可以使用分阶段推出来使用 ProxyAddresses 将电子邮件 Sign-In 部署到特定 Azure AD 组。 在通过主领域发现策略将功能部署到整个租户之前，此功能可帮助你尝试该功能。 [文档](../authentication/howto-authentication-use-email-signin.md)中提供了通过分步推出 Sign-In 通过 ProxyAddresses 部署电子邮件的说明。
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>有限预览-登录诊断

**类型：** 新功能  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
通过登录诊断的初始预览版本，管理员现在可以查看用户登录。管理员可以接收有关在登录期间发生的情况、特定的、相关的详细信息以及如何解决问题的指导。 诊断在 Azure AD 级别和条件访问诊断和解决边栏选项卡中均可用。 此版本中涵盖的诊断方案是条件性访问、多重身份验证和登录成功。

有关详细信息，请参阅 [什么是登录诊断 Azure AD？](../reports-monitoring/overview-sign-in-diagnostics.md)。
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>改进了不熟悉的登录属性

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

  已更新不熟悉的登录属性检测。 客户可能会注意到更高风险的登录属性检测。 有关详细信息，请参阅 [什么是风险？](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>云预配代理的公共预览版刷新现在可用 (版本： 1.1.281.0) 

**类型：** 已更改的功能  
**服务类别：** Azure AD 云预配  
**产品功能：** 标识生命周期管理
 
云预配代理已在公共预览版中发布，现可通过门户获得。 此版本包含多项改进，包括支持域的 GMSA，从而提供更好的安全性、改进的初始同步周期以及对大型组的支持。 有关更多详细信息，请查看发布版本 [历史记录](../app-provisioning/provisioning-agent-release-version-history.md) 。 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker 恢复密钥 API 终结点现已在/informationProtection 下

**类型：** 已更改的功能  
**服务类别：** 设备访问管理  
**产品功能：** 设备生命周期管理
 
以前，你可以通过/bitlocker 终结点恢复 BitLocker 密钥。 最终，我们将弃用此终结点，客户应该开始使用现在属于/informationProtection. 的 API。 

有关文档更新的详细说明，请参阅 [BitLocker 恢复 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) 。

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>远程桌面服务 HTML5 Web 客户端的应用程序代理支持的公开上市

**类型：** 已更改的功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制
 
 (RDS) Web 客户端的远程桌面服务 Azure AD 应用程序代理支持现已公开上市。 RDS web 客户端允许用户通过任何支持 HTLM5 的浏览器（如 Microsoft Edge、Internet Explorer 11、Google Chrome 等）访问远程桌面基础结构。 用户可以与远程应用或桌面进行交互，就像在任何位置使用本地设备一样。 

通过使用 Azure AD 应用程序代理，你可以通过为所有类型的丰富客户端应用强制执行预身份验证和条件性访问策略，从而提高 RDS 部署的安全性。 若要了解详细信息，请参阅 [通过 Azure AD 应用程序代理发布远程桌面](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>新的增强型动态组服务采用公共预览版

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 协作
 
增强的动态组服务现在处于公共预览版中。 在其租户中创建动态组的新客户将使用新服务。 创建动态组所需的时间将与所创建的组的大小成正比，而不是租户的大小。 当客户创建较小的组时，此更新将显著提高大型租户的性能。 

新服务还旨在完成成员添加和删除，因为在数分钟内属性更改。 此外，单一处理失败不会阻止租户处理。 若要了解有关创建动态组的详细信息，请参阅 [文档](../enterprise-users/groups-create-rule.md)。
 
---
## <a name="october-2020"></a>2020 年 10 月

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>受 Azure TLS 证书更改影响 Azure AD 本地混合代理

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 平台

Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 此更新是因为当前 CA 证书不符合某个 CA/浏览器论坛基线要求。 此更改将影响本地安装的混合代理 Azure AD，这些代理具有使用固定的根证书列表的已强化环境，并将需要更新为信任新的证书颁发者。

如果不立即采取措施，此更改将导致服务中断。 这些代理包括用于远程访问本地、[直通身份验证](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理的[应用程序代理连接器](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy)，这些代理允许用户使用相同的密码登录到应用程序，以及用于执行 AD Azure AD 同步的[云预配预览](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理。 

如果你的环境中的防火墙规则设置为仅允许对特定证书吊销列表的出站调用 (CRL) 下载，则需要允许使用以下 CRL 和 OCSP Url。 有关更改的完整详细信息以及用于启用对的 CRL 和 OCSP Url 的详细信息，请参阅  [AZURE TLS 证书更改](../../security/fundamentals/tls-certificate-changes.md)。

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>将从审核日志中删除预配事件，并仅将其发布到预配日志

**类型：** 更改计划  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
SCIM [预配服务](../app-provisioning/user-provisioning.md) 的活动记录在审核日志和设置日志中。 这包括活动，例如在 ServiceNow 中创建用户、在 GSuite 中进行分组或从 AWS 导入角色。 将来，这些事件只会发布到预配日志中。 实现此更改是为了避免跨日志重复发生的事件，以及客户在 log analytics 中使用日志产生的额外成本。 

完成日期后，我们将提供更新。 此弃用未计划用于2020日历年。 

> [!NOTE]
> 这不会影响预配服务发出的同步事件之外的审核日志中的任何事件。 事件（例如，应用程序的创建、条件性访问策略、目录中的用户等）将继续在审核日志中发出。 [了解详细信息](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)。
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD 由 Azure 传输层安全性影响的本地混合代理 (TLS) 证书更改

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 平台
 
Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 由于当前 CA 证书未遵循 CA/浏览器论坛基线要求之一，将会有更新。 此更改将影响本地安装的混合代理 Azure AD，这些代理具有使用固定根证书列表的已强化环境。 需要更新这些代理以信任新的证书颁发者。

如果不立即采取措施，此更改将导致服务中断。 这些代理包括： 
- 用于远程访问本地的[应用程序代理连接器](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- 允许用户使用同一密码登录到应用程序的[直通身份验证](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理
- 将 AD Azure AD 同步的[云预配预览](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)代理。 

如果你的环境中的防火墙规则设置为仅允许对特定证书吊销列表的出站调用 (CRL) 下载，则需要允许 CRL 和 OCSP Url。 有关更改的完整详细信息以及用于启用对的 CRL 和 OCSP Url 的详细信息，请参阅  [AZURE TLS 证书更改](../../security/fundamentals/tls-certificate-changes.md)。
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>US Gov 云中 Azure Active Directory TLS 1.0、TLS 1.1 和3DES 弃用

**类型：** 更改计划  
**服务类别：** 所有 Azure AD 应用程序  
**产品功能：** 标准
 
Azure Active Directory 将在2021年3月31日前弃用以下协议：
- TLS 1.0
- TLS 1.1
- TLS_RSA_WITH_3DES_EDE_CBC_SHA 的3DES 密码套件 () 

所有客户端服务器和浏览器-服务器组合应使用 TLS 1.2 和新式密码套件来维护 Azure、Office 365 和 Microsoft 365 服务的 Azure Active Directory 安全连接。

受影响的环境包括：
- Azure US Gov
- [Office 365 GCC 高 & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>将应用程序分配到 AU 上的角色和对象范围

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
利用此功能，可以将应用程序 (SPN) 分配给管理单元范围的管理员角色。 若要了解详细信息，请参阅 [将作用域内角色分配给管理单元](../roles/admin-units-assign-roles.md)。

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>现在，当来宾用户被拒绝访问资源时，可以禁用和删除它们

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识调控
 
"禁用" 和 "删除" 是 Azure AD 访问评审的一项高级控制，可帮助组织更好地管理组和应用中的外部来宾。 如果在访问评审中拒绝来宾，则 " **禁用" 和 "删除** " 会在30天内自动阻止登录。 30天后，将从租户中全部删除。

有关此功能的详细信息，请参阅 [使用 Azure AD 访问评审 (预览) 禁用和删除外部标识 ](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview)。
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>访问评审创建者可以通过电子邮件将自定义消息添加到审阅者

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识调控
 
在 Azure AD 访问评审中，创建评审的管理员现在可以将自定义消息写入评审者。 审阅者将在收到的电子邮件中看到该消息，提示他们完成审阅。 若要了解有关使用此功能的详细信息，请参阅 [创建一个或多个访问评审](../governance/create-access-review.md#create-one-or-more-access-reviews) 部分的步骤14。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年10月

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
 
集成助手 (预览版) 体验现可用于 Azure AD B2C 应用注册。 此体验可帮助指导你配置应用程序的常见方案。 详细了解 [Microsoft 标识平台的最佳实践和建议](../develop/identity-platform-integration-checklist.md)。
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>在 Azure 门户 UI 中查看角色模板 ID

**类型：** 新功能  
**服务类别：** Azure 角色  
**产品功能：** 访问控制
 

你现在可以在 Azure 门户中查看每个 Azure AD 角色的模板 ID。 在 Azure AD 中，选择所选角色的 "  **说明** "。 

建议客户在其 PowerShell 脚本和代码中使用角色模板 Id，而不是显示名称。 角色模板 ID 支持用于 [directoryRoles](/graph/api/resources/directoryrole) 和 [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) 对象。 有关角色模板 Id 的详细信息，请参阅 [角色模板 id](../roles/permissions-reference.md#role-template-ids)。

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C 注册用户流的 API 连接器现在处于公共预览状态

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 

API 连接器现在可用于 Azure Active Directory B2C。 利用 API 连接器，你可以使用 web Api 自定义注册用户流和与外部云系统集成。 可以使用 API 连接器执行以下操作：

- 与自定义批准工作流集成
- 验证用户输入数据
- 覆盖用户属性 
- 运行自定义业务逻辑 

 请访问 [使用 API 连接器以自定义和扩展注册](../../active-directory-b2c/api-connectors-overview.md) 文档以了解详细信息。

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>授权管理中已连接组织的状态属性

**类型：** 新功能  
**服务类别：** 目录管理 **产品功能：** 权利管理
 

 所有连接的组织现在都有一个名为 "状态" 的附加属性。 状态将控制在引用 "所有已连接的组织" 的策略中如何使用连接的组织。 该值将为 "已配置" (意味着组织在使用 "all" 子句的策略作用域中) 或 "建议的" (意味着组织不在范围内) 。  

手动创建的已连接组织的默认设置为 "已配置"。 同时，自动创建的 (通过允许 internet 上的任何用户请求访问权限的策略创建的) 默认为 "已建议"。  9 2020 年9月之前创建的任何已连接组织都将设置为 "已配置"。 管理员可以根据需要更新此属性。 [了解详细信息](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)。
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory 外部标识现在具有 B2C 高级安全设置

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
标识保护的基于风险的条件访问和风险检测功能现在可在 [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md)中使用。 利用这些高级安全功能，客户现在可以：
- 利用智能见解来评估 B2C 应用和最终用户帐户的风险。 检测包括典型旅行、匿名 IP 地址、恶意软件链接的 IP 地址和 Azure AD 威胁情报。 还提供了基于门户和 API 的报表。
- 通过为 B2C 用户配置自适应身份验证策略来自动解决风险。 应用开发人员和管理员可以通过要求进行多重身份验证 (MFA) 或阻止访问（具体取决于所检测到的用户风险级别）来降低实时风险，并基于位置、组和应用提供附加控件。
- 与 Azure AD B2C 用户流和自定义策略集成。 可以从 Azure AD B2C 中的内置用户流触发条件，也可以将其合并到 B2C 自定义策略中。 与 B2C 用户流的其他方面一样，最终用户体验消息也可以自定义。 自定义取决于组织的语音、品牌和缓解方案。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD 应用程序库中提供了新的联合应用-2020 年10月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
10月2020，我们已在应用程序库中添加了以下27个新应用程序，并提供联合身份验证支持：

[卫士](../saas-apps/sentry-tutorial.md)、 [Bumblebee-Superapp](https://app.yellowmessenger.com/user/login)， [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md)， [EAComposer](../saas-apps/eacomposer-tutorial.md)， [Genesys cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/)，[区域技术门户](https://portail.zonetechnologie.com/signin)， [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md)， [Datawiza ACCESS Broker](https://console.datawiza.com/)， [ZOKRI](https://app.zokri.com/)， [CheckProof](../saas-apps/checkproof-tutorial.md)， [Ecochallenge.org](https://events.ecochallenge.org/users/login)，atSpoke [](http://atspoke.com/login)，[约会提醒](https://app.appointmentreminder.co.nz/account/login) [，](https://cloud.market/)SSO， [Web TravelPerk](../saas-apps/web-cargo-air-tutorial.md)， [Greetly，](../saas-apps/travelperk-tutorial.md) [](../saas-apps/loop-flow-crm-tutorial.md) [OrgVitality](https://app.greetly.com/)，[货物](../saas-apps/orgvitality-sso-tutorial.md)， [Starmind](../saas-apps/starmind-tutorial.md) [，](../saas-apps/mevisio-tutorial.md)[零售 Workstem](../saas-apps/retail-zipline-tutorial.md) [，Zipline，](../saas-apps/hoxhunt-tutorial.md) [Hoxhunt，MEVISIO](https://hrm.workstem.com/login) [，](../saas-apps/samsara-tutorial.md) [Samsara](../saas-apps/nimbus-tutorial.md)，[脉冲安全虚拟流量管理器](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>现在可以将预配日志流式传输到 log analytics

**类型：** 新功能  
**服务类别：** 报表  
**产品功能：** 监视和报告
 

将预配日志发布到 log analytics，以便：
- 存储预配日志超过30天
- 定义自定义警报和通知
- 生成仪表板以可视化日志
- 执行复杂的查询以分析日志 

若要了解如何使用此功能，请参阅 [了解预配如何与 Azure Monitor 日志集成](../app-provisioning/application-provisioning-log-analytics.md)。
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>预配日志现在可以由应用程序所有者查看

**类型：** 已更改的功能  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
你现在可以允许应用程序所有者通过预配服务监视活动，并排除问题，而无需为其提供特权角色或使其成为瓶颈。 [了解详细信息](../reports-monitoring/concept-provisioning-logs.md)。
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>重命名10个 Azure Active Directory 角色

**类型：** 已更改的功能  
**服务类别：** Azure 角色  
**产品功能：** 访问控制
 
某些 Azure Active Directory (AD) 内置角色的名称不同于 Microsoft 365 管理中心、Azure AD 门户和 Microsoft Graph 中显示的名称。 这种不一致会导致自动过程中出现问题。 在此更新中，我们将重命名10个角色名称，使其保持一致。 下表具有新的角色名称：

![新角色名称表](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C 对使用 MSAL JS 2.x 的 Spa 的身份验证代码流的支持

**类型：** 已更改的功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
MSAL.js 版本2.x 现在包含对 (Spa) 的单页面 web 应用的授权代码流的支持。 Azure AD B2C 现在支持在 Azure 门户上使用 SPA 应用类型，并将 MSAL.js 授权代码流与 PKCE 用于单页应用。 这将允许使用 Azure AD B2C 的 Spa 使用较新的浏览器来维护 SSO，并遵守新的身份验证协议建议。 Azure Active Directory B2C 教程中的 " [向注册单页应用程序 (SPA) ](../../active-directory-b2c/tutorial-register-spa.md) 入门"。

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>更新以在受信任的设备设置上 (MFA) 进行多重身份验证

**类型：** 已更改的功能  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 

最近，我们已更新 " [记住多重身份验证" (MFA) ](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) 受信任的设备功能，将身份验证扩展到最多365天。 Azure Active Directory (Azure AD) 高级许可证，还可以使用 " [条件性访问–登录频率" 策略](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) ，该策略可为重新身份验证设置提供更大的灵活性。

为了获得最佳的用户体验，我们建议使用条件性访问登录频率来扩展受信任设备、位置或低风险会话上的会话生存期，作为 "记住对受信任设备的 MFA" 设置的替代方法。 若要开始，请查看 [有关优化重新验证体验的最新指南](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

---

## <a name="september-2020"></a>2020 年 9 月

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年9月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot 安全意识](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>云预配公共预览版刷新

**类型：** 新功能  
**服务类别：** Azure AD 云预配 **产品功能：** 标识生命周期管理
 
Azure AD Connect 云预配公共预览版刷新功能通过客户反馈开发了两个主要的增强功能： 

- Azure 门户的属性映射体验

    利用此功能，IT 管理员可以使用目前存在的各种映射类型将用户、组或联系人属性从 AD 映射到 Azure AD。 属性映射是一种功能，用于标准化从 Active Directory 流到 Azure Active Directory 的属性的值。 可以确定是直接将属性值映射到 Azure AD，还是在预配用户时使用表达式来转换属性值。 [了解详细信息](../cloud-provisioning/how-to-attribute-mapping.md)

- 按需预配或测试用户体验

    设置配置后，你可能需要进行测试，以查看用户转换是否按预期方式工作，然后再将其应用到作用域中的所有用户。 利用按需预配，IT 管理员可以输入 AD 用户 (DN) 的可分辨名称，并查看其是否按预期同步。 按需设置提供了一种很好的方法来确保以前执行的属性映射。 [了解详细信息](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD-公共预览版中审核的 BitLocker 恢复

**类型：** 新功能  
**服务类别：** 设备访问管理  
**产品功能：** 设备生命周期管理
 
如果 IT 管理员或最终用户读取 (的 BitLocker 恢复密钥) 有权访问该密钥，Azure Active Directory 现在会生成审核日志来捕获访问恢复密钥的人员。 相同的审核提供与 BitLocker 密钥关联的设备的详细信息。

最终用户可以 [通过我的帐户访问其恢复密钥](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key)。 IT 管理员可在 beta 版或通过 Azure AD 门户通过 [BitLocker 恢复密钥 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) 访问恢复密钥。 若要了解详细信息，请参阅 [在 Azure AD 门户中查看或复制 BitLocker 密钥](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)。

---

### <a name="teams-devices-administrator-built-in-role"></a>团队设备管理员内置角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
具有 [团队设备管理员](../roles/permissions-reference.md#teams-devices-administrator) 角色的用户可以从团队管理中心管理已 [认证的团队设备](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) 。 

此角色允许用户在一眼内查看所有设备，并能够搜索和筛选设备。 用户还可以查看每个设备的详细信息，包括登录帐户以及设备的品牌和型号。 用户可以更改设备上的设置并更新软件版本。 此角色不会授予检查团队活动和调用设备质量的权限。
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>目录对象的高级查询功能

**类型：** 新功能  
**服务类别：** MS Graph  
**产品功能：** 开发人员体验
 
在 Azure AD Api 中为目录对象引入的所有新查询功能现在都可在 v1.0 终结点和生产就绪状态中使用。 开发人员可以使用标准 OData 运算符对目录对象和相关链接进行计数、搜索、筛选和排序。

若要了解详细信息，请参阅 [此处](https://aka.ms/BlogPostMezzoGA)的文档，还可以通过此 [简短调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)发送反馈。
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>公共预览版：配置条件访问策略的租户的持续访问评估

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 标识安全和保护
 
持续访问评估 (CAE) 现已在公共预览版中提供，适用于具有条件访问策略的 Azure AD 租户。 对于 CAE，将实时评估关键安全事件和策略。 这包括帐户禁用、密码重置和位置更改。 若要了解详细信息，请参阅[连续访问评估](../conditional-access/concept-continuous-access-evaluation.md)。

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>公共预览版：向用户请求访问包，以改进批准决策

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
现在，管理员可以要求请求访问包的用户回答 Azure AD 授权管理的访问门户中的业务理由以外的其他问题。 然后向审批者显示用户答案，以帮助他们做出更准确的访问批准决策。 若要了解详细信息，请参阅 [收集其他请求程序信息以供审批 (预览) ](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)。
 
---

### <a name="public-preview-enhanced-user-management"></a>公共预览版：增强的用户管理

**类型：** 新功能  
**服务类别：** 用户管理  
**产品功能：** 用户管理
 

已更新 Azure AD 门户，使用户能够更轻松地在 "所有用户" 和 "已删除用户" 页中查找用户。 预览中的更改包括： 
- 更直观的用户属性，包括对象 ID、目录同步状态、创建类型和标识颁发者。
- 搜索现在允许对名称、电子邮件和对象 Id 进行合并搜索。
- 按用户类型增强筛选 (member、guest 和 none) 、目录同步状态、创建类型、公司名称和域名。
- 新的排序功能，如名称、用户主体名称和删除日期。
- 新用户总数使用任何搜索或筛选器进行更新。

有关详细信息，请参阅 [Azure Active Directory 中的用户管理增强功能 (预览) ](../enterprise-users/users-search-enhanced.md)。

---

### <a name="new-notes-field-for-enterprise-applications"></a>适用于企业应用程序的新 "说明" 字段

**类型：** 新功能  
**服务类别：** 企业应用 **产品功能：** SSO

您可以向企业应用程序添加免费文本注释。 可以添加任何有助于企业应用程序下的管理器应用程序的相关信息。 有关详细信息，请参阅 [快速入门：在 Azure Active Directory 中配置应用程序的属性 (Azure AD) 租户](../manage-apps/add-application-portal-configure.md)。 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD 应用程序库中提供了新的联合应用-2020 年9月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020年9月，我们已在应用程序库中添加了以下34新应用程序，并提供联合身份验证支持：

[VMware 范围-统一访问网关]()、[脉冲安全 pc](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md)、 [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md)、 [Frontitude](https://services.enteksystems.de/sso/microsoft/signup)、 [BookWidgets](https://www.bookwidgets.com/sso/office365)、 [ZVD_SERVER](https://zaas.zenmutech.com/user/signin)、 [HashData for Business](https://hashdata.app/login.xhtml)、 [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login)、 [CyberSolutions MAILBASEΣ/cms](../saas-apps/cybersolutions-mailbase-tutorial.md)、 [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md)、 [LimbleCMMS](https://auth.limblecmms.com/)， [Glint inc.](../saas-apps/glint-inc-tutorial.md)， [zeroheight](../saas-apps/zeroheight-tutorial.md)，[性别适用性](https://app.genderfitness.com/)， [Coeo 门户](https://my.coeo.com/)， [Grammarly](../saas-apps/grammarly-tutorial.md)， [Fivetran](../saas-apps/fivetran-tutorial.md)， [Kumolus](../saas-apps/kumolus-tutorial.md)， [RSA Archer](../saas-apps/rsa-archer-suite-tutorial.md) [Suite](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal) [](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [，](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)TeamzSkill， [raumfürraum](../saas-apps/teamzskill-tutorial.md) [，Saviynt](../saas-apps/raumfurraum-tutorial.md) [，BizMerlinHR](../saas-apps/saviynt-tutorial.md) [，](https://app.cloudcadi.com/login) [Mobile 保险箱](https://marketplace.bizmerlin.net/bmone/signup) [，Zengine](../saas-apps/mobile-locker-tutorial.md) [，CloudCADI，Simfoni](../saas-apps/zengine-tutorial.md)分析， [Priva](https://simfonianalytics.com/accounts/microsoft/login/)Identity & [Access Management，Nitro](https://my.priva.com/)Pro [，Eventfinity](https://www.gonitro.com/nps/product-details/downloads)， [Fexa](../saas-apps/eventfinity-tutorial.md) [，Wistec](../saas-apps/fexa-tutorial.md)Enterprise Portal Enterprise Portal [](https://wisteconline.com/auth/oidc)

你还可以从此处查找所有应用程序的文档： https://aka.ms/AppsTutorial 。

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息： https://aka.ms/AzureADAppRequest 。

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD 权限管理中的新委派角色：访问包分配管理器

**类型：** 新功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 
已在 Azure AD 的权利管理中添加了一个新的访问包分配管理器角色，以提供管理分配的具体权限。 你现在可以将任务委托给此角色中的用户，这些用户可以将访问包的分配管理委托给企业所有者。 但是，访问包分配管理器无法更改管理员设置的访问包策略或其他属性。 

使用这项新角色，你可以受益于委托管理分配所需的最少特权，并对所有其他访问包配置保持管理控制。 若要了解详细信息，请参阅 [权利管理角色](../governance/entitlement-management-delegate.md#entitlement-management-roles)。
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management 的载入流的更改

**类型：** 已更改的功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
以前，加入到 Privileged Identity Management (PIM) 必需的用户同意，并在 PIM 的边栏选项卡中加入在 Azure AD MFA 中注册的加入流程。 由于最近将 PIM 体验集成到 Azure AD 角色和管理员 "边栏选项卡中，我们将消除这种体验。 具有有效 P2 许可证的任何租户都将自动载入到 PIM。

加入 PIM 对你的租户没有任何直接负面影响。 可能会发生以下更改：
- 当你在 PIM 或 Azure AD 角色和管理员边栏选项卡中进行分配时，其他分配选项（如活动与符合开始和结束时间）。 
- 其他范围机制，如管理单元和自定义角色，直接引入分配体验。 
- 如果你是 "全局管理员" 或 "特权角色管理员"，则可以开始获取一些其他电子邮件，如 PIM 每周摘要。 
- 你可能还会在审核日志中看到与角色分配相关的 ms pim 服务主体。 预期的更改不应影响你的常规工作流。

 有关详细信息，请参阅 [开始使用 Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)。

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD 的权利管理： access 包资源的 "选择" 窗格现在默认显示当前在所选目录中的资源

**类型：** 已更改的功能  
**服务类别：** 用户访问管理  
**产品功能：** 权利管理
 

在访问包创建流中的 "资源角色" 选项卡下，"选择" 窗格行为将更改。 当前，默认行为是显示用户拥有的所有资源，以及添加到所选目录的资源。 

默认情况下，此体验将更改为仅显示当前在目录中添加的资源，以便用户可以从目录中轻松选择资源。 此更新将帮助发现要添加到 access 包的资源，并减少无意添加不属于目录的用户所拥有的资源的风险。 若要了解详细信息，请参阅 [Azure AD 授权管理中创建新的访问包](../governance/entitlement-management-access-package-create.md#resource-roles)。
 
---

## <a name="august-2020"></a>2020 年 8 月 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure 多重身份验证服务器防火墙要求的更新

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
从2020年10月开始，Azure MFA 服务器防火墙要求需要额外的 IP 范围。

如果组织中有出站防火墙规则，请更新规则，以便 MFA 服务器可以与所有必需的 IP 范围通信。 Azure 中记录了 IP 范围 [多重身份验证服务器防火墙要求](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)。

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>对标识安全分数的用户体验的即将发生的更改

**类型：** 更改计划  
**服务类别：** 标识保护 **产品功能：** 标识安全 & 保护

我们正在更新标识安全分数门户，使之与 Microsoft Secure 评分的 [新版本](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)中引入的更改一致。 

带有更改的预览版本将在9月开始时可用。 预览版本中的更改包括：
- "标识安全分数" 已重命名为 "标识的安全评分"，以实现与 Microsoft 安全分数的品牌一致
- 规范化为标准缩放的点，并按百分比（而不是点）报告

在此预览版中，客户可以在现有体验和新体验之间切换。 此预览版将在11月2020最后结束。 预览后，客户将自动定向到新的 UX 体验。

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD-公共预览版中的新受限来宾访问权限

**类型：** 新功能  
**服务类别：** 访问控制   
**产品功能：** 用户管理

已更新来宾用户的目录级别权限。 这些权限允许管理员要求对外部来宾用户访问的其他限制和控制。 管理员现在可以为外部来宾对用户和组的访问权限和成员身份信息添加其他限制。 使用此公共预览功能，客户可以通过模糊处理组成员身份，以大规模方式管理外部用户访问权限，包括限制来宾用户查看组成员身份的成员身份)  (。

若要了解详细信息，请参阅 [受限来宾访问权限](../enterprise-users/users-restrict-guest-permissions.md) 和 [用户默认权限](./users-default-permissions.md)。
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>服务主体的增量查询的公开上市

**类型：** 新功能  
**服务类别：** MS Graph  
**产品功能：** 开发人员体验
 
Microsoft Graph 增量查询现在支持1.0 版中的资源类型：
- Service Principal

现在，客户端可以有效地跟踪对这些资源所做的更改，并提供最佳解决方案，以便使用本地数据存储来同步对这些资源所做的更改。 若要了解如何在查询中配置这些资源，请参阅 [使用增量查询跟踪 Microsoft Graph 数据中的更改](/graph/delta-query-overview)。
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>OAuth2PermissionGrant 的增量查询的公开上市

**类型：** 新功能  
**服务类别：** MS Graph  
**产品功能：** 开发人员体验

Microsoft Graph 增量查询现在支持1.0 版中的资源类型：
- OAuth2PermissionGrant

客户端现在可以有效地跟踪对这些资源所做的更改，并提供最佳解决方案，以便使用本地数据存储来同步对这些资源所做的更改。 若要了解如何在查询中配置这些资源，请参阅 [使用增量查询跟踪 Microsoft Graph 数据中的更改](/graph/delta-query-overview)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD 应用程序库中提供了新的联合应用程序-2020 年8月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020年8月，我们已在应用程序库中添加了以下25个新应用程序，并提供联合支持：

[Backup365](https://portal.backup365.io/login)， [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2)， [Alma SIS](https://almau.getalma.com/)， [Enlyft Dynamics 365 连接器](http://enlyft.com/)， [Serraview Space 利用率软件解决方案](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md)， [Uniq](https://web.uniq.app/)，[直观](../saas-apps/visibly-tutorial.md)， [Zylo](../saas-apps/zylo-tutorial.md)， [Edmentum-课件评估准确路径](https://auth.edmentum.com/elf/login)， [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome)， [](../saas-apps/fortigate-ssl-vpn-tutorial.md)Altamira [HRM](../saas-apps/altamira-hrm-tutorial.md)， [WireWheel，Zix](../saas-apps/wirewheel-tutorial.md)[相容性和捕获](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)，Greenlight[企业业务控制平台](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [，genetec clearance](https://app.vivun.com/dashboard/calendar/connect)[净空](https://www.clearance.network/) [，](https://nestiolistings.com/sso/oidc/azure/authorize/)iSAMS [，VeraSMART，Amiko](../saas-apps/verasmart-tutorial.md) [，Twingate](https://amiko.web.rivero.app/) [，Scalefusion，Bpanda](https://auth.twingate.com/signup)， [Vivun，FortiGate](https://scalefusion.com/users/sign_in/) [，Wandera](https://goto.bpanda.com/login)[最终用户](https://www.wandera.com/) [](../saas-apps/isams-tutorial.md)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>资源林现在可用于 Azure AD DS 

**键入：** 新功能 **服务类别：** Azure AD 域服务   
**产品功能：** Azure AD 域服务
 
Azure AD 域服务中的资源林功能现已正式发布。 你现在可以在没有密码哈希同步的情况下使用 Azure AD 域服务（包括智能卡授权）启用授权。 若要了解详细信息，请参阅 [副本集 Azure Active Directory 域服务 (预览) 的概念和功能 ](../../active-directory-domain-services/concepts-replica-sets.md)。
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>现在提供了 Azure AD DS 托管域的区域副本支持

**类型：** 新功能   
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务
 
可以扩展托管域，使每个 Azure AD 租户具有多个副本集。 可以将副本集添加到任何支持 Azure AD 域服务的 Azure 区域中的任何对等互连虚拟网络。 如果某个 Azure 区域处于离线状态，则不同 Azure 区域中的其他副本集可为旧版应用程序提供地理灾难恢复。 若要了解详细信息，请参阅 [副本集 Azure Active Directory 域服务 (预览) 的概念和功能 ](../../active-directory-domain-services/concepts-replica-sets.md)。

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD 我的 Sign-Ins 的公开上市

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 最终用户体验
 
Azure AD 我的 Sign-Ins 是一项新功能，允许企业用户检查其登录历史记录以检查是否有任何异常活动。 此外，此功能允许最终用户在可疑活动上报告 "这不是我" 或 "这是我"。 若要了解有关使用此功能的详细信息，请参阅在 ["我的 Sign-Ins" 页中查看和搜索最近的登录活动](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)。
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR 驱动的用户预配到 Azure AD 现已正式发布

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
你现在可以将 SAP SuccessFactors 作为权威标识源与 Azure AD 集成，并使用 HR 事件（例如新员工和终止）自动完成端到端标识生命周期，以在 Azure AD 中推动帐户的预配和取消预配。 

若要详细了解如何将 SAP SuccessFactors 入站设置配置为 Azure AD，请参阅教程 [配置 Sap SuccessFactors 以 Active Directory 用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)。
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>自定义打开 ID 连接 MS 图形 API 支持 Azure AD B2C

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
以前，只能通过 Azure 门户添加或管理自定义打开 ID 连接提供程序。 现在，Azure AD B2C 的客户也可以通过 Microsoft Graph Api beta 版本来添加和管理它们。 若要了解如何通过 Api 配置此资源，请参阅 [identityProvider 资源类型](/graph/api/resources/identityprovider?view=graph-rest-beta)。
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>将 Azure AD 内置角色分配给云组

**类型：** 新功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制

你现在可以将 Azure AD 内置角色分配给具有这一新功能的云组。 例如，可以将 SharePoint 管理员角色分配到 Contoso_SharePoint_Admins 组。 你还可以使用 PIM 使组成为角色的合格成员，而不是授予持续访问权限。 若要了解如何配置此功能，请参阅 [使用云组管理 Azure Active Directory (preview) 中的角色分配 ](../roles/groups-concept.md)。
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>现已推出 Insights Business 领导者内置角色

**类型：** 新功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
Insights Business 主管角色中的用户可以通过 [M365 Insights 应用程序](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)访问一组仪表板和见解。 其中包括对所有仪表板以及提供的见解和数据探索功能的完全访问权限。 但是，此角色中的用户无法访问产品配置设置，这是 Insights 管理员角色的责任。 若要了解有关此角色的详细信息，请参阅 [中的管理员角色权限 Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>现已推出 Insights 管理员内置角色

**类型：** 新功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
Insights 管理员角色中的用户可以访问 [M365 Insights 应用程序](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)中的完整管理功能集。 此角色中的用户可以读取目录信息、监视服务运行状况、文件支持票证，以及访问 Insights 管理员设置方面的内容。 若要了解有关此角色的详细信息，请参阅 [中的管理员角色权限 Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>应用程序管理员和云应用程序管理员可以管理应用程序的扩展属性

**类型：** 已更改的功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
以前，只有全局管理员才能管理 [扩展属性](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)。 现在，我们也为应用程序管理员和云应用程序管理员启用了此功能。
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 修补程序4.6.263.0 和连接器1.1.1301。0

**类型：** 已更改的功能  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理

[ (生成 4.6.263.0) 的修补程序汇总包](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident)可用于 MICROSOFT IDENTITY MANAGER (MIM) 2016 Service Pack 2 (SP2) 。 此汇总包包含 MIM CM、MIM 同步管理器和 PAM 组件的更新。 此外，MIM 通用连接器 build 1.1.1301.0 包含 Graph 连接器的更新。

---
 


