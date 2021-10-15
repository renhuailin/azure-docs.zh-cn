---
title: 使用 Azure Active Directory B2C 在示例 Angular SPA 中配置身份验证
description: 了解如何使用 Azure Active Directory B2C 在 Angular SPA 中进行用户登录和注册。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 65190ed6f79a72f59c4329b6f413428ea8e7bb35
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426541"
---
# <a name="configure-authentication-in-a-sample-angular-single-page-application-by-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在示例 Angular 单页应用程序中配置身份验证

本文使用一个示例 Angular 单页应用程序 (SPA)，说明了如何向 Angular 应用添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是基于 OAuth 2.0 构建的身份验证协议，可用于将用户安全登录到应用程序。 此 Angular 示例使用 [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) 和 [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)。 MSAL 是 Microsoft 提供的一个库，可简化向 Angular SPA 添加身份验证和授权支持的过程。

### <a name="sign-in-flow"></a>登录流

登录流涉及以下步骤：

1. 用户打开应用并选择“登录”。 
1. 应用启动身份验证请求，并将用户重定向到 Azure AD B2C。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)并[重置密码](add-password-reset-policy.md)，或使用[社交帐户](add-identity-provider.md)登录。
1. 成功登录后，Azure AD B2C 向应用返回一个授权代码。 应用执行下列操作：
   1. 使用授权代码来交换 ID 令牌、访问令牌和刷新令牌。
   1. 读取 ID 令牌声明。
   1. 将访问令牌和刷新令牌存储在内存中的缓存中供以后使用。 访问令牌允许用户调用受保护的资源，例如 Web API。 刷新令牌用于获取新的访问令牌。

### <a name="app-registration"></a>应用注册

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，你需要在 Azure AD B2C 目录中注册两个应用程序：  

- 注册单页应用程序 (Angular) 后，你的应用就能够使用 Azure AD B2C 进行登录。 在应用注册过程中，请指定重定向 URI。 重定向 URI 是用户使用 Azure AD B2C 进行身份验证后重定向到的终结点。 此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为该应用的唯一标识 。 本文使用示例“应用 ID：1”。

- 注册 Web API 后，你的应用就能够调用受保护的 Web API。 注册将公开 Web API 权限（范围）。 应用注册过程会生成一个应用程序 ID，作为 Web API 的唯一标识。 本文使用示例“应用 ID：2”。 向应用（应用 ID：1）授予对 Web API 作用域（应用 ID：2）的权限 。  

以下关系图描述了应用注册和应用体系结构。

