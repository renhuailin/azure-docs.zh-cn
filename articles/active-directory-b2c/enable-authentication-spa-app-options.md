---
title: 使用 Azure Active Directory B2C 启用 SPA 应用程序选项
description: 本文讨论了几种启用 SPA 应用程序的方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 675db9c9d0a21b805ce8b4edeaff1a3b29ae7bfd
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068250"
---
# <a name="configure-authentication-options-in-a-single-page-application-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在单页应用程序中配置身份验证选项

本文介绍如何自定义和增强单页应用程序 (SPA) 的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 

在开始之前，请先自行熟悉以下文章：[在示例 Web 应用程序中配置身份验证](configure-authentication-sample-spa-app.md)。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

若要在身份验证 URL 中使用自定义域和租户 ID，请按照[启用自定义域](custom-domain.md)中的指南进行操作。 找到 Microsoft 身份验证库 (MSAL) 配置对象并更改 authorities 和 knownAuthorities 来使用自定义域名和租户 ID 。

以下 JavaScript 代码显示了更改前的 MSAL 配置对象： 

```Javascript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

以下 JavaScript 代码显示了更改后的 MSAL 配置对象： 

```Javascript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 创建一个对象来存储 login_hint，并将该对象传递到 MSAL loginPopup() 方法中 。

    ```javascript
    let loginRequest = {
        loginHint: "bob@contoso.com"
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 创建一个对象来存储 extraQueryParameters，并将该对象传递到 MSAL loginPopup() 方法中 。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {domain_hint: 'facebook.com'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [配置语言自定义](language-customization.md)。
1. 创建一个对象来存储 extraQueryParameters，并将该对象传递到 MSAL loginPopup() 方法中 。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {ui_locales: 'en-us'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. 配置 [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 元素。
1. 创建一个对象来存储 extraQueryParameters，并将该对象传递到 MSAL loginPopup() 方法中 。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {campaignId: 'germany-promotion'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 在自定义策略中，定义 [ID 令牌提示技术配置文件](id-token-hint.md)。
1. 创建一个对象来存储 extraQueryParameters，并将该对象传递到 MSAL loginPopup() 方法中 。

    ```javascript
    let loginRequest = {
         extraQueryParameters: {id_token_hint: 'id-token-hint-value'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

## <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不管为应用程序指定的回复 URL 为何。 但是，如果传递了有效的 `id_token_hint` 并启用了“注销请求中需要 ID 令牌”，则在执行重定向之前，Azure AD B2C 将验证 `post_logout_redirect_uri` 的值是否与应用程序的某个已配置重定向 URI 相匹配。 如果没有为应用程序配置匹配的回复 URL，则会显示一条错误消息，而用户不会重定向。

若要支持安全的注销重定向 URI，请按照以下步骤进行操作：

1. 创建一个全局可访问的变量来存储 `id_token`。
    ```javascript
    let id_token = "";
    ```
    
1. 在 MSAL `handleResponse` 函数中，将 `authenticationResult` 对象中的 `id_token` 解析为 `id_token` 变量。
    ```javascript
    function handleResponse(response) {
        if (response !== null) {
            setAccount(response.account);
            id_token = response.idToken;
        } else {
            selectAccount();
        }
    }
    ```
    
1. 在 `signOut` 函数中，将 `id_token_hint` 参数添加到 logoutRequest 对象。
    ```javascript
    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
            //set id_token_hint to the id_token value
            idTokenHint : id_token,
            mainWindowRedirectUri: msalConfig.auth.redirectUri
        };
        myMSALObj.logoutPopup(logoutRequest);
    }
    ```

在上述示例中，传递到注销请求中的 post_logout_redirect_uri 的格式为 `https://your-app.com/`。 必须将此 URL 添加到应用程序注册的答复 URL。

## <a name="enable-single-logout"></a>启用单一注销

Azure AD B2C 中的单一注销使用 OpenId Connect 前通道注销向用户通过 Azure AD B2C 登录的所有应用程序发出注销请求。

这些注销请求是在隐藏的 Iframe 中从 Azure AD B2C 注销页面发出的。 Iframe 向为 Azure AD B2C 记录为已登录的应用注册的所有前通道注销终结点发出 HTTP 请求。 

每个应用程序的注销终结点必须调用 MSAL logout() 方法。 在这种情况下，你还需要通过将 `allowRedirectInIframe` 设置为 `true` 来将 MSAL 显式配置为在 Iframe 内运行。

以下代码示例将 `allowRedirectInIframe` 设置为 `true`：

```javascript
const msalConfig = {
    auth: {
        clientId: "enter_client_id_here",
        .....
    },
    cache: {
        cacheLocation: "..",
        ....
    },
    system: {
        allowRedirectInIframe: true
    };
}

async function logoutSilent(MSAL) {
   return MSAL.logout({
      onRedirectNavigate: (url) => {
         return false;
       }
```

## <a name="next-steps"></a>后续步骤

详细了解 [MSAL.js 配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)。
