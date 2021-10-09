---
title: 在应用中实现基于角色的访问控制
titleSuffix: Microsoft identity platform
description: 了解如何在应用程序中实现基于角色的访问控制。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/17/2021
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: ccbdbde261914e95470e37b0e2046a95e1fe5898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698944"
---
# <a name="implement-role-based-access-control-in-apps"></a>在应用中实现基于角色的访问控制

基于角色的访问控制 (RBAC) 使用户或组拥有针对他们有权访问的资源、他们可以对这些资源执行的操作以及资源管理人员的特定权限。

通常，当我们讨论如何实现 RBAC 来保护资源时，我们希望保护 Web 应用程序、单页应用程序 (SPA) 或 API。  这可以适用于整个应用程序或 API，也可以适用于特定区域、功能或 API 方法。

本文介绍如何实现特定于应用程序的基于角色的访问控制。  有关授权基础知识的详细信息，请参阅[授权基础知识](./authorization-basics.md)。

## <a name="implementing-rbac-using-the-microsoft-identity-platform"></a>使用 Microsoft 标识平台实现 RBAC

如[应用程序开发人员的基于角色的访问控制](./custom-rbac-for-developers.md)中所述，有三种方法可以使用 Microsoft 标识平台实现 RBAC：

- 应用角色 - 将[应用程序注册中的应用角色功能](./howto-add-app-roles-in-azure-ad-apps.md#declare-roles-for-an-application)与应用程序中的逻辑结合使用来解释传入应用角色分配。
- 组 - 将传入标识的组分配与应用程序中的逻辑结合使用来解释组分配。 
- 自定义数据存储 - 使用应用程序中的逻辑检索和解释角色分配。

首选方法是使用应用角色，因为这种方法最容易实现。 此方法由使用 Microsoft 标识平台生成应用的 SDK 直接支持。 有关如何选择方法的详细信息，请参阅[选择方法](./custom-rbac-for-developers.md#choosing-an-approach)。

本文的其余部分将介绍如何定义应用角色并在应用程序中使用应用角色实现 RBAC。

## <a name="defining-roles-for-your-application"></a>为应用程序定义角色

为应用程序实现 RBAC 的第一步是定义应用程序所需的角色，并将用户或组分配给这些角色。  [如何：在应用程序中添加应用角色并在令牌中接收它们](./howto-add-app-roles-in-azure-ad-apps.md)中概述了此过程。 定义角色并分配用户或组后，即可访问传入应用程序的令牌中的角色分配，并相应地执行操作。

## <a name="implementing-rbac-in-aspnet-core"></a>在 ASP.NET Core 中实现 RBAC 

ASP.NET Core 支持将 RBAC 添加到 ASP.NET Core Web 应用程序或 Web API。  这样，便可以结合使用[角色检查](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)和 ASP.NET Core Authorize 特性来轻松实现 RBAC。 还可以使用 ASP.NET Core 对[基于策略的角色检查](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#policy-based-role-checks)的支持。

### <a name="aspnet-core-mvc-web-application"></a>ASP.NET Core MVC Web 应用程序 

在 ASP.NET Core MVC Web 应用程序中实现 RBAC 非常简单。  它主要涉及使用 Authorize 特性来指定应允许哪些角色访问特定控制器或控制器中的操作。 按照以下步骤在 ASP.NET Core MVC 应用程序中实现 RBAC：
1. 使用应用角色和分配创建应用注册，如上面的 *为应用程序定义角色* 中所述。
1. 执行以下步骤中的一个：
    - 使用 dotnet cli 创建新的 ASP.NET Core MVC Web 应用项目。  指定包含单租户身份验证的 SingleOrg 或多租户身份验证的 MultiOrg 的 --auth 标志，如果来自应用注册，则指定包含客户端的 --client-id 标志，如果来自 Azure AD 租户，则指定包含租户的 --tenant-id 标志：
 
      ```bash 
    
      dotnet new mvc --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID>  
    
      ```
      
    - 将 Microsoft.Identity.Web 和 Microsoft.Identity.Web.UI 库添加到现有 ASP.NET Core MVC 项目：
 
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Microsoft.Identity.Web.UI 

      ```

   然后按照[快速入门：向 ASP.NET Core Web 应用添加 Microsoft 登录功能](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true)中指定的说明向应用程序添加身份验证。
1. 对控制器操作添加角色检查，如[添加角色检查](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)中所述。
1. 通过尝试访问其中一个受保护的 MVC 路由来测试应用程序。

### <a name="aspnet-core-web-api"></a>ASP.NET Core Web API

在 ASP.NET Core Web API 中实现 RBAC 主要涉及使用 Authorize 特性来指定应允许哪些角色访问特定控制器或控制器中的操作。 按照以下步骤在 ASP.NET Core Web API 中实现 RBAC：
1. 使用应用角色和分配创建应用注册，如上面的 *为应用程序定义角色* 中所述。
1. 执行以下步骤中的一个：
    - 使用 dotnet cli 创建新的 ASP.NET Core MVC Web API 项目。  指定包含单租户身份验证的 SingleOrg 或多租户身份验证的 MultiOrg 的 --auth 标志，如果来自应用注册，则指定包含客户端的 --client-id 标志，如果来自 Azure AD 租户，则指定包含租户的 --tenant-id 标志：

      ```bash 
    
      dotnet new webapi --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID> 
    
      ```

    - 将 Microsoft.Identity.Web 和 Swashbuckle.AspNetCore 库添加到现有 ASP.NET Core MVC Web API 项目：
      
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Swashbuckle.AspNetCore 

      ```
    
   然后按照[快速入门：向 ASP.NET Core Web 应用添加 Microsoft 登录功能](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true)中指定的说明向应用程序添加身份验证。
1. 对控制器操作添加角色检查，如[添加角色检查](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)中所述。
1. 从客户端应用调用 API。  有关端到端示例，请参阅[调用 .NET Core Web API 并使用应用角色实现基于角色的访问控制的 Angular 单页应用程序](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles)。


## <a name="implementing-rbac-in-other-platforms"></a>在其他平台中实现 RBAC

### <a name="angular-spa-using-msalguard"></a>使用 MsalGuard 的 Angular SPA
在 Angular SPA 中实现 RBAC 涉及使用 [msal-angular](https://www.npmjs.com/package/@azure/msal-angular) 授予对应用程序中包含的 Angular 路由的访问权限。  [通过 Microsoft 标识平台，使 Angular 单页应用程序能够登录用户和调用 API](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial#chapter-5-control-access-to-your-protected-api-using-app-roles-and-security-groups) 示例中对此进行了说明。

> [!NOTE]
> 客户端 RBAC 实现应与服务器端 RBAC 配对，以防止未经授权的应用程序访问敏感资源。

### <a name="nodejs-with-express-application"></a>使用 Express 应用程序的 Node.js
在使用 Express 应用程序的 Node.js 中实现 RBAC 涉及使用 MSAL 授予对应用程序中包含的 Express 路由的访问权限。  [通过 Microsoft 标识平台，使 Node.js Web 应用能够登录用户和调用 API](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial#chapter-4-control-access-to-your-app-using-app-roles-and-security-groups) 中对此进行了说明。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Microsoft 标识平台中的权限和许可](./v2-permissions-and-consent.md)。
- 详细了解[应用程序开发人员的基于角色的访问控制](./custom-rbac-for-developers.md)。
