---
title: 快速入门：从 Node.js 控制台应用调用 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入门中，你将下载并运行一个代码示例，该代码示例演示 Node.js 控制台应用程序如何使用应用自己的标识获取访问令牌并调用受 Microsoft 标识平台终结点保护的 API
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: ffe6f750604546a51a7d3d7852df5745ba07dd3a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619129"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>快速入门：使用应用的标识获取令牌并从 Node.js 控制台应用中调用 Microsoft Graph API

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Node.js 控制台应用程序如何使用应用的标识获取访问令牌来调用 Microsoft Graph API 并在目录中显示[用户列表](/graph/api/user-list)。 代码示例演示无人参与的作业或 Windows 服务如何使用应用程序标识而不是用户标识运行。

本快速入门通过[客户端凭据授权](v2-oauth2-client-creds-grant-flow.md)使用[适用于 Node.js 的 Microsoft 身份验证库 (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)。

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>注册并下载示例应用程序
>
> 请按照以下步骤开始使用。
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的名称（例如 `msal-node-cli`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 选择“注册”  。
> 1. 在“管理”下，选择“证书和机密”。  
> 1. 在“客户端机密”下，选择“新建客户端机密”，输入名称，然后选择“添加”  。 将机密值记录在安全的位置，以供在后面的步骤中使用。
> 1. 在“管理”下，选择“API 权限” > “添加权限”  。 选择“Microsoft Graph”。
> 1. 选择“应用程序权限”。
> 1. 在“用户”节点下选择“User.Read.All”，然后选择“添加权限”  。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>下载并配置示例应用
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使本快速入门的代码示例正常运行，需创建客户端机密，并添加 Graph API 的 **User.Read.All** 应用程序权限。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-netcore-daemon/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-nodejs-sample-project"></a>步骤 2：下载 Node.js 示例项目

> [!div renderon="docs"]
> [下载代码示例](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>步骤 3：配置 Node.js 示例项目
>
> 1. 将 zip 文件提取到靠近磁盘根目录的本地文件夹，例如 C:/Azure-Samples。
> 1. 编辑 .env，将字段 `TENANT_ID`、`CLIENT_ID`、`CLIENT_SECRET` 的值替换为以下代码片段：
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    其中：
>    - `Enter_the_Application_Id_Here` - 先前注册的应用程序的应用程序（客户端）ID。 在 Azure 门户的应用注册“概述”窗格中找到此 ID。
>    - `Enter_the_Tenant_Id_Here` - 将此值替换为租户 ID 或租户名称（例如 contoso.microsoft.com） 。  在 Azure 门户的应用注册“概述”窗格中找到这些值。
>    - `Enter_the_Client_Secret_Here` - 将此值替换为先前创建的客户端密码。 若要生成新密钥，请在 Azure 门户的应用注册设置中使用“证书和密码”。
>
> > [!WARNING]
> > 源代码中的任何纯文本机密都会增加安全风险。 本文使用纯文本客户端机密只是为了简单起见。 在机密客户端应用程序中使用[证书凭据](active-directory-certificate-credentials.md)（而不是客户端密码），尤其是计划部署到生产环境的应用。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>步骤 3：管理员同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>步骤 4：管理员同意

如果尝试在此时运行应用程序，则会收到“HTTP 403 - 禁止访问”错误：`Insufficient privileges to complete the operation`。 之所以出现这种错误，是因为任何仅限应用的权限都需要管理员同意：目录的全局管理员必须为应用程序授予同意。 根据自己的角色选择下面的一个选项：

##### <a name="global-tenant-administrator"></a>全局租户管理员

> [!div renderon="docs"]
> 如果你是全局租户管理员，请转到 Azure 门户的“应用程序注册”中的“API 权限”页，选择“为 {租户名称} 授予管理员同意”（其中，{租户名称} 是目录的名称） 。

> [!div renderon="portal" class="sxs-lookup"]
> 如果你是全局管理员，请转到“API 权限”页，选择“为 Enter_the_Tenant_Name_Here 授予管理员许可”
> > [!div id="apipermissionspage"]
> > [转到“API 权限”页]()

##### <a name="standard-user"></a>标准用户

如果你是租户的标准用户，则需请求全局管理员为你的应用程序授予管理员同意。 为此，请将以下 URL 提供给管理员：

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> 其中：
>> * `Enter_the_Tenant_Id_Here` - 将此值替换为 **租户 ID** 或 **租户名称**（例如 contoso.microsoft.com）
>> * `Enter_the_Application_Id_Here` - 是已注册应用程序的 **应用程序（客户端）ID**。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>步骤 4：运行应用程序

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>步骤 5：运行应用程序

定位到命令提示符或控制台中示例的根文件夹（`package.json` 所在位置）。 需要安装一次此示例的依赖项：

```console
npm install
```

然后，通过命令提示符或控制台运行应用程序：

```console
node . --op getUsers
```

应该会在控制台输出上看到一些 JSON 片段，表示 Azure AD 目录中的用户列表。

## <a name="about-the-code"></a>关于代码

下面讨论了示例应用程序的一些重要方面。

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) 是一个用于登录用户和请求令牌的库，此类令牌用于访问受 Microsoft 标识平台保护的 API。 如前所述，本快速入门通过应用程序权限请求令牌（使用应用程序自身的标识），而不是通过委托的权限。 此示例中使用的身份验证流称为 [OAuth 2.0 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。 若要详细了解如何搭配使用 MSAL Node 和守护程序应用，请参阅[方案：守护程序应用程序](scenario-daemon-overview.md)。

 可通过运行以下 npm 命令安装 MSAL Node。

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL 初始化

可以通过添加以下代码，为 MSAL 添加引用：

```javascript
const msal = require('@azure/msal-node');
```

然后，使用以下代码对 MSAL 进行初始化：

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | 其中： |说明 |
> |---------|---------|
> | `clientId` | 是在 Azure 门户中注册的应用程序的 **应用程序(客户端) ID**。 可以在 Azure 门户的应用的“概览”页中找到此值。 |
> | `authority`    | 用户要进行身份验证的 STS 终结点。 对于公有云，通常为 `https://login.microsoftonline.com/{tenant}`，其中 {tenant} 是租户名称或租户 ID。|
> | `clientSecret` | 是在 Azure 门户中为应用程序创建的客户端机密。 |

有关详细信息，请参阅 [`ConfidentialClientApplication` 的参考文档](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>请求令牌

若要通过应用的标识来请求令牌，请使用 `acquireTokenByClientCredential` 方法：

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |其中：| 说明 |
> |---------|---------|
> | `tokenRequest` | 包含请求的范围。 对于机密客户端，这应该使用与 `{Application ID URI}/.default` 类似的格式，指示所请求的范围是在 Azure 门户的应用对象集中静态定义的范围（就 Microsoft Graph 来说，`{Application ID URI}` 指向 `https://graph.microsoft.com`）。 对于自定义 Web API，`{Application ID URI}` 在 Azure 门户的“应用程序注册”的“公开 API”部分中定义。 |
> | `tokenResponse` | 响应包含所请求范围的访问令牌。 |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解如何通过 MSAL Node 进行守护程序/控制台应用开发，请参阅教程：

> [!div class="nextstepaction"]
> [调用 Web API 的守护程序应用程序](tutorial-v2-nodejs-console.md)