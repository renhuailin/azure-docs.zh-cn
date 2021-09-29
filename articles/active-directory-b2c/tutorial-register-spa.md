---
title: 教程：注册单页应用程序
titleSuffix: Azure AD B2C
description: 按照此教程进行操作，了解如何使用 Azure 门户在 Azure Active Directory B2C 中注册单页应用程序 (SPA)。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 82454e41ea9895c8006ac179a7c731a57a0c5c3b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581639"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中注册单页应用程序 (SPA)

必须在你管理的租户中注册应用程序，然后这些[应用程序](application-types.md)才能与 Azure Active Directory B2C (Azure AD B2C) 交互。 本教程介绍如何使用 Azure 门户注册单页应用程序 (SPA)。

## <a name="overview-of-authentication-options"></a>身份验证选项概述

许多新式 Web 应用程序都构建为客户端单页应用程序 (SPA)。 开发人员使用 JavaScript 或 SPA 框架（例如 Angular、Vue 和 React）来编写它们。 这些应用程序在 Web 浏览器上运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。

Azure AD B2C 提供了两个选项，用于允许单页应用程序让用户登录并获取用于访问后端服务或 Web API 的令牌：

### <a name="authorization-code-flow-with-pkce"></a>授权代码流（带有 PKCE）
- [OAuth 2.0 授权代码流（使用 PKCE）](./authorization-code-flow.md)。 授权代码流允许应用程序用授权代码来交换 **ID** 令牌（表示已经过身份验证的用户），以及交换调用受保护 API 所需的 **访问** 令牌。 此外，它还返回 **刷新** 令牌，这类令牌提供以用户身份长期访问资源而无需与这些用户交互的权限。 

这是 **建议的** 做法。 拥有使用期有限的刷新令牌可以帮助你的应用适应 Safari ITP 之类的[新式浏览器 cookie 隐私限制](../active-directory/develop/reference-third-party-cookies-spas.md)。

若要利用此流，应用程序可以使用支持它的身份验证库，如 [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)。 

![单页应用程序 - 授权](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>隐式授予流
- [OAuth 2.0 隐式流](implicit-flow-single-page-application.md)。 某些库（如 [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)）仅支持隐式授权流。 隐式授权流允许应用程序获取 **ID** 和 **访问** 令牌。 与授权代码流不同，隐式授权流不会返回 **刷新令牌**。 

![单页应用程序 - 隐式](./media/tutorial-single-page-app/spa-app.svg)

此身份验证流不包括使用 Electron 和 React-Native 之类的跨平台 JavaScript 框架的应用程序方案。 这些方案需要更多功能才能与本机平台进行交互。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 可以使用现有的 Azure AD B2C 租户。

## <a name="register-the-spa-application"></a>注册 SPA 应用程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如 spaapp1。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 
1. 在“重定向 URI”下，选择“单页应用程序(SPA)”，然后在 URL 文本框中输入 `https://jwt.ms` 。

    “重定向 URI”是授权服务器（在本例中为 Azure AD B2C）在完成与用户的交互后将用户定向到的终结点，也是在成功授权后向其发送访问令牌或授权代码的终结点。 在生产应用程序中，它通常是运行应用的公共可访问终结点，如 `https://contoso.com/auth-response`。 出于类似本教程的测试目的，你可以将它设置为 `https://jwt.ms`，这是一个 Microsoft 拥有的 Web 应用程序，用于显示已解码的令牌内容（令牌内容始终保留在浏览器中）。 在应用开发期间，你可以添加应用程序本地侦听的终结点，如 `http://localhost:5000`。 可以随时在注册的应用程序中添加和修改重定向 URI。

    重定向 URI 存在以下限制：

    * 除非使用 `localhost`，否则回复 URL 必须以方案 `https` 开头。
    * 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框。
1. 选择“注册”  。


## <a name="enable-the-implicit-flow"></a>启用隐式流
如果使用隐式流，则需要在应用注册中启用隐式授权流。

1. 在左侧菜单中的“管理”下，选择“身份验证” 。
1. 在“隐式授权”下，选中“访问令牌”和“ID 令牌”复选框  。
1. 选择“保存”。

## <a name="migrate-from-the-implicit-flow"></a>从隐式流迁移

如果拥有使用隐式流的现有应用程序，我们建议使用支持它的框架（如 [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)）迁移到使用授权代码流的应用程序。

当应用注册表示的所有生产单页应用程序都在使用授权代码流时，请禁用隐式授权流设置。 

1. 在左侧菜单中的“管理”下，选择“身份验证” 。
1. 在“隐式授权”下，取消选中“访问令牌”和“ID 令牌”复选框  。
1. 选择“保存”。

如果启用（选中）隐式流，则使用隐式流的应用程序可以继续运行。

* * *

## <a name="next-steps"></a>后续步骤

接下来，了解如何创建用户流以使用户能够注册、登录和管理其个人资料。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中创建用户流](tutorial-create-user-flows.md)
