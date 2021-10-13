---
title: 通过 Azure AD 合作伙伴集成实现安全的混合访问
description: 帮助客户发现 SaaS 应用程序，将 SaaS 应用程序迁移到 Azure AD，以及将使用旧式身份验证方法的应用与 Azure AD 相连接。
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f700b4850f68f9938bc574b26172f49c1d7c0a73
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613807"
---
# <a name="secure-hybrid-access-with-azure-active-directory-partner-integrations"></a>通过 Azure Active Directory 合作伙伴集成实现安全的混合访问

Azure Active Directory (Azure AD) 支持新式身份验证协议，在联系紧密的基于云的环境中，新式身份验证协议能够帮助保护应用程序的安全。 但是，许多业务应用程序设计为在受保护的企业网络中工作，而且其中一些应用程序使用旧式身份验证方法。 随着公司寻求制定零信任策略并支持混合工作环境和云优先工作环境，他们需要能够将应用连接到 Azure AD 并为旧版应用程序提供新式身份验证解决方案的解决方案。

Azure AD 原生支持 SAML、WS-Fed 和 OIDC 等新式协议。 Azure AD 中的应用代理支持 Kerberos 和基于头的身份验证。 目前尚不支持 SSH、NTLM、LDAP 和 Cookie 等其他协议。 但是，ISV 可以创建解决方案来将这些应用程序与 Azure AD 相连接，以帮助客户开启零信任历程。

ISV 可以帮助客户发现服务型软件 (SaaS) 应用程序并将其迁移到 Azure AD。 他们还可以将使用旧式身份验证方法的应用与 Azure AD 相连接。 这将帮助客户合并为单个平台 (Azure AD)，以简化其应用管理并使他们能够实施零信任原则。 支持使用旧式身份验证的应用能够改善用户安全性。 在客户对其应用进行现代化改造以支持新式身份验证协议之前，这是个不错的权宜之计。

## <a name="solution-overview"></a>解决方案概述

构建的解决方案可以包括以下部分：

1. 应用发现。 通常，客户并不知道他们正在使用的所有应用程序。 因此，首先可以将应用程序发现功能构建到解决方案中，并在用户界面中公开所发现的应用程序。 这样，客户可以确定优先事项，以确定如何着手处理其应用程序与 Azure AD 的集成。
2. 应用迁移。 接下来，你可以创建产品内工作流，客户可以在其中直接将应用与 Azure AD 集成，而无需转到 Azure AD 门户。 如果你不在解决方案中实现发现功能，可以从此处开始实现解决方案，并将客户了解的应用程序与 Azure AD 集成。
3. 旧式身份验证支持。 可以将使用旧式身份验证方法的应用链接到 Azure AD，以便它们能够从单一登录 (SSO) 及其他功能获益。
4. **条件访问**。 作为一项附加功能，你可以使客户从解决方案中将 Azure AD [条件访问](/azure/active-directory/conditional-access/overview/)策略应用于应用程序，而无需转到 Azure AD 门户。

本指南的其余部分将介绍有关实现解决方案的技术注意事项和建议。

## <a name="publishing-your-application-to-azure-marketplace"></a>将应用程序发布到 Azure 市场

可以按照[将应用程序发布到 Azure 市场](/azure/active-directory/develop/v2-howto-app-gallery-listing/)过程，将应用程序与 Azure AD 预先集成，以支持 SSO 和自动化预配。 Azure 市场是面向 IT 管理员的可信应用程序来源。 其中列出的应用程序经验证与 Azure AD 兼容。 它们支持 SSO，可以自动化用户预配，而且可以通过自动化应用注册轻松地集成到客户租户中。

此外，建议你成为[经过验证的发布者](/azure/active-directory/develop/publisher-verification-overview/)，以便客户知道你是可信的应用程序发布者。

## <a name="enabling-single-sign-on-for-it-admins"></a>为 IT 管理员启用单一登录

