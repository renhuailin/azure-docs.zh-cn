---
title: 教程：创建使用 Microsoft 标识平台通过授权代码流进行身份验证的 Angular 应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将生成一个 Angular 单页应用 (SPA)，该应用通过使用 Microsoft 标识平台的授权代码流将用户登录，并获取访问令牌以代表用户调用 Microsoft Graph API。
services: active-directory
author: joarroyo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/14/2021
ms.author: joarroyo
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 01c35297a35b41b58b83aaf361aaa47a76cfecee
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230848"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa-using-auth-code-flow"></a>教程：使用授权代码流从 Angular 单页应用程序 (SPA) 将用户登录并调用 Microsoft Graph API

在本教程中，你将生成一个 Angular 单页应用程序 (SPA)，该应用使用授权代码流和 PKCE 将用户登录并调用 Microsoft Graph API。 生成的 SPA 使用适用于 Angular 的 Microsoft 身份验证库 (MSAL) v2。

本教程的内容：

> [!div class="checklist"]
> * 使用 `npm` 创建 Angular 项目
> * 在 Azure 门户中注册应用程序
> * 添加代码以支持用户登录和注销
> * 添加代码以调用 Microsoft Graph API
> * 测试应用

MSAL Angular v2 支持浏览器中的授权代码流（而不是隐式授权流），从而在 MSAL Angular v1 的基础上进行了改进。 MSAL Angular v2 不支持隐式流。

## <a name="prerequisites"></a>先决条件

