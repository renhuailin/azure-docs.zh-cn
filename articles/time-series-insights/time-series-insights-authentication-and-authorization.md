---
title: API 身份验证和授权 - Azure 时序见解 | Microsoft Docs
description: 本文介绍如何为调用 Azure 时序见解 API 的自定义应用程序配置身份验证和授权。
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 02d9edd555566f86fd8bb09cf4acef4956ae53e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041206"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure 时序见解 API 的身份验证和授权

根据你的业务需要，你的解决方案可能包括一个或多个用于与 Azure 时序见解环境的 [api](/rest/api/time-series-insights/reference-data-access-overview)进行交互的客户端应用程序。 Azure 时序见解使用 [基于 OAUTH 2.0 Azure AD 安全令牌](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)执行身份验证。 若要对客户端 () 进行身份验证，你需要获取具有适当权限的持有者令牌，并将其传递给你的 API 调用。 本文档介绍了几种凭据获取方法，你可以使用这些方法来获取持有者令牌并进行身份验证。


  如何使用新的 Azure Active Directory 边栏选项卡在 Azure Active Directory 中注册应用。 在 Azure Active Directory 中注册的应用可让用户进行身份验证，并有权使用与 Azure 时序见解环境关联的 Azure 时序见解 API。

## <a name="managed-identities"></a>托管标识

以下部分介绍如何使用 Azure Active Directory (Azure AD) 中的托管标识访问 Azure 时序见解 API。 在 Azure 上，托管标识可为 Azure AD 中的 Azure 资源提供标识并使用它来获取 Azure Active Directory (Azure AD) 令牌，从而使开发人员无需管理凭据。 下面是使用托管标识的一些好处：

- 你无需管理凭据， 而且你甚至可能都无法访问凭据。
- 可以使用托管标识向支持 Azure AD 身份验证的任何 Azure 服务（包括 Azure Key Vault）进行身份验证。
- 无需额外付费也可使用托管标识。

有关这两种类型的托管标识的详细信息，请参阅 [Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md)

你可以使用中的托管标识：

- Azure VM
- Azure 应用服务
- Azure Functions
- Azure 容器实例
- 更多 .。。

有关完整列表，请参阅 [支持 azure 资源的托管标识的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) 。

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory 应用注册

