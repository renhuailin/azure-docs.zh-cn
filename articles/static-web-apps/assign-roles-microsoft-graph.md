---
title: 教程：使用 Microsoft Graph 分配 Azure Static Web Apps 角色
description: 了解如何使用无服务器函数根据 Active Directory 组成员身份分配自定义用户角色。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: cshoe
keywords: 静态 Web 应用授权, 分配用户角色, 自定义角色
ms.openlocfilehash: bf9089f7e645dd32bbfd5f30041f76f71a04a5ec
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858822"
---
# <a name="tutorial-assign-custom-roles-with-a-function-and-microsoft-graph"></a>教程：使用函数和 Microsoft Graph 分配自定义角色

本文演示如何使用函数查询 [Microsoft Graph](https://developer.microsoft.com/graph) 并根据用户的 Active Directory 组成员身份向用户分配自定义角色。

在本教程中，学习：

- 部署静态 Web 应用。
- 创建 Azure Active Directory 应用注册。
- 使用 Azure Active Directory 设置自定义身份验证。
- 配置一个[无服务器函数](authentication-authorization.md?tabs=function#role-management)，该函数查询用户的 Active Directory 组成员身份并返回自定义角色的列表。

> [!NOTE]
> 本教程要求[使用函数来分配角色](authentication-authorization.md?tabs=function#role-management)。 基于函数的角色管理目前为预览版。

## <a name="prerequisites"></a>先决条件

- **有效的 Azure 帐户：** 如果你没有帐户，可以 [免费创建一个帐户](https://azure.microsoft.com/free/)。
- 你必须有足够的权限才能创建 Azure Active Directory 应用程序。

## <a name="create-a-github-repository"></a>创建 GitHub 存储库

1. 导航到以下位置以创建新存储库：
    - [https://github.com/staticwebdev/roles-function/generate](https://github.com/login?return_to=/staticwebdev/roles-function/generate)

1. 将存储库命名为“my-custom-roles-app”。

1. 选择“从模板创建存储库”。

## <a name="deploy-the-static-web-app-to-azure"></a>将静态 Web 应用部署到 Azure

1. 从浏览器的新窗口中导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 选择左上角的“创建资源”。

1. 在搜索框中键入“静态 Web 应用”。

1. 选择“静态 Web 应用”。

1. 选择“创建”。

1. 为 Azure 静态 Web 应用配置以下信息：

    | **输入** | 值 | 备注 |
    | ---------- | ---------- | ---------- |
    | _订阅_ | 选择 Azure 订阅 | |
    | _资源组_ | 创建名为“my-custom-roles-app-group”的新资源组 | |
    | _名称_ | my-custom-roles-app  | |
    | _托管计划_ | **标准** | 使用函数自定义身份验证和分配角色需要标准计划 |
    | _区域_ | 选择离你最近的区域 | |
    | _部署详细信息_ | 选择“GitHub”作为源 | |

1. 选择“使用 GitHub 登录”按钮，然后向 GitHub 进行身份验证。

1. 选择在其中创建存储库的“组织”名称。

1. 在“存储库”下拉列表中选择“my-custom-roles-app”。

1. 在“分支”下拉列表中选择“main”。

1. 在“生成详细信息”部分中，添加此应用的配置详细信息。

    | **输入** | 值 | 备注 |
    | ---------- | ---------- | ---------- |
    | 生成预设 | **自定义** | |
    | 应用位置 | 前端 | 存储库中包含应用的文件夹 |
    | _API 位置_ | api | 存储库中包含 API 的文件夹 |
    | 输出位置 | | 此应用没有生成输出 |

1. 选择“查看 + 创建”。 然后选择“创建”以创建静态 Web 应用并启动第一次部署。

1. 选择“转到资源”，打开新的静态 Web 应用。

1. 在概述部分中，找到应用程序的“URL”。 将此值复制到文本编辑器中，因为需要此 URL 来设置 Active Directory 身份验证并测试应用。

## <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

1. 在 Azure 门户中，搜索并导航到“Azure Active Directory”。

1. 在菜单栏中选择“应用注册”。

1. 选择“+ 新建注册”，以打开“注册应用程序”页面

1. 输入应用程序的名称。 例如“MyStaticWebApp”。

1. 对于“支持的帐户类型”设置，请选择“仅限此组织目录中的帐户”。 

1. 对于“重定向 URI”，请选择“Web”并输入静态 Web 应用的 Azure Active Directory 登录[身份验证回调](authentication-custom.md#authentication-callbacks)。 例如 `<YOUR_SITE_URL>/.auth/login/aad/callback`。

    将 `<YOUR_SITE_URL>` 替换为静态 Web 应用的 URL。

    :::image type="content" source="media/assign-roles-microsoft-graph/create-app-registration.png" alt-text="创建应用注册":::

1. 选择“注册”  。

1. 在应用注册创建后，将“基本信息”部分中的“应用程序(客户端) ID”和“目录(租户) ID”复制到文本编辑器中 。 在静态 Web 应用中需要这些值来配置 Active Directory 身份验证。

### <a name="enable-id-tokens"></a>启用 ID 令牌

1. 在菜单栏中，选择“身份验证”。

1. 在“隐式授权和混合流”部分，选择“ID 令牌(用于隐式流和混合流)”。

    :::image type="content" source="media/assign-roles-microsoft-graph/enable-id-tokens.png" alt-text="启用 ID 令牌":::
    
    Static Web Apps 需要此配置来对用户进行身份验证。

1. 选择“保存”。

### <a name="create-a-client-secret"></a>创建客户端机密

1. 在菜单栏中，选择“证书和密码”。

1. 在“客户端密码”部分中，选择“+ 新建客户端密码”。

1. 输入客户端密码的名称。 例如“MyStaticWebApp”。

1. 将“有效期”字段保留为默认值“6 个月”。

    > [!NOTE]
    > 必须在到期日期前通过生成新密码并使用它的值更新应用，来轮换密码。

1. 选择“添加”。

1. 记下你创建的客户端密码的值。 在静态 Web 应用中需要此值来配置 Active Directory 身份验证。

    :::image type="content" source="media/assign-roles-microsoft-graph/create-client-secret.png" alt-text="创建客户端机密":::

## <a name="configure-active-directory-authentication"></a>配置 Active Directory 身份验证

1. 在浏览器中，打开 GitHub 存储库，其中包含你部署的静态 Web 应用。 导航到应用的配置文件“frontend/staticwebapp.config.json”。 它包含以下部分：

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
              "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

    > [!NOTE]
    > 若要获取 Microsoft Graph 的访问令牌，`loginParameters` 字段必须配置为 `resource=https://graph.microsoft.com`。

1. 选择“编辑”按钮更新文件。

1. 通过将 `<YOUR_AAD_TENANT_ID>` 替换为 Azure Active Directory 的目录（租户）ID 来更新 `https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>` 的 openIdIssuer 值。

1. 选择“直接提交到主分支”并选择“提交更改” 。

1. GitHub Actions 运行触发器来更新静态 Web 应用。

1. 在 Azure 门户中导航到静态 Web 应用资源。

1. 在菜单栏中选择“配置”。

1. 在“应用程序设置”部分，添加以下设置：

    | 名称 | 值 |
    |------|-------|
    | `AAD_CLIENT_ID` | Active Directory 应用程序（客户端）ID |
    | `AAD_CLIENT_SECRET` | Active Directory 应用程序客户端密码值 |

1. 选择“保存”。

## <a name="verify-custom-roles"></a>验证自定义角色

该示例应用程序包含一个无服务器函数 (api/GetRoles/index.js)，该函数可查询 Microsoft Graph 来确定用户是否位于预定义的组中。 该函数基于用户的组成员身份为用户分配自定义角色。 应用程序配置为根据这些自定义角色限制某些路由。

1. 在 GitHub 存储库中，导航到位于“api/GetRoles/index.js”的“GetRoles”函数 。 在顶部附近有一个将自定义用户角色映射到 Azure Active Directory 组的 `roleGroupMappings` 对象。

1. 单击“编辑”按钮。

1. 用 Azure Active Directory 租户中的组 ID 更新对象。

    例如，如果你有 ID 为 `6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` 和 `b6059db5-9cef-4b27-9434-bb793aa31805` 的组，则可将对象更新为：

    ```js
    const roleGroupMappings = {
      'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
      'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    每当用户成功使用 Azure Active Directory 进行身份验证时，就会调用“GetRoles”函数。 该函数使用用户的访问令牌在 Microsoft Graph 中查询其 Active Directory 组成员身份。 如果用户是在 `roleGroupMappings` 对象中定义的任何组的成员，则该函数将返回相应的自定义角色。
    
    在上面的示例中，如果用户是 ID 为 `b6059db5-9cef-4b27-9434-bb793aa31805` 的 Active Directory 组的成员，则将为其授予 `reader` 角色。

1. 选择“直接提交到主分支”并选择“提交更改” 。

1. GitHub Actions 运行触发器来更新静态 Web 应用。

1. 部署完成后，可通过导航到应用的 URL 来验证更改。

1. 使用 Azure Active Directory 登录到静态 Web 应用。

1. 登录时，示例应用会根据标识的 Active Directory 组成员身份显示所分配的角色的列表。 根据这些角色，允许或禁止访问应用中的某些路由。

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来清理你部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。

1. 在“按名称筛选”字段中输入资源组名称。

1. 选择在本教程中使用的资源组名称。

1. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [身份验证和授权](./authentication-authorization.md)
