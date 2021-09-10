---
title: 使用 Azure Active Directory B2C 构建基块在 Angular 应用程序中启用身份验证
description: 使用 Azure Active Directory B2C 构建基块在 Angular 应用程序中登录和注册用户。
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
ms.openlocfilehash: 100fed96c2a7adaa0d0934ab316db1d70bffdcb9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220810"
---
# <a name="enable-authentication-in-your-own-angular-application-by-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在自己的 Angular 应用程序中启用身份验证

本文介绍如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证添加到你自己的 Angular 单页应用程序 (SPA)。 了解如何将 Angular 应用程序与 [MSAL for Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) 身份验证库相集成。 

将此文章与标题为[在示例 Angular 单页应用程序中配置身份验证](./configure-authentication-sample-angular-spa-app.md)的相关文章结合使用。 将示例 Angular 应用替换为你自己的 Angular 应用。 完成本文中的步骤后，应用程序将接受通过 Azure AD B2C 进行登录。

## <a name="prerequisites"></a>先决条件

查看[在示例 Angular 单页应用程序中配置身份验证](configure-authentication-sample-angular-spa-app.md)一文中的先决条件和集成步骤。

## <a name="create-an-angular-app-project"></a>创建 Angular 应用项目

可以使用现有的 Angular 应用项目或新建一个项目。 若要创建新项目，请运行以下命令。

命令：

