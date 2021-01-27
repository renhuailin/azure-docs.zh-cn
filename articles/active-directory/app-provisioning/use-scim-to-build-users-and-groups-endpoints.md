---
title: 生成 SCIM 终结点，以便用户预配到 Azure Active Directory 的应用
description: 跨域身份管理系统 (SCIM) 将自动用户预配标准化。 了解如何开发 SCIM 终结点，如何将 SCIM API 与 Azure Active Directory 集成，并开始使用 Azure Active Directory 自动将用户和组预配到你的云应用程序中。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 34fa76197c4e08cffd1d8c66d6877b3e427e9fd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918138"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>教程：开发示例 SCIM 终结点

没有人希望从头开始构建新的终结点，因此，我们已经创建了一些 [参考代码](https://aka.ms/scimreferencecode) ，以便你开始使用 [SCIM](https://aka.ms/scimoverview)。 本教程介绍如何在 Azure 中部署 SCIM 引用代码，并使用 Postman 或通过与 Azure AD SCIM 客户端集成来测试该代码。 只需5分钟即可启动并运行 SCIM 终结点，无需任何代码。 本教程适用于想要开始使用 SCIM 或其他对 SICM 终结点进行测试的开发人员。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中部署 SCIM 终结点
> * 测试 SCIM 终结点

## <a name="deploy-your-scim-endpoint-in-azure"></a>在 Azure 中部署 SCIM 终结点

此处提供的步骤使用 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 和 [AZURE 应用服务](https://docs.microsoft.com/azure/app-service/)将 SCIM 终结点部署到服务。 SCIM 引用代码还可以在本地运行，由本地服务器托管，也可以部署到另一个外部服务。 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>打开解决方案并部署到 Azure App Service

1. 从 GitHub 中转到 [引用代码](https://github.com/AzureAD/SCIMReferenceCode) ，然后选择 " **克隆" 或 "下载**"。

1. 选择 **在桌面中打开**，或复制链接，打开 **Visual Studio**，然后选择 "克隆" **或 "签出代码** " 以输入复制的链接并创建本地副本。

1. 在 **Visual Studio** 中，请确保登录到有权访问托管资源的帐户。

1. 在 **解决方案资源管理器** 中，打开 *SCIM* ，然后右键单击 *SCIM. WebHostSample* 文件。 选择“发布”。

    ![云发布](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 若要在本地运行此解决方案，请双击项目，然后选择 " **IIS Express** " 以将项目作为包含本地主机 URL 的网页启动。

1. 选择 " **创建配置文件** "，确保选中 " **应用服务** " 和 " **新建** "。

    ![云发布2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 单步执行对话框选项，并将应用重命名为你选择的名称。 此名称用于应用和 SCIM 终结点 URL。

    ![云发布3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 选择要使用的资源组，然后选择 " **发布**"。

1. 在 **Azure 应用 Services** 配置 "中导航到应用程序  >   ，然后选择"**新建应用程序设置**"以添加具有值的 *Token__TokenIssuer* 设置 `https://sts.windows.net/<tenant_id>/` 。 `<tenant_id>`将替换为你的 Azure AD tenant_id 如果你想要使用 [POSTMAN](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)测试 SCIM 终结点，则还要添加一个具有值的 *ASPNETCORE_ENVIRONMENT* 设置 `Development` 。 

   ![appservice 设置](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   当使用 Azure 门户中的企业应用程序测试终结点时，请选择将环境保留为 `Development` ，并提供从 `/scim/token` 终结点生成的用于测试或将环境更改为的令牌 `Production` ，并将 [Azure 门户](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)中企业应用程序的令牌字段留空。 

就这么简单！ 现在，你的 SCIM 终结点已发布，并允许你使用 Azure App Service URL 来测试 SCIM 终结点。

## <a name="test-your-scim-endpoint"></a>测试 SCIM 终结点

对 SCIM 终结点的请求需要授权，并且 SCIM 标准保留了多个用于身份验证和授权的选项，如 cookie、基本身份验证、TLS 客户端身份验证或 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)中列出的任何方法。

请确保避免使用不安全的方法（如用户名/密码），以支持更安全的方法，例如 OAuth。 Azure AD 为库和非库应用程序支持长生存期持有者令牌 () 和 OAuth 授权授予应用库中发布的应用程序的 () 。

> [!NOTE]
> 存储库中提供的授权方法仅用于测试。 与 Azure AD 集成时，可以查看授权指南，请参阅 [计划 SCIM 终结点的预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery)。 

开发环境启用了生产不安全的功能，如控制安全令牌验证行为的引用代码。 令牌验证代码配置为使用自签名安全令牌，并且签名密钥存储在配置文件中，请参阅 IssuerSigningKey *on fileappsettings.Development.js* 中的 **token：** 参数。

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> 通过将 **GET** 请求发送到 `/scim/token` 终结点，将使用配置的密钥来颁发令牌，并可将其用作用于后续授权的持有者令牌。

默认令牌验证代码配置为使用 Azure Active Directory 颁发的令牌，并且需要使用 *appsettings.js文件上* 的 **令牌： TokenIssuer** 参数配置颁发租户。

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>使用 Postman 测试终结点

部署 SCIM 终结点后，可以进行测试，确保它 SCIM 符合 RFC。 此示例在 **Postman** 中提供一组测试，以验证对用户和组的 CRUD 操作、筛选、对组成员身份的更新以及禁用用户。

终结点位于 `{host}/scim/` 目录中，可以使用标准 HTTP 请求进行交互。 若要修改 `/scim/` 路由，请参阅 **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **控制器** 中的 ControllerConstant.cs。

> [!NOTE]
> 只能将 HTTP 终结点用于本地测试，因为 Azure AD 预配服务需要终结点支持 HTTPS。

#### <a name="open-postman-and-run-tests"></a>打开 Postman 并运行测试

1. 下载 [Postman](https://www.getpostman.com/downloads/) 并启动应用程序。
1. 复制链接 [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) 并粘贴到 Postman 中以导入测试集合。

    ![postman 集合](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 使用以下变量创建测试环境：

   |环境|变量|值|
   |-|-|-|
   |使用 IIS Express 本地运行项目|||
   ||**服务器**|`localhost`|
   ||端口|`:44359`*(忘记 **：**)*|
   ||**Api**|`scim`|
   |使用 Kestrel 在本地运行项目|||
   ||**服务器**|`localhost`|
   ||端口|`:5001`*(忘记 **：**)*|
   ||**Api**|`scim`|
   |在 Azure 中托管终结点|||
   ||**服务器**|*(输入 SCIM URL)*|
   ||端口|*(留空)*|
   ||**Api**|`scim`|

1. 使用 Postman 集合中的 " **获取密钥** " 将 **get** 请求发送到令牌终结点，并检索要在 **令牌** 变量中存储的用于后续请求的安全令牌。 

   ![postman 获取密钥](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > 若要使 SCIM 终结点安全，在连接之前需要安全令牌，教程使用 `{host}/scim/token` 终结点生成自签名令牌。

就这么简单！ 你现在可以运行 **Postman** 集合来测试 SCIM 终结点功能。

## <a name="next-steps"></a>后续步骤

若要为客户端开发具有互操作性的 SCIM 兼容用户和组终结点，请参阅 [SCIM 客户端实现](http://www.simplecloud.info/#Implementations2)。

> [!div class="nextstepaction"]
> [教程：开发和规划 SCIM 终结点](use-scim-to-provision-users-and-groups.md) 
>  的预配[教程：配置库应用的预配](configure-automatic-user-provisioning-portal.md)