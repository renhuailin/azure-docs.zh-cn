---
title: 快速入门：在 Angular 单页应用中将用户登录 - Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍 Angular 应用如何调用 API，该 API 需要 Microsoft 标识平台颁发的访问令牌。
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: e30ed370ea5858d97f2cc0a64bc92d9bc9e64b3f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108128534"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>快速入门：在 Angular 单页应用程序中将用户登录并获取访问令牌

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Angular 单页应用程序 (SPA) 如何让用户登录并调用 Microsoft Graph。 此代码示例演示如何获取访问令牌来调用 Microsoft Graph API 或任何 Web API。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 [免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Node.js](https://nodejs.org/en/download/)。
* 用于编辑项目文件的 [Visual Studio Code](https://code.visualstudio.com/download)，或用于运行项目的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>注册并下载快速入门应用
>
> 若要启动快速入门应用，请使用以下选项之一。
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>选项 1（快速）：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 在快速入门窗格中，找到 Angular 快速入门。 遵照说明下载内容，系统会自动配置新应用程序。
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>选项 2（手动）：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-the-application"></a>步骤 1：注册应用程序
>
> 1. 按照说明在 Azure 门户中[注册单页应用程序](./scenario-spa-app-registration.md)。
> 1. 在应用注册的“身份验证”窗格中添加新的平台，并注册重定向 URI：`http://localhost:4200/`。
> 1. 本快速入门使用[隐式授权流](v2-oauth2-implicit-grant-flow.md)。 在“隐式授权和混合流”部分，选择“ID 令牌”和“访问令牌”  。 由于此应用会将用户登录并调用 API，因此需要 ID 令牌和访问令牌。

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
>
> 为使此快速入门中的代码示例正常运行，需要将重定向 URI 添加为 `http://localhost:4200/` 并启用“隐式授权”。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-javascript/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-code-sample"></a>步骤 2：下载代码示例
>[!div renderon="docs"]
>若要使用 Node.js 在 Web 服务器中运行项目，请克隆[示例存储库](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)或[下载核心项目文件](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)。 使用 Visual Studio Code 之类的编辑器打开这些文件。

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>步骤 3：配置 JavaScript 应用
>
> 在 src/app 文件夹中编辑 app.module.ts，并在 `MsalModule.forRoot` 下设置 `clientId` 和 `authority` 值。
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> 替换以下值：
>
>|值名称|说明|
>|---------|---------|
>|Enter_the_Application_Id_Here|在应用程序注册的“概览”页中，这是你的“应用程序(客户端) ID”值。  |
>|Enter_the_Cloud_Instance_Id_Here|这是 Azure 云实例。 对于主要云或全球 Azure 云，请输入 `https://login.microsoftonline.com`。 对于国家/地区云（例如中国云），请参阅[国家/地区云](./authentication-national-cloud.md)。|
>|Enter_the_Tenant_Info_Here| 设置为以下选项之一：如果应用程序支持此组织目录中的帐户，请将此值替换为目录（租户）ID 或租户名称（例如 `contoso.microsoft.com`）。 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为“`organizations`”。 如果应用支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为“`common`”。 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为“`consumers`”。 |
>|Enter_the_Redirect_Uri_Here|替换为 `http://localhost:4200` 。|
>|cacheLocation  | （可选）针对身份验证状态设置浏览器存储。 默认为 `sessionStorage`。   |
>|storeAuthStateInCookie  | （可选）标识用于存储身份验证请求状态的库。 在浏览器 Cookie 中验证身份验证流时需要此状态。 此 Cookie 为 Internet Explorer 和 Microsoft Edge 而设置，可以适应这两个浏览器。 有关更多详细信息，请参阅[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)。 |
>
> 若要查找“应用程序(客户端) ID”、“目录(租户) ID”和“支持的帐户类型”的值，请转到 Azure 门户中应用的“概述”页。   

> 有关可用的可配置选项的详细信息，请阅读[初始化客户端应用程序](msal-js-initializing-client-applications.md)。

> 可以在 GitHub 上的 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 存储库中找到 MSAL.js 库的源代码。

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
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>步骤 4：运行项目

如果使用 Node.js：

1. 从项目目录运行以下命令以启动服务器：

   ```console
   npm install
   npm start
   ```

1. 转到 `http://localhost:4200/`。
1. 选择“登录名”。 首次登录时，系统会提示你允许应用程序访问你的个人资料并自动将你登录。
1. 选择“个人资料”以调用 Microsoft Graph。 页面上会显示用户配置文件信息。

## <a name="how-the-sample-works"></a>示例工作原理

![示意图，展示了本快速入门中的示例应用的工作原理。](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>后续步骤

在 Angular 教程中了解如何将用户登录和获取令牌：

> [!div class="nextstepaction"]
> [Angular 教程](./tutorial-v2-angular.md)