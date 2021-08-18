---
title: AuthN/AuthZ 中的用户标识
description: 了解如何在使用应用服务中的内置身份验证和授权时访问用户标识。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: ae483e4213ec17cfa3a4532fbf615a40c760ad6e
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230425"
---
# <a name="work-with-user-identities-in-azure-app-service-authentication"></a>在 Azure 应用服务身份验证中使用用户标识

本文演示如何在使用[应用服务中的内置身份验证和授权](overview-authentication-authorization.md)时使用用户标识。 

## <a name="access-user-claims-in-app-code"></a>在应用代码中访问用户声明

对于所有语言框架，应用服务都通过将传入令牌（无论此令牌是来自经过身份验证的最终用户，还是来自客户端应用程序）中的声明注入请求头，使其可供代码使用。 不允许外部请求设置这些标头，因此，只会提供应用服务设置的标头。 部分标头示例如下：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

使用任何语言或框架编写的代码均可从这些标头获取所需信息。 

> [!NOTE]
> 不同的语言框架可能会以不同格式（例如小写或标题字首大写）向应用代码显示这些标头。

对于 ASP.NET 4.6 应用，应用服务会在 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) 中填充经过身份验证的用户声明，使你能够遵循标准的 .NET 代码模式（包括 `[Authorize]` 属性）。 同样，对于 PHP 应用，应用服务会填充 `_SERVER['REMOTE_USER']` 变量。 对于 Java 应用，[可从 Tomcat servlet 访问](configure-language-java.md#authenticate-users-easy-auth)声明。

对于 [Azure Functions](../azure-functions/functions-overview.md)，没有为 .NET 代码填充 `ClaimsPrincipal.Current`，但你仍然可以在请求标头中找到用户声明，也可通过请求上下文甚至通过绑定参数来获取 `ClaimsPrincipal` 对象。 有关详细信息，请参阅[在 Azure Functions 中使用客户端标识](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)。

对于 .NET Core，[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 支持使用应用服务身份验证填充当前用户。 若要了解详细信息，可以在 [Microsoft.Identity.Web Wiki](https://github.com/AzureAD/microsoft-identity-web/wiki/1.2.0#integration-with-azure-app-services-authentication-of-web-apps-running-with-microsoftidentityweb) 上阅读相关内容，或查看[本教程中有关访问 Microsoft Graph 的 Web 应用的演示](./scenario-secure-app-access-microsoft-graph-as-user.md?tabs=command-line#install-client-library-packages)。

## <a name="access-user-claims-using-the-api"></a>使用 API 访问用户声明

如果已为应用启用[令牌存储](overview-authentication-authorization.md#token-store)，你还可以通过调用 `/.auth/me` 获得有关经过身份验证的用户的其他详细信息。 移动应用服务器 SDK 提供处理该数据的帮助器方法。 有关详细信息，请参阅[如何使用 Azure 移动应用 Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) 和[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：对用户进行端到端身份验证和授权](tutorial-auth-aad.md)