---
title: 单页应用登录和注销
titleSuffix: Microsoft identity platform
description: 了解如何生成单页应用程序（登录）
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: fd160749fef327efa21094ba69daf2db88449918
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357474"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>单页应用程序：登录和注销

了解如何将登录添加到单页应用程序的代码中。

在应用程序中获取用于访问 API 的令牌之前，需要经身份验证的用户上下文。 可以在 MSAL.js 中以两种方式将用户登录到应用程序：

* [弹出窗口](#sign-in-with-a-pop-up-window)（使用 `loginPopup` 方法）
* [重定向](#sign-in-with-redirect)（使用 `loginRedirect` 方法）

也可选择传递 API 的作用域，该作用域需在用户登录时获得用户许可。

> [!NOTE]
> 如果应用程序已经可以访问经身份验证的用户上下文或 ID 令牌，则可跳过登录步骤，直接获取令牌。 有关详细信息，请参阅[不使用 MSAL.js 登录名进行 SSO](msal-js-sso.md#sso-without-msaljs-login)。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>在弹出窗口或重定向体验之间进行选择

在弹出窗口和重定向体验之间进行的选择取决于应用程序流：

* 如果不希望用户在身份验证期间离开主应用程序页，建议使用弹出窗口方法。 由于身份验证重定向发生在弹出窗口中，系统会保留主应用程序的状态。

* 如果用户的浏览器约束或策略禁用了弹出窗口，则可使用重定向方法。 请对 Internet Explorer 浏览器使用重定向方法，因为 [Internet Explorer 上具有弹出窗口的已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)。

## <a name="sign-in-with-a-pop-up-window"></a>通过弹出窗口登录

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        accountId = loginResponse.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

借助 MSAL Angular 包装器，可通过将 `MsalGuard` 添加到路由定义来确保应用程序中特定路由的安全。 访问该路由时，此防护会调用用于登录的方法。

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
    {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [
            MsalGuard
        ]
    },
    {
        path: '',
        component: HomeComponent
    }
];

@NgModule({
    imports: [RouterModule.forRoot(routes, { useHash: false })],
    exports: [RouterModule]
})
export class AppRoutingModule { }
```

对于弹出窗口体验，请在“防护配置”中将 `interactionType` 配置设置为 `InteractionType.Popup`。 也可传递需要许可的作用域，如下所示：

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Popup, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
借助 MSAL Angular 包装器，可通过将 `MsalGuard` 添加到路由定义来确保应用程序中特定路由的安全。 访问该路由时，此防护会调用用于登录的方法。
```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

对于弹出窗口体验，请启用 `popUp` 配置选项。 也可传递需要许可的作用域，如下所示：

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```

# <a name="react"></a>[React](#tab/react)

MSAL React 包装器使你可以通过将特定组件包装在 `MsalAuthenticationTemplate` 组件中来对其进行保护。 如果用户尚未登录，则此组件将调用登录名，否则将呈现子组件。

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Popup}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

还可以直接使用 `@azure/msal-browser` API 来调用与 `AuthenticatedTemplate` 和/或 `UnauthenticatedTemplate` 组件配对的登录名，以分别向登录或注销的用户呈现特定的内容。 如果由于用户交互（例如单击按钮）而需要调用登录名，则建议使用此方法。

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginPopup();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-in-with-redirect"></a>使用重定向登录

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    if (response !== null) {
        accountId = response.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    } else {
        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();

        if (currentAccounts.length === 0) {
            // no accounts signed-in, attempt to sign a user in
            myMsal.loginRedirect(loginRequest);
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            accountId = currentAccounts[0].homeAccountId;
        }
    }
}

myMsal.handleRedirectPromise().then(handleResponse);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

重定向方法不会返回承诺，因为已从主应用离开。 若要处理并访问返回的令牌，请在调用重定向方法之前注册成功和错误回叫。

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

此处的代码与前面有关使用弹出窗口登录的部分中所述的代码相同，不同之处在于，将 MsalGuard 配置的 `interactionType` 设置为了 `InteractionType.Redirect`，并且启动了 `MsalRedirectComponent` 来处理重定向。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

此处的代码与本部分之前所述关于使用弹出窗口登录的代码相同。 默认流为重定向。

# <a name="react"></a>[React](#tab/react)

MSAL React 包装器使你可以通过将特定组件包装在 `MsalAuthenticationTemplate` 组件中来对其进行保护。 如果用户尚未登录，则此组件将调用登录名，否则将呈现子组件。

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Redirect}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

还可以直接使用 `@azure/msal-browser` API 来调用与 `AuthenticatedTemplate` 和/或 `UnauthenticatedTemplate` 组件配对的登录名，以分别向登录或注销的用户呈现特定的内容。 如果由于用户交互（例如单击按钮）而需要调用登录名，则建议使用此方法。

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginRedirect();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-popup-window"></a>使用弹出窗口进行注销

MSAL.js v2 提供了一种 `logoutPopup` 方法，该方法可清除浏览器存储中的缓存，并打开一个指向 Azure Active Directory (Azure AD) 注销页面的弹出窗口。 注销后，Azure AD 会将弹出窗口重定向回应用程序，而 MSAL.js 将关闭弹出窗口。

可以通过设置 `postLogoutRedirectUri` 来配置此 URI（在注销后 Azure AD 应重定向到此 URI）。 此 URI 应在应用程序注册中注册为重定向 URI。

还可配置 `logoutPopup`，在注销完成后将 `mainWindowRedirectUri` 作为请求的一部分进行传递，以将主窗口重定向到其他页面，例如主页或登录页面。

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", // defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId),
    mainWindowRedirectUri: "your_app_main_window_redirect_uri"
}

await myMsal.logoutPopup(logoutRequest);
```
# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

MSAL.js v1 中不支持使用弹出窗口进行注销

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutPopup({
        mainWindowRedirectUri: "/"
    });
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

MSAL Angular v1 中不支持使用弹出窗口进行注销

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        mainWindowRedirectUri: "your_app_main_window_redirect_uri",
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutPopup(logoutRequest);
}

// SignOutButton Component returns a button that invokes a popup logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-redirect"></a>使用重定向进行注销

MSAL.js 在 v1 中提供了 `logout` 方法，在 v2 中提供了 `logoutRedirect` 方法，该方法可清除浏览器存储中的缓存，并将窗口重定向到 Azure Active Directory (Azure AD) 注销页面。 注销后，Azure AD 重定向回默认情况下调用注销的页面。

可以通过设置 `postLogoutRedirectUri` 来配置此 URI（在注销后应该重定向到此 URI）。 此 URI 应在应用程序注册中注册为重定向 URI。

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId)
}

myMsal.logoutRedirect(logoutRequest);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutRedirect();
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})
// In app.component.ts
this.authService.logout();
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutRedirect(logoutRequest);
}

// SignOutButton Component returns a button that invokes a redirect logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[获取应用的令牌](scenario-spa-acquire-token.md)。