* 用于运行本地 Web 服务器的 [Node.js](https://nodejs.org/en/download/)。
* 用于修改项目文件的 [Visual Studio Code](https://code.visualstudio.com/download) 或其他编辑器。

## <a name="how-the-sample-app-works"></a>示例应用工作原理

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

本教程中创建的示例应用程序使 Angular SPA 能够查询 Microsoft Graph API 或 Web API，该 API 接受 Microsoft 标识平台颁发的令牌。 它使用适用于 Angular 的 Microsoft 身份验证库 (MSAL) v2 - MSAL.js v2 库的包装器。 MSAL Angular 使 Angular 9+ 应用程序能够使用 Azure Active Directory (Azure AD) 对企业用户和具有 Microsoft 帐户和社交标识（如 Facebook、Google 和 LinkedIn）的用户进行身份验证。 使用此库，应用程序还可以获取对 Microsoft 云服务和 Microsoft Graph 的访问权限。

在此方案中，用户登录后请求了访问令牌，并通过授权标头将其添加到 HTTP 请求。 令牌获取和续订通过 MSAL 处理。

### <a name="libraries"></a>库

本教程使用以下库：

|库|说明|
|---|---|
|[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)|适用于 JavaScript Angular 的 Microsoft 身份验证库包装器|
|[MSAL 浏览器](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|适用于 JavaScript 的 Microsoft 身份验证库 v2 浏览器包 |

可以在 GitHub 上的 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 存储库中找到所有 MSAL.js 库的源代码。

## <a name="create-your-project"></a>创建项目

安装 [Node.js](https://nodejs.org/en/download/) 后，打开终端窗口，然后运行以下命令以生成新的 Angular 应用程序：

```bash
npm install -g @angular/cli                         # Install the Angular CLI
ng new msal-angular-tutorial --routing=true --style=css --strict=false    # Generate a new Angular app
cd msal-angular-tutorial                            # Change to the app directory
npm install @angular/material @angular/cdk          # Install the Angular Material component library (optional, for UI)
npm install @azure/msal-browser @azure/msal-angular # Install MSAL Browser and MSAL Angular in your application
ng generate component home                          # To add a home page
ng generate component profile                       # To add a profile page
```

## <a name="register-your-application"></a>注册应用程序

按照说明在 Azure 门户中[注册单页应用程序](./scenario-spa-app-registration.md)。

在注册的应用“概述”页上，记下“应用程序(客户端) ID”值供稍后使用。 

将 http://localhost:4200/ 注册为“重定向 URI”值，将“SPA”注册为类型 。

## <a name="configure-the-application"></a>配置应用程序

1. 在 src/app 文件夹中编辑 app.module.ts，将 `MsalModule` 和 `MsalInterceptor` 添加到 `imports`，并添加 `isIE` 常量 。 代码应如下所示：

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

    替换以下值：

    |值名称|关于|
    |---------|---------|
    |Enter_the_Application_Id_Here|在应用程序注册的“概览”页中，这是你的“应用程序(客户端) ID”值。  |
    |Enter_the_Cloud_Instance_Id_Here|这是 Azure 云的实例。 对于主要云或全球 Azure 云，请输入 **https://login.microsoftonline.com** 。 对于国家/地区云（例如中国云），请参阅[国家/地区云](./authentication-national-cloud.md)。|
    |Enter_the_Tenant_Info_Here| 设置为以下选项之一：如果应用程序支持此组织目录中的帐户，请将此值替换为目录（租户）ID 或租户名称（例如 contoso.microsoft.com）。 如果应用程序支持“任何组织目录中的帐户”，请将此值替换为 **organizations**。 如果应用程序支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为 **common**。 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为 **consumers**。 |
    |Enter_the_Redirect_Uri_Here|替换为 **http://localhost:4200** 。|

    有关可用的可配置选项的详细信息，请阅读[初始化客户端应用程序](msal-js-initializing-client-applications.md)。

2. 将路由添加到 src/app/app-routing.module.ts 中的主组件和配置文件组件。 代码应如下所示：

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

## <a name="replace-base-ui"></a>替换基础 UI

1. 将 src/app/app.component.html 中的占位符代码替换为以下内容：

    ```HTML
    <mat-toolbar color="primary">
      <a class="title" href="/">{{ title }}</a>

      <div class="toolbar-spacer"></div>

      <a mat-button [routerLink]="['profile']">Profile</a>

      <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>

    </mat-toolbar>
    <div class="container">
      <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
      <router-outlet *ngIf="!isIframe"></router-outlet>
    </div>
    ```

2. 将材料模块添加到 src/app/app.module.ts。 `AppModule` 现在应如下所示：

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, 
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

3. （可选）将 CSS 添加到 src/style.css：

    ```css
    @import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

    html, body { height: 100%; }
    body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
    .container { margin: 1%; }
    ```

4. （可选）将 CSS 添加到 src/app/app.component.css：

    ```css
    .toolbar-spacer {
        flex: 1 1 auto;
      }
      
      a.title {
        color: white;
      }
    ```

## <a name="sign-in-a-user"></a>将用户登录

添加以下节中的代码，以使用弹出窗口或全框架重定向来调用登录： 

### <a name="sign-in-using-popups"></a>使用弹出窗口登录

1. 将 src/app/app.component.ts 中的代码更改为以下内容，以使用弹出窗口将用户登录：

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginPopup()
          .subscribe({
            next: (result) => {
              console.log(result);
              this.setLoginDisplay();
            },
            error: (error) => console.log(error)
          });
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

> [!NOTE]
> 本快速入门的余下内容对 Microsoft Internet Explorer 使用 `loginRedirect` 方法，因为 Internet Explorer 在处理弹出窗口时会出现一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md)。

### <a name="sign-in-using-redirects"></a>使用重定向登录

1. 更新 src/app/app.module.ts 以启动 `MsalRedirectComponent`。 这是一个用于处理重定向的专用重定向组件。 代码现应如下所示：

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular'; // Updated import
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent, MsalRedirectComponent] // MsalRedirectComponent bootstrapped here
    })
    export class AppModule { }
    ```

2. 将 `<app-redirect>` 选择器添加到 src/index.html。 此选择器由 `MsalRedirectComponent` 使用。 src/index.html 应如下所示：

    ```HTML
    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>msal-angular-tutorial</title>
      <base href="/">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <link rel="icon" type="image/x-icon" href="favicon.ico">
    </head>
    <body>
      <app-root></app-root>
      <app-redirect></app-redirect>
    </body>
    </html>
    ```

3. 将 src/app/app.component.ts 中的代码替换为以下内容，以使用全框架重定向将用户登录：

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

4. 替换 src/app/home/home.component.ts 中的现有代码以订阅 `LOGIN_SUCCESS` 事件。 这样，就可以在使用重定向成功登录后访问结果。 代码应如下所示：

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
    import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
    import { filter } from 'rxjs/operators';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {
      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
      }
    }
    ```

## <a name="conditional-rendering"></a>按条件呈现

若要仅向已经过身份验证的用户呈现特定的 UI，组件必须订阅 `MsalBroadcastService`，以确定用户是否已登录以及交互是否已完成。

1. 将 `MsalBroadcastService` 添加到 src/app/app.component.ts 并订阅 `inProgress$` 可观测对象，以便在呈现 UI 之前，检查交互是否已完成以及帐户是否已登录。 代码现应如下所示：

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
    import { InteractionStatus } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

        this.broadcastService.inProgress$
        .pipe(
          filter((status: InteractionStatus) => status === InteractionStatus.None),
          takeUntil(this._destroying$)
        )
        .subscribe(() => {
          this.setLoginDisplay();
        })
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