[选择 OIDC 或 SAML](/azure/active-directory/manage-apps/sso-options#choosing-a-single-sign-on-method/)，为 IT 管理员启用到解决方案的 SSO。 最佳选项是使用 OIDC。 

Microsoft Graph 使用 [OIDC/OAuth](/azure/active-directory/develop/v2-protocols-oidc/)。 如果你的解决方案将 OIDC 与 Azure AD 结合使用来实现 IT 管理员 SSO，则客户将能获得无缝的端到端体验。 客户将使用 OIDC 登录到你的解决方案，然后可以使用 Azure AD 颁发的同一个 JSON Web 令牌 (JWT) 来与 Microsoft Graph 交互。

相反，如果你的解决方案使用 [SAML](/azure/active-directory/manage-apps/configure-saml-single-sign-on/) 来实现 IT 管理员 SSO，则你的解决方案无法使用 SAML 令牌与 Microsoft Graph 交互。 你仍可以使用 SAML 来实现 IT 管理员 SSO，但你的解决方案需要支持 OIDC 与 Azure AD 的集成，以便从 Azure AD 获取 JWT 来正常与 Microsoft Graph 交互。 可以使用以下方法之一：

- 建议的 SAML 方法：在 Azure 市场中创建新的注册，该注册是 [OIDC 应用](/azure/active-directory/saas-apps/openidoauth-tutorial/)。 此方法可为客户提供最无缝的体验。 客户需要同时将 SAML 应用和 OIDC 应用添加到其租户。 如果应用程序当前不在 Azure AD 库中，可以从非库[多租户应用程序](/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant/)开始。

- 备选 SAML 方法：客户可以在其 Azure AD 租户中手动[创建 OIDC 应用程序注册](/azure/active-directory/saas-apps/openidoauth-tutorial/)，并确保设置本文后面指定的正确 URI、终结点和权限。

你需要使用 [client_credentials 授权类型](/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#get-a-token/)。 该授权类型要求你的解决方案允许每个客户在用户界面中输入 client_ID 和机密，并要求你存储这些信息。 从 Azure AD 获取 JWT，然后使用它来与 Microsoft Graph 交互。

如果选择此方法，你应该编写好文档，使客户知道如何在其 Azure AD 租户中创建该应用程序注册。 此信息包括终结点、URI 和所需的权限。

> [!NOTE]
> 客户的 IT 管理员需要[在其租户中同意应用程序](/azure/active-directory/manage-apps/grant-admin-consent/)，然后才能为相应的应用程序启用 IT 管理员或用户 SSO。

## <a name="authentication-flows"></a>身份验证流

该解决方案包含三个支持以下方案的密钥身份验证流：

- 客户的 IT 管理员使用 SSO 进行登录来管理你的解决方案。

- 客户的 IT 管理员使用你的解决方案通过 Microsoft Graph 将应用程序与 Azure AD 集成。

- 用户登录到由你的解决方案和 Azure AD 保护的旧版应用程序。

### <a name="your-customers-it-administrator-does-single-sign-on-to-your-solution"></a>客户的 IT 管理员对你的解决方案执行单一登录

当客户的 IT 管理员登录时，你的解决方案可以使用 SAML 或 OIDC 来实现 SSO。 无论采用哪种方法，都建议确保 IT 管理员可以使用其 Azure AD 凭据登录到你的解决方案。 这样可以启用无缝的体验，并使他们能够使用现有的安全控制。 解决方案应与 Azure AD 集成，以通过 SAML 或 OIDC 实现 SSO。

下面是此用户身份验证流的示意图和摘要：

![该示意图显示用户身份验证流中，解决方案将 IT 管理员重定向到 Azure AD 以登录，然后 Azure AD 将其重定向回到解决方案。](./media/secure-hybrid-access-integrations/admin-flow.png)

1. IT 管理员需要使用其 Azure AD 凭据来登录到你的解决方案。

2. 你的解决方案通过 SAML 或 OIDC 登录请求将 IT 管理员重定向到 Azure AD。

3. Azure AD 对 IT 管理员进行身份验证，使用 SAML 令牌或 JWT 将其发送回你的解决方案，然后在你的解决方案中为他们授权。

### <a name="the-it-administrator-integrates-applications-with-azure-ad-by-using-your-solution"></a>IT 管理员使用你的解决方案将应用程序与 Azure AD 集成

IT 管理员的第二段旅程是使用你的解决方案将应用程序与 Azure AD 集成。 为此，你的解决方案将使用 Microsoft Graph 来创建应用程序注册和 Azure AD 条件访问策略。

下面是此用户身份验证流的示意图和摘要：

![用户身份验证流中的重定向示意图，以及 IT 管理员、Azure Active Directory、你的解决方案和 Microsoft Graph 之间的其他交互。](./media/secure-hybrid-access-integrations/registration-flow.png)


1. IT 管理员需要使用其 Azure AD 凭据来登录到你的解决方案。

2. 你的解决方案通过 SAML 或 OIDC 登录请求将 IT 管理员重定向到 Azure AD。

3. Azure AD 对 IT 管理员进行身份验证，使用 SAML 令牌或 JWT 将其发送回你的解决方案，然后在你的解决方案中为他们授权。

4. 当 IT 管理员想要将他们的一个应用程序与 Azure AD 集成时，不必转到 Azure AD 门户，你的解决方案将使用他们的现有 JWT 来调用 Microsoft Graph 以注册该应用程序，或者对他们应用 Azure AD 条件访问策略。

### <a name="users-sign-in-to-the-applications-secured-by-your-solution-and-azure-ad"></a>用户登录到由你的解决方案和 Azure AD 保护的应用程序

当用户需要登录到由你的解决方案和 Azure AD 保护的各个应用程序时，他们将使用 OIDC 或 SAML。 如果应用程序需要与 Microsoft Graph 或任何受 Azure AD 保护的 API 交互，我们建议将它们配置为使用 OICD。 此配置将确保应用程序从 Azure AD 获取的用于进行身份验证的 JWT 也可用于与 Microsoft Graph 交互。 如果各个应用程序无需与 Microsoft Graph 或任何受 Azure AD 保护的 API 交互，则使用 SAML 就足够了。

下面是此用户身份验证流的示意图和摘要：

![用户身份验证流中的重定向示意图，以及用户、Azure Active Directory、你的解决方案和应用程序之间的其他交互。](./media/secure-hybrid-access-integrations/end-user-flow.png)

1. 用户想要登录到由你的解决方案和 Azure AD 保护的应用程序。
2. 你的解决方案通过 SAML 或 OIDC 登录请求将用户重定向到 Azure AD。
3. Azure AD 对用户进行身份验证，使用 SAML 令牌或 JWT 将其发送回你的解决方案，然后在你的解决方案中为他们授权。
4. 授权后，你的解决方案将使用应用程序的首选协议允许对应用程序的原始请求完成。

## <a name="summary-of-microsoft-graph-apis"></a>Microsoft Graph API 摘要

你的解决方案需要使用以下 API。 Azure AD 允许配置委托的权限或应用程序权限。 对于此解决方案，只需要委托的权限。

- [应用程序模板 API](/graph/application-saml-sso-configure-api#retrieve-the-gallery-application-template-identifier/)：如果要搜索 Azure 市场，可以使用此 API 来查找匹配的应用程序模板。 所需的权限：Application.Read.All。

- [应用程序注册 API](/graph/api/application-post-applications)：使用此 API 可创建 OIDC 或 SAML 应用程序注册，使用户能够登录到客户已使用你的解决方案进行保护的应用程序。 这样做会使这些应用程序也受 Azure AD 保护。 所需的权限：Application.Read.All 和 Application.ReadWrite.All。

- [服务主体 API](/graph/api/serviceprincipal-update)：注册应用后，需要更新服务主体对象，以设置某些 SSO 属性。 所需的权限：Application.ReadWrite.All、Directory.AccessAsUser.All 和 AppRoleAssignment.ReadWrite.All（用于分配）。

- [条件访问 API](/graph/api/resources/conditionalaccesspolicy)：如果还要将 Azure AD 条件访问策略应用于这些用户应用程序，可以使用此 API。 所需的权限：Policy.Read.All、Policy.ReadWrite.ConditionalAccess 和 Application.Read.All。

## <a name="example-graph-api-scenarios"></a>示例图形 API 方案

本部分提供了一些参考示例，介绍如何使用 Microsoft Graph API 通过你的解决方案实现应用程序注册、连接旧版应用程序以及启用条件访问策略。 本部分还提供了有关实现管理员同意自动化、获取令牌签名证书以及分配用户和组的指导。 此功能在你的解决方案中可能很有用。

### <a name="use-the-graph-api-to-register-apps-with-azure-ad"></a>使用图形 API 向 Azure AD 注册应用

#### <a name="add-apps-that-are-in-azure-marketplace"></a>添加 Azure 市场中的应用

客户使用的某些应用程序已在 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps)中提供。 可以创建一个解决方案，以编程方式将这些应用程序添加到客户的租户。 以下代码示例使用 Microsoft Graph API 在 Azure 市场中搜索匹配的模板，然后在客户的 Azure AD 租户中注册应用程序。

在 Azure 市场中搜索匹配的应用程序。 使用应用程序模板 API 时，显示名称区分大小写。

```http
Authorization: Required with a valid Bearer token
Method: Get

https://graph.microsoft.com/v1.0/applicationTemplates?$filter=displayname eq "Salesforce.com"
```

如果前一 API 调用找到了匹配项，则捕获 ID，然后发出以下 API 调用，同时在 JSON 正文中提供应用程序的用户友好的显示名称：

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/cd3ed3de-93ee-400b-8b19-b61ef44a0f29/instantiate
{
    "displayname": "Salesforce.com"
}
```

发出上述 API 调用时，还将生成一个服务主体对象，这可能需要几秒钟的时间。 确保捕获应用程序 ID 和服务主体 ID。 将在后续的 API 调用中使用这些 ID。

接下来，使用 SAML 协议和相应的登录 URL 来修补服务主体对象：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.salesforce.com"
}
```

最后，使用相应的重定向 URI 和标识符 URI 修补应用程序对象：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.salesforce.com"]},
    "identifierUris":["https://www.salesforce.com"]
}
```

#### <a name="add-apps-that-are-not-in-azure-marketplace"></a>添加不在 Azure 市场中的应用

如果在 Azure 市场中找不到匹配项，或者你只想集成自定义应用程序，可以使用以下模板 ID 在 Azure AD 中注册自定义应用程序：8adf8e6e-67b2-4cf2-a259-e3dc5476c621。 然后发出以下 API 调用，同时在 JSON 正文中提供应用程序的用户友好的显示名称：

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

发出上述 API 调用时，还将生成一个服务主体对象，这可能需要几秒钟的时间。 确保捕获应用程序 ID 和服务主体 ID。 将在后续的 API 调用中使用这些 ID。

接下来，使用 SAML 协议和相应的登录 URL 来修补服务主体对象：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

最后，使用相应的重定向 URI 和标识符 URI 修补应用程序对象：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="cut-over-to-azure-ad-single-sign-on"></a>切换为 Azure AD 单一登录

在 Azure AD 中注册 SaaS 应用程序后，仍需要将应用程序切换为开始使用 Azure AD 作为其标识提供者。 有两种方法可以实现此目的：

- 如果应用程序支持一键式 SSO，则 Azure AD 可以为客户切换应用程序。 客户只需转到 Azure AD 门户，并使用受支持 SaaS 应用程序的管理凭据执行一键式 SSO 即可。 有关详细信息，请参阅[单一登录的一键式应用配置](/azure/active-directory/manage-apps/one-click-sso-tutorial/)。
- 如果应用程序不支持一键式 SSO，则客户需要手动切换应用程序才能开始使用 Azure AD。 有关详细信息，请参阅[有关将 SaaS 应用程序与 Azure Active Directory 集成的教程](/azure/active-directory/saas-apps/tutorial-list/)。

### <a name="connect-apps-by-using-legacy-authentication-methods-to-azure-ad"></a>使用旧身份验证方法将应用连接到 Azure AD

通过执行此操作，你的解决方案可以位于 Azure AD 和应用程序之间，并使客户能够从 SSO 及其他 Azure Active Directory 功能获益，甚至对于不受支持的应用程序也是如此。 为此，应用程序将调用 Azure AD 来对用户进行身份验证，并应用 Azure AD 条件访问策略，然后用户才能使用旧式协议访问这些应用程序。

使客户能够直接从控制台执行此集成，使发现和集成成为无缝的端到端体验。 这将涉及在你的平台和 Azure AD 之间创建 SAML 或 OIDC 应用程序注册。

#### <a name="create-a-saml-application-registration"></a>创建 SAML 应用程序注册

若要创建 SAML 应用程序注册，请为自定义应用程序使用以下自定义应用程序模板 ID：8adf8e6e-67b2-4cf2-a259-e3dc5476c621。 然后发出以下 API 调用，同时在 JSON 正文中提供用户友好的显示名称：

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

发出上述 API 调用时，还将生成一个服务主体对象，这可能需要几秒钟的时间。 确保捕获应用程序 ID 和服务主体 ID。 将在后续的 API 调用中使用这些 ID。

接下来，使用 SAML 协议和相应的登录 URL 来修补服务主体对象：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

最后，使用相应的重定向 URI 和标识符 URI 修补应用程序对象：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="create-an-oidc-application-registration"></a>创建 OIDC 应用程序注册

若要创建 OIDC 应用程序注册，请为自定义应用程序使用以下模板 ID：8adf8e6e-67b2-4cf2-a259-e3dc5476c621。 然后发出以下 API 调用，同时在 JSON 正文中提供用户友好的显示名称：

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom OIDC App"
}
```

从 API 调用中，捕获应用程序 ID 和服务主体 ID。 将在后续的 API 调用中使用这些 ID。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/{Application Object ID}
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["[https://www.samlapp.com"],
    "requiredResourceAccess": [
    {
        "resourceAppId": "00000003-0000-0000-c000-000000000000",
        "resourceAccess": [
        {
            "id": "7427e0e9-2fba-42fe-b0c0-848c9e6a8182",
            "type": "Scope"
        },
        {
            "id": "e1fe6dd8-ba31-4d61-89e7-88639da4683d",
            "type": "Scope"
        },
        {
            "id": "37f7f235-527c-4136-accd-4a02d197296e",
            "type": "Scope"
        }]
    }]
}
```

> [!NOTE]
> 在 `resourceAccess` 节点中列出的 API 权限将向应用程序授予 openid、User.Read 和 offline_access 权限，这些权限应该足以使用户能够登录到你的解决方案  。 有关权限的详细信息，请参阅 [Microsoft Graph 权限参考](/graph/permissions-reference/)。

### <a name="apply-conditional-access-policies"></a>应用条件访问策略

客户与合作伙伴也能使用 Microsoft Graph API 来创建条件访问策略或者将条件访问策略应用到客户的应用程序。 对于合作伙伴，这可以提供附加价值，因为客户可以直接从你的解决方案中应用这些策略，而无需转到 Azure AD 门户。 

在应用 Azure AD 条件访问策略时，有两个选项可用：

- 将应用程序分配到现有条件访问策略。
- 创建新的条件访问策略，并将应用程序分配到该新策略。

#### <a name="use-an-existing-conditional-access-policy"></a>使用现有条件访问策略

首先运行以下查询，以获取所有条件访问策略的列表。 获取要修改的策略的对象 ID。

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

接下来，修补该策略，具体方法是在 JSON 正文中将应用程序对象 ID 包括到 `includeApplications` 的范围内：

```https
Authorization: Required with a valid Bearer token
Method: PATCH

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
{
    "displayName":"Existing CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": 
        {
            "includeApplications":[
                "00000003-0000-0ff1-ce00-000000000000", 
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": 
    {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

#### <a name="create-a-new-conditional-access-policy"></a>创建新的条件访问策略

在 JSON 正文中将应用程序对象 ID 添加到 `includeApplications` 的范围内：

```https
Authorization: Required with a valid Bearer token
Method: POST

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/
{
    "displayName":"New CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

如果要创建新的 Azure AD 条件访问策略，以下是其他一些模板，这些模板可帮助你开始使用[条件访问 API](/azure/active-directory/conditional-access/howto-conditional-access-apis/)：

```https
#Policy Template for Requiring Compliant Device

{
    "displayName":"Enforce Compliant Device",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "compliantDevice",
            "domainJoinedDevice"
        ]
    }
}

#Policy Template for Block

{
    "displayName":"Block",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "block"
        ]
    }
}
```

### <a name="automate-admin-consent"></a>管理员同意自动化

如果客户从你的平台将大量应用程序加入到 Azure AD，你可以将管理员同意自动化，使他们无需手动同意大量应用程序。 也可以通过 Microsoft Graph 实现这种自动化。 需要在先前的 API 调用中创建的应用程序的服务主体对象 ID，以及客户的租户中 Microsoft Graph 的服务主体对象 ID。

通过发出以下 API 调用来获取 Microsoft Graph 的服务主体对象 ID：

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/serviceprincipals/?$filter=appid eq '00000003-0000-0000-c000-000000000000'&$select=id,appDisplayName
```

