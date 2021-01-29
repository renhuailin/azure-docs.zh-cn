---
title: 登录到基于 SAML 的单一登录配置的应用时出现问题
description: 当登录到已配置为基于 SAML 的联合单一登录的应用程序时，针对特定错误的指导 Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: 62b828a23805c6d3ab544dd2b9f78b45a3327cde
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053391"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>登录到基于 SAML 的单一登录配置的应用时出现问题
若要解决下面的登录问题，建议执行以下操作来更好地诊断和自动执行解决步骤：

- 安装[我的应用安全浏览器扩展](my-apps-deployment-plan.md)，以在 Azure 门户中使用测试体验时帮助 Azure Active Directory (Azure AD) 提供更好的诊断和解决方法。
- 使用 Azure 门户的应用配置页中的测试体验重现该错误。 详细了解如何 [调试基于 SAML 的单一登录应用程序](./debug-saml-sso-issues.md)

如果在 Azure 门户中使用 "我的应用" 安全浏览器扩展的 [测试体验](./debug-saml-sso-issues.md) ，则无需手动执行以下步骤来打开 "基于 SAML 的单一登录配置" 页。

若要打开基于 SAML 的单一登录配置页：
1.  打开 [**Azure 门户**](https://portal.azure.com/) 并以 **全局管理员** 或 **Coadmin** 登录。
1.  通过选择主左侧导航菜单顶部的 "**所有服务**" 打开 **Azure Active Directory 扩展**。
1.  在筛选器搜索框中键入 **"Azure Active Directory"** ，并选择 **Azure Active Directory** 项目。
1.  在 Azure Active Directory 的左侧导航菜单中，选择“企业应用程序”。
1.  选择“所有应用程序”，查看所有应用程序的列表。

    如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

1.  选择要配置为单一登录的应用程序。
1. 加载应用程序后，在应用程序的左侧导航菜单中选择 " **单一登录** "。
1. 选择“基于 SAML 的 SSO”。

## <a name="application-not-found-in-directory"></a>在目录中未找到应用程序
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

可能的原因 

`Issuer`从应用程序发送到 SAML 请求中 Azure AD 的属性与为 Azure AD 中的应用程序配置的标识符值不匹配。

**分辨率**

确保 `Issuer` SAML 请求中的属性与 Azure AD 中配置的标识符值匹配。

在 "基于 SAML 的 SSO 配置" 页上的 " **基本 SAML 配置** " 部分中，确认 "标识符" 文本框中的值与错误中显示的标识符值的值匹配。

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>回复地址与为应用程序配置的回复地址不匹配
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

可能的原因 

`AssertionConsumerServiceURL`SAML 请求中的值与 Azure AD 中配置的 "回复 URL" 值或模式不匹配。 `AssertionConsumerServiceURL`SAML 请求中的值是在错误中看到的 URL。

**分辨率**

确保 `AssertionConsumerServiceURL` SAML 请求中的值与 Azure AD 中配置的 "回复 URL" 值匹配。 

验证或更新 "回复 URL" 文本框中的值，使其与 `AssertionConsumerServiceURL` SAML 请求中的值匹配。   

在 Azure AD 中更新 "答复 URL" 值并将它与应用程序在 SAML 请求中发送的值匹配后，应该可以登录到应用程序。

## <a name="user-not-assigned-a-role"></a>未为用户分配角色
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

可能的原因 

未在 Azure AD 中授予用户访问应用程序的权限。

**分辨率**

若要直接将一个或多个用户分配到应用程序，请参阅 [快速入门：将用户分配到应用](add-application-portal-assign-users.md)。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 请求
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

可能的原因 

Azure AD 不支持应用程序针对单一登录所发送的 SAML 请求。 常见问题如下：
- SAML 请求中缺少必填字段
- SAML 请求编码方法

**分辨率**

1. 捕获 SAML 请求。 按照教程 [如何在 Azure AD 中调试基于 SAML 的单一登录应用程序](./debug-saml-sso-issues.md) ，了解如何捕获 saml 请求。
1. 联系应用程序供应商并共享以下信息：
    - SAML 请求
    - [Azure AD 单一登录 SAML 协议要求](../develop/single-sign-on-saml-protocol.md)

应用程序供应商应该验证它们是否支持单一登录的 Azure AD SAML 实现。

## <a name="misconfigured-application"></a>应用程序配置错误
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

可能的原因 

`Issuer`从应用程序发送到 SAML 请求中 Azure AD 的属性与为 Azure AD 中的应用程序配置的标识符值不匹配。

**分辨率**

确保 `Issuer` SAML 请求中的属性与 Azure AD 中配置的标识符值匹配。 

验证 "标识符" 文本框中的值与错误中显示的标识符值的值是否匹配。

## <a name="certificate-or-key-not-configured"></a>证书或密钥未配置
`Error AADSTS50003: No signing key configured.`

可能的原因 

应用程序对象已损坏，Azure AD 无法识别为应用程序配置的证书。

**分辨率**

若要删除并创建新证书，请按照以下步骤操作：
1. 在 "基于 SAML 的 SSO 配置" 屏幕上，选择 " **saml 签名证书**" 部分下的 "**创建新证书**"。
1. 选择 "到期日期"，然后单击 " **保存**"。
1. 选中 " **使新证书处于活动状态** " 以替代活动证书。 然后，单击窗格顶部的“保存”并选择接受以激活滚动更新证书。
1. 在 " **SAML 签名证书** " 部分下，单击 " **删除** " 以删除 **未使用** 的证书。

## <a name="saml-request-not-present-in-the-request"></a>请求中不存在 SAML 请求
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

可能的原因 

Azure AD 无法识别 HTTP 请求中的 URL 参数中的 SAML 请求。 如果在将 SAML 请求发送到 Azure AD 时，应用程序未使用 HTTP 重定向绑定，则可能会发生这种情况。

**分辨率**

应用程序需要使用 HTTP 重定向绑定将编码的 SAML 请求发送到位置标头。 有关如何实现它的详细信息，请阅读 [SAML 协议规范文档](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)中的“HTTP 重定向绑定”部分。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD 正在将令牌发送到不正确的终结点
可能的原因 

在单一登录期间，如果登录请求不包含显式回复 URL (断言使用者服务 URL) 则 Azure AD 将为该应用程序选择任何已配置的回复 Url。 即使应用程序已配置了显式回复 URL，用户也可能会被重定向 https://127.0.0.1:444 。 

当应用程序被添加为非库应用时，Azure Active Directory 将此回复 URL 创建为默认值。 此行为已更改，Azure Active Directory 默认情况下不再添加此 URL。 

**分辨率**

删除为应用程序配置的未使用的答复 Url。

在 "基于 SAML 的 SSO 配置" 页上的 " **回复 URL (断言使用者服务 URL")** 部分中，删除系统创建的未使用或默认的回复 url。 例如，`https://127.0.0.1:444/applications/default.aspx`。


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>使用服务进行身份验证的用户不匹配请求的身份验证方法的身份验证方法
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

