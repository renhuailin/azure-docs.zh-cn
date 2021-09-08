---
title: 如何在 Microsoft Azure Maps 中通过非交互式登录保护单页 Web 应用程序
titleSuffix: Azure Maps
description: 如何使用非交互式 Azure 基于角色的访问控制 (Azure RBAC) 和 Azure Maps Web SDK 配置单页 Web 应用程序。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js, subject-rbac-steps
ms.openlocfilehash: 9bf18a9122bbe8406b76cfd822cc2a5a86339a52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122868241"
---
# <a name="how-to-secure-a-single-page-web-application-with-non-interactive-sign-in"></a>如何通过非交互式登录保护单页 Web 应用程序

本文介绍如何在用户无法登录到 Azure AD 时，使用 Azure Active Directory (Azure AD) 保护单页 Web 应用程序。

为了创建此非交互式身份验证流，我们将创建一个负责从 Azure AD 获取访问令牌的 Azure 函数安全 Web 服务。 此 Web 服务将仅适用于单页 Web 应用程序。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps 可以支持来自用户登录或交互式流的访问令牌。 可以使用交互式流对访问吊销和机密管理的范围进行更严格的限制。

## <a name="create-an-azure-function"></a>创建 Azure 函数

若要创建一个受保护的 Web 服务应用程序（负责向 Azure AD 进行身份验证），请执行以下操作：

1. 在 Azure 门户中创建函数。 有关详细信息，请参阅 [Azure Functions 入门](../azure-functions/functions-get-started.md)。

2. 在 Azure 函数上配置 CORS 策略，供单页 Web 应用程序访问。 CORS 策略可确保浏览器客户端仅访问允许的 Web 应用程序源。 有关详细信息，请参阅[添加 CORS 功能](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)。

3. 在 Azure 函数上[添加系统分配的标识](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)，以便创建用于对 Azure AD 进行身份验证的服务主体。  

4. 向系统分配的标识授予对 Azure Maps 帐户的基于角色的访问权限。 有关详细信息，请参阅[授予基于角色的访问权限](#grant-role-based-access-for-users-to-azure-maps)。

5. 为 Azure 函数编写代码，以使用系统分配的标识通过受支持的机制之一或 REST 协议获取 Azure Maps 访问令牌。 有关详细信息，请参阅[获取 Azure 资源的令牌](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    下面是一个示例 REST 协议：

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    下面是一个示例响应：

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

6. 为 Azure 函数 HttpTrigger 配置安全性：

   1. [创建函数访问密钥](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   1. 在生产环境中[保护 Azure 函数的 HTTP 终结点](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)。

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

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>后续步骤

进一步了解单页应用程序方案：
> [!div class="nextstepaction"]
> [单页应用程序](../active-directory/develop/scenario-spa-overview.md)

查找 Azure Maps 帐户的 API 使用指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)

了解演示如何将 Azure AD 与 Azure Maps 集成的其他示例：
> [!div class="nextstepaction"]
> [Azure Maps 示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
