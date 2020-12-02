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
ms.date: 11/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b836c28443790466084b1840edcf08dc09dcf4cc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518271"
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

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD 应用程序库中提供了新的联合应用-2020 年11月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2020年11月，我们已在应用程序库中添加了以下52新应用程序，并提供联合身份验证支持：

[旅游 & 支出管理](https://app.expenseonce.com/Account/Login)、 [Tribeloo](../saas-apps/tribeloo-tutorial.md)、 [Itslearning 文件选取器](https://pmteam.itslearning.com/)、[危机控制](../saas-apps/crises-control-tutorial.md) [CourtAlert](https://www.courtalert.com/)， [StealthMail](https://stealthmail.com/)， [Edmentum-研究岛](https://app.studyisland.com/cfw/login/)，[虚拟风险管理器](../saas-apps/virtual-risk-manager-tutorial.md)， [TIMU](../saas-apps/timu-tutorial.md)， [Looker 分析平台](../saas-apps/looker-analytics-platform-tutorial.md)， [Talview](https://recruit.talview.com/login)，实时翻译器，Klaxoon， [Klaxoon](https://access.klaxoon.com/login) [Podbean](../saas-apps/podbean-tutorial.md)， [zcal](https://zcal.co/signup)， [expensemanager](https://api.expense-manager.com/) [，Netsparker，trak](../saas-apps/netsparker-enterprise-tutorial.md) [，Appian](../saas-apps/panorays-tutorial.md) [，](https://portal.builterra.com/) [EVA 签入](https://my.evacheckin.com/organization) [，Panorays](https://portal.en-trak.app/)Builterra [，HowNow](../saas-apps/appian-tutorial.md) [，WebApp，](../saas-apps/coupa-risk-assess-tutorial.md)Coupa，Lucid imagination [ (所有产品) ](../saas-apps/lucid-tutorial.md)， [GoBright](https://portal.brightbooking.eu/)， [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md)，[Resource Central](../saas-apps/resource-central-tutorial.md)， [UiPathStudioO365App](https://www.uipath.com/product/platform)， [Jedox](../saas-apps/jedox-tutorial.md)， [Cequence 应用程序安全性](../saas-apps/cequence-application-security-tutorial.md)， [PerimeterX](../saas-apps/perimeterx-tutorial.md)， [TrendMiner](../saas-apps/trendminer-tutorial.md)， [Lexion](../saas-apps/lexion-tutorial.md)， [WorkWare](../saas-apps/workware-tutorial.md)， [ProdPad](../saas-apps/prodpad-tutorial.md)， [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md)， [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md)， [Luum](../saas-apps/luum-tutorial.md)，[运费度量值](https://www.gpcsl.com/freight.html)，Terraform[云](../saas-apps/terraform-cloud-tutorial.md)，[性质研究](../saas-apps/nature-research-tutorial.md)，[播放数字告示](https://login.playsignage.com/login) [，RemotePC，Prolorus](../saas-apps/remotepc-tutorial.md) [，Hirebridge](../saas-apps/prolorus-tutorial.md) [ATS](../saas-apps/hirebridge-ats-tutorial.md)， [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure)， [Roadmunk](../saas-apps/roadmunk-tutorial.md)，[日出 Software 关系 CRM](https://cloud.relations-crm.com/) [，Procaire，](../saas-apps/procaire-tutorial.md)[导师®](https://www.edriving.com/) [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md) [Gradle Enterprise](https://gradle.com/)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>公共预览版-适用于企业应用的自定义角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
 [用于委派的企业应用程序管理的自定义 RBAC 角色现提供](../users-groups-roles/roles-custom-available-permissions.md) 公共预览版。 这些新权限建立在应用注册管理的自定义角色之上，这允许对管理员拥有的访问权限进行精细控制。 随着时间的推移，将发布对 Azure AD 的委派管理的其他权限。

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

### <a name="azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Azure AD 应用程序代理本身支持对使用标头进行身份验证的应用程序进行单一登录访问

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

有关详细信息，请参阅 [通过 Azure AD 自动完成用户预配到 SaaS 应用程序](../manage-apps/user-provisioning.md)。
 
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

有关文档更新的详细说明，请参阅 [BitLocker 恢复 API](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) 。

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
- [Office 365 GCC 高 & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
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
 
在 Azure AD 访问评审中，创建评审的管理员现在可以将自定义消息写入评审者。 审阅者将在收到的电子邮件中看到该消息，提示他们完成审阅。 若要了解有关使用此功能的详细信息，请参阅 " [高级设置](../governance/create-access-review.md#advanced-settings) " 部分的步骤6。

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

建议客户在其 PowerShell 脚本和代码中使用角色模板 Id，而不是显示名称。 角色模板 ID 支持用于 [directoryRoles](/graph/api/resources/directoryrole?view=graph-rest-1.0) 和 [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) 对象。 有关角色模板 Id 的详细信息，请参阅 [角色模板 id](../roles/permissions-reference.md#role-template-ids)。

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

[卫士](../saas-apps/sentry-tutorial.md)， [Bumblebee Superapp](https://app.yellowmessenger.com/user/login)， [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md)， [EAComposer](../saas-apps/eacomposer-tutorial.md)， [Genesys cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/)，[区域技术门户](https://portail.zonetechnologie.com/signin)， [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md)， [Datawiza Access Broker](https://console.datawiza.com/)， [ZOKRI](https://app.zokri.com/)， [CheckProof](../saas-apps/checkproof-tutorial.md)， [Ecochallenge.org](https://events.ecochallenge.org/users/login)，atSpoke， [atSpoke](http://atspoke.com/login)[约会提醒](https://app.appointmentreminder.co.nz/account/login)，[云和](https://cloud.market/)marketplace，TravelPerk， [Greetly](https://app.greetly.com/)，[OrgVitality SSO} (。 [TravelPerk](../saas-apps/travelperk-tutorial.md)/saas-apps/orgvitality-sso-tutorial.md) ， [Web 货物 Air](../saas-apps/web-cargo-air-tutorial.md)，[循环流 CRM](../saas-apps/loop-flow-crm-tutorial.md)， [Starmind](../saas-apps/starmind-tutorial.md)， [Workstem](https://hrm.workstem.com/login)，[零售 Zipline](../saas-apps/retail-zipline-tutorial.md)， [Hoxhunt](../saas-apps/hoxhunt-tutorial.md)， [MEVISIO](../saas-apps/mevisio-tutorial.md)， [Samsara](../saas-apps/samsara-tutorial.md)， [Nimbus](../saas-apps/nimbus-tutorial.md)，[脉冲安全虚拟流量管理器](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

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
 
持续访问评估 (CAE) 现已在公共预览版中提供，适用于具有条件访问策略的 Azure AD 租户。 对于 CAE，将实时评估关键安全事件和策略。 这包括帐户禁用、密码重置和位置更改。 若要了解详细信息，请参阅 [持续访问评估](../conditional-access/concept-continuous-access-evaluation.md)。

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

[VMware 范围-统一访问网关]()、[脉冲安全 pc](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md)、 [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md)、 [Frontitude](https://services.enteksystems.de/sso/microsoft/signup)、 [BookWidgets](https://www.bookwidgets.com/sso/office365)、 [ZVD_SERVER](https://zaas.zenmutech.com/user/signin)、 [HashData for Business](https://hashdata.app/login.xhtml)、 [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login)、 [CyberSolutions MAILBASEΣ/cms](../saas-apps/cybersolutions-mailbase-tutorial.md)、 [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md)、 [LimbleCMMS](https://auth.limblecmms.com/)， [Glint inc.](../saas-apps/glint-inc-tutorial.md)， [zeroheight](../saas-apps/zeroheight-tutorial.md)，[性别适用性](https://app.genderfitness.com/)， [Coeo 门户](https://my.coeo.com/)， [Grammarly](../saas-apps/grammarly-tutorial.md)， [Fivetran](../saas-apps/fivetran-tutorial.md)， [Kumolus](../saas-apps/kumolus-tutorial.md)， [RSA Archer](../saas-apps/rsa-archer-suite-tutorial.md) [Suite](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal) [Oracle PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [，](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)TeamzSkill， [raumfürraum](../saas-apps/teamzskill-tutorial.md) [，Saviynt](../saas-apps/raumfurraum-tutorial.md) [，BizMerlinHR](../saas-apps/saviynt-tutorial.md) [，](https://app.cloudcadi.com/login) [Mobile 保险箱](https://marketplace.bizmerlin.net/bmone/signup) [，Zengine](../saas-apps/mobile-locker-tutorial.md) [，CloudCADI，Simfoni](../saas-apps/zengine-tutorial.md)分析， [Priva](https://simfonianalytics.com/accounts/microsoft/login/)Identity & [Access Management，Nitro](https://my.priva.com/)Pro [，Eventfinity](https://www.gonitro.com/nps/product-details/downloads)， [Fexa](../saas-apps/eventfinity-tutorial.md) [，Wistec](../saas-apps/fexa-tutorial.md)Enterprise Portal Enterprise Portal [Wistec Online](https://wisteconline.com/auth/oidc)

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

我们正在更新标识安全分数门户，使之与 Microsoft Secure 评分的 [新版本](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)中引入的更改一致。 

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

[Backup365](https://portal.backup365.io/login)， [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2)， [Alma SIS](https://almau.getalma.com/)， [Enlyft Dynamics 365 连接器](http://enlyft.com/)， [Serraview Space 利用率软件解决方案](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md)， [Uniq](https://web.uniq.app/)，[直观](../saas-apps/visibly-tutorial.md)， [Zylo](../saas-apps/zylo-tutorial.md)， [Edmentum-课件评估准确路径](https://auth.edmentum.com/elf/login)， [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome)， [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)Altamira [HRM](../saas-apps/altamira-hrm-tutorial.md)， [WireWheel，Zix](../saas-apps/wirewheel-tutorial.md)[相容性和捕获](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)，Greenlight[企业业务控制平台](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [，genetec clearance](https://app.vivun.com/dashboard/calendar/connect)[净空](https://www.clearance.network/) [，](https://nestiolistings.com/sso/oidc/azure/authorize/)iSAMS [，VeraSMART，Amiko](../saas-apps/verasmart-tutorial.md) [，Twingate](https://amiko.web.rivero.app/) [，Scalefusion，Bpanda](https://auth.twingate.com/signup)， [Vivun，FortiGate](https://scalefusion.com/users/sign_in/) [，Wandera](https://goto.bpanda.com/login)[最终用户](https://www.wandera.com/) [iSAMS](../saas-apps/isams-tutorial.md)

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
 
## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>作为 IT 管理员，我想要使用条件性访问来面向客户端应用

**类型：** 更改计划   
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
由于条件访问中的客户端应用条件的 GA 版本，默认情况下，新策略将默认应用于所有客户端应用程序。 这包括旧身份验证客户端。 现有策略将保持不变，但会从现有策略中删除 *"配置是/否* " 切换，以便轻松查看策略所应用到的客户端应用。 

创建新策略时，请确保排除仍使用旧身份验证的用户和服务帐户;否则，它们将被阻止。 [了解详细信息](../conditional-access/concept-conditional-access-conditions.md)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>即将推出的 SCIM 合规性修复

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务利用 SCIM 标准来与应用程序集成。 我们 SCIM 标准的实现正在不断发展，我们希望对我们如何执行修补程序操作以及如何在资源上设置属性 "活动" 的行为进行更改。 [了解详细信息](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 管理门户上的组所有者设置将更改

**类型：** 更改计划  
**服务类别：** 组管理  
**产品功能：** 协作

"组常规设置" 页上的 "所有者设置" 可以配置为在 Azure 管理门户和访问面板中将所有者分配权限限制为一组有限的用户。 我们很快就可以在这两个 UX 门户上分配组所有者特权，但也可以在后端强制执行策略，以跨终结点（例如 PowerShell 和 Microsoft Graph）提供一致的行为。 

我们将开始为不使用它的客户禁用当前设置，并将在接下来的几个月中提供一个选项来向用户提供组所有者权限。 有关更新组设置的指导，请参阅使用 [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)编辑组信息。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 注册服务正在结束 TLS 1.0 和1.1 的支持

**类型：** 更改计划  
**服务类别：** 设备注册和管理  
**产品功能：** 平台

传输层安全性 (TLS) 1.2 和更新服务器和客户端不久将与 Azure Active Directory 设备注册服务通信。 支持 TLS 1.0 和1.1 以便与 Azure AD 设备注册 service 进行通信：
- 在2020年8月31日，在所有主权云中 (GCC 高、DoD 等 ) 
- 所有商业云中的2020年10月30日

[详细了解](../devices/reference-device-registration-tls-1-2.md) Azure AD 注册服务的 TLS 1.2。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD 登录日志中显示 Windows Hello 企业版登录

**类型：** 已修复  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
Windows Hello 企业版允许最终用户使用手势 (（例如 PIN 或生物识别) ）登录到 Windows 计算机。 Azure AD 管理员可能希望将 Windows Hello 企业版登录从其他 Windows 登录区，作为组织的无密码身份验证过程的一部分。 

管理员现在可以通过在 Azure 门户的 Azure AD Sign-Ins 边栏选项卡中检查 Windows 登录事件的 "身份验证详细信息" 选项卡，来查看 Windows 身份验证是否使用了 Windows Hello 企业版。 Windows Hello 企业版身份验证将在 "身份验证方法" 字段中包括 "WindowsHelloForBusiness"。 有关解释 Sign-In 日志的详细信息，请参阅 [登录日志文档](../reports-monitoring/concept-sign-ins.md)。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>修复了组删除行为和性能改进

**类型：** 已修复  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
以前，当某个组从 "范围中" 更改为 "超出作用域"，并且管理员在完成更改之前单击了 "重新启动" 时，不会删除组对象。 现在，当组对象超出范围 (禁用、删除、未分配或未通过范围筛选器) 时，会从目标应用程序中删除。 [了解详细信息](../app-provisioning/how-provisioning-works.md#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>公共预览版：管理员现在可以将电子邮件中的自定义内容添加到审阅者创建访问评审

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识调控
 
创建新的访问评审后，审阅者将收到一封电子邮件，要求他们完成访问评审。 许多客户要求向电子邮件添加自定义内容（例如联系信息）或其他其他支持内容，以指导审阅者。 

管理员可以在公共预览版中提供，通过在 Azure AD 访问评审的 "高级" 部分添加内容来指定发送给审阅者的电子邮件中的自定义内容。 有关创建访问评审的指南，请参阅 [在 Azure AD 访问评审中创建组和应用程序的访问评审](../governance/create-access-review.md)。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>提供单页面应用的授权代码流

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 开发人员体验
 
由于新的浏览器第三方 cookie 限制（如 Safari ITP），Spa 将必须使用授权代码流，而不是隐式流来维护 SSO，MSAL.js 版本 2. x 现在支持授权代码流。 

Azure 门户有相应的更新，以便你可以将你的 SPA 更新为 "SPA" 类型并使用身份验证代码流。 若要进一步指导，请参阅 [使用身份验证代码流登录用户和获取 JAVASCRIPT SPA 中的访问令牌](../develop/quickstart-v2-javascript-auth-code.md) 。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD 应用程序代理现在支持远程桌面服务 Web 客户端

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制

Azure AD 应用程序代理现在支持 (RDS) Web 客户端的远程桌面服务。 RDS web 客户端允许用户通过任何支持 HTLM5 的浏览器（如 Microsoft Edge、Internet Explorer 11、Google Chrome 等）访问远程桌面基础结构。用户可以与远程应用或桌面进行交互，就像在任何位置使用本地设备一样。 通过使用 Azure AD 应用程序代理，你可以通过为所有类型的丰富客户端应用强制执行预身份验证和条件性访问策略，从而提高 RDS 部署的安全性。 有关指南，请参阅将 [远程桌面与 Azure AD 应用程序代理一起发布](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>公共预览版中的下一代 Azure AD B2C 用户流

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
简化的用户流体验提供具有预览功能的功能奇偶校验，并是所有新功能的主页。 用户能够在同一用户流中启用新功能，从而减少了使用每个新功能版本创建多个版本的需要。 最后，新的、用户友好的 UX 可简化用户流的选择和创建。 立即通过 [创建用户流](../../active-directory-b2c/tutorial-create-user-flows.md)来试用。 

有关用户流的详细信息，请参阅 [Azure Active Directory B2C 中的用户流版本](../../active-directory-b2c/user-flow-versions.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD 应用程序库中提供了新的联合应用程序-2020 年7月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年7月，我们已在应用程序库中添加了以下55新应用程序，并提供联合身份验证支持：

[击掌](http://www.rmit.com.ar/)， [Appreiz](https://microsoftteams.appreiz.com/)， [Inextor 保管库](https://inexto.com/inexto-suite/inextor)， [Beekast](https://my.beekast.com/)， [Templafy](https://app.templafy.com/)， [Control 塔](https://bpm.tnxcorp.com/sso/microsoft)， [PeterConnects，](https://msteams.peterconnects.com/)[硬币构造云](https://sso.coinsconstructioncloud.com/#login/) [，](https://appfusions.alohacloud.com/auth) [AlohaCloud MT](https://task.teamsmain.medx.im/authorization) [，Cocoom，](https://start.cocoom.com/) [Medxnote，Reflekt](https://reflekt.konsolute.com/login) [，Rever](https://app.reverscore.net/access) [，MyCompanyArchive](https://login.mycompanyarchive.com/) [，GReminders](../saas-apps/titanfile-tutorial.md) [，Titanfile](../saas-apps/wootric-tutorial.md) [，](../saas-apps/kpifire-tutorial.md) [Wootric，](../saas-apps/intsights-tutorial.md) [SolarWinds，Orion](https://app.greminders.com/o365-oauth) [，OpenText](../saas-apps/datasite-tutorial.md) [，Datasite，](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US) [BlogIn、](../saas-apps/opentext-directory-services-tutorial.md) [IntSights、](../saas-apps/textline-tutorial.md)kpifire [、system.windows.media.textformatting.textline>](../saas-apps/blogin-tutorial.md)、[社区 Spark](../saas-apps/community-spark-tutorial.md)、 [Chatwork、](../saas-apps/cloud-academy-sso-tutorial.md) [CloudSign](../saas-apps/cloudsign-tutorial.md)、 [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md) [，SmartHR ](https://smartschoolz.com/login)， [Chatwork](../saas-apps/chatwork-tutorial.md) [NumlyEngage](https://smarthr.jp/)™ [，密歇根](../saas-apps/numlyengage-tutorial.md)Data [Hub 单一登录，出口](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md)， [SendSafely](../saas-apps/egress-tutorial.md)， [Eletive](../saas-apps/sendsafely-tutorial.md)，[右手](https://app.eletive.com/)网络安全[ADI，Fyde](https://right-hand.ai/)Enterprise [Authentication，Verme](https://enterprise.fyde.com/)， [Lenses.io](../saas-apps/verme-tutorial.md) [，Momenta，Uprise](../saas-apps/lensesio-tutorial.md) [，CloudCords](../saas-apps/momenta-tutorial.md) [，](https://app.inspiresoftware.com/)TellMe Identity [Orchestrator SAML 连接器，Maverics](https://www.strata.io/identity-fabric/)，Smartschool， [Zepto](https://app.uprise.co/sign-in)， [Q](https://q.moduleq.com/login)timekeeping， [CloudCords](../saas-apps/cloudcords-tutorial.md)Studi.ly， [TellMe Bot](https://tellme365liteweb.azurewebsites.net/) [Trackplan，Skedda](https://studi.ly/) [，WhosOnLocation，](https://user.zepto-ai.com/signin) [Coggle，](../saas-apps/skedda-tutorial.md) [，](../saas-apps/whos-on-location-tutorial.md)) [ (，](../saas-apps/coggle-tutorial.md) [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)， [BrowserStack 单一登录](../saas-apps/browserstack-single-sign-on-tutorial.md) [Trackplan](http://www.trackplanfm.com/)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年7月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成

你现在可以自动创建、更新和删除新集成的应用 [LinkedIn 学习](../saas-apps/linkedin-learning-provisioning-tutorial.md)的用户帐户。

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](../app-provisioning/user-provisioning.md)。

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>查看所有范围内的角色分配，并将其下载到 csv 文件

**类型：** 已更改的功能  
**服务类别：** Azure AD 角色  
**产品功能：** 访问控制
 
你现在可以在 Azure AD 门户的 "角色和管理员" 选项卡中查看角色的所有作用域中的角色分配。 你还可以将每个角色的角色分配下载到 CSV 文件中。 有关查看和添加角色分配的指导，请参阅 [在 Azure Active Directory 中查看和分配管理员角色](../roles/manage-roles-portal.md)。
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure 多重身份验证软件开发 (Azure MFA SDK) 弃用

**类型：** 已弃用  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
Azure 多重身份验证软件开发 (Azure MFA SDK) 已于2018年11月14日结束，如11月 2017 11 日公布。 Microsoft 将关闭 SDK 服务，在2020年9月30日生效。 对 SDK 的任何调用都将失败。

如果你的组织使用的是 Azure MFA SDK，则需要在2020年9月30日之前迁移：
- 用于 MIM 的 Azure MFA SDK：如果将 SDK 与 MIM 一起使用，则应迁移到 Azure MFA 服务器，并按照这些 [说明](/microsoft-identity-manager/working-with-mfaserver-for-mim)激活 Privileged Access Management () PAM。   
- 适用于自定义应用的 Azure MFA SDK：考虑将你的应用集成到 Azure AD 并使用条件性访问来强制执行 MFA。 若要开始，请查看此 [页](../manage-apps/plan-an-application-integration.md)。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>条件访问策略中的用户风险条件

**类型：** 更改计划  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 

Azure AD 条件性访问策略中的用户风险支持，可创建多个基于风险的用户策略。 不同的用户和应用可能需要不同的最低用户风险级别。 根据用户风险，你可以创建策略来阻止访问、需要多重身份验证、安全密码更改或重定向到 Microsoft Cloud App Security 来强制执行会话策略，如其他审核。

用户风险条件需要 Azure AD Premium P2，因为它使用的是 P2 产品/服务。 有关条件性访问的详细信息，请参阅 [Azure AD 条件访问文档](../conditional-access/index.yml)。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO 现在支持需要在请求时设置 SPNameQualifier 的应用

**类型：** 已修复  
**服务类别：** 企业应用  
**产品功能：** SSO
 
某些 SAML 应用程序需要在请求时在断言主题中返回 SPNameQualifier。 现在 Azure AD 在请求 NameID 策略中请求 SPNameQualifier 时正确响应。 这也适用于 SP 发起的登录，并将在 IdP 发起的登录。  若要详细了解 Azure Active Directory 中的 SAML 协议，请参阅 [单一 Sign-On SAML 协议](../develop/single-sign-on-saml-protocol.md)。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B 协作支持邀请 MSA 和 Azure 政府租户中的 Google 用户

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

使用 B2B 协作功能的 Azure 政府租户现在可以邀请具有 Microsoft 或 Google 帐户的用户。 若要查明你的租户是否可以使用这些功能，请按照[如何判断我的 AZURE 美国政府租户中是否提供了 B2B 协作中](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)的说明进行操作？

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 中的用户对象现在包含 externalUserState 和 externalUserStateChangedDateTime 属性

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

"ExternalUserState" 和 "externalUserStateChangedDateTime" 属性可用于查找未接受其邀请的受邀 B2B 来宾，还可用于生成自动化，如删除在数天后未接受其邀请的用户。 现在，MS Graph v1 中提供了这些属性。 有关使用这些属性的指南，请参阅 [用户资源类型](/graph/api/resources/user?view=graph-rest-1.0)。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>管理身份验证会话 Azure AD 的条件访问现已正式发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
通过身份验证会话管理功能，你可以配置用户需要提供登录凭据的频率，以及在关闭和重新打开浏览器后是否需要提供凭据，以在你的环境中提供更高的安全性和灵活性。
 
此外，身份验证会话管理仅适用于 Azure AD 联接、混合 Azure AD 联接和 Azure AD 注册设备上的第一因素身份验证。 现在，身份验证会话管理也适用于 MFA。 有关详细信息，请参阅 [使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 应用程序库中提供了新的联合应用程序-2020 年6月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年6月，我们已在应用程序库中添加了以下29个新应用程序，并提供联合身份验证支持：

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md)， [Ekarda](../saas-apps/ekarda-tutorial.md)， [MailGates](../saas-apps/mailgates-tutorial.md)， [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md)， [Raketa](../saas-apps/raketa-tutorial.md)， [Segment](../saas-apps/segment-tutorial.md)， [Ai 审计员](https://www.mindbridge.ai/products/ai-auditor/)， [Pobuca Connect](https://app.pobu.ca/)， [Proto.io](../saas-apps/proto.io-tutorial.md)， [网关守卫](https://www.gatekeeperhq.com/)， [中心规划](../saas-apps/hub-planner-tutorial.md)人员， [Ansira-合作伙伴走向市场工具箱](https://ansira.com/technology/channel-engagement)， [IBM 数字业务自动化，适用于云](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md)， [Kisi 物理安全性](../saas-apps/kisi-physical-security-tutorial.md)， [ViewpointOne](https://team.viewpoint.com/)， [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md)， [pymetrics](../saas-apps/pymetrics-tutorial.md)， [零](https://www.teamzero.com/)， [InStation](https://instation.invillia.com/)， [edX for Business SAML 2.0 集成](../saas-apps/edx-for-business-saml-integration-tutorial.md)， [MOOC Office 365](https://mooc.office365-training.com/en/)， [SmartKargo](../saas-apps/smartkargo-tutorial.md)， [PKIsigning 平台](https://platform.pkisigning.nl/)， [SiteIntel](../saas-apps/siteintel-tutorial.md)， [字段 iD](../saas-apps/field-id-tutorial.md)， [课程 saml](../saas-apps/curricula-saml-tutorial.md)，Perforce [Helix](../saas-apps/perforce-helix-core-tutorial.md) [，Helix](https://cloud.metacompliance.com/) [SSH](https://smallstep.com/sso-ssh/)  

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial 。 若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息： https://aka.ms/AzureADAppRequest 。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>用于外部标识的 API 连接器现在提供公共预览版

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
使用外部标识 API 连接器，可以利用 web Api 将自助注册与外部云系统集成。 这意味着，现在可以将 web Api 作为注册流中的特定步骤调用，以触发基于云的自定义工作流。 例如，可以使用 API 连接器执行以下操作：

- 与自定义批准工作流集成。
- 执行标识校对
- 验证用户输入数据
- 覆盖用户属性
- 运行自定义业务逻辑

有关 API 连接器的所有可能的体验的详细信息，请参阅 [使用 api 连接器自定义和扩展自助注册](../external-identities/api-connectors-overview.md)，或 [自定义使用 web API 集成的外部标识自助服务注册](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>按需预配，并在几秒钟内使用户进入你的应用

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务当前以循环方式进行操作。 此服务每40分钟运行一次。 按 [需预配功能](https://aka.ms/provisionondemand) 允许您选择用户并在数秒内进行设置。 此功能可让你快速排查预配问题，无需重新启动即可强制开始预配周期。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>在 Graph 中使用 Azure AD 权限管理的新权限

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理
 
新的委托权限 EntitlementManagement。现在，可以在 Microsoft Graph beta 版中与授权管理 API 一起使用。 若要了解有关可用 Api 的详细信息，请参阅使用 [Azure AD 的权利管理 API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)。

---

### <a name="identity-protection-apis-available-in-v10"></a>在1.0 版中提供 Identity Protection Api

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 
RiskyUsers 和 riskDetections Microsoft Graph Api 现已正式发布。 现在，在 v1.0 终结点上提供了它们，我们邀请你在生产环境中使用它们。 有关详细信息，请查看 [Microsoft Graph 文档](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>将策略应用到 Microsoft 365 组的敏感度标签现已正式发布

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作
 

你现在可以创建敏感度标签，并使用标签设置将策略应用到 Microsoft 365 组，包括隐私 (公用或专用) 和外部用户访问策略。 你可以创建一个包含隐私策略的标签，并使用外部用户访问策略不允许添加来宾用户。 当用户将此标签应用于组时，该组将为 "专用"，并且不允许向该组添加来宾用户。 

敏感度标签非常重要，可保护你的关键业务数据，并使你能够以相容且安全的方式管理规模较高的组。 有关使用敏感度标签的指南，请参阅 [Azure Active Directory (预览) 中的 Microsoft 365 组分配敏感度标签 ](../enterprise-users/groups-assign-sensitivity-labels.md)。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>更新以支持 Azure AD Premium 客户 Microsoft Identity Manager

**类型：** 已更改的功能  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理
 
Azure 支持现可用于 Microsoft Identity Manager 2016 的 Azure AD 集成组件，Microsoft Identity Manager 2016 的扩展支持结束。 [使用 Microsoft Identity Manager 为 Azure AD Premium 客户提供更多有关支持更新的](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)信息。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>增加 SSO 声明配置中组成员条件的使用

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
以前，根据任何单个应用程序配置中的组成员条件更改声明时，可以使用的组数限制为10个。 SSO 声明配置中的组成员条件的使用现在已增加到最多50组。 有关如何配置声明的详细信息，请参阅 [企业应用程序 SSO 声明配置](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>在公司品牌的登录页文本组件上启用基本格式设置。

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
已更新 Azure AD/Microsoft 365 登录体验的公司品牌功能，以允许客户添加超链接和简单格式设置，包括粗体、下划线和斜体。 有关使用此功能的指南，请参阅 [将品牌添加到组织的 Azure Active Directory 登录页](./customize-branding.md)。

---

### <a name="provisioning-performance-improvements"></a>预配性能改进

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
已更新预配服务，以缩短 [增量周期](../app-provisioning/how-provisioning-works.md#incremental-cycles) 的完成时间。 这意味着将用户和组预配到其应用程序中的速度比以前更快。 6/10/2020 之后创建的所有新预配作业都将自动受益于性能改进。 在6/10/2020 之前配置为预配的任何应用程序都需要在6/10/2020 后重启一次，以便充分利用性能改进。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>宣布弃用 ADAL 和 MS Graph 奇偶校验

**类型：** 已弃用  
**服务类别：** N/A  
**产品功能：** 设备生命周期管理

现在，Microsoft 身份验证库 (MSAL) 提供，我们将不再向 Azure Active Directory 身份验证库添加新功能 (ADAL) 并将于2022年6月30日结束安全修补程序。 有关如何迁移到 MSAL 的详细信息，请参阅将 [应用程序迁移到 Microsoft 身份验证库 (MSAL) ](../develop/msal-migration.md)。

此外，我们还完成了通过 MS Graph 提供所有 Azure AD Graph 功能的工作。 因此，Azure AD Graph Api 在2022年6月30日之前仅接收到 bug 修复和安全修补程序。 有关详细信息，请参阅 [更新应用程序以使用 Microsoft 身份验证库和 MICROSOFT GRAPH API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