2. 更新 src/app/home/home.component.ts 中的代码，以便在更新 UI 之前也检查交互是否已完成。 代码现应如下所示：

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
    import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
    import { filter } from 'rxjs/operators';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {
      loginDisplay = false;

      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
        
        this.msalBroadcastService.inProgress$
          .pipe(
            filter((status: InteractionStatus) => status === InteractionStatus.None)
          )
          .subscribe(() => {
            this.setLoginDisplay();
          })
      }
      
      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

3. 将 src/app/home/home.component.html 中的代码替换为以下用于按条件显示内容的代码：

    ```HTML
    <div *ngIf="!loginDisplay">
        <p>Please sign-in to see your profile information.</p>
    </div>

    <div *ngIf="loginDisplay">
        <p>Login successful!</p>
        <p>Request your profile information by clicking Profile above.</p>
    </div>
    ```

## <a name="guarding-routes"></a>保护路由

### <a name="angular-guard"></a>Angular 保护

MSAL Angular 提供 `MsalGuard`，使用该类可以保护路由，并要求在访问受保护路由之前先完成身份验证。 以下步骤将 `MsalGuard` 添加到 `Profile` 路由。 保护 `Profile` 路由意味着，即使用户不使用 `Login` 按钮登录，在他们尝试访问 `Profile` 路由或单击 `Profile` 按钮时，`MsalGuard` 在显示 `Profile` 页面之前也会提示用户通过弹出窗口或重定向进行身份验证。

`MsalGuard` 是一个可用于改善用户体验的便捷类，但出于安全考虑，不应依赖于此类。 攻击者可能会绕过客户端保护，你应该确保服务器不会返回用户不应访问的任何数据。

1. 在 src/app/app.module.ts 中添加 `MsalGuard` 类作为应用程序中的提供程序，并添加 `MsalGuard` 的配置。 可以在 `authRequest` 中提供稍后用于获取令牌的范围，可将保护功能的交互类型设置为 `Redirect` 或 `Popup`。 代码应如下所示：

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard } from '@azure/msal-angular'; // MsalGuard added to imports
    import { PublicClientApplication, InteractionType } from '@azure/msal-browser'; // InteractionType added to imports

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
            interactionType: InteractionType.Redirect, // MSAL Guard Configuration
            authRequest: {
              scopes: ['user.read']
            }
        }, null)
      ],
      providers: [
        MsalGuard // MsalGuard added as provider here
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }
    ```

2. 在 src/app/app-routing.module.ts 中针对要保护的路由设置 `MsalGuard`：

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';
    import { MsalGuard } from '@azure/msal-angular';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [MsalGuard]
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

3. 在 src/app/app.component.ts 中调整登录调用，以将保护配置中设置的 `authRequest` 考虑在内。 代码现在应如下所示：

    ```javascript
    import { Component, OnInit, Inject } from '@angular/core';
    import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
    import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

        this.broadcastService.inProgress$
        .pipe(
          filter((status: InteractionStatus) => status === InteractionStatus.None),
          takeUntil(this._destroying$)
        )
        .subscribe(() => {
          this.setLoginDisplay();
        })
      }

      login() {
        if (this.msalGuardConfig.authRequest){
          this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
        } else {
          this.authService.loginRedirect();
        }
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

## <a name="acquire-a-token"></a>获取令牌

### <a name="angular-interceptor"></a>Angular 侦听器

MSAL Angular 向已知的受保护资源提供一个 `Interceptor` 类，该类可自动为使用 Angular `http` 客户端的传出请求获取令牌。

1. 在 *src/app/app.module.ts* 中，将 `Interceptor` 类作为提供程序添加到应用程序，并添加此类的配置。 代码现应如下所示：

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';
    import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http"; // Import 

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard, MsalInterceptor } from '@azure/msal-angular'; // Import MsalInterceptor
    import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        HttpClientModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_Here',
            authority: 'Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here',
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
          interactionType: InteractionType.Redirect, // MSAL Interceptor Configuration
          protectedResourceMap: new Map([ 
              ['Enter_the_Graph_Endpoint_Here/v1.0/me', ['user.read']]
          ])
        })
      ],
      providers: [
        {
          provide: HTTP_INTERCEPTORS,
          useClass: MsalInterceptor,
          multi: true
        },
        MsalGuard
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }

    ```

    受保护的资源是作为 `protectedResourceMap` 提供的。 在 `protectedResourceMap` 集合中输入的 URL 区分大小写。 对于每个资源，添加所请求的、需要在访问令牌中返回的范围。

    例如：

    * Microsoft Graph 的 `["user.read"]`
    * 自定义 Web API 的 `["<Application ID URL>/scope"]`（即 `api://<Application ID>/access_as_user`）
    
    按下面所述修改 `protectedResourceMap` 中的值：

    |值名称| 关于|
    |----------|------|
    |`Enter_the_Graph_Endpoint_Here`| 应用程序应与之通信的 Microsoft Graph API 实例。 对于全球 Microsoft Graph API 终结点，请将此字符串的两个实例都替换为 `https://graph.microsoft.com`。 对于国家/地区云部署中的终结点，请参阅 Microsoft Graph 文档中的[国家/地区云部署](/graph/deployments)。|

