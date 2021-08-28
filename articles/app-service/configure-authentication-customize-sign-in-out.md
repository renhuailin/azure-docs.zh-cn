---
title: 自定义登录和注销
description: 在应用服务中使用内置的身份验证和授权，同时自定义登录和注销行为。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: bdb100afa821aa08fb831aac53b1eb80cdda043e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429541"
---
# <a name="customize-sign-in-and-sign-out-in-azure-app-service-authentication"></a>在 Azure 应用服务身份验证中自定义登录和注销

本文介绍如何在使用[应用服务中的内置身份验证和授权](overview-authentication-authorization.md)时自定义用户登录和注销。 

## <a name="use-multiple-sign-in-providers"></a>使用多个登录提供程序

门户配置不会向用户全面提供多个登录提供程序（例如 Facebook 和 Twitter）。 但是，将此功能添加到应用并不困难。 步骤概括如下：

首先，在 Azure 门户中的“身份验证/授权”页上，配置想要启用的每个标识提供者。 

在“请求未经身份验证时需执行的操作”中，选择“允许匿名请求(无操作)”。  

在登录页、导航栏或应用的其他任何位置中，将一个登录链接添加到已启用的每个提供程序 (`/.auth/login/<provider>`)。 例如：

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

当用户单击其中一个链接时，系统会打开相应的登录页让用户登录。

若要将登录后用户重定向到自定义 URL，请使用 `post_login_redirect_uri` 查询字符串参数（不要与标识提供者配置中的重定向 URI 混淆）。 例如，若要在登录后将用户导航至 `/Home/Index`，使用以下 HTML 代码：

```html
<a href="/.auth/login/<provider>?post_login_redirect_uri=/Home/Index">Log in</a>
```

## <a name="client-directed-sign-in"></a>客户端主导的登录

