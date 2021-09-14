---
title: 使用 Azure Active Directory B2C 在示例 Python Web 应用程序中配置身份验证
description: 本文讨论如何在 Python Web 应用程序中使用 Azure Active Directory B2C 执行用户登录和注册。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 89fb5fdd217ddc38a4b3f80504b3c48a882c4251
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471923"
---
# <a name="configure-authentication-in-a-sample-python-web-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在示例 Python Web 应用中配置身份验证

本文使用一个示例 Python Web 应用程序来说明如何向 Web 应用程序添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是在 OAuth 2.0 上构建的身份验证协议。 可以使用 OIDC 安全地将用户登录到应用程序。 此 Web 应用示例使用[适用于 Python 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-python)。 通过 MSAL for Python，可更轻松地向 Python Web 应用添加身份验证和授权支持。 

登录流涉及以下步骤：

1. 用户前往 Web 应用，并选择“登录”。 
1. 应用发起身份验证请求，并将用户重定向到 Azure AD B2C。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)、[重置密码](add-password-reset-policy.md)，或使用[社交帐户](add-identity-provider.md)登录。
1. 用户成功登录后，Azure AD B2C 向应用返回 ID 令牌。
1. 应用使用 ID 令牌交换授权代码、验证 ID 令牌、读取声明，然后向用户返回安全页。


### <a name="sign-out"></a>注销

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>先决条件

运行以下内容的计算机： 

