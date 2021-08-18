---
title: 验证受范围和应用角色保护的 Web API | Azure
titleSuffix: Microsoft identity platform
description: 验证 API 是否仅由代表具有适当范围的用户的应用程序以及具有适当应用程序角色的守护程序应用调用。
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
ms.openlocfilehash: 2243f149ebe89bcb3d52d5940ba930891925d788
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724848"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>受保护的 Web API：验证范围和应用角色

本文介绍如何将授权添加到 Web API。 这种保护可确保只有以下对象才能调用 API：

- 代表具有适当范围的用户的应用程序。
- 具有适当应用程序角色的守护程序应用。

> [!NOTE]
> 本文中的代码片段摘自 GitHub 上的以下代码示例：
>
> - [ASP.NET Core Web API 增量教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET Web API 示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

若要保护 ASP.NET 或 ASP.NET Core Web API，必须在下列其中一项中添加 `[Authorize]` 属性：

- 控制器本身（若要保护所有控制器操作）
- API 的单个控制器操作

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     // ...
    }
```

但是，这种保护并不足够。 它只能保证 ASP.NET 和 ASP.NET Core 对该令牌进行验证。 你的 API 需要验证用来调用 API 的令牌是否是使用预期的声明请求的。 具体而言，这些声明需要验证：

- 作用域（如果代表用户调用 API）。 
- 应用角色（如果可从守护程序应用调用 API）。 

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>在代表用户调用的 API 中验证作用域

如果某个客户端应用代表用户调用了你的 API，则该 API 需要请求具有该 API 的特定作用域的持有者令牌。 有关详细信息，请参阅[代码配置 | 持有者令牌](scenario-protected-web-api-app-configuration.md#bearer-token)。

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，可以使用 Microsoft.Identity.Web 来验证每个控制器操作的范围。 还可以在控制器级别或针对整个应用程序对其进行验证。

#### <a name="verify-the-scopes-on-each-controller-action"></a>验证每个控制器操作的范围

可以使用 `[RequiredScope]` 属性验证控制器操作的范围。 此属性具有多个覆盖。 一个直接采用所需范围，另一个采用配置的密钥。

##### <a name="verify-the-scopes-on-a-controller-action-with-hardcoded-scopes"></a>使用硬编码范围验证控制器操作的范围

以下代码片段显示了如何使用 `[RequiredScope]` 属性与硬编码范围。

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    [RequiredScope(scopeRequiredByApi)]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-action-with-scopes-defined-in-configuration"></a>使用配置中定义的范围验证控制器操作的范围

还可以在配置中声明这些必需的范围，并引用配置密钥：

例如，如果 appsettings.json 中有以下配置：

```JSon
{
 "AzureAd" : {
   // more settings
   "Scopes" : "access_as_user access_as_admin"
  }
}
```

然后，在 `[RequiredScope]` 属性中对其进行引用：

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    [RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-scopes-conditionally"></a>有条件地验证范围

在某些情况下，需要有条件地验证范围。 可以使用 `HttpContext` 上的 `VerifyUserHasAnyAcceptedScope` 扩展方法执行此操作。

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-at-the-level-of-the-controller"></a>验证控制器级别的范围

还可以验证整个控制器的范围

##### <a name="verify-the-scopes-on-a-controller-with-hardcoded-scopes"></a>使用硬编码范围验证控制器的范围

以下代码片段显示了如何在控制器上使用 `[RequiredScope]` 属性与硬编码范围。

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(scopeRequiredByApi)]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-with-scopes-defined-in-configuration"></a>使用配置中定义的范围验证控制器的范围

与针对操作类似，还可以在配置中声明这些必需的范围，并引用配置密钥：

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-more-globally"></a>更全面地验证范围

建议的方法是为 Web API 定义粒度范围并验证每个控制器操作中的范围。 但是，也可以在应用程序或控制器级别验证范围。 有关详细信息，请参阅 ASP.NET 核心文档中的[基于声明的授权](/aspnet/core/security/authorization/claims) 。

#### <a name="what-is-verified"></a>验证的内容

`[RequiredScope]` 属性和 `VerifyUserHasAnyAcceptedScope` 方法将执行诸如以下步骤的某些操作：

- 验证是否存在名为 `http://schemas.microsoft.com/identity/claims/scope` 或 `scp` 的声明。
- 验证该声明的值是否包含 API 预期的作用域。

### <a name="aspnet-classic"></a>[ASP.NET 经典](#tab/aspnet)