准备好将管理员同意自动化后，发出以下 API 调用：

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/oauth2PermissionGrants
{
    "clientId":"{Service Principal Object ID of Application}",
    "consentType":"AllPrincipals",
    "principalId":null,
    "resourceId":"{Service Principal Object ID Of Microsoft Graph}",
    "scope":"openid user.read offline_access}"
}
```

### <a name="get-the-token-signing-certificate"></a>获取令牌签名证书

若要获取所有这些应用程序的令牌签名证书的公共部分，可以从应用程序的 Azure AD 元数据终结点使用 `GET`：

```https
Method:GET

https://login.microsoftonline.com/{Tenant_ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Application_ID}
```

### <a name="assign-users-and-groups"></a>分配用户和组

将应用程序发布到 Azure AD 后，可以选择性地将应用程序分配给用户和组，以确保它显示在 [MyApplications](/azure/active-directory/user-help/my-applications-portal-workspaces/) 门户中。 此分配存储在创建应用程序时生成的服务主体对象中。

首先，获取可能与应用程序关联的任何 `AppRole` 实例。 SaaS 应用程序通常与多个 `AppRole` 实例相关联。 对于自定义应用程序，通常只有一个默认的 `AppRole` 实例。 获取要分配的 `AppRole` 实例的 ID：

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
```

