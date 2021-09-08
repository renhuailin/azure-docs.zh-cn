---
title: 用户流和自定义策略的开发人员说明
titleSuffix: Azure AD B2C
description: 有关使用用户流和自定义策略配置和维护 Azure AD B2C 的开发人员说明。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 33389224bcc4abf05ffbb261e23409eb95896781
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220867"
---
# <a name="developer-notes-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的开发人员说明

Azure Active Directory B2C [用户流和自定义策略](user-flow-overview.md)已正式发布。 Azure AD B2C 功能的开发工作正在继续，因此，尽管大部分功能已正式发布，但有些功能还处于软件发布周期的不同阶段。 本文讨论了 Azure AD B2C 的累积改进，并详细说明了功能可用性。

## <a name="terms-for-features-in-public-preview"></a>公共预览版功能的使用条款

- 建议将公共预览功能仅用于评估。
- [服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/active-directory-b2c) 不适用于公共预览功能。
- 可通过普通支持渠道提出公共预览功能支持请求。

## <a name="user-flows"></a>用户流

|Feature  |用户流  |自定义策略  |备注  |
|---------|:---------:|:---------:|---------|
| 使用电子邮件和密码[注册并登录](add-sign-up-and-sign-in-policy.md)。 | GA | GA| |
| 使用用户名和密码[注册并登录](add-sign-up-and-sign-in-policy.md)。| GA | GA | |
| [配置文件编辑流](add-profile-editing-policy.md) | GA | GA | |
| [自助式密码重置](add-password-reset-policy.md) | GA| GA| |
| [强制执行密码重置](force-password-reset.md) | GA | NA | |
| [电话注册和登录](phone-authentication-user-flows.md) | GA | GA | |
| [条件访问和标识保护](conditional-access-user-flow.md) | GA | GA | 不适用于 SAML 应用程序 |

## <a name="oauth-20-application-authorization-flows"></a>OAuth 2.0 应用程序授权流

下表总结了可以与 Azure AD B2C 集成的 OAuth 2.0 和 OpenId Connect 应用程序身份验证流。

