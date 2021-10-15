---
title: AuthN/AuthZ 中的 OAuth 标记
description: 了解在应用服务中使用内置身份验证和授权时，如何检索令牌和刷新令牌以及扩展会话。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f158f9f76820635a65737b75f3c016ff67a3a92a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352081"
---
# <a name="work-with-oauth-tokens-in-azure-app-service-authentication"></a>在 Azure 应用服务身份验证中使用 OAuth 标记

本文介绍如何在使用[应用服务中的内置身份验证和授权](overview-authentication-authorization.md)时使用 OAuth 标记。 

## <a name="retrieve-tokens-in-app-code"></a>检索应用代码中的令牌

在服务器代码中，提供程序特定的令牌将注入到请求标头中，使你可以轻松访问这些令牌。 下表显示了可能的令牌标头名称：

| 提供程序 | 标头名称 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 令牌 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

> [!NOTE]
> 不同的语言框架可能会以不同格式（例如小写或标题字首大写）向应用代码显示这些标头。

在客户端代码（例如移动应用或浏览器中 JavaScript）中，将 HTTP `GET` 请求发送到 `/.auth/me`（必须启用[令牌存储](overview-authentication-authorization.md#token-store)）。 返回的 JSON 包含提供程序特定的令牌。

> [!NOTE]
> 访问令牌用于访问提供程序资源，因此，仅当使用客户端机密配置了提供程序时，才提供这些令牌。 若要了解如何获取刷新令牌，请参阅“刷新访问令牌”。

## <a name="refresh-auth-tokens"></a>刷新身份验证令牌

当提供程序的访问令牌（而不是[会话令牌](#extend-session-token-expiration-grace-period)）到期时，需要在再次使用该令牌之前重新验证用户。 向应用程序的 `/.auth/refresh` 终结点发出 `GET` 调用可以避免令牌过期。 调用应用服务时，应用服务会自动刷新已经过身份验证用户的[令牌存储](overview-authentication-authorization.md#token-store)中的访问令牌。 应用代码发出的后续令牌请求将获取刷新的令牌。 但是，若要正常刷新令牌，令牌存储必须包含提供程序的[刷新令牌](https://auth0.com/learn/refresh-tokens/)。 每个提供程序会阐述获取刷新令牌的方式。以下列表提供了简短摘要：

- **Google**：将一个 `access_type=offline` 查询字符串参数追加到 `/.auth/login/google` API 调用。 如果使用移动应用 SDK，可将该参数添加到 `LogicAsync` 重载之一（请参阅 [Google 刷新令牌](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)）。
- **Facebook**：不提供刷新令牌。 生存期较长的令牌在 60 天后过期（请参阅 [Facebook 访问令牌的过期和延期](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)）。
- **Twitter**：访问令牌不会过期（请参阅 [Twitter OAuth 常见问题解答](https://developer.twitter.com/en/docs/authentication/faq)）。
- Microsoft：在 [https://resources.azure.com](https://resources.azure.com) 中，执行以下步骤：
    1. 在页面顶部，选择“读/写”。
    2. 在左侧浏览器中，导航到“subscriptions > **\<subscription\_name_** > resourceGroups > ****\<resource\_group\_name> > providers > Microsoft.Web > sites > **_**\<app\_name> > config > authsettingsV2” __ 。
    3. 单击 **“编辑”** 。
    4. 修改以下属性。

        ```json
        "identityProviders": {
          "azureActiveDirectory": {
            "login": {
              "loginParameters": ["scope=openid profile email offline_access"]
            }
          }
        }
        ```

    5. 单击“放置”。
    
    > [!NOTE]
    > 提供刷新令牌的范围为 [offline_access](../active-directory/develop/v2-permissions-and-consent.md#offline_access)。 请在[教程：在 Azure 应用服务中对用户进行端到端身份验证和授权](tutorial-auth-aad.md)中了解它的使用方法。 应用服务默认已请求其他范围。 有关这些默认范围的信息，请参阅 [OpenID Connect 范围](../active-directory/develop/v2-permissions-and-consent.md#openid-connect-scopes)。

配置提供程序以后，即可在令牌存储中[找到访问令牌的刷新令牌和过期时间](#retrieve-tokens-in-app-code)。 

若要随时刷新访问令牌，只需以任何语言调用 `/.auth/refresh`。 以下代码片段从 JavaScript 客户端使用 jQuery 刷新访问令牌。

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

如果用户撤销了授予应用的权限，对 `/.auth/me` 的调用可能会失败并返回 `403 Forbidden` 响应。 若要诊断错误，请查看应用程序日志了解详细信息。

## <a name="extend-session-token-expiration-grace-period"></a>延长会话令牌过期宽限期

经过身份验证的会话会在 8 小时后过期。 经过身份验证的会话过期后，默认会提供 72 小时的宽限期。 在此宽限期内，可以使用应用服务刷新会话令牌，而无需重新对用户进行身份验证。 会话令牌失效后，只需调用 `/.auth/refresh`，而不需要自行跟踪令牌过期时间。 72 小时的宽限期过后，用户必须重新登录才能获取有效的会话令牌。

如果 72 小时的时间不够，可以延长此过期期限。 大大延长过期时间可能会造成严重的安全风险（例如身份验证令牌泄密或被盗）。 因此，应将宽限期保留为默认 72 小时，或者将延期设为最小值。

若要延长默认的过期期限，请在 [Cloud Shell](../cloud-shell/overview.md) 中运行以下命令。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 宽限期仅适用于应用服务的已经身份验证的会话，而不适用于来自标识提供者的令牌。 已过期的提供程序令牌没有宽限期。 
>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
