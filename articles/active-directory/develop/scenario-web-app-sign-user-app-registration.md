---
title: 注册登录用户的 Web 应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何注册用于登录用户的 Web 应用
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0a32bef26db276b8858614ae3c2b86586f040aad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630350"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>用于登录用户的 Web 应用：应用注册

文本介绍用于登录用户的 Web 应用的应用注册步骤。

若要注册应用程序，可以使用：

- [Web 应用快速入门](#register-an-app-by-using-the-quickstarts)。 除了提供创建应用程序的第一手体验，Azure 门户中的快速入门还包含名为“为我进行此更改”的按钮。  可以使用此按钮设置所需属性，对现有应用也可以这样做。 根据自己的情况调整这些属性的值。 具体而言，应用的 Web API URL 可能会不同于建议的默认值，后者还会影响注销 URI。
- 用于[手动注册应用程序](#register-an-app-by-using-the-azure-portal)的 Azure 门户。
- PowerShell 和命令行工具。

## <a name="register-an-app-by-using-the-quickstarts"></a>按照快速入门注册应用

可使用以下链接启动 Web 应用程序的创建：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>使用 Azure 门户注册应用程序

> [!NOTE]
> 要使用的门户根据应用程序是在 Microsoft Azure 公有云中运行还是在国家云或主权云中运行而异。 有关详细信息，请参阅[国家云](./authentication-national-cloud.md#app-registration-endpoints)。


1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。 
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
1. 搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册” > “新建注册”  。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. “注册应用程序”页出现后，请输入应用程序的注册信息： 
   1. 输入应用程序的名称（例如 `AspNetCore-WebApp`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
   1. 为应用程序选择支持的帐户类型。 （请参阅[支持的帐户类型](./v2-supported-account-types.md)。）
   1. 在“重定向 URI”中，添加将在成功进行身份验证后接受返回的令牌响应的应用程序类型和 URI 目标。  例如，输入 `https://localhost:44321`。
   1. 选择“注册”。
1. 在“管理”下，选择“身份验证”，然后添加以下信息：
   1. 在“Web”部分，添加 `https://localhost:44321/signin-oidc` 作为“重定向 URI” 。
   1. 在前通道注销 URL 中，输入 `https://localhost:44321/signout-oidc`。
   1. 在“隐式授权和混合流”下，选择“ID 令牌” 。
   1. 选择“保存”。
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 输入应用程序的名称（例如 `MailApp-openidconnect-v2`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
   1. 为应用程序选择支持的帐户类型。 （请参阅[支持的帐户类型](./v2-supported-account-types.md)。）
   1. 在“重定向 URI（可选）”部分的组合框中选择“Web”，然后输入 `https://localhost:44326/` 的重定向 URI  。
   1. 选择“注册”以创建应用程序。
1. 在“管理”下，选择“身份验证”。 
1. 在“隐式授权和混合流”部分，选择“ID 令牌” 。 本示例需要启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)才能将用户登录。
1. 选择“保存”。

# <a name="java"></a>[Java](#tab/java)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息： 
    1. 输入应用程序的名称（例如 `java-webapp`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。 
    1. 选择“任何组织目录中的帐户和个人 Microsoft 帐户”（例如 Skype、Xbox、Outlook.com）。
    1. 选择“注册”以注册应用程序。
1. 在“管理”下，选择“身份验证” > “添加平台”  。
1. 选择“Web”。
1. 对于“重定向 URI”，请输入同一主机和端口号，后跟 `/msal4jsample/secure/aad` 作为登录页。 
1. 选择“配置” 。
1. 在 **Web** 部分，使用主机和端口号，后跟`/msal4jsample/graph/me`作为用户信息页的 **重定向 URI**。
默认情况下，该示例使用：
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. 选择“保存”。
1. 在“管理”下，选择“证书和机密”。  
1. 在“客户端机密”部分，选择“新建客户端机密”，然后： 

   1. 输入密钥说明。
   1. 选择密钥持续时间“1 年”。
   1. 选择 **添加** 。
   1. 显示密钥值后，请将其复制供稍后使用。 此值不会再次显示，也无法通过任何其他方式检索。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 输入应用程序的名称（例如 `node-webapp`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
   1. 将“支持的帐户类型”更改为“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)” 。
   1. 在“重定向 URI (可选)”部分，选择组合框中的“Web”并输入以下重定向 URI：`http://localhost:3000/redirect`。
   1. 选择“注册”以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值供稍后使用。  你将需要使用该值为此项目配置配置文件。
1. 在“管理”下，选择“证书和机密”。  
1. 在“客户端机密”部分，选择“新建客户端机密”，然后：
   1. 输入密钥说明。
   1. 选择密钥持续时间“1 年”。
   1. 选择 **添加** 。
   1. 显示密钥值后，请将其复制。 稍后需要用到此信息。

# <a name="python"></a>[Python](#tab/python)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 输入应用程序的名称（例如 `python-webapp`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
   1. 将“支持的帐户类型”更改为“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)” 。
   1. 在“重定向 URI (可选)”部分，选择组合框中的“Web”并输入以下重定向 URI：`http://localhost:5000/getAToken`。
   1. 选择“注册”以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值供稍后使用。  稍后需要使用它为此项目配置 Visual Studio 配置文件。
1. 在“管理”下，选择“证书和机密”。  
1. 在“客户端机密”部分，选择“新建客户端机密”，然后：
   1. 输入密钥说明。
   1. 选择密钥持续时间“1 年”。
   1. 选择 **添加** 。
   1. 显示密钥值后，请将其复制。 稍后需要用到此信息。
---

## <a name="register-an-app-by-using-powershell"></a>使用 PowerShell 注册应用

> [!NOTE]
> 目前，Azure AD PowerShell 只能使用下述受支持帐户类型创建应用程序：
>
> - MyOrg（仅限此组织目录中的帐户）
> - AnyOrg（任何组织目录中的帐户）
>
> 你可以创建一个用户使用其个人 Microsoft 帐户（例如 Skype、Xbox 或 Outlook.com）进行登录的应用程序。 首先，创建一个多租户应用程序。 支持的帐户类型为任何组织目录中的帐户。 然后，在 Azure 门户的[应用程序清单](./reference-app-manifest.md)中，将 [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) 属性更改为 2，将 [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) 属性更改为 `AzureADandPersonalMicrosoftAccount`。 有关详细信息，请参阅 ASP.NET Core 教程中的[步骤 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)。 可以将此步骤通用化到任何语言的 Web 应用。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[应用的代码配置](scenario-web-app-sign-user-app-configuration.md)。