2. 替换 src/app/profile/profile.component.ts 中的代码，以使用 HTTP 请求检索用户的配置文件：

    ```JavaScript
    import { Component, OnInit } from '@angular/core';
    import { HttpClient } from '@angular/common/http';

    const GRAPH_ENDPOINT = 'Enter_the_Graph_Endpoint_Here/v1.0/me';

    type ProfileType = {
      givenName?: string,
      surname?: string,
      userPrincipalName?: string,
      id?: string
    };

    @Component({
      selector: 'app-profile',
      templateUrl: './profile.component.html',
      styleUrls: ['./profile.component.css']
    })
    export class ProfileComponent implements OnInit {
      profile!: ProfileType;

      constructor(
        private http: HttpClient
      ) { }

      ngOnInit() {
        this.getProfile();
      }

      getProfile() {
        this.http.get(GRAPH_ENDPOINT)
          .subscribe(profile => {
            this.profile = profile;
          });
      }
    }
    ```

3. 替换 src/app/profile/profile.component.html 中的 UI，以显示配置文件信息： 

    ```HTML
    <div>
        <p><strong>First Name: </strong> {{profile?.givenName}}</p>
        <p><strong>Last Name: </strong> {{profile?.surname}}</p>
        <p><strong>Email: </strong> {{profile?.userPrincipalName}}</p>
        <p><strong>Id: </strong> {{profile?.id}}</p>
    </div>
    ```

## <a name="sign-out"></a>注销

更新 src/app/app.component.html 中的代码，以按条件显示 `Logout` 按钮：

```HTML
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
  <router-outlet *ngIf="!isIframe"></router-outlet>
</div>
```

### <a name="sign-out-using-redirects"></a>使用重定向注销

更新 src/app/app.component.ts 中的代码，以使用重定向将用户注销：

```javascript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { // Add log out function here
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

### <a name="sign-out-using-popups"></a>使用弹出窗口注销

更新 src/app/app.component.ts 中的代码，以使用弹出窗口将用户注销：

```javascript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, PopupRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest)
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    } else {
      this.authService.loginPopup()
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    }
  }

  logout() { // Add log out function here
    this.authService.logoutPopup({
      mainWindowRedirectUri: "/"
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

## <a name="test-your-code"></a>测试代码

1.  通过在命令行提示符下从应用程序文件夹运行以下命令，启动 Web 服务器来侦听端口：

    ```bash
    npm install
    npm start
    ```
1. 在浏览器中输入 **http://localhost:4200** 或 **http://localhost:{port}** ，其中，port 是 Web 服务器正在侦听的端口。 应会看到如下所示的页面。

    :::image type="content" source="media/tutorial-v2-angular-auth-code/angular-01-not-signed-in.png" alt-text="显示登录对话框的 Web 浏览器":::


### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次开始登录到应用程序时，系统会提示你授予其访问你的个人资料的权限，并允许其将你登录：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="显示在 Web 浏览器中的内容对话框":::

如果你同意请求的权限，Web 应用程序将显示成功登录页面：

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-02-signed-in.png" alt-text="Web 浏览器中的登录成功结果":::

### <a name="call-the-graph-api"></a>调用图形 API

登录后，选择“配置文件”，以查看在调用 Microsoft Graph API 后提供的响应中返回的用户配置文件信息：

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-03-profile-data.png" alt-text="浏览器中显示的 Microsoft Graph 中的个人资料信息":::

## <a name="add-scopes-and-delegated-permissions"></a>添加范围和委托的权限

Microsoft Graph API 需要 _User.Read_ 作用域来读取用户的个人资料。 User.Read 范围会自动添加到你在 Azure 门户中创建的每个应用注册。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要使用 Mail.Read 作用域来列出用户的电子邮件。

添加作用域时，可能会提示用户为添加的作用域提供额外许可。

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

在由多部分组成的文章系列中，深入了解 Microsoft 标识平台上的单页应用程序 (SPA) 开发。

> [!div class="nextstepaction"]
> [方案：单页应用程序](scenario-spa-overview.md)
