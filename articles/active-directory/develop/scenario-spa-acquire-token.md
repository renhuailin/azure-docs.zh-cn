---
title: 获取用于调用 Web API 的令牌（单页应用程序）| Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建单页应用程序（获取用于调用 API 的令牌）
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/2/2021
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 2e4369c729fc5497f615f64c1f7235d19c293f98
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227733"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>单页应用程序：获取用于调用 API 的令牌

使用 [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) `acquireTokenSilent` 获取 API 的令牌时，其模式是首先使用 方法来尝试无提示令牌请求。 调用此方法时，该库会首先检查浏览器存储中的缓存，看是否存在有效的令牌，在有的情况下会将其返回。 缓存中没有有效令牌时，它会尝试使用其刷新令牌来获取令牌。 如果刷新令牌的 24 小时生存期已到期，MSAL.js 将打开一个隐藏的 iframe，以无提示的方式请求新的授权代码，该代码用于交换新的有效刷新令牌。 有关 Azure AD 中的单一登录会话和令牌生存期值的详细信息，请参阅[令牌生存期](active-directory-configurable-token-lifetimes.md)。

可能会因某些原因（例如密码更改，或者更新的条件访问策略）而导致以无提示方式向 Azure AD 请求令牌失败。  而失败更常见的原因是刷新令牌的 24 小时生存期已过期以及[浏览器阻止第三方缓存](reference-third-party-cookies-spas.md)，这会阻止使用隐藏的 iframe 继续对用户进行身份验证。  在这些情况下，应调用其中一个交互式方法（可能会提示用户）来获取令牌：

* [弹出窗口](#acquire-a-token-with-a-pop-up-window)，此方法使用 `acquireTokenPopup`
* [重定向](#acquire-a-token-with-a-redirect)，此方法使用 `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在弹出窗口或重定向体验之间进行选择

在弹出窗口和重定向体验之间进行的选择取决于应用程序流：

* 如果不希望用户在身份验证期间离开主应用程序页，建议使用弹出窗口方法。 由于身份验证重定向发生在弹出窗口中，系统会保留主应用程序的状态。

* 如果用户的浏览器约束或策略禁用了弹出窗口，则可使用重定向方法。 请对 Internet Explorer 浏览器使用重定向方法，因为 [Internet Explorer 上具有弹出窗口的已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)。

可以设置 API 作用域，在生成访问令牌请求时需要访问令牌包括这些作用域。 请注意，可能不会在访问令牌中授予所有请求的作用域。 具体取决于用户的许可。

## <a name="acquire-a-token-with-a-pop-up-window"></a>通过弹出窗口获取令牌

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

以下代码将前面描述的模式与弹出体验的方法结合起来：

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error instanceof InteractionRequiredAuthError) {
        publicClientApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
            let accessToken = accessTokenResponse.accessToken;
            // Call your API with token
            callApi(accessToken);
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

以下代码将前面描述的模式与弹出体验的方法结合起来：

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

MSAL Angular 包装器提供 HTTP 侦听器，后者会自动以无提示方式获取访问令牌并将其附加到针对 API 的 HTTP 请求。

可以在 `protectedResourceMap` 配置选项中指定 API 的作用域。 `MsalInterceptor` 将在自动获取令牌时请求这些作用域。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule } from '@azure/msal-angular';

@NgModule({
    declarations: [
        // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
        auth: {
            clientId: 'Enter_the_Application_Id_Here',
        },
        cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
        }
        }), {
            interactionType: InteractionType.Popup,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Popup,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

以无提示方式获取令牌时，不管是成功还是失败，MSAL Angular 都会提供可以订阅的事件。 此外还要记住取消订阅。

```javascript
import { MsalBroadcastService } from '@azure/msal-angular';
import { EventMessage, EventType } from '@azure/msal-browser';

// In app.component.ts
export class AppComponent implements OnInit {
    private readonly _destroying$ = new Subject<void>();

    constructor(private broadcastService: MsalBroadcastService) { }

    ngOnInit() {
        this.broadcastService.msalSubject$
        .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.ACQUIRE_TOKEN_SUCCESS),
            takeUntil(this._destroying$)
        )
        .subscribe((result: EventMessage) => {
            // Do something with event payload here
        });
    }

    ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
    }
}
```

另外，也可通过显式方式使用获取令牌方法来获取令牌，如核心 MSAL.js 库中所述。

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
MSAL Angular 包装器提供 HTTP 侦听器，后者会自动以无提示方式获取访问令牌并将其附加到针对 API 的 HTTP 请求。
可以在 `protectedResourceMap` 配置选项中指定 API 的作用域。 `MsalInterceptor` 将在自动获取令牌时请求这些作用域。

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

以无提示方式获取令牌时，不管是成功还是失败，MSAL Angular 都会提供回叫，可进行订阅。 此外还要记住取消订阅。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}
ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

另外，也可通过显式方式使用获取令牌方法来获取令牌，如核心 MSAL.js 库中所述。

# <a name="react"></a>[React](#tab/react)

以下代码将前面描述的模式与弹出体验的方法结合起来：

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        if (!apiData && inProgress === InteractionStatus.None) {
            const accessTokenRequest = {
                scopes: ["user.read"],
                account: accounts[0]
            }
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
                        // Acquire token interactive success
                        let accessToken = accessTokenResponse.accessToken;
                        // Call your API with token
                        callApi(accessToken).then((response) => { setApiData(response) });
                    }).catch(function(error) {
                        // Acquire token interactive failure
                        console.log(error);
                    });
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

