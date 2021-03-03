---
title: 生成 SCIM 终结点，以便用户预配到 Azure Active Directory 的应用
description: 了解如何开发 SCIM 终结点，如何将 SCIM API 与 Azure AD 集成，并使用 Azure Active Directory 自动将用户和组预配到你的云应用程序中。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: b69e701177c6f017388521ed05c37de1271c7e60
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650152"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>教程：开发示例 SCIM 终结点

没有人希望从头开始构建新的终结点，因此，我们创建了一些 [参考代码](https://aka.ms/scimreferencecode) ，让你开始使用 [系统进行跨域标识管理 (SCIM) ](https://aka.ms/scimoverview)。 只需5分钟即可启动并运行无代码的 SCIM 终结点。

本教程介绍如何在 Azure 中部署 SCIM 引用代码，并使用 Postman 或通过将与 Azure Active Directory (Azure AD 与) SCIM 客户端集成来对其进行测试。 本教程适用于想要开始使用 SCIM 的开发人员，或对测试 SCIM 终结点感兴趣的任何人。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 在 Azure 中部署 SCIM 终结点。
> * 测试 SCIM 终结点。

## <a name="deploy-your-scim-endpoint-in-azure"></a>在 Azure 中部署 SCIM 终结点

此处的步骤使用 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 和 [Azure App Service](../../app-service/index.yml)将 SCIM 终结点部署到服务。 SCIM 引用代码还可以在本地运行，由本地服务器托管，也可以部署到另一个外部服务。

1. 从 GitHub 中转到 [引用代码](https://github.com/AzureAD/SCIMReferenceCode) ，然后选择 " **克隆" 或 "下载**"。

1. 选择 " **在桌面中打开**"，或复制链接，打开 Visual Studio，然后选择 " **克隆" 或 "查看代码** " 以输入复制的链接并创建本地副本。

1. 在 Visual Studio 中，请确保登录到有权访问托管资源的帐户。

1. 在解决方案资源管理器中，打开 *SCIM* ，然后右键单击 *SCIM. WebHostSample* 文件。 选择“发布”。

    ![显示示例文件的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 若要在本地运行此解决方案，请双击项目，然后选择 " **IIS Express** " 以将项目作为带有本地主机 URL 的网页启动。

1. 选择 " **创建配置文件** "，并确保选中 " **应用服务** " 和 " **新建** "。

    ![显示发布窗口的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 单步执行对话框选项，并将应用重命名为你选择的名称。 此名称用于应用和 SCIM 终结点 URL。

    ![显示创建新应用服务的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 选择要使用的资源组，然后选择 " **发布**"。

1. 在 " **Azure App Service** 配置" 中，选择 "应用程序"  >   ，然后选择 "**新建应用程序" 设置**，添加值为 *Token__TokenIssuer* 设置 `https://sts.windows.net/<tenant_id>/` 。 `<tenant_id>`将替换为你的 Azure AD 租户 ID。 如果要使用 [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)测试 SCIM 终结点，请使用值添加 *ASPNETCORE_ENVIRONMENT* 设置 `Development` 。

   ![显示 "应用程序设置" 窗口的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   在 [Azure 门户](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)中使用企业应用程序测试终结点时，有两种选择。 你可以将环境保留在中 `Development` ，并提供终结点的测试令牌 `/scim/token` ，也可以将环境更改为 `Production` ，并将 "标记" 字段留空。

就这么简单！ 现在，你的 SCIM 终结点已发布，可以使用 Azure App Service URL 来测试 SCIM 终结点。

## <a name="test-your-scim-endpoint"></a>测试 SCIM 终结点

对 SCIM 终结点的请求需要授权。 SCIM 标准具有多个用于身份验证和授权的选项，包括 cookie、基本身份验证、TLS 客户端身份验证或 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)中列出的任何方法。

请确保避免使用不安全的方法（例如用户名和密码），以支持更安全的方法，例如 OAuth。 Azure AD 为库和非库应用程序支持长生存期持有者令牌 () 和 OAuth 授权 (库应用程序) 。

> [!NOTE]
> 存储库中提供的授权方法仅用于测试。 与 Azure AD 集成时，可以查看授权指南。 请参阅 [计划 SCIM 终结点的预配](use-scim-to-provision-users-and-groups.md)。

开发环境启用了对生产不安全的功能，如控制安全令牌验证行为的引用代码。 令牌验证代码使用自签名安全令牌，签名密钥存储在配置文件中。 请参阅 *appsettings.Development.json* file 中的 **Token： IssuerSigningKey** 参数。

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> 向终结点发送 **GET** 请求时 `/scim/token` ，将使用配置的密钥发出令牌。 该令牌可以用作持有者令牌以供后续授权使用。

默认令牌验证代码配置为使用 Azure AD 令牌，并且需要使用 *appsettings.json* file 中的 **token： TokenIssuer** 参数配置发证租户。

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>使用 Postman 测试终结点

部署 SCIM 终结点之后，可以进行测试，以确保其符合 SCIM RFC。 此示例在 Postman 中提供一组测试，这些测试将验证 CRUD (创建、读取、更新和删除用户和组上的) 操作、筛选、对组成员身份的更新以及禁用用户。

终结点在目录中 `{host}/scim/` ，你可以使用标准 HTTP 请求与它们进行交互。 若要修改 `/scim/` 路由，请参阅 **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **控制器** 中的 ControllerConstant.cs。

> [!NOTE]
> 只能使用 HTTP 终结点进行本地测试。 Azure AD 预配服务要求终结点支持 HTTPS。

1. 下载 [Postman](https://www.getpostman.com/downloads/) 并启动应用程序。
1. 将此链接复制并粘贴到 Postman 中以导入测试集合： `https://aka.ms/ProvisioningPostman` 。

    ![显示如何导入 Postman 中的测试集合的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 创建具有以下变量的测试环境：

   |环境|变量|Value|
   |-|-|-|
   |使用 IIS Express 在本地运行项目|||
   ||**Server**|`localhost`|
   ||端口 |`:44359`*(忘记 **`:`**)*|
   ||**Api**|`scim`|
   |使用 Kestrel 在本地运行项目|||
   ||**Server**|`localhost`|
   ||端口 |`:5001`*(忘记 **`:`**)*|
   ||**Api**|`scim`|
   |在 Azure 中托管终结点|||
   ||**Server**|*(输入 SCIM URL)*|
   ||端口 |*(留空)*|
   ||**Api**|`scim`|

1. 使用 Postman 集合中的 " **获取密钥** " 将 **get** 请求发送到令牌终结点，并检索要在 **令牌** 变量中存储的用于后续请求的安全令牌。

   ![显示 Postman 获取密钥文件夹的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > 若要使 SCIM 终结点安全，需要在连接前使用安全令牌。 本教程使用 `{host}/scim/token` 终结点生成自签名令牌。

就这么简单！ 你现在可以运行 **Postman** 集合来测试 SCIM 终结点功能。

## <a name="next-steps"></a>后续步骤

若要为客户端开发具有互操作性的 SCIM 兼容用户和组终结点，请参阅 [SCIM 客户端实现](http://www.simplecloud.info/#Implementations2)。

> [!div class="nextstepaction"]
> [教程：开发和规划 SCIM 终结点](use-scim-to-provision-users-and-groups.md) 
>  的预配[教程：配置库应用的预配](configure-automatic-user-provisioning-portal.md)