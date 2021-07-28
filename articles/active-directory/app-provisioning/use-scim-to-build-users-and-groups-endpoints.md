---
title: 构建 SCIM 终结点以便从 Azure Active Directory 将用户预配到应用
description: 了解如何开发 SCIM 终结点、将 SCIM API 与 Azure Active Directory 集成，以及自动将用户和组预配到云应用程序中。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ac876ff4370c20ca0b4db59575a3d8ab49b7b0a3
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109784838"
---
# <a name="tutorial-develop-a-sample-scim-endpoint-in-azure-active-directory"></a>教程：在 Azure Active Directory 中开发示例 SCIM 终结点

没有人希望从头开始构建新终结点，因此我们创建了一些[参考代码](https://aka.ms/scimreferencecode)，以供你开始使用[跨域身份管理系统 (SCIM)](https://aka.ms/scimoverview)。 无需代码，只需五分钟便可设置并运行 SCIM 终结点。

本教程介绍如何通过使用 Postman 或通过与 Azure Active Directory (Azure AD) SCIM 客户端集成，在 Azure 中部署 SCIM 参考代码并进行测试。 本教程适用于要开始使用 SCIM 的开发人员，或对测试 SCIM 终结点感兴趣的任何人。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 在 Azure 中部署 SCIM 终结点。
> * 测试 SCIM 终结点。

## <a name="deploy-your-scim-endpoint-in-azure"></a>在 Azure 中部署 SCIM 终结点

此处的步骤使用 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 和 [Azure 应用服务](../../app-service/index.yml)将 SCIM 终结点部署到服务。 SCIM 参考代码还可以在本地运行、由本地服务器托管或是部署到另一个外部服务。

1. 从 GitHub 转到[参考代码](https://github.com/AzureAD/SCIMReferenceCode)，然后选择“克隆或下载”。

1. 选择“在桌面中打开”，或复制链接，打开 Visual Studio，然后选择“克隆或签出代码”以输入复制的链接并创建本地副本 。

1. 在 Visual Studio 中，确保登录有权访问托管资源的帐户。

1. 在解决方案资源管理器中，打开 Microsoft.SCIM.sln，然后右键单击 Microsoft.SCIM.WebHostSample 文件 。 选择“发布”。

    ![显示示例文件的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 若要在本地运行此解决方案，请双击项目并选择“IIS Express”以将项目作为带有本地主机 URL 的网页进行启动。

1. 选择“创建配置文件”，并确保选择“应用服务”和“新建”  。

    ![显示“发布”窗口的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 逐个完成对话框选项，并将应用重命名为你选择的名称。 此名称会在应用和 SCIM 终结点 URL 中使用。

    ![显示如何创建新应用服务的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 选择要使用的资源组，然后选择“发布”。

    ![显示如何发布新应用服务的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. 在“Azure 应用服务” > “配置”中转到应用程序，然后选择“新建应用程序设置”以添加值为 `https://sts.windows.net/<tenant_id>/` 的 Token__TokenIssuer 设置。 将 `<tenant_id>` 替换为 Azure AD 租户 ID。 如果要使用 [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) 测试 SCIM 终结点，请添加值为 `Development` 的 ASPNETCORE_ENVIRONMENT 设置。

   ![显示“应用程序设置”窗口的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   在 [Azure 门户](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)中使用企业应用程序测试终结点时，有两种选项。 可以将环境保留在 `Development` 中并提供来自 `/scim/token` 终结点的测试令牌，也可以将环境更改为 `Production` 并将令牌字段留空。

就这么简单！ 你的 SCIM 终结点现在已发布，可以使用 Azure 应用服务 URL 测试 SCIM 终结点。

## <a name="test-your-scim-endpoint"></a>测试 SCIM 终结点

对 SCIM 终结点进行的请求需要授权。 SCIM 标准具有多个用于身份验证和授权的选项，包括 cookie、基本身份验证、TLS 客户端身份验证或 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2) 中列出的任何方法。

请确保避免使用不安全的方法（例如用户名和密码），而改用更安全的方法（例如 OAuth）。 Azure AD 支持长期持有者令牌（适用于库和非库应用程序）和 OAuth 授权（适用于库应用程序）。

> [!NOTE]
> 存储库中提供的授权方法仅用于测试。 与 Azure AD 集成时，可以查看授权指导。 请参阅[计划 SCIM 终结点的预配](use-scim-to-provision-users-and-groups.md)。

开发环境实现了对生产不安全的功能，如控制安全令牌验证行为的参考代码。 令牌验证代码使用自签名安全令牌，签名密钥存储在配置文件中。 请查看 appsettings.Development.json 文件中的 Token:IssuerSigningKey 参数。

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> 向 `/scim/token` 终结点发送 GET 请求时，会使用配置的密钥颁发令牌。 该令牌可以用作持有者令牌以便进行后续授权。

默认令牌验证代码配置为使用 Azure AD 令牌，需要使用 appsettings.json 文件中的 Token:TokenIssuer 参数配置颁发租户。

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>使用 Postman 测试应用

部署 SCIM 终结点之后，可以进行测试，以确保它符合 SCIM RFC。 此示例提供一组采用 Postman 的测试，这些测试会验证对用户和组进行的 CRUD（创建、读取、更新和删除）操作、筛选、对组成员身份的更新以及禁用用户。

终结点在 `{host}/scim/` 目录中，你可以使用标准 HTTP 请求与它们进行交互。 若要修改 `/scim/` 路由，请在“AzureADProvisioningSCIMreference” > “ScimReferenceApi” > “控制器”中查看 ControllerConstant.cs。

> [!NOTE]
> 只能使用 HTTP 终结点进行本地测试。 Azure AD 预配服务要求终结点支持 HTTPS。

1. 下载 [Postman](https://www.getpostman.com/downloads/) 并启动应用程序。
1. 将此链接复制并粘贴到 Postman 中以导入测试集合：`https://aka.ms/ProvisioningPostman`。

    ![显示如何在 Postman 中导入测试集合的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 创建具有以下变量的测试环境：

   |环境|变量|值|
   |-|-|-|
   |使用 IIS Express 在本地运行项目|||
   ||**服务器**|`localhost`|
   ||端口|`:44359`（请勿忘记 `:`）|
   ||**Api**|`scim`|
   |使用 Kestrel 在本地运行项目|||
   ||**服务器**|`localhost`|
   ||端口|`:5001`（请勿忘记 `:`）|
   ||**Api**|`scim`|
   |在 Azure 中托管终结点|||
   ||**服务器**|（输入 SCIM URL）|
   ||端口|*（留空）*|
   ||**Api**|`scim`|

1. 使用来自 Postman 集合的“获取密钥”将 GET 请求发送到令牌终结点，并检索要在令牌变量中存储以用于后续请求的安全令牌  。

   ![显示 Postman“获取密钥”文件夹的屏幕截图。](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > 若要使 SCIM 终结点安全，需要在连接前使用安全令牌。 本教程使用 `{host}/scim/token` 终结点生成自签名令牌。

就这么简单！ 现在可以运行 Postman 集合以测试 SCIM 终结点功能。

## <a name="next-steps"></a>后续步骤

若要为客户端开发具有互操作性的符合 SCIM 标准的用户和组终结点，请参阅 [SCIM 客户端实现](http://www.simplecloud.info/#Implementations2)。

> [!div class="nextstepaction"]
> [教程：为 SCIM 终结点开发和计划预配](use-scim-to-provision-users-and-groups.md)
> [教程：为库应用配置预配](configure-automatic-user-provisioning-portal.md)