在 ASP.NET 应用程序中，可以通过以下方式验证范围：

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateScopes(new[] {"read"; "admin" } );
       // ...
    }
```

以下是简化版的 `ValidateScopes`：

```csharp
private void ValidateScopes(IEnumerable<string> acceptedScopes)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim scopeClaim = ClaimsPrincipal.Current.FindFirst("scp");
    if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Forbidden,
            ReasonPhrase = $"The 'scp' claim does not contain '{scopeClaim}' or was not found"
        });
    }
}
```

对于 ASP.NET Core 的完整版 `ValidateScopes`，请参阅 [*ScopesRequiredHttpContextExtensions.cs*](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/ScopesRequiredHttpContextExtensions.cs)

---

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>验证守护程序应用调用的 API 中的应用角色

如果 Web API 由某个[守护程序应用](scenario-daemon-overview.md)调用，该应用应该对该 Web API 拥有应用程序权限。 如[公开应用程序权限（应用角色）](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)中所示，你的 API 将公开此类权限。 一个示例是 `access_as_application` 应用角色。

现在，你需要让 API 验证它收到的令牌是否包含 `roles` 声明，以及此声明是否具有预期的值。 验证代码类似于对委托权限进行验证的代码，不同之处在于，你的控制器操作针对角色进行测试，而非针对作用域进行测试：

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

以下代码片段显示了如何验证应用程序角色

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        // ...
    }
```

相反，可以在控制器或操作（或 razor 页面）上使用 [Authorize("role")] 属性。

```CSharp
[Authorize("role")]
MyController : ApiController
{
    // ...
}
```

但对于这种情况，需将角色声明映射到 Startup.cs 文件中的“roles”：


```CSharp
 services.Configure<OpenIdConnectOptions>(OpenIdConnectDefaults.AuthenticationScheme, options =>
 {
    // The claim in the Jwt token where App roles are available.
    options.TokenValidationParameters.RoleClaimType = "roles";
 });
```

如果还需要基于组进行授权，则这不是最佳解决方案。

有关详细信息，请参阅有关[按角色和组进行授权](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ)的 Web 应用增量教程。

### <a name="aspnet-classic"></a>[ASP.NET 经典](#tab/aspnet)

在 ASP.NET 应用程序中，可以通过以下方式验证应用角色：

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateAppRole("access_as_application");
       // ...
    }
```

以下是简化版的 `ValidateAppRole`：

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
```

对于 ASP.NET Core 的完整版 `ValidateAppRole`，请参阅 [*RolesRequiredHttpContextExtensions.cs*](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs) 代码。

---

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>当 Web API 只能由守护程序应用调用时接受仅限应用的令牌

用户还可以在用户分配模式下使用角色声明，如[如操作指南：在应用程序中添加应用角色并在令牌中接收它们](howto-add-app-roles-in-azure-ad-apps.md)中所示。 如果角色可同时分配给用户和应用，只需选中相应的角色就能让应用以用户身份登录，以及让用户以应用身份登录。 建议为用户和应用声明不同的角色，以避免出现这种混淆。

如果你希望只有守护程序应用能够调用 Web API，请在验证应用角色时添加一个条件，规定该令牌是仅限应用的令牌。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnly = oid != null && sub != null && oid == sub;
```

选中反向条件将只允许用于将用户登录的应用调用你的 API。

### <a name="using-acl-based-authorization"></a>使用基于 ACL 的授权

除了基于应用角色的授权，还可以使用基于访问控制列表 (ACL) 的授权模式来保护 Web API，[在没有 `roles` 声明的情况下控制令牌](v2-oauth2-client-creds-grant-flow.md#controlling-tokens-without-the-roles-claim)。

如果在 ASP.NET 核心上使用 Microsoft.Identity.Web，则需声明使用的是基于 ACL 的授权，否则 Microsoft Identity Web 将在提供的声明中既没有角色也没有范围时引发异常：

```Text
System.UnauthorizedAccessException: IDW10201: Neither scope or roles claim was found in the bearer token.
```

 若要避免此异常，请在 appsettings.json 中或以编程方式将 `AllowWebApiToBeAuthorizedByACL` 配置属性设置为 true。

```Json
{
 "AzureAD"
 {
  // other properties
  "AllowWebApiToBeAuthorizedByACL" : true,
  // other properties
 }
}
```

如果将 `AllowWebApiToBeAuthorizedByACL` 设置为 true，则需负责确保 ACL 机制。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[移到生产环境](scenario-protected-web-api-production.md)。
