---
title: 快速入门：使用授权代码在 JavaScript Angular 单页应用 (SPA) 中让用户登陆并调用 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍 JavaScript Angular 单页应用程序 (SPA) 如何通过授权代码流使用个人帐户、工作帐户和学校帐户将用户登录，并调用 Microsoft Graph。
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: f9993521e26250b5d296e1dddb982fdc43e317a5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516877"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>快速入门：使用授权代码流在 Angular SPA 中登陆并获取访问令牌

在本快速入门中，你将下载并运行一个代码示例，该示例演示 JavaScript Angular 单页应用程序 (SPA) 如何使用授权代码流让用户登录并调用 Microsoft Graph。 此代码示例演示如何获取访问令牌来调用 Microsoft Graph API 或任何 Web API。 

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

本快速入门将 MSAL Angular v2 与授权代码流配合使用。 若要查看将 MSAL Angular 1.x 与隐式流结合使用的类似快速入门，请参阅[快速入门：在 JavaScript 单页应用中登录用户](./quickstart-v2-angular.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建 Azure 订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>注册并下载快速入门应用程序
> 若要启动快速入门应用程序，请使用以下选项之一。
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1（快速）：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/AngularSpaQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
> 1. 选择“注册”。
> 1. 转到快速入门窗格，按说明下载并自动配置新应用程序。
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>选项 2（手动）：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，选择要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”  。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的 **名称**。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
> 1. 选择“注册”。 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用 。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 在“平台配置”下，选择“添加平台” 。 在打开的窗格中，选择“单页应用程序”。
> 1. 将“重定向 URI”值设置为 `http://localhost:4200/`。 这是将在本地计算机上侦听的默认端口 NodeJS。 在成功对用户进行身份验证后，我们会将身份验证响应返回到此 URl。 
> 1. 选择“配置”来应用更改。
> 1. 在“平台配置”下，展开“单页应用程序” 。
> 1. 确认在授权类型![已配置](media/quickstart-v2-javascript/green-check.png)下，重定向 URI 符合带有 PKCE 的授权代码流的条件。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使此快速入门中的代码示例正常运行，请添加重定向 URI `http://localhost:4200/`。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-javascript/green-check.png) 应用程序已使用这些属性进行配置。

 #### <a name="step-2-download-the-project"></a>步骤 2：下载项目

> [!div renderon="docs"]
> 若要使用 Node.js 在 Web 服务器中运行项目，请[下载核心项目文件](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)。

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Node.js 在 Web 服务器中运行项目

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>步骤 3：配置 JavaScript 应用
>
> 在 src 文件夹中，打开“应用”文件夹，然后打开 app.module.ts 文件并更新 `auth` 对象中的 `clientID`、`authority` 和 `redirectUri` 值  。
>
> ```javascript
> // MSAL instance to be passed to msal-angular
> export function MSALInstanceFactory(): IPublicClientApplication {
>   return new PublicClientApplication({
>     auth: {
>       clientId: 'Enter_the_Application_Id_Here',
>       authority: 'Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here',
>       redirectUri: 'Enter_the_Redirect_Uri_Here'
>     },
>     cache: {
>       cacheLocation: BrowserCacheLocation.LocalStorage,
>       storeAuthStateInCookie: isIE, // set to true for IE 11
>     },
>   });
> }
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> 修改 `auth` 部分中的值，如下所述：
>
> - `Enter_the_Application_Id_Here` 是已注册应用程序的应用程序（客户端）ID。
>
>    若要查找“应用程序(客户端) ID”的值，请转到 Azure 门户中应用注册的“概览”页面 。
> - `Enter_the_Cloud_Instance_Id_Here` 是 Azure 云的实例。 对于主要云或全球 Azure 云，请输入 `https://login.microsoftonline.com/`。 对于 **国家** 云（例如“中国”云），请参阅 [国家云](authentication-national-cloud.md)。
> - `Enter_the_Tenant_info_here` 设置为以下选项之一：
>   - 如果应用程序支持“此组织目录中的帐户”，请将此值替换为“租户 ID”或“租户名称”。 例如，`contoso.microsoft.com`。
>
>    若要查找“目录(租户) ID”的值，请转到 Azure 门户中应用注册的“概述”页 。
>   - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为“`organizations`”。
>   - 如果应用支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为“`common`”。 本快速入门中使用 `common`。
>   - 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为“`consumers`”。
>
>    若要查找“支持的帐户类型”的值，请转到 Azure 门户中应用注册的“概述”页 。
> - `Enter_the_Redirect_Uri_Here` 为 `http://localhost:4200/`。
>
> 如果使用的是主要（全球）Azure 云，则 app.module.ts 中的 `authority` 值应类似于：
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值。

> [!div renderon="docs"]
>
> 在同一文件中向下滚动并更新 `graphMeEndpoint`。 
> - 将字符串 `Enter_the_Graph_Endpoint_Herev1.0/me` 替换为 `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` 是将针对其进行 API 调用的终结点。 对于主要或全局 Microsoft Graph API 服务，请输入 `https://graph.microsoft.com/`（包括末尾的正斜杠）。 有关详细信息，请参阅[本文档](/graph/deployments)。
>
>
> ```javascript
> export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration {
>   const protectedResourceMap = new Map<string, Array<string>>();
>   protectedResourceMap.set('Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']);
>
>   return {
>     interactionType: InteractionType.Redirect,
>     protectedResourceMap
>   };
> }
> ```
>
>
 #### <a name="step-4-run-the-project"></a>步骤 4：运行项目

使用 Node.js 在 Web 服务器中运行项目：

1. 若要启动服务器，请从项目目录中运行以下命令：
    ```console
    npm install
    npm start
    ```
1. 浏览到 `http://localhost:4200/`。

1. 选择“登录”以启动登录过程，然后调用 Microsoft Graph API。

    首次登录时，系统会提示你允许应用程序访问你的个人资料并将你登录。 成功登录后，单击“个人资料”按钮，系统便会在页面上显示用户信息。

## <a name="more-information"></a>详细信息

### <a name="how-the-sample-works"></a>示例工作原理

![展示单页应用程序的授权代码流的示意图。](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.js 库会让用户登录并请求用于访问受 Microsoft 标识平台保护的 API 的令牌。 

如果已安装 Node.js，则可通过 Node.js 包管理器 (npm) 下载最新版本：

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>后续步骤

若要查看详细的分步指南了解如何使用 Vanilla JavaScript 生成身份验证代码流应用程序，请查看以下教程：

> [!div class="nextstepaction"]
> [有关登录和调用 MS Graph 的教程](./tutorial-v2-javascript-auth-code.md)