接下来，获取要从 Azure AD 中分配到应用程序的用户或组的对象 ID。 此外，还需要从先前的 API 调用获取应用角色 ID，并作为服务主体的 patch 正文的一部分提交该 ID：

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "principalId":"{Principal Object ID of User -or- Group}",
    "resourceId":"{Service Principal Object ID}",
    "appRoleId":"{App Role ID}"
}
```

## <a name="partnerships"></a>合作关系

Microsoft 已与以下应用程序传送控制器 (ADC) 提供商建立了合作关系，以帮助保护旧版应用程序，同时使用现有网络和传送控制器。

| ADC 提供商 | **链接**。 |
| --- | --- |
| Akamai 企业应用程序访问 | [https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial](/azure/active-directory/saas-apps/akamai-tutorial) |
| Citrix ADC | [https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial](/azure/active-directory/saas-apps/citrix-netscaler-tutorial) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration](/azure/active-directory/manage-apps/f5-aad-integration) |
| Kemp LoadMaster | [https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial](/azure/active-directory/saas-apps/kemp-tutorial) |
| 脉冲安全虚拟流量管理器 | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial](/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial) |

以下 VPN 解决方案提供商与 Azure AD 相连接，以启用 SSO 和多重身份验证等新式身份验证和授权方法。

| VPN 供应商 | **链接** |
| --- | --- |
| Cisco AnyConnect | [https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect](/azure/active-directory/saas-apps/cisco-anyconnect) |
| Fortinet FortiGate | [https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial](/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn](/azure/active-directory/manage-apps/f5-aad-password-less-vpn) |
| 帕洛阿尔托网络全局保护 | [https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial](/azure/active-directory/saas-apps/paloaltoadmin-tutorial) |
| Pulse Connect Secure | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial](/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial) |

以下软件定义边界 (SDP) 解决方案提供商与 Azure AD 相连接，以启用 SSO 和多重身份验证等新式身份验证和授权方法。

| SDP 供应商 | **链接**。 |
| --- | --- |
| Datawiza Access Broker | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/datawiza-with-azure-ad](/azure/active-directory/manage-apps/datawiza-with-azure-ad) |
| Perimeter 81 | [https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial](/azure/active-directory/saas-apps/perimeter-81-tutorial) |
| Silverfort Authentication Platform | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/silverfort-azure-ad-integration](/azure/active-directory/manage-apps/silverfort-azure-ad-integration) |
| Strata Maverics Identity Orchestrator | [https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial](/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial) |
| Zscaler Private Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial](/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) |
