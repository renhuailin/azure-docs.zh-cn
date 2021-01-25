---
title: 获取调用 Web API 的 Web API 的令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 Web API 的 Web API（要求获取应用的令牌）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756398"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>调用 Web API 的 Web API：获取应用的令牌

构建客户端应用程序对象后，使用它来获取可用于调用 Web API 的令牌。

## <a name="code-in-the-controller"></a>控制器中的代码

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web 添加了扩展方法，这些方法为调用 Microsoft Graph 或下游 Web API 提供便利服务。 若要详细了解这些方法，请参阅[调用 Web API 的 Web API：调用 API](scenario-web-api-call-api-call-api.md)。 通过这些帮助程序方法，你无需手动获取令牌。

但是，如果你确实想要手动获取令牌，以下代码演示了如何使用 Microsoft.Identity.Web 在 API 控制器中执行此操作。 它调用下游 API（名为 *todolist*）。
若要获取令牌以调用下游 API，可以通过控制器构造函数中（如果使用 Blazor，则在页面构造函数中）的依赖项注入，来注入 `ITokenAcquisition` 服务，并在控制器操作中使用该服务，从而为用户 (`GetAccessTokenForUserAsync`)，或如果是在守护程序方案下则为应用程序本身 (`GetAccessTokenForAppAsync`) 获取令牌。

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

有关 `callTodoListService` 方法的详细信息，请参阅[调用 Web API 的 Web API：调用 API](scenario-web-api-call-api-call-api.md)。

### <a name="java"></a>[Java](#tab/java)

下面是在 API 控制器的操作中调用的代码示例。 它调用下游 API - Microsoft Graph。

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
Python Web API 需要借助中间件来验证从客户端接收的持有者令牌。 然后，Web API 可通过调用 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 方法，使用 MSAL Python 库获取下游 API 的访问令牌。
 
下面是使用 `acquire_token_on_behalf_of` 方法和 Flask 框架获取访问令牌的代码示例。 它调用下游 API - Azure 管理订阅终结点。
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>（高级）从后台应用、API 和服务访问已登录用户的令牌缓存

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[调用 API](scenario-web-api-call-api-call-api.md)。