在客户端主导的登录中，应用程序使用特定于提供者的 SDK 将用户登录到标识提供者。 然后，应用程序代码使用 HTTP POST 请求将生成的身份验证令牌提交给应用服务进行验证（请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)）。 [Azure 移动应用 SDK](https://github.com/Azure/azure-mobile-apps) 使用此登录流。 此验证本身不实际向你授予对所需应用资源的访问权限，但成功的验证会向你提供一个会话令牌，可以使用该令牌来访问应用资源。

若要验证提供程序令牌，必须首先为应用服务应用配置所需的提供程序。 在运行时，从你的提供程序检索身份验证令牌后，将令牌发布到 `/.auth/login/<provider>` 进行验证。 例如：

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

令牌格式根据提供程序而略有不同。 有关详细信息，请参阅下表：

| 提供程序值 | 请求正文中必需的 | 注释 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | `id_token`、`refresh_token` 和 `expires_in` 属性是可选项。 |
| `microsoftaccount` | `{"access_token":"<access_token>"}` 或 `{"authentication_token": "<token>"`| `authentication_token` 优先于 `access_token`。 `expires_in` 属性为可选。 <br/> 从 Live 服务请求令牌时，将始终请求 `wl.basic` 作用域。 |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` 属性为可选。 提供 `authorization_code` 值会将访问令牌和刷新令牌添加到令牌存储中。 指定 `authorization_code` 时，还可以选择同时指定 `redirect_uri` 属性。 |
| `facebook`| `{"access_token":"<user_access_token>"}` | 使用来自 Facebook 的有效[用户访问令牌](https://developers.facebook.com/docs/facebook-login/access-tokens)。 |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

如果提供程序令牌成功通过验证，则 API 将在响应正文中返回一个 `authenticationToken`，这是你的会话令牌。 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

获得此会话令牌后，你可以通过向 HTTP 请求中添加 `X-ZUMO-AUTH` 标头来访问受保护的应用资源。 例如： 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>注销会话

用户可通过向应用的 `/.auth/logout` 终结点发送 `GET` 请求来启动注销。 `GET` 请求可执行以下操作：

- 清除当前会话中的身份验证 Cookie。
- 从令牌存储中删除当前用户的令牌。
- 对于 Azure Active Directory 和 Google，请对标识提供程序执行服务器端的注销。

以下是网页中一个简单的注销链接：

```html
<a href="/.auth/logout">Sign out</a>
```

默认情况下，成功注销后，客户端会重定向到 URL `/.auth/logout/done`。 通过添加 `post_logout_redirect_uri` 查询参数可更改注销后的重定向页面。 例如：

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

建议对 `post_logout_redirect_uri` 的值进行[编码](https://wikipedia.org/wiki/Percent-encoding)。

使用完全限定的 URL 时，URL 必须托管在同一域中，或配置为允许应用访问的外部重定向 URL。 在以下示例中，若要重定向到未托管在同一域中的 `https://myexternalurl.com`：

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令：

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>保留 URL 片段

用户登录应用后，通常希望会重定向到同一页面的同一部分，例如 `/wiki/Main_Page#SectionZ`。 然而，由于从未向服务器发送 [URL 片段](https://wikipedia.org/wiki/Fragment_identifier)（例如，`#SectionZ`），因此默认情况下，OAuth 登录完成并重定向回应用后，会保留这些片段。 然后，当用户需再次导航到所需定位点时，他们无法获得最佳体验。 此限制存在于所有服务器端身份验证解决方案中。

在应用服务身份验证中，可跨 OAuth 登录保留 URL 片段。 为此，请将名为 `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` 的应用设置设为 `true`。 可在 [Azure 门户](https://portal.azure.com) 中执行此操作，或只需在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令：

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="limit-the-domain-of-sign-in-accounts"></a>限制登录帐户的域

Microsoft 帐户和 Azure Active Directory 都允许从多个域登录。 例如，Microsoft 帐户允许 _outlook.com_、_live.com_ 和 _hotmail.com_ 帐户。 Azure AD 允许对登录帐户使用任意数量的自定义域。 但是，建议将用户直接转到自己品牌的 Azure AD 登录页面（如 `contoso.com`）。 若要推荐登录帐户的域名，请执行以下步骤。

在 [https://resources.azure.com](https://resources.azure.com) 中，导航到“subscriptions > ** _\<subscription\_name_** > resourceGroups > \<resource\_group\_name> **_ _** > providers >  Microsoft.Web >  sites > **_ _**\<app\_name> > config >  authsettings ”。 

单击“编辑”，修改以下属性，然后单击“放置”。 请务必将 \<domain\_name> 替换为所需的域。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

此设置将 `domain_hint` 查询字符串参数追加到登录重定向 URL。 

> [!IMPORTANT]
> 接收重定向 URL 之后，客户端可能删除 `domain_hint` 参数，然后使用其他域登录。 所以虽然此功能非常方便，但它不是一项安全功能。
>

## <a name="authorize-or-deny-users"></a>授权或拒绝用户

尽管应用服务会处理最简单的授权问题（例如，拒绝未经身份验证的请求），但应用可能需要更精细的授权行为，例如，仅将访问权限限制给特定的一组用户。 在某些情况下，需要编写自定义应用程序代码以允许或拒绝已登录用户的访问。 在其他情况下，应用服务或标识提供者可能无需进行代码更改即可提供帮助。

- [服务器级别](#server-level-windows-apps-only)
- [标识提供者级别](#identity-provider-level)
- [应用程序级别](#application-level)

### <a name="server-level-windows-apps-only"></a>服务器级别（仅限 Windows 应用）

对于任何 Windows 应用，可以通过编辑 *Web.config* 文件来定义 IIS Web 服务器的授权行为。 Linux 应用不使用 IIS，无法通过 Web.config 进行配置。

1. 导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. 在打开应用服务文件的浏览器资源管理器中，导航到“site/wwwroot”。  如果 *Web.config* 不存在，请选择“+” > “新建文件”来创建该文件。   

1. 选择“Web.config”旁边的铅笔图标对其进行编辑。  添加以下配置代码，然后单击“保存”。  如果 *Web.config* 已存在，则只需在其中添加包含任何内容的 `<authorization>` 元素即可。 在 `<allow>` 元素中添加要允许的帐户。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>标识提供者级别

标识提供者可能提供某种统包授权。 例如：

- 对于 [Azure 应用服务](configure-authentication-provider-aad.md)，你可以直接在 Azure AD 中[管理企业级访问权限](../active-directory/manage-apps/what-is-access-management.md)。 有关说明，请参阅[如何删除用户对应用程序的访问权限](../active-directory/manage-apps/methods-for-removing-user-access.md)。
- 对于 [Google](configure-authentication-provider-google.md)，可以将属于[组织](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations)的 Google API 项目配置为仅允许组织中的用户访问（请参阅 [Google 的“设置 OAuth 2.0”支持页](https://support.google.com/cloud/answer/6158849?hl=en)）。

### <a name="application-level"></a>应用程序级别

如果其他任何级别不提供所需的授权，或者平台或标识提供者不受支持，则必须编写自定义代码，以基于[用户声明](configure-authentication-user-identities.md)为用户授权。

## <a name="more-resources"></a>更多资源

- [教程：对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
- [用于身份验证的环境变量和应用设置](reference-app-settings.md#authentication--authorization)
