---
title: 使用资源所有者密码凭据授予进行登录 | Azure
titleSuffix: Microsoft identity platform
description: 支持使用资源所有者密码凭据 (ROPC) 授予的无浏览器身份验证流。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2ee33ec1ff87a73e31e55f06fe70672314384a6e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059466"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft 标识平台和 OAuth 2.0 资源所有者密码凭据

Microsoft 标识平台支持 [OAuth 2.0 资源所有者密码凭据 (ROPC) 授权](https://tools.ietf.org/html/rfc6749#section-4.3)，后者允许应用程序通过直接处理用户的密码来登录用户。  本文介绍如何在应用程序中直接针对协议进行编程。  如果可能，建议改为使用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

> [!WARNING]
> Microsoft 建议不要使用 ROPC 流。 在大多数情况下，可以使用我们建议的更安全的替代方案。 此流需要应用程序中存在很高程度的信任，并且带有在其他流中不存在的风险。 仅当无法使用其他更安全的流时，才使用此流。


> [!IMPORTANT]
>
> * Microsoft 标识平台仅支持将 ROPC 用于 Azure AD 租户而非个人帐户。 这意味着，必须使用特定于租户的终结点 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 或 `organizations` 终结点。
> * 受邀加入 Azure AD 租户的个人帐户不能使用 ROPC。
> * 没有密码的帐户无法使用 ROPC 登录，这意味着 SMS 登录、FIDO 等功能以及 Authenticator 应用都不可用于该流。 如果你的应用或用户需要这些功能，请使用 ROPC 以外的流。
> * 如果用户需使用[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md) 来登录应用程序，则系统会改为阻止用户。
> * [混合联合身份验证](../hybrid/whatis-fed.md)方案（例如，用于对本地帐户进行身份验证的 Azure AD 和 ADFS）不支持 ROPC。 如果用户被整页重定向到本地标识提供程序，Azure AD 无法针对该标识提供程序测试用户名和密码。 不过，ROPC 支持[传递身份验证](../hybrid/how-to-connect-pta.md)。
> * 混合联合身份身份验证方案的一种例外情况如下：当本地密码同步到云时，将 AllowCloudPasswordValidation 设置为 TRUE 时，Home Realm Discovery 策略将启用 ROPC 流来处理联合用户。 有关详细信息，请参阅[为旧版应用程序启用对联合用户的直接 ROPC 身份验证](../manage-apps/home-realm-discovery-policy.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications)。

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>协议图

下图显示了 ROPC 流。

![显示资源所有者密码凭据流的关系图](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>授权请求

ROPC 流是单一请求：它将客户端标识和用户的凭据发送到 IDP，然后接收返回的令牌。 在这样做之前，客户端必须请求用户的电子邮件地址 (UPN) 和密码。 在成功进行请求之后，客户端应立即以安全方式释放内存中的用户凭据， 客户端不得保存客户凭据。

```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必选 | 一个目录租户，用户需登录到其中。 此参数可采用 GUID 或友好名称格式。 此参数不能设置为 `common` 或 `consumers`，但可以设置为 `organizations`。 |
| `client_id` | 必选 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给你的应用的应用程序（客户端）ID。 |
| `grant_type` | 必选 | 必须设置为 `password`。 |
| `username` | 必选 | 用户的电子邮件地址。 |
| `password` | 必选 | 用户的密码。 |
| `scope` | 建议 | 以空格分隔的[范围](v2-permissions-and-consent.md)或权限的列表，这是应用需要的。 在交互式流中，管理员或用户必须提前同意这些作用域。 |
| `client_secret`| 有时必需 | 如果应用是公共客户端，则无法包括 `client_secret` 或 `client_assertion`。  如果应用是机密客户端，则它必须包括在内。|
| `client_assertion` | 有时必需 | 使用证书生成的不同形式的 `client_secret`。  有关更多详细信息，请参阅[证书凭据](active-directory-certificate-credentials.md)。 |

> [!WARNING]
> 正式版 SDK 不支持机密客户端（即那些使用机密或断言的客户端）使用此流，这是不建议使用此流的一部分原因。 你可能会发现想要使用的 SDK 不允许你在使用 ROPC 时添加机密。 

### <a name="successful-authentication-response"></a>成功的身份验证响应

以下示例显示了一个成功的令牌响应：

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 参数 | 格式 | 说明 |
| --------- | ------ | ----------- |
| `token_type` | 字符串 | 始终设置为 `Bearer`。 |
| `scope` | 空格分隔的字符串 | 如果返回了访问令牌，则此参数会列出该访问令牌的有效范围。 |
| `expires_in`| int | 包含的访问令牌的有效时间，以秒为单位。 |
| `access_token`| 不透明字符串 | 针对请求的[范围](v2-permissions-and-consent.md)颁发。 |
| `id_token` | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。 |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。 |

可以运行 [OAuth 代码流文档](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中描述的同一个流，使用刷新令牌来获取新的访问令牌和刷新令牌。

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response"></a>错误响应

如果用户未提供正确的用户名或密码，或者客户端未收到请求的许可，则身份验证会失败。

| 错误 | 说明 | 客户端操作 |
|------ | ----------- | -------------|
| `invalid_grant` | 身份验证失败 | 凭据不正确，或者客户端没有所请求范围的许可。 如果没有授予范围，则会返回 `consent_required` 错误。 如果发生这种情况，客户端应通过 Webview 或浏览器向用户发送交互式提示。 |
| `invalid_request` | 请求的构造方式不正确 | 授予类型在 `/common` 或 `/consumers` 身份验证上下文中不受支持。  请改用 `/organizations` 或租户 ID。 |

## <a name="learn-more"></a>了解详细信息

有关使用 ROPC 的示例，请参阅 GitHub 上的 [.NET 核心控制台应用程序](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)代码示例。