* [Visual Studio Code](https://code.visualstudio.com/) 或其他代码编辑器
* [Python](https://nodejs.org/en/download/) 2.7+ 或 3+ 

## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>步骤 2：注册 Web 应用

若要让应用程序使用 Azure AD B2C 登录，请在 Azure AD B2C 目录中注册应用。 注册应用会在应用与 Azure AD B2C 之间建立信任关系。  

在应用注册过程中，要指定重定向 URI。 重定向 URI 是用户通过 Azure AD B2C 完成身份验证之后 Azure AD B2C 将用户重定向到的终结点。 此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为该应用的唯一标识。  注册应用后，Azure AD B2C 将使用应用程序 ID 和重定向 URI 来创建身份验证请求。 

### <a name="step-21-register-the-app"></a>步骤 2.1：注册应用

若要创建 Web 应用注册，请执行下列操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 在“名称”下，输入应用程序的名称（例如 webapp1）。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“Web”，然后在 URL 框中输入 `http://localhost:5000/getAToken` 。
1. 在“权限”下，选中“授予对 OpenID 和脱机访问权限的管理员许可”复选框。 
1. 选择“注册”  。
1. 选择“概述”。
1. 记录“应用程序(客户端) ID”，以便在稍后配置 Web 应用程序时使用。

    ![用于记录 Web 应用 ID 的 Web 应用概述页面屏幕截图。](./media/configure-authentication-sample-python-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-create-a-web-app-client-secret"></a>步骤 2.2：创建 Web 应用客户端密码

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]

## <a name="step-3-get-the-web-app-sample"></a>步骤 3：获取 Web 应用示例

[下载 zip 文件](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)，或从 GitHub 克隆示例 Web 应用。 

```bash
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

将示例文件提取到文件夹，该文件夹路径的总字符长度须不得超过 260 个字符。

## <a name="step-4-configure-the-sample-web-app"></a>步骤 4：配置示例 Web 应用

在项目的根目录中，执行以下操作：

1. 将 app_config.py 文件重命名为 app_config.py.OLD 。
1. 将 app_config_b2c.py 文件重命名为 app_config.py 。 

打开 app_config.py 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 更新以下应用设置属性： 

|密钥  |值  |
|---------|---------|
|`b2c_tenant`| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分（例如 `contoso`）。|
|`CLIENT_ID`| [步骤 2.1](#step-21-register-the-app) 中的 Web API 应用 ID。|
|`CLIENT_SECRET`| [步骤 2.2](#step-22-create-a-web-app-client-secret) 中创建的客户端密码。 为了帮助提高安全性，请考虑改为将它存储在注释中建议的环境变量中。 |
|`*_user_flow`|用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|
| | |

你的最终配置文件应类似于以下 Python 代码：

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.
```

> [!IMPORTANT]
> 如代码片段注释中所述，建议不要在应用程序代码中以明文形式存储机密。 在代码示例中使用硬编码变量只是为了方便起见。 请考虑使用环境变量或机密存储，例如 Azure 密钥保管库。


## <a name="step-5-run-the-sample-web-app"></a>步骤 5：运行示例 Web 应用

1. 在控制台或终端中，切换到包含示例的目录。 例如：

    ```console
    cd ms-identity-python-webapp
    ```
1. 运行以下命令，来从 PyPi 安装所需的包，并在本地计算机上运行 Web 应用：

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    控制台窗口显示本地运行的应用程序的端口号：

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on `http://localhost:5000/` (Press CTRL+C to quit)
    ```

 
1. 若要查看在本地计算机上运行的 Web 应用程序，请转到 `http://localhost:5000`。 

1. 选择“登录”。

    ![显示使用 Azure AD B2C 登录的屏幕截图。](./media/configure-authentication-sample-python-web-app/web-app-sign-in.png)


1. 完成注册或登录过程。

1. 身份验证成功后，你会看到显示名称，如下所示： 

    ![显示 Web 应用令牌的显示名称声明的屏幕截图。](./media/configure-authentication-sample-python-web-app/web-app-token-claims.png)


## <a name="step-6-call-to-a-web-api"></a>步骤 6：调用 Web API

要使应用能够通过 Azure AD B2C 登录并调用 Web API，必须在 Azure AD B2C 目录中注册两个应用程序。  

- [步骤 2](#step-2-register-a-web-application) 中已创建的 Web 应用程序 (Python) 注册。 通过此应用注册，你的应用可使用 Azure AD B2C 进行登录。  此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为该应用的唯一标识。  例如“应用 ID：1”。

- 注册 Web API 后，你的应用就能够调用受保护的 Web API。 注册将公开 Web API 权限（范围）。 此应用注册过程会生成一个应用程序 ID，它可唯一标识你的 Web API（例如应用 ID：2） 。 向应用（应用 ID：1）授予对 Web API 范围（应用 ID：2）的权限。  

下图描述了应用注册和应用程序体系结构：

![图中描绘了一个涉及 Web API、注册和令牌的 Web 应用。](./media/configure-authentication-sample-python-web-app/web-app-with-api-architecture.png) 

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="step-61-register-the-web-api-app"></a>步骤 6.1：注册 Web API 应用

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-62-configure-scopes"></a>步骤 6.2：配置范围

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-63-grant-the-web-app-permissions"></a>步骤 6.3：授予 Web 应用权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

### <a name="step-64-configure-your-web-api"></a>步骤 6.4：配置 Web API

此示例获取一个访问令牌，它具有 Web 应用可对 Web API 使用的相关范围。 要从代码调用 Web API，请使用现有的 Web API 或创建新的 API。 有关详细信息，请参阅[使用 Azure AD B2C 在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)。

### <a name="step-65-configure-the-sample-app-with-the-web-api"></a>步骤 6.5：使用 Web API 配置示例应用

打开 app_config.py 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 更新应用设置的以下属性： 

|键  |值  |
|---------|---------|
|`ENDPOINT`| Web API 的 URI（例如 `https://localhost:44332/hello`）。|
|`SCOPE`| 你创建的 Web API [范围](#step-62-configure-scopes)。|
| | |

你的最终配置文件应类似于以下 Python 代码：

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.

### More code here

# This is the API resource endpoint
ENDPOINT = 'https://localhost:44332' 


SCOPE = ["https://contoso.onmicrosoft.com/api/demo.read", "https://contoso.onmicrosoft.com/api/demo.write"] 
```

### <a name="step-66-run-the-sample-app"></a>步骤 6.6：运行示例应用

1. 在控制台或终端中，切换到包含示例的目录。 
1. 停止应用。 然后重新运行。
1. 选择“调用 Microsoft Graph API”。

    ![显示如何调用 Web API 的屏幕截图。](./media/configure-authentication-sample-python-web-app/call-web-api.png)

## <a name="step-7-deploy-your-application"></a>步骤 7：部署应用程序 

在生产应用程序中，应用注册重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://contoso.com/getAToken`。 

可以随时在注册的应用程序中添加和修改重定向 URI。 重定向 URI 存在以下限制：

* 回复 URL 必须以方案 `https` 开头。
* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 Azure AD B2C 在 Python Web 应用中配置验证选项](enable-authentication-python-web-app-options.md)。