![描述具有 Web API、注册和令牌的单页应用程序的关系图。](./media/configure-authentication-sample-angular-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>调用 Web API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>注销流

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>先决条件

在按照本文中的步骤操作之前，请确保计算机正在运行：

* [Visual Studio Code](https://code.visualstudio.com/) 或其他代码编辑器。
* [Node.js 运行时](https://nodejs.org/en/download/)和 [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)。
* [Angular CLI](https://angular.io/cli)。

## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-angular-spa-and-api"></a>步骤 2：注册 Angular SPA 和 API

在此步骤中，你将为 Angular SPA 和 Web API 应用创建注册。 你还将指定 Web API 的作用域。

### <a name="21-register-the-web-api-application"></a>2.1 注册 Web API 应用程序

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 配置作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-angular-app"></a>2.3 注册 Angular 应用

按照以下步骤创建 Angular 应用注册：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 对于“名称”，请输入应用程序的名称。 例如，输入“MyApp”。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“单页应用程序(SPA)”，然后在 URL 框中输入 `http://localhost:4200` 。
1. 在“权限”下，选中“授予对 OpenID 和脱机访问权限的管理员许可”复选框。 
1. 选择“注册”  。
1. 记录“应用程序(客户端) ID”值，以便在稍后配置 Web 应用程序时使用。
    ![屏幕截图显示了如何获取 Angular 应用程序 ID。](./media/configure-authentication-sample-angular-spa-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 授予权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-angular-sample-code"></a>步骤 3：获取 Angular 示例代码

此示例演示了 Angular 单页应用程序如何使用 Azure AD B2C 来实现用户注册和登录。 然后，该应用将获取访问令牌并调用受保护的 Web API。

  [下载示例的 .zip 文件](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/archive/refs/heads/main.zip)，或使用以下命令从 [GitHub 存储库](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)克隆示例：

  ```
  git clone https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial.git
  ```

### <a name="31-configure-the-angular-sample"></a>3.1 配置 Angular 示例

获取 SPA 示例后，请使用 Azure AD B2C 和 Web API 值更新代码。 在示例文件夹的 src/app 文件夹下，打开 auth-config.ts 文件。 使用相应的值更新键：  


|部分  |密钥  |值  |
|---------|---------|---------|
| b2cPolicies | 姓名 |你在[步骤 1](#step-1-configure-your-user-flow) 中创建的用户流或自定义策略。 |
| b2cPolicies | authorities | 将 `your-tenant-name` 替换为你的 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)。 例如，使用 `contoso.onmicrosoft.com`。 然后，将策略名称替换为你在[步骤 1](#step-1-configure-your-user-flow) 中创建的用户流或自定义策略。 例如：`https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`。 |
| b2cPolicies | authorityDomain|你的 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)。 例如：`contoso.onmicrosoft.com`。 |
| 配置 | clientId | [步骤 2.3](#23-register-the-angular-app) 中的 Angular 应用程序 ID。 |
| protectedResources| endpoint| Web API 的 URL：`http://localhost:5000/api/todolist`。 |
| protectedResources| 范围| 你在[步骤 2.2](#22-configure-scopes) 中创建的 Web API 作用域。 例如：`b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`。 |

生成的 src/app/auth-config.ts 代码应类似于以下示例：

```typescript
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
    // More configuration here
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
```

## <a name="step-4-get-the-web-api-sample-code"></a>步骤 4：获取 Web API 示例代码

注册 Web API 并定义其作用域后，请配置 Web API 代码，使其适用于你的 Azure AD B2C 租户。

[下载 \*.zip 存档](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)，或者从 GitHub 克隆示例 Web API 项目。 还可以使用以下命令直接浏览到 GitHub 上的 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 项目：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-configure-the-web-api"></a>4.1 配置 Web API

在示例文件夹中，打开 config.json 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 Web API 应用使用此信息来验证访问令牌，该访问令牌由 Web 应用当作持有者令牌传递。 更新应用设置的以下属性：

|部分  |密钥  |值  |
|---------|---------|---------|
|凭据|tenantName| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一个部分。 例如：`contoso`。|
|凭据|clientID| 步骤 [2.1](#21-register-the-web-api-application) 中的 Web API 应用程序 ID。 在[上图](#app-registration)中，它是使用“应用 ID：2”的应用程序。|
|凭据| 颁发者| （可选）令牌颁发者 `iss` 声明值。 Azure AD B2C 默认返回采用以下格式的令牌：`https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`。 请将 `<your-tenant-name>` 替换为你的 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 将 `<your-tenant-ID>` 替换为你的 [Azure AD B2C 租户 ID](tenant-management.md#get-your-tenant-id)。 |
|策略|policyName|你在[步骤 1](#step-1-configure-your-user-flow) 中创建的用户流或自定义策略。 如果你的应用程序使用多个用户流或自定义策略，请仅指定一个。 例如，使用注册或登录用户流。|
| resource| scope | [步骤 2.5](#25-grant-permissions) 中的 Web API 应用程序注册的作用域。 |

你的最终配置文件应类似于下面的 JSON：

```json
{
    "credentials": {
        "tenantName": "<your-tenant-namee>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    // More settings here
} 
```

## <a name="step-5-run-the-angular-spa-and-web-api"></a>步骤 5：运行 Angular SPA 和 Web API

你现在可以测试 Angular 对 API 在作用域内的访问权限了。 在此步骤中，请在本地计算机上运行 Web API 和示例 Angular 应用程序。 然后，登录到 Angular 应用程序，并选择“待办事项列表”按钮来启动对受保护 API 的请求。

### <a name="run-the-web-api"></a>运行 Web API

1. 打开控制台窗口，切换到包含 Web API 示例的目录。 例如：

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 运行以下命令：

    ```console
    npm install && npm update
    node index.js
    ```

    控制台窗口将显示托管该应用程序的端口号：

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-angular-application"></a>运行 Angular 应用程序

1. 打开另一个控制台窗口，切换到包含 Angular 示例的目录。 例如：

    ```console
    cd ms-identity-javascript-angular-tutorial-main/3-Authorization-II/2-call-api-b2c/SPA
    ```

1. 运行以下命令：

    ```console
    npm install && npm update
    npm start
    ```

    控制台窗口将显示托管该应用程序的端口号：

    ```console
    Listening on port 4200...
    ```

1. 在浏览器中转到 `http://localhost:4200`，查看此应用程序。
1. 选择“登录名”。

    ![屏幕截图显示了带有登录链接的 Angular 示例应用。](./media/configure-authentication-sample-angular-spa-app/sample-app-sign-in.png)

1. 完成注册或登录过程。
1. 成功登录后，应会看到你的个人资料。 从菜单中选择“待办事项列表”。

    ![屏幕截图显示了 Angular 示例应用，其中包含用户个人资料以及对待办事项列表的调用。](./media/configure-authentication-sample-angular-spa-app/sample-app-result.png)

1. 选择“添加”向列表添加新项，或者使用图标删除或编辑项。

    ![屏幕截图显示了 Angular 示例应用对待办事项列表的调用。](./media/configure-authentication-sample-angular-spa-app/sample-app-calls-web-api.png)

## <a name="deploy-your-application"></a>部署应用程序 

在生产应用程序中，应用注册的重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://contoso.com`。 

可以随时在注册的应用程序中添加和修改重定向 URI。 重定向 URI 存在以下限制：

* 回复 URL 必须以方案 `https` 开头。
* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 

## <a name="next-steps"></a>后续步骤

* [详细了解代码示例](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)
* [在自己的 Angular 应用程序中启用身份验证](enable-authentication-angular-spa-app.md)
* [在 Angular 应用程序中配置身份验证选项](enable-authentication-angular-spa-app-options.md)
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)