建议尽可能使用托管标识，这样就不需要管理凭据。 如果客户端应用程序未托管在支持托管标识的 Azure 服务上，则可以将应用程序注册到 Azure AD 租户。 将应用程序注册到 Azure AD 时，需要创建应用程序的标识配置，使其能够与 Azure AD 集成。 在 [Azure 门户](https://portal.azure.com/)中注册应用时，可以选择单租户（只能在自己的租户中访问）或多租户（可在其他租户中访问），也可以选择设置重定向 URI（将访问令牌发送到的位置）。

完成应用注册后，你将拥有应用（应用程序对象）的全局唯一实例，该实例存在于你的主租户或目录中。 而且你的应用拥有全局唯一 ID（应用或客户端 ID）。 然后，在门户中，你便可以添加机密或证书和作用域以使应用正常工作，在登录对话框中自定义应用的品牌等等。

如果在门户中注册应用程序，会在主租户中自动创建应用程序对象以及服务主体对象。 如果使用 Microsoft Graph API 注册/创建应用程序，则通过一个单独步骤创建服务主体对象。 请求令牌需要服务主体对象。

确保查看应用程序的 [安全](../active-directory/develop/identity-platform-integration-checklist.md#security) 清单。 最佳做法是，应该使用 [证书凭据](../active-directory/develop/active-directory-certificate-credentials.md)，而不是密码凭据 (客户端机密) 。

有关更多详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md) 。

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>步骤1：创建托管标识或应用注册

确定使用托管标识还是应用注册后，下一步是预配一个。

### <a name="managed-identity"></a>托管标识

用于创建托管标识的步骤会因代码所在的位置以及是否要创建系统分配的或用户分配的标识而有所不同。 读取 [管理的标识类型](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 以了解不同之处。 选择标识类型后，请在 "Azure AD 管理的标识" [文档](../active-directory/managed-identities-azure-resources/index.yml)中找到并遵循正确的教程。 在这里，你将找到有关如何为配置托管标识的说明：

- [Azure VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [应用服务和 Azure Functions](../app-service/overview-managed-identity.md)
- [Azure 容器实例](../container-instances/container-instances-managed-identity.md)
- 更多 .。。

### <a name="application-registration"></a>应用程序注册

按照 [注册应用程序](../active-directory/develop/quickstart-register-app.md#register-an-application)中列出的步骤进行操作。

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>步骤2：授予访问权限

当 Azure 时序见解环境收到请求时，首先会验证调用方的持有者令牌。 如果验证通过，则调用方已经过身份验证，然后进行了另一次检查，以确保调用方有权执行请求的操作。 若要对任何用户或服务主体授权，必须首先向其授予对该环境的访问权限，方法是将其分配到 "读取者" 或 "参与者" 角色。

- 若要通过 [Azure 门户](https://portal.azure.com/) UI 授予访问权限，请按照 [授予对环境的数据访问权限一](concepts-access-policies.md) 文中列出的说明进行操作。 选择用户时，可以按名称或 ID 搜索托管标识或应用注册。

- 若要使用 Azure CLI 授予访问权限，请运行以下命令。 查看 [此处](/cli/azure/ext/timeseriesinsights/tsi/access-policy) 的文档，以获取可用于管理访问权限的完整命令列表。

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> 用于 Azure CLI 的 timeseriesinsights-environment-with-eventhub 扩展需要版本2.11.0 或更高版本。 首次运行 az tsi 访问策略命令时，扩展将自动安装。 [了解](/cli/azure/azure-cli-extensions-overview) 有关扩展的详细信息。

## <a name="step-3-requesting-tokens"></a>步骤3：请求令牌

预配托管标识或应用注册并为其分配角色后，便可以开始使用它来请求 OAuth 2.0 持有者令牌。 用于获取令牌的方法将有所不同，具体取决于代码的托管位置以及所选的语言。 指定资源 (也称为令牌) 的 "受众" 时，可以通过其 URL 或 GUID 来识别 Azure 时序见解：

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> 如果使用 URL 作为资源 ID，则必须将令牌准确颁发给 `https://api.timeseries.azure.com/` 。 尾部反斜杠是必需项。

> * 如果使用 [Postman](https://www.getpostman.com/) ，则 **AuthURL** 将为： `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` 有效，但 `https://api.timeseries.azure.com` 无效。

### <a name="managed-identities"></a>托管标识

从 Azure App Service 或函数进行访问时，请按照 [获取 Azure 资源的令牌](../app-service/overview-managed-identity.md)中的指南进行操作。

> [!TIP]
> 对于 .NET 应用程序和函数，使用托管标识的最简单方法是通过适用于 .NET 的 [Azure 标识客户端库](/dotnet/api/overview/azure/identity-readme) 。 

对于 .NET 应用程序和函数，使用托管标识最简单的方法是通过 Microsoft.Azure.Services.AppAuthentication 包。 由于此包简单易用，因此很普遍。 开发人员可以编写代码一次，并让客户端库确定如何基于应用程序环境进行身份验证，无论是在开发人员工作站上使用开发人员的帐户还是在 Azure 中使用托管服务标识部署的。 有关前置 Microsoft.azure.services.appauthentication 库中的迁移指南，请参阅 [microsoft.azure.services.appauthentication 到 Azure。标识迁移指南](/dotnet/api/overview/azure/app-auth-migration)。

使用 c # 和适用于 .NET 的 Azure 标识客户端库请求 Azure 时序见解的令牌：

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>应用注册

* 开发人员可以使用 [Microsoft 身份验证库](../active-directory/develop/msal-overview.md) (MSAL) 获取应用注册令牌。

MSAL 可用于许多应用程序方案中，包括但不限于：

* [单页应用程序 (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Web 应用程序登录用户并代表用户调用 web API](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Web API 代表已登录用户调用其他下游 Web API](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [桌面应用程序代表已登录用户调用 Web API](../active-directory/develop/scenario-desktop-overview.md)
* [代表以交互方式登录的用户调用 Web API 的移动应用程序](../active-directory/develop/scenario-mobile-overview.md)。
* [桌面/服务守护程序应用程序代表自己调用 Web API](../active-directory/develop/scenario-daemon-overview.md)

有关演示如何获取令牌作为应用注册并从 Gen2 环境查询数据的示例 c # 代码，请查看[GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)上的示例应用

> [!IMPORTANT]
> 如果使用的是 [Active Directory 身份验证库 (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md)，请阅读[迁移到 MSAL](../active-directory/develop/msal-net-migration.md)。

## <a name="common-headers-and-parameters"></a>常用标头和参数

本部分介绍用于对 Azure 时序见解 Gen1 和 Gen2 API 进行查询的常用 HTTP 请求标头和参数。 [Azure 时序见解 REST API 参考文档](/rest/api/time-series-insights/)中更详细地介绍了特定于 API 的要求。

> [!TIP]
> 阅读 [Azure REST API 参考](/rest/api/azure/)，了解有关如何使用 REST API、发出 HTTP 请求和处理 HTTP 响应的详细信息。

### <a name="http-headers"></a>HTTP 标头

必需的请求标头如下所述。

| 必需的请求标头 | 说明 |
| --- | --- |
| 授权 | 若要使用 Azure 时序见解进行身份验证，必须在 [授权标头](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)中传递有效的 OAuth 2.0 持有者令牌。 |

> [!TIP]
> 阅读托管的 Azure 时序见解[客户端 SDK 示例可视化](https://tsiclientsample.azurewebsites.net/)，了解如何使用 [JavaScript 客户端 SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 以及图表和图以编程方式通过 Azure 时序见解 API 进行身份验证。

可选请求标头如下所述。

| 可选请求标头 | 说明 |
| --- | --- |
| Content-type | 仅支持 `application/json`。 |
| x-ms-client-request-id | 客户端请求 ID。 服务记录此值。 允许服务跨服务跟踪操作。 |
| x-ms-client-session-id | 客户端会话 ID。 服务记录此值。 允许服务跨服务跟踪一组相关操作。 |
| x-ms-client-application-name | 生成此请求的应用程序的名称。 服务记录此值。 |

可选但建议的响应标头如下所述。

| 响应标头 | 说明 |
| --- | --- |
| Content-type | 仅支持 `application/json`。 |
| x-ms-request-id | 服务器生成的请求 ID。 可用于与 Microsoft 联系以调查请求。 |
| x-ms-property-not-found-behavior | GA API 可选响应标头。 可能的值为 `ThrowError`（默认）或 `UseNull`。 |

### <a name="http-parameters"></a>HTTP 参数

> [!TIP]
> 如需查找有关必需和可选查询信息的详细信息，请参阅[参考文档](/rest/api/time-series-insights/)。

必需的 URL 查询字符串参数依赖于 API 版本。

| 发布 | API 版本值 |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

可选 URL 查询字符串参数包括为 HTTP 请求执行时间设置超时。

| 可选查询参数 | 说明 | 版本 |
| --- |  --- | --- |
| `timeout=<timeout>` | 用于执行 HTTP 请求的服务器端超时。 仅适用于[获取环境事件](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)和[获取环境聚合](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API。 超时值应采用 ISO 8601 持续时间格式（例如 `"PT20S"`），并且应在 `1-30 s` 范围内。 默认值为 `30 s`。 | Gen1 |
| `storeType=<storeType>` | 对于启用了 Warm 存储的 Gen2 环境，可以对 `WarmStore` 或 `ColdStore` 执行查询。 查询中的此参数定义应对哪个存储执行查询。 如果未定义，将对 Cold 存储区执行查询。 若要查询 Warm 存储，需要将 storeType 设置为 `WarmStore`。 如果未定义，将对 Cold 存储区执行查询。 | Gen2 |

## <a name="next-steps"></a>后续步骤

* 有关调用 Gen1 Azure 时序见解 API 的示例代码，请参阅[使用 C# 查询 Gen1 数据](./time-series-insights-query-data-csharp.md)。

* 有关调用 Gen2 Azure 时序见解 API 示例代码的示例代码，请参阅[使用 C# 查询 Gen2 数据](./time-series-insights-update-query-data-csharp.md)。

* 有关 API 参考信息，请阅读[查询 API 参考](/rest/api/time-series-insights/reference-query-apis)文档。