可能的原因 

`RequestedAuthnContext`属于 SAML 请求。 这意味着应用程序应为 `AuthnContext` 指定的 `AuthnContextClassRef` 。 但是，用户在访问应用程序之前已经过身份验证， `AuthnContext` (身份验证方法) 用于以前的身份验证，这与请求的身份验证不同。 例如，对 myapps 和 WIA 的联合用户进行了访问。 `AuthnContextClassRef`将为 `urn:federation:authentication:windows` 。 AAD 不会执行全新的身份验证请求，它将使用通过 IdP (ADFS 或任何其他联合身份验证服务传递的身份验证上下文) 。 因此，如果应用程序请求的不是，则会出现不匹配 `urn:federation:authentication:windows` 。 另一种情况是使用多因素时： `'X509, MultiFactor` 。

**分辨率**


`RequestedAuthnContext` 可选值。 如果可能，请询问应用程序是否可以删除。

另一种方法是确保 `RequestedAuthnContext` 将会遵守。 这将通过请求全新身份验证完成。 通过执行此操作，在处理 SAML 请求时，将完成一种全新的身份验证，并将采取此操作 `AuthnContext` 。 若要请求全新身份验证，SAML 请求最多包含值 `forceAuthn="true"` 。 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自定义发送到应用程序的 SAML 声明时出现问题
若要了解如何自定义发送到应用程序的 SAML 属性声明，请参阅 [中的声明映射 Azure Active Directory](../develop/active-directory-claims-mapping.md)。

## <a name="errors-related-to-misconfigured-apps"></a>与配置错误的应用相关的错误
确认门户中的配置与应用中的配置相匹配。 具体而言，比较客户端/应用程序 ID、回复 URL、客户端密码/密钥和应用 ID URI。

将在代码中请求访问的资源与“所需资源”选项卡中的已配置权限进行比较，确保仅请求已配置的资源。

## <a name="next-steps"></a>后续步骤
- [应用程序管理的快速入门系列](add-application-portal-assign-users.md)
- [如何在 Azure AD 中调试对应用程序进行基于 SAML 的单一登录](./debug-saml-sso-issues.md)
- [Azure AD 单一登录 SAML 协议要求](../develop/single-sign-on-saml-protocol.md)