1. 使用 npm 包管理器安装 [Angular CLI](https://angular.io/cli)。
1. 使用路由模块[创建 Angular 工作区](https://angular.io/cli/new)。 应用名称为 `msal-angular-tutorial`。 可将其更改为任何有效的 Angular 应用名称，例如 `contoso-car-service`。
1. 切换到应用目录文件夹。

```
npm install -g @angular/cli 
ng new msal-angular-tutorial --routing=true --style=css --strict=false
cd msal-angular-tutorial
```

## <a name="install-the-dependencies"></a>安装依赖项

若要在应用程序中安装 [MSAL 浏览器](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-browser)和 [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) 库，请在命令行界面中运行以下命令：

```
npm install @azure/msal-browser @azure/msal-angular
```

安装 [Angular 材料组件库](https://material.angular.io/)（可选，用于 UI）：

```
npm install @angular/material @angular/cdk
```

## <a name="add-the-authentication-components"></a>添加身份验证组件

该示例代码包括以下组成部分： 

|组件  |类型  |说明  |
|---------|---------|---------|
| auth-config.ts| 常量 | 此配置文件包含有关 Azure AD B2C 标识提供者和 Web API 服务的信息。 Angular 应用使用此信息与 Azure AD B2C 建立信任关系、登录和注销用户、获取令牌以及验证令牌。 |
| app.module.ts| [Angular 模块](https://angular.io/guide/architecture-modules)| 此组件描述应用程序部件如何装配到一起。 这是用于启动和开启应用程序的根模块。 在本演练中，你要将一些组件添加到 app.module.ts 模块，并使用 MSAL 配置对象来启动 MSAL 库。  |
| app-routing.module.ts | [Angular 路由模块](https://angular.io/tutorial/toh-pt5) | 此组件通过解释浏览器 URL 并加载相应的组件来启用导航。 在本演练中，你将在路由模块中添加一些组件，并使用 [MSAL Guard](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-guard.md) 来保护组件。 只有授权用户才能访问受保护的组件。   |
| app.component.* | [Angular 组件](https://angular.io/guide/architecture-components) | `ng new` 命令创建一个带有根组件的 Angular 项目。 在本演练中，你将更改应用组件以承载顶部导航栏。 导航栏包含各种按钮，其中包括登录和注销按钮。 `app.component.ts` 类处理登录和注销事件。  |
| home.component.* | [Angular 组件](https://angular.io/guide/architecture-components)|在本演练中，你将添加 home 组件以呈现匿名访问主页。 此组件演示如何检查用户是否已登录。  |
| profile.component.* | [Angular 组件](https://angular.io/guide/architecture-components) | 在本演练中，你将添加 profile 组件以了解如何读取 ID 令牌声明。 |
| webapi.component.* | [Angular 组件](https://angular.io/guide/architecture-components)| 在本演练中，你将添加 webapi 组件以了解如何调用 Web API。 |

若要将以下组件添加到应用，请运行以下 Angular CLI 命令。 `generate component` 命令：

1. 为每个组件创建一个文件夹。 该文件夹包含 TypeScript、HTML、CSS 和测试文件。 
1. 更新 `app.module.ts` 和 `app-routing.module.ts` 文件，使其包含对新组件的引用。 

```
ng generate component home
ng generate component profile
ng generate component webapi
```

## <a name="add-the-app-settings"></a>添加应用设置

Azure AD B2C 标识提供者和 Web API 的设置存储在 auth-config.ts 文件中。 在 src/app 文件夹中，创建包含以下代码的名为 auth-config.ts 的文件 。 然后按照 [3.1 配置 Angular 示例](configure-authentication-sample-angular-spa-app.md#31-configure-the-angular-sample)中所述更改设置。

```typescript
import { LogLevel, Configuration, BrowserCacheLocation } from '@azure/msal-browser';

const isIE = window.navigator.userAgent.indexOf("MSIE ") > -1 || window.navigator.userAgent.indexOf("Trident/") > -1;
 
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn.
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
     cache: {
         cacheLocation: BrowserCacheLocation.LocalStorage,.
         storeAuthStateInCookie: isIE, 
     },
     system: {
         loggerOptions: {
            loggerCallback: (logLevel, message, containsPii) => {
                console.log(message);
             },
             logLevel: LogLevel.Verbose,
             piiLoggingEnabled: false
         }
     }
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
export const loginRequest = {
  scopes: []
};
```

## <a name="start-the-authentication-libraries"></a>启动身份验证库

公共客户端应用程序不可信，不能安全地保存应用程序机密，因此它们不包含客户端机密。 在 src/app 文件夹中，打开 app.module.ts 并进行以下更改 ：

1. 导入 MSAL Angular 和 MSAL 浏览器库。
1. 导入 Azure AD B2C 配置模块。
1. 导入 `HttpClientModule`。 HTTP 客户端用于调用 Web API。
1. 导入 Angular HTTP 拦截器。 MSAL 使用拦截器将持有者令牌注入 HTTP 授权头。
1. 添加必要的 Angular 材料。
1. 使用多个帐户的公共客户端应用程序对象实例化 MSAL。 MSAL 初始化包括传递以下对象：
    1. auth-config.ts 的配置对象。
    1. 路由防护的配置对象。
    1. MSAL 侦听器的配置对象。 interceptor 类会自动为向已知受保护资源发出的、使用 Angular [HttpClient](https://angular.io/api/common/http/HttpClient) 类的传出请求获取令牌。
1. 配置 `HTTP_INTERCEPTORS` 和 `MsalGuard` [Angular 提供程序](https://angular.io/guide/providers)。  
1. 将 `MsalRedirectComponent` 添加到 [Angular 启动](https://angular.io/guide/bootstrapping)。

在 src/app 文件夹中，编辑 app.module.ts 并按以下代码片段中所示进行修改 。 更改的部分标有“在此处开始更改”和“在此处结束更改”。 

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

/* Changes start here. */
// Import MSAL and MSAL browser libraries. 
import { MsalGuard, MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';
import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

// Import the Azure AD B2C configuration 
import { msalConfig, protectedResources } from './auth-config';

// Import the Angular HTTP interceptor. 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ProfileComponent } from './profile/profile.component';
import { HomeComponent } from './home/home.component';
import { WebapiComponent } from './webapi/webapi.component';

// Add the essential Angular materials.
import { MatButtonModule } from '@angular/material/button';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatListModule } from '@angular/material/list';
import { MatTableModule } from '@angular/material/table';
/* Changes end here. */

@NgModule({
  declarations: [
    AppComponent,
    ProfileComponent,
    HomeComponent,
    WebapiComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    /* Changes start here. */
    // Import the following Angular materials. 
    MatButtonModule,
    MatToolbarModule,
    MatListModule,
    MatTableModule,
    // Import the HTTP client. 
    HttpClientModule,

    // Initiate the MSAL library with the MSAL configuration object
    MsalModule.forRoot(new PublicClientApplication(msalConfig),
      {
        // The routing guard configuration. 
        interactionType: InteractionType.Redirect,
        authRequest: {
          scopes: protectedResources.todoListApi.scopes
        }
      },
      {
        // MSAL interceptor configuration.
        // The protected resource mapping maps your web API with the corresponding app scopes. If your code needs to call another web API, add the URI mapping here.
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
        ])
      })
    /* Changes end here. */
  ],
  providers: [
    /* Changes start here. */
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalGuard
    /* Changes end here. */
  ],
  bootstrap: [
    AppComponent,
    /* Changes start here. */
    MsalRedirectComponent
    /* Changes end here. */
  ]
})
export class AppModule { }
```

## <a name="configure-routes"></a>配置路由

在本部分配置 Angular 应用程序的路由。 当用户选择页面上的链接以在单页应用程序中移动时，或者在地址栏中输入 URL 时，路由会将该 URL 映射到 Angular 组件。 Angular 路由 [canActivate](https://angular.io/api/router/CanActivate) 接口使用 MSAL Guard 来检查用户是否已登录。 如果用户未登录，MSAL 会将用户定向到 Azure AD B2C 进行身份验证。

在 src/app 文件夹中，编辑 app-routing.module.ts 并按以下代码片段中所示进行修改 。 更改的部分标有“在此处开始更改”和“在此处结束更改”。 

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';
import { WebapiComponent } from './webapi/webapi.component';

const routes: Routes = [
  /* Changes start here. */
  {
    path: 'profile',
    component: ProfileComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    path: 'webapi',
    component: WebapiComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    // The home component allows anonymous access
    path: '',
    component: HomeComponent
  }
  /* Changes end here. */
];


@NgModule({
  /* Changes start here. */
  // Replace the following line with the next one
  //imports: [RouterModule.forRoot(routes)],
  imports: [RouterModule.forRoot(routes, {
    initialNavigation:'enabled'
  })],
  /* Changes end here. */
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## <a name="add-the-sign-in-and-sign-out-buttons"></a>添加登录和注销按钮

在本部分，你将向 app 组件添加登录和注销按钮。 在 src/app 文件夹中，打开 app.component.ts 文件并进行以下更改 ：

1. 导入所需的组件。
1. 更改类以实现 [OnInit 方法](https://angular.io/api/core/OnInit)。 `OnInit` 方法订阅了 [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$` 可观测事件。 使用此事件来了解用户交互状态，尤其是检查交互是否已完成。 

   在与 MSAL 帐户对象交互之前，请检查 `InteractionStatus` 属性是否返回 `InteractionStatus.None`。 `subscribe` 事件调用 `setLoginDisplay` 方法来检查用户是否已完成身份验证。
1. 添加类变量。
1. 添加用于启动授权流的 `login` 方法。
1. 添加用于将用户注销的 `logout` 方法。 
1. 添加用于检查用户是否已完成身份验证的 `setLoginDisplay` 方法。
1. 添加 [ngOnDestroy](https://angular.io/api/core/OnDestroy) 方法以清理 `inProgress$` 订阅事件。

更改后的代码应如以下代码片段所示：

```typescript
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

/* Changes start here. */
export class AppComponent implements OnInit{
  title = 'msal-angular-tutorial';
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {

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

  logout() { 
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
  /* Changes end here. */
}
```

在 src/app 文件夹中，编辑 app.component.html 并进行以下更改 ：

1. 添加指向配置文件和 Web API 组件的链接。
1. 添加登录按钮并将单击事件属性设置为 `login()` 方法。 仅当 `loginDisplay` 类变量为 `false` 时才显示此按钮。
1. 添加注销按钮并将单击事件属性设置为 `logout()` 方法。 仅当 `loginDisplay` 类变量为 `true` 时才显示此按钮。
1. 添加 [router-outlet](https://angular.io/api/router/RouterOutlet) 元素。 

更改后的代码应如以下代码片段所示：

```html
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>
  <a mat-button [routerLink]="['webapi']">Web API</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <router-outlet></router-outlet>
</div>
```

（可选）使用以下 CSS 代码片段更新 app.component.css 文件： 

```css
.toolbar-spacer {
    flex: 1 1 auto;
  }

  a.title {
    color: white;
  }
```

## <a name="handle-the-app-redirects"></a>处理应用重定向 

将重定向与 MSAL 配合使用时，必须将 [app-redirect](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/redirects.md) 指令添加到 index.html 中。 在 src 文件夹中，编辑 index.html，如以下代码片段中所示 ：

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MsalAngularTutorial</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <!-- Changes start here -->
  <app-redirect></app-redirect>
  <!-- Changes end here -->
</body>
</html>
```

## <a name="set-app-css-optional"></a>设置应用 CSS（可选）

在 /src 文件夹中，使用以下 CSS 代码片段更新 style.css 文件 ： 

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
.container { margin: 1%; }
```

> [!TIP]
> 此时可以运行应用并测试登录体验。 若要运行应用，请参阅[运行 Angular 应用程序](#run-the-angular-application)部分。

## <a name="check-if-a-user-is-authenticated"></a>检查用户是否已完成身份验证

home.component 文件演示如何检查用户是否已完成身份验证。 在 src/app/home 文件夹中，使用以下代码片段更新 home.component.ts 。 

代码：

1. 订阅 [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `msalSubject$` 和 `inProgress$` 可观测事件。 
1. 确保 `msalSubject$` 事件将身份验证结果写入浏览器控制台。
1. 确保 `inProgress$` 事件检查用户是否已完成身份验证。 `getAllAccounts()` 方法返回一个或多个对象。


```typescript
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

在 src/app/home 文件夹中，使用以下 HTML 代码片段更新 home.component.html 。 [*ngIf](https://angular.io/api/common/NgIf) 指令检查 `loginDisplay` 类变量，以显示或隐藏欢迎消息。

```html
<div *ngIf="!loginDisplay">
    <p>Please sign-in to see your profile information.</p>
</div>

<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <p>Request your profile information by clicking Profile above.</p>
</div>
```

## <a name="read-the-id-token-claims"></a>读取 ID 令牌声明

profile.component 文件演示如何访问用户的 ID 令牌声明。 在 src/app/profile 文件夹中，使用以下代码片段更新 profile.component.ts 。 

代码：

1. 导入所需的组件。
1. 订阅 [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$` 可观测事件。 该事件加载帐户并读取 ID 令牌声明。
1. 确保 `checkAndSetActiveAccount` 方法检查并设置活动帐户。 此操作在应用与多个 Azure AD B2C 用户流或自定义策略交互时很常见。
1. 确保 `getClaims` 方法从活动的 MSAL 帐户对象获取 ID 令牌声明。 然后，该方法将声明添加到 `dataSource` 数组。 该数组通过组件的模板绑定呈现给用户。

```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css']
})

export class ProfileComponent implements OnInit {
  displayedColumns: string[] = ['claim', 'value'];
  dataSource: Claim[] = [];
  private readonly _destroying$ = new Subject<void>();
  
  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) =>  status === InteractionStatus.None || status === InteractionStatus.HandleRedirect),
        takeUntil(this._destroying$)
      )
      .subscribe(() => {
        this.checkAndSetActiveAccount();
        this.getClaims(this.authService.instance.getActiveAccount()?.idTokenClaims)
      })
  }

  checkAndSetActiveAccount() {

    let activeAccount = this.authService.instance.getActiveAccount();

    if (!activeAccount && this.authService.instance.getAllAccounts().length > 0) {
      let accounts = this.authService.instance.getAllAccounts();
      this.authService.instance.setActiveAccount(accounts[0]);
    }
  }

  getClaims(claims: any) {

    let list: Claim[]  =  new Array<Claim>();

    Object.keys(claims).forEach(function(k, v){
      
      let c = new Claim()
      c.id = v;
      c.claim = k;
      c.value =  claims ? claims[k]: null;
      list.push(c);
    });
    this.dataSource = list;

  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}

export class Claim {
  id: number;
  claim: string;
  value: string;
}
``` 

在 src/app/profile 文件夹中，使用以下 HTML 代码片段更新 profile.component.html ： 

```html
<h1>ID token claims:</h1>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">

  <!-- Claim Column -->
  <ng-container matColumnDef="claim">
    <th mat-header-cell *matHeaderCellDef> Claim </th>
    <td mat-cell *matCellDef="let element"> {{element.claim}} </td>
  </ng-container>

  <!-- Value Column -->
  <ng-container matColumnDef="value">
    <th mat-header-cell *matHeaderCellDef> Value </th>
    <td mat-cell *matCellDef="let element"> {{element.value}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
</table>
```

## <a name="call-a-web-api"></a>调用 Web API

若要调用[基于令牌的授权 Web API](enable-authentication-web-api.md)，应用需有有效的访问令牌。 [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) 提供程序会自动为向已知受保护资源发出的、使用 Angular [HttpClient](https://angular.io/api/common/http/HttpClient) 类的传出请求获取令牌。

> [!IMPORTANT]
> MSAL 初始化方法（在 `app.module.ts` 类中）使用 `protectedResourceMap` 对象在所需的应用范围映射受保护的资源（例如 Web API）。 如果你的代码需要调用另一个 Web API，请将 Web API URI、Web API HTTP 方法以及相应的范围添加到 `protectedResourceMap` 对象。 有关详细信息，请参阅[受保护的资源映射](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/master/lib/msal-angular/docs/v2-docs/msal-interceptor.md#protected-resource-map)。


当 [HttpClient](https://angular.io/api/common/http/HttpClient) 对象调用 Web API 时，[MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) 提供程序将执行以下步骤：

1. 获取对 Web API 终结点拥有所需权限（范围）的访问令牌。 
1. 使用以下格式，在 HTTP 请求的授权标头中将该访问令牌作为持有者令牌进行传递：

   ```http
   Authorization: Bearer <access-token>
   ```

webapi.component 文件演示如何调用 Web API。 在 src/app/webapi 文件夹中，使用以下代码片段更新 webapi.component.ts 。  

代码：

1. 使用 Angular [HttpClient](https://angular.io/guide/http) 类调用 Web API。
1. 读取 `auth-config` 类的 `protectedResources.todoListApi.endpoint` 元素。 此元素指定 Web API URI。 MSAL 拦截器基于 Web API URI 获取具有相应范围的访问令牌。 
1. 从 Web API 获取配置文件，并设置 `profile` 类变量。

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { protectedResources } from '../auth-config';

type ProfileType = {
  name?: string
};

@Component({
  selector: 'app-webapi',
  templateUrl: './webapi.component.html',
  styleUrls: ['./webapi.component.css']
})
export class WebapiComponent implements OnInit {
  todoListEndpoint: string = protectedResources.todoListApi.endpoint;
  profile!: ProfileType;

  constructor(
    private http: HttpClient
  ) { }

  ngOnInit() {
    this.getProfile();
  }

  getProfile() {
    this.http.get(this.todoListEndpoint)
      .subscribe(profile => {
        this.profile = profile;
      });
  }
}
```

在 src/app/webapi 文件夹中，使用以下 HTML 代码片段更新 webapi.component.html 。 组件的模板将呈现 Web API 返回的名称。 在页面底部，模板将呈现 Web API 地址。

```html
<h1>The web API returns:</h1>
<div>
    <p><strong>Name: </strong> {{profile?.name}}</p>
</div>

<div class="footer-text">
    Web API: {{todoListEndpoint}}
</div>
```

（可选）使用以下 CSS 代码片段更新 webapi.component.css 文件： 

```css
.footer-text {
    position: absolute;
    bottom: 50px;
    color: gray;
}
```

## <a name="run-the-angular-application"></a>运行 Angular 应用程序


运行以下命令：

```console
npm start
```

控制台窗口将显示该应用程序所在的端口号。

```console
Listening on port 4200...
```

> [!TIP]
> 或者，若要运行 `npm start` 命令，可以使用 [Visual Studio Code 调试程序](https://code.visualstudio.com/docs/editor/debugging)。 该调试器有助于加速编辑、编译和调试循环。

在浏览器中转到 `http://localhost:4200`，查看此应用程序。


## <a name="next-steps"></a>后续步骤

* [使用 Azure AD B2C 在自己的 Angular 应用程序中配置身份验证选项](enable-authentication-angular-spa-app-options.md)
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)
