---
title: 使用 Azure Active Directory B2C 启用 Angular 应用程序选项
description: 通过多种方式启用 Angular 应用程序选项。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: c2b94a1a2659825314d45d9baeecaa11904dc527
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220725"
---
# <a name="configure-authentication-options-in-an-angular-application-by-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在 Angular 应用程序中配置身份验证选项

本文介绍自定义和增强 Angular 单页应用程序 (SPA) 的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验的方式。 在开始之前，请先熟悉以下文章：[在 Angular SPA 中配置身份验证](configure-authentication-sample-angular-spa-app.md)或[在你自己的 Angular SPA 中启用身份验证](enable-authentication-angular-spa-app.md)。


## <a name="sign-in-and-sign-out-behavior"></a>登录和注销行为


可以通过两种方式将单页应用程序配置为使用 MSAL.js 登录用户：

- 弹出窗口：身份验证在弹出窗口中发生，并保留应用程序的状态。 如果不希望用户在身份验证过程中离开应用程序页，请使用此方法。  请注意，[Internet Explorer 上的弹出窗口存在已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)。
  - 若要使用弹出窗口登录，请在 `src/app/app.component.ts` 类中使用 `loginPopup` 方法。  
  - 在 `src/app/app.module.ts` 类中，将 `interactionType` 属性设置为 `InteractionType.Popup`。
  - 若要使用弹出窗口注销，请在 `src/app/app.component.ts` 类中使用 `logoutPopup` 方法。 还可配置 `logoutPopup`，在注销完成后将 `mainWindowRedirectUri` 作为请求的一部分进行传递，以将主窗口重定向到其他页面，例如主页或登录页面。
- 重定向：将用户重定向到 Azure AD B2C 以完成身份验证流。 如果用户有禁用弹出窗口的浏览器限制或策略，请使用此方法。 
  - 若要使用重定向登录，请在 `src/app/app.component.ts` 类中使用 `loginRedirect` 方法。  
  - 在 `src/app/app.module.ts` 类中，将 `interactionType` 属性设置为 `InteractionType.Redirect`。
  - 若要使用重定向注销，请在 `src/app/app.component.ts` 类中使用 `logoutRedirect` 方法。 通过设置 `postLogoutRedirectUri` 来配置此 URI（在注销后应该重定向到此 URI）。 此 URI 应在应用程序注册中注册为重定向 URI。
  
以下示例演示了如何登录和注销：

#### <a name="pop-up"></a>[弹出](#tab/popup)


```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest);
  } else {
    this.authService.loginPopup();
  }
}

logout() { 
  this.authService.logoutPopup({
    mainWindowRedirectUri: '/',
  });
}
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
  } else {
    this.authService.loginRedirect();
  }
}

logout() { 
  this.authService.logoutRedirect({
    postLogoutRedirectUri: 'http://localhost:4200'
  });
}
```

---

MSAL Angular 库具有三个登录流：交互式登录（其中用户选择登录按钮）、MSAL Guard 和 MSAL 侦听器。 当用户尝试在没有有效访问令牌的情况下访问受保护的资源时，MSAL Guard 和 MSAL 侦听器配置会生效。 在这种情况下，MSAL 库会强制用户登录。 

以下示例演示如何配置 MSAL Guard 和 MSAL 侦听器，以使用弹出窗口或重定向登录： 

#### <a name="pop-up"></a>[弹出](#tab/popup)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Popup,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Redirect,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

---  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 创建或使用现有的 `PopupRequest` 或 `RedirectRequest` MSAL 配置对象。
1. 使用相应的登录提示设置 `loginHint` 属性。 

以下代码片段演示如何传递登录提示参数。 它们使用 `bob@contoso.com` 作为属性值。

#### <a name="pop-up"></a>[弹出](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

---  


[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 创建或使用现有的 `PopupRequest` 或 `RedirectRequest` MSAL 配置对象。
1. 使用相应的域提示设置 `domainHint` 属性。

以下代码片段演示如何传递域提示参数。 它们使用 `facebook.com` 作为属性值。

#### <a name="pop-up"></a>[弹出](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

---  

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [配置语言自定义](language-customization.md)。 
1. 创建或使用具有 `extraQueryParameters` 属性的现有 `PopupRequest` 或 `RedirectRequest` MSAL 配置对象。
1. 将具有相应语言代码的 `ui_locales` 参数添加到 `extraQueryParameters` 属性。

以下代码片段演示如何传递域提示参数。 它们使用 `es-es` 作为属性值。

#### <a name="pop-up"></a>[弹出](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

---   
 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. 配置 [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 元素。
1. 创建或使用具有 `extraQueryParameters` 属性的现有 `PopupRequest` 或 `RedirectRequest` MSAL 配置对象。
1. 添加自定义查询字符串参数（如 `campaignId`）。 设置参数值。 

以下代码片段演示如何传递自定义查询字符串参数。 它们使用 `germany-promotion` 作为属性值。

#### <a name="pop-up"></a>[弹出](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"campaignId" : "germany-promotion"}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 在自定义策略中，定义 [ID 令牌提示的技术配置文件](id-token-hint.md)。
1. 创建或使用具有 `extraQueryParameters` 属性的现有 `PopupRequest` 或 `RedirectRequest` MSAL 配置对象。
1. 使用可存储 ID 令牌的相应变量添加 `id_token_hint` 参数。

以下代码片段演示如何定义 ID 令牌提示：

#### <a name="pop-up"></a>[弹出](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

#### <a name="redirect"></a>[重定向](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};;

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

若要将自定义域用于身份验证 URL 中的租户 ID，请按照[启用自定义域](custom-domain.md)中的指南进行操作。 打开 `src/app/auth-config.ts` MSAL 配置对象并将 `authorities` 和 `knownAuthorities` 更改为使用自定义域名和租户 ID。  

以下 JavaScript 显示了更改前的 MSAL 配置对象： 

```typescript
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

以下 JavaScript 显示了更改后的 MSAL 配置对象： 

```typescript
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


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]

若要配置 Angular [日志记录](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/logging.md)，请在 src/app/auth-config.ts 中配置以下密钥：

- `loggerCallback` 是记录器回调函数。 
- `logLevel` 允许指定日志记录级别。 可能的值：`Error`、`Warning`、`Info` 和 `Verbose`。
- `piiLoggingEnabled` 启用个人数据的输入。 可能的值：`true` 或 `false`。
 
下面的代码片段演示如何配置 MSAL 日志记录：

```typescript
export const msalConfig: Configuration = {
  ...
  system: {
    loggerOptions: {
        loggerCallback: (logLevel, message, containsPii) => {  
            console.log(message);
          },
          logLevel: LogLevel.Verbose,
          piiLoggingEnabled: false
      }
  }
  ...
}
```

## <a name="next-steps"></a>后续步骤

- 详细了解：[MSAL.js 配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)。