或者，如果需要在 React 组件的外部获取令牌，可以调用 `acquireTokenSilent`，但如果失败，则不应回退到交互。 所有交互都应在组件树中的 `MsalProvider` 组件下进行。

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="acquire-a-token-with-a-redirect"></a>通过重定向获取令牌

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

以下模式如前文所述，但显示的是如何使用重定向方法以交互方式获取令牌。 需要调用页面上的 `handleRedirectPromise` 并等待其加载完成。

```javascript
const redirectResponse = await publicClientApplication.handleRedirectPromise();
if (redirectResponse !== null) {
    // Acquire token silent success
    let accessToken = redirectResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
} else {
    // MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
    const account = publicClientApplication.getAllAccounts()[0];
    
    const accessTokenRequest = {
        scopes: ["user.read"],
        account: account
    }
    
    publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
        // Acquire token silent success
        // Call API with token
        let accessToken = accessTokenResponse.accessToken;
        // Call your API with token
        callApi(accessToken);
    }).catch(function (error) {
        //Acquire token silent failure, and send an interactive request
        console.log(error);
        if (error instanceof InteractionRequiredAuthError) {
            publicClientApplication.acquireTokenRedirect(accessTokenRequest);
        }
    });
}
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

以下模式如前文所述，但显示的是如何使用重定向方法以交互方式获取令牌。 你需注册重定向回叫，如前文所述。

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>请求可选声明

可以使用可选声明来实现以下目的：

- 在应用程序的令牌中包括其他声明。
- 更改 Azure AD 在令牌中返回的某些声明的行为。
- 添加和访问应用程序的自定义声明。

若要请求 `IdToken` 中的可选声明，可以将一个字符串化声明对象发送到 `AuthenticationParameters.ts` 类的 `claimsRequest` 字段。

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

如需了解详细信息，请参阅[可选声明](active-directory-optional-claims.md)。

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

此代码与前面所述的相同，但建议启动 `MsalRedirectComponent` 来处理重定向。 `MsalInterceptor` 配置也可以更改为使用重定向。

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    declarations: [
      // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Redirect,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
此代码与前文所述相同。

# <a name="react"></a>[React](#tab/react)

如果 `acquireTokenSilent` 失败，则回退到 `acquireTokenRedirect`。 此方法将启动全帧重定向，并在响应返回到应用程序时对其进行处理。 在从重定向返回后呈现此组件时，`acquireTokenSilent` 应该会成功，因为将从缓存中拉取令牌。

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        const accessTokenRequest = {
            scopes: ["user.read"],
            account: accounts[0]
        }
        if (!apiData && inProgress === InteractionStatus.None) {
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenRedirect(accessTokenRequest);
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

或者，如果需要在 React 组件的外部获取令牌，可以调用 `acquireTokenSilent`，但如果失败，则不应回退到交互。 所有交互都应在组件树中的 `MsalProvider` 组件下进行。

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[调用 Web API](scenario-spa-call-api.md)。
