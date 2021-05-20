---
title: 如何通过非交互式登录保护单页应用程序
titleSuffix: Azure Maps
description: 如何使用非交互式 Azure 基于角色的访问控制 (Azure RBAC) 和 Azure Maps Web SDK 配置单页应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 9d2af0bf731ab069a8512cb10feccf5ba18d3fa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092724"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>如何通过非交互式登录保护单页应用程序

以下指南适用于使用 Azure Active Directory (Azure AD) 在用户无法登录到 Azure AD 时向 Azure Maps 应用程序提供访问令牌的应用程序。 此流需要托管必须只允许单页 Web 应用程序访问的 Web 服务。 有多个实现可以完成对 Azure AD 的身份验证。 本指南利用 Azure 函数产品获取访问令牌。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps 可以支持来自用户登录/交互式流的访问令牌。 交互式流使访问吊销和机密管理的范围受到更严格的限制。

## <a name="create-azure-function"></a>创建 Azure 函数

创建一个受保护的 Web 服务应用程序，用于负责对 Azure AD 进行身份验证。 

1. 在 Azure 门户中创建函数。 有关详细信息，请参阅[创建 Azure 函数](../azure-functions/functions-get-started.md)。

2. 在 Azure 函数上配置 CORS 策略，供单页 Web 应用程序访问。 这可确保浏览器客户端仅访问允许的 Web 应用程序来源。 请参阅[添加 CORS 功能](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)。

3. 在 Azure 函数上[添加系统分配的标识](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)，以便创建用于对 Azure AD 进行身份验证的服务主体。  

4. 向系统分配的标识授予对 Azure Maps 帐户的基于角色的访问权限。 有关详细信息，请参阅[授予基于角色的访问权限](#grant-role-based-access)。

5. 为 Azure 函数编写代码，以使用系统分配的标识通过受支持的机制之一或 REST 协议获取 Azure Maps 访问令牌。 请参阅[获取 Azure 资源的令牌](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    示例 REST 协议：

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    示例响应：

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. 为 Azure 函数 HttpTrigger 配置安全性

   * [创建函数访问密钥](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * 在生产环境中[保护 Azure 函数的 HTTP 终结点](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)。
   
7. 配置 Web 应用程序 Azure Maps Web SDK。 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>授予基于角色的访问权限

可以通过将系统分配的标识分配到一个或多个 Azure 角色定义，授予 *Azure 基于角色的访问控制 (Azure RBAC)* 访问权限。 若要查看可用于 Azure Maps 的 Azure 角色定义，请转到“访问控制(IAM)”。 选择“角色”，然后搜索以“Azure Maps”开头的角色。

1. 转到你的 Azure Maps 帐户。 选择“访问控制(IAM)” > “角色分配” 。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure RBAC 授予访问权限](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在“角色分配”选项卡上的“角色”下，选择一个内置 Azure Maps 角色定义，例如“Azure Maps 数据读取器”或“Azure Maps 数据参与者”   。 在“将访问权限分配给”下，选择“函数应用” 。 按名称选择主体。 再选择“保存”。

   * 有关详细信息，请参阅[分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

> [!WARNING]
> Azure Maps 内置角色定义提供了一种范围很广的授权访问权限，可以访问许多 Azure Maps REST API。 若要将 API 访问权限限制为最小范围，请参阅[创建自定义角色定义并将系统分配的标识分配给自定义角色定义](../role-based-access-control/custom-roles.md)。 这样即可将访问权限限制为应用程序访问 Azure Maps 所需的最少特权。

## <a name="next-steps"></a>后续步骤

进一步了解单页应用程序场景：
> [!div class="nextstepaction"]
> [单页应用程序](../active-directory/develop/scenario-spa-overview.md)

查找 Azure Maps 帐户的 API 使用指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

了解演示如何将 Azure AD 与 Azure Maps 集成的其他示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)