|Feature  |用户流  |自定义策略  |注释  |
|---------|:---------:|:---------:|---------|
[授权代码](authorization-code-flow.md) | GA | GA | 允许用户登录到 Web 应用程序。 Web 应用程序接收授权代码。 兑换该授权代码可获取用于调用 Web API 的令牌。|
[采用 PKCE 的授权代码](authorization-code-flow.md)| GA | GA | 允许用户登录到移动和单页应用程序。 应用程序接收采用代码交换证明密钥 (PKCE) 的授权代码。 兑换该授权代码可获取用于调用 Web API 的令牌。  |
[客户端凭据授予](https://tools.ietf.org/html/rfc6749#section-4.4)| GA | GA | 允许使用应用程序的标识访问 Web 托管的资源。 通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。  <br />  <br />  若要在 Azure AD B2C 租户中使用此功能，请使用 Azure AD B2C 租户的 Azure AD 终结点。 有关详细信息，请参阅 [OAuth 2.0 客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 此流不使用 Azure AD B2C [用户流或自定义策略](user-flow-overview.md)设置。 |
[设备授权授予](https://tools.ietf.org/html/rfc8628)| NA | NA | 允许用户登录到智能电视、IoT 设备或打印机等输入受限的设备。  |
[隐式流](implicit-flow-single-page-application.md) | GA | GA |  允许用户登录到单页应用程序。 应用直接获取令牌，无需执行后端服务器凭据交换。|
[代理](../active-directory/develop/v2-oauth2-on-behalf-of-flow.md)| NA | NA | 应用程序调用某个服务或 Web API，而后者又需要调用另一个服务或 Web API。 <br />  <br /> 若要使中间层服务向下游服务发出经过身份验证的请求，请在授权标头中传递一个客户端凭据令牌。 可以有选择地将自定义标头包含在 Azure AD B2C 用户令牌中。  |
[OpenId Connect](openid-connect.md) | GA | GA | OpenID Connect 引入了 ID 令牌的概念，这是一种安全令牌，可让客户端验证用户的标识。 |
[OpenId Connect 混合流](openid-connect.md) | GA | GA | 允许 Web 应用程序检索授权请求上的 ID 令牌以及授权代码。  |
[资源所有者密码凭据 (ROPC)](add-ropc-policy.md) | 预览 | 预览 | 允许移动应用程序通过直接处理用户的密码让用户登录。 |

### <a name="oauth-20-options"></a>OAuth 2.0 选项

|Feature  |用户流  |自定义策略  |备注  |
|---------|:---------:|:---------:|---------|
| [将登录重定向到社交提供者](direct-signin.md#redirect-sign-in-to-a-social-provider) | GA | GA | 查询字符串参数 `domain_hint`。 |
| [预填充登录名](direct-signin.md#prepopulate-the-sign-in-name) | GA | GA | 查询字符串参数 `login_hint`。 |
| 通过 `client_assertion` 将 JSON 插入用户历程| NA| 已放弃 |  |
| 将 JSON 作为 [id_token_hint](id-token-hint.md) 插入到用户旅程中 | NA | GA | |
| [向应用程序传递标识提供者令牌](idp-pass-through-user-flow.md)| 预览| 预览| 例如，从 Facebook 到应用。 |

## <a name="saml2-application-authentication-flows"></a>SAML2 应用程序身份验证流

下表总结了可以与 Azure AD B2C 集成的安全断言标记语言 (SAML) 应用程序身份验证流。

|Feature  |用户流  |自定义策略  |注释  |
|---------|:---------:|:---------:|---------|
[SP 启动的](saml-service-provider.md) | NA | GA | POST 和重定向绑定。 |
[IDP 启动的](saml-service-provider-options.md#configure-idp-initiated-flow) | NA | GA | 其中发起的标识提供程序为 Azure AD B2C。  |

## <a name="user-experience-customization"></a>用户体验自定义

|Feature  |用户流  |自定义策略  |备注  |
|---------|:---------:|:---------:|---------|
| [多语言支持](localization.md)| GA | GA | |
| [自定义电子邮件验证](custom-email-mailjet.md) | NA | GA| |
| [使用内置模板自定义用户界面](customize-ui.md) | GA| GA| |
| [使用自定义模板自定义用户界面](customize-ui-with-html.md) | GA| GA| 通过使用 HTML 模板。 |
| [页面布局版本](page-layout.md) | GA | GA | |
| [JavaScript](javascript-and-page-layout.md) | GA | GA | |
| [嵌入的登录体验](embedded-login.md) | NA |  预览| 通过使用内联框架元素 `<iframe>`。 |
| [密码复杂性](password-complexity.md) | GA | GA | |
| [禁用电子邮件验证](disable-email-verification.md) | GA|  GA| 不建议在生产环境中使用。 在注册过程中禁用电子邮件验证可能会导致垃圾邮件。 |



## <a name="identity-providers"></a>标识提供者

|Feature  |用户流  |自定义策略  |备注  |
|---------|:---------:|:---------:|---------|
|[AD FS](identity-provider-adfs.md) | NA | GA | |
|[Amazon](identity-provider-amazon.md) | GA | GA | |
|[Apple](identity-provider-apple-id.md) | 预览 | 预览 | |
|[Azure AD（单租户）](identity-provider-azure-ad-single-tenant.md) | GA | GA | |
|[Azure AD（多租户）](identity-provider-azure-ad-multi-tenant.md) | NA  | GA | |
|[Azure AD B2C](identity-provider-azure-ad-b2c.md) | GA | GA | |
|[eBay](identity-provider-ebay.md) | NA | 预览 | |
|[Facebook](identity-provider-facebook.md) | GA | GA | |
|[GitHub](identity-provider-github.md) | GA | GA | |
|[Google](identity-provider-google.md) | GA | GA | |
|[ID.me](identity-provider-id-me.md) | GA | GA | |
|[LinkedIn](identity-provider-linkedin.md) | GA | GA | |
|[Microsoft 帐户](identity-provider-microsoft-account.md) | GA | GA | |
|[QQ](identity-provider-qq.md) | 预览 | GA | |
|[Salesforce](identity-provider-salesforce.md) | GA | GA | |
|[Salesforce（SAML 协议）](identity-provider-salesforce-saml.md) | NA | GA | |
|[Twitter](identity-provider-twitter.md) | GA | GA | |
|[微信](identity-provider-wechat.md) | 预览 | GA | |
|[微博](identity-provider-weibo.md) | 预览 | GA | |

## <a name="generic-identity-providers"></a>一般标识提供者

|Feature  |用户流  |自定义策略  |备注  |
|---------|:---------:|:---------:|---------|
|[OAuth2](oauth2-technical-profile.md) | NA | GA | 例如 [Google](identity-provider-google.md)、[GitHub](identity-provider-github.md) 和 [Facebook](identity-provider-facebook.md)。|
|[OAuth1](oauth1-technical-profile.md) | NA | GA | 例如 [Twitter](identity-provider-twitter.md)。 |
|[OpenID Connect](openid-connect-technical-profile.md) | GA | GA | 例如 [Azure AD](identity-provider-azure-ad-single-tenant.md)。  |
|[SAML2](identity-provider-generic-saml.md) | NA | GA | 例如 [Salesforce](identity-provider-salesforce-saml.md) 和 [AD-FS](identity-provider-adfs.md)。 |
| WSFED | NA | NA | |

### <a name="api-connectors"></a>API 连接器

|Feature  |用户流  |自定义策略  |备注  |
|---------|:---------:|:---------:|---------|
|[API 连接器](api-connectors-overview.md) | 预览 | GA | |
|[通过基本身份验证进行保护](secure-rest-api.md#http-basic-authentication) | 预览 | GA | |
|[通过客户端证书身份验证进行保护](secure-rest-api.md#https-client-certificate-authentication) | 预览 | GA | |
|[通过 OAuth2 持有者身份验证进行保护](secure-rest-api.md#oauth2-bearer-authentication) | NA | GA | |
|[通过 API 密钥身份验证进行保护](secure-rest-api.md#api-key-authentication) | NA | GA | |


## <a name="custom-policy-features"></a>自定义策略功能

### <a name="session-management"></a>会话管理

| Feature |  自定义策略 | 注释 |
| ------- | :--: | ----- |
| [默认 SSO 会话提供程序](custom-policy-reference-sso.md#defaultssosessionprovider) | GA |  |
| [外部登录会话提供程序](custom-policy-reference-sso.md#externalloginssosessionprovider) | GA |  |
| [SAML SSO 会话提供程序](custom-policy-reference-sso.md#samlssosessionprovider) | GA |  |
| [OAuth SSO 会话提供程序](custom-policy-reference-sso.md#oauthssosessionprovider)  | GA|  |
| [单一登录](session-behavior.md#sign-out)  |  预览 |  |

### <a name="components"></a>组件

| Feature | 自定义策略 | 注释 |
| ------- | :--: | ----- |
| [电话因素身份验证](phone-factor-technical-profile.md) | GA |  |
| [Azure AD MFA 身份验证](multi-factor-auth-technical-profile.md) | 预览 |  |
| [一次性密码](one-time-password-technical-profile.md) | GA |  |
| [Azure Active Directory](active-directory-technical-profile.md) 用作本地目录 | GA |  |
| [谓词验证](predicates.md) | GA | 例如，密码复杂性。 |
| [显示控件](display-controls.md) | GA |  |

### <a name="developer-interface"></a>开发人员接口

| Feature | 自定义策略 | 备注 |
| ------- | :--: | ----- |
| Azure 门户 | GA |   |
| [Application Insights 用户旅程日志](troubleshoot-with-application-insights.md) | 预览 | 用于在开发过程中进行故障排除。  |
| [Application Insights 事件日志](analytics-with-application-insights.md) | 预览 | 用于监视生产中的用户流。 |

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自定义策略功能集开发人员的责任

手动策略配置授予对 Azure AD B2C 基础平台的较低访问级别，因此要求创建唯一的信任框架。 自定义标识提供者、信任关系、与外部服务的集成以及分步工作流的诸多可能组合方式要求在设计和配置方面采用有条理的方法。

使用自定义策略功能集的开发人员应遵守以下指导原则：

- 熟悉自定义策略和密钥/机密管理的配置语言。 有关详细信息，请参阅 [TrustFrameworkPolicy](trustframeworkpolicy.md)。
- 取得方案和自定义集成的所有权。 阐述自己的工作并告知实时站点组织。
- 执行有序的方案测试。
- 遵循软件开发和暂存最佳做法。 建议至少使用一个开发和测试环境。
- 随时了解与之集成的标识提供程序和服务的新进展。 例如，跟踪机密的更改情况以及对服务的计划内和计划外更改。
- 设置主动监控，监控生产环境的响应能力。 有关与 Application Insights 集成的详细信息，请参阅 [Azure Active Directory B2C：收集日志](analytics-with-application-insights.md)。
- 在 Azure 订阅中保留最新的联系电子邮件地址，并快速回复 Microsoft 活动站点团队的电子邮件。
- 根据 Microsoft 活动站点团队的通知及时采取措施。

## <a name="next-steps"></a>后续步骤

- 检查[可用于 Azure AD B2C 的 Microsoft Graph 操作](microsoft-graph-operations.md)。
- 详细了解[自定义策略以及与用户流的区别](custom-policy-overview.md)。
