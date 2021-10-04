---
title: 在国家/地区云应用中使用 MSAL | Azure
titleSuffix: Microsoft identity platform
description: 借助 Microsoft 身份验证库 (MSAL)，应用程序开发人员可以获取令牌，以调用受保护的 Web API。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、合作伙伴 Web API 或你自己的 Web API。 MSAL 支持多个应用程序体系结构和平台。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/21/2021
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8b8ae31aa2af84a6f8dfd4f93c90a09e58805f73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598793"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在国家/地区云环境中使用 MSAL

[国家/地区云](authentication-national-cloud.md)又称为主权云，是 Azure 的物理独立实例。 Azure 的这些区域可帮助确保数据驻留、主权及合规性要求在地理边界内得到遵从。

除了 Microsoft 全球云以外，Microsoft 身份验证库 (MSAL) 使得国家/地区云中的应用程序开发人员能够获取令牌，以便进行身份验证并调用受保护的 Web API。 这些 Web API 可以是 Microsoft Graph 或其他 Microsoft API。

包括全球 Azure 云在内，Azure Active Directory (Azure AD) 在以下国家/地区云中部署： 

- Azure Government
- Azure 中国世纪互联
- Azure 德国（[于 2021 年 10 月 29 日关闭](https://www.microsoft.com/cloud-platform/germany-cloud-regions)）

本指南演示了如何登录到工作和学校帐户、获取访问令牌，并在 [Azure 政府云](https://azure.microsoft.com/global-infrastructure/government/)环境中调用 Microsoft Graph API。

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure 德国（Microsoft 云德国版）

> [!WARNING]
> Azure 德国（Microsoft 云德国版）将[于 2021 年 10 月 29 日关闭](https://www.microsoft.com/cloud-platform/germany-cloud-regions)。 你选择不在该日期之前将其迁移到全球 Azure 中的某个区域的服务和应用程序将变得不可访问。

如果尚未从 Azure 德国迁移应用程序，请按[有关如何从 Azure 德国进行迁移的 Azure Active Directory 信息](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad)中的说明开始迁移。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保满足以下先决条件。

### <a name="choose-the-appropriate-identities"></a>选择合适的标识

[Azure 政府版](../../azure-government/index.yml)应用程序可以使用 Azure AD 政府版标识和 Azure AD 公共版标识对用户进行身份验证。 因为任意这些标识都可以使用，所以需决定应为方案选择哪个颁发机构终结点：

- Azure AD 公共版：如果组织已有 Azure AD 公共版租户，支持 Microsoft 365（公共或 GCC）或其他应用程序，则通常使用此标识。
- Azure AD 政府版：如果组织已有 Azure AD 政府版租户，支持 Office 365（GCC High 或 DoD），或者正在 Azure AD 政府版中创建新的租户，则通常使用此标识。

确定之后，还需特别考虑在哪里执行应用注册。 如果为 Azure 政府版应用程序选择了 Azure AD 公共版标识，则必须在 Azure AD 公共版租户中注册该应用程序。

### <a name="get-an-azure-government-subscription"></a>获取 Azure 政府版订阅

若要获取 Azure 政府版订阅，请参阅[管理和连接 Azure 政府版订阅](../../azure-government/compare-azure-government-global-azure.md)。

如果没有 Azure 政府版订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/global-infrastructure/government/request/)。

若要详细了解如何使用特定编程语言的国家/地区云，请选择与语言相匹配的选项卡：

## <a name="net"></a>[.NET](#tab/dotnet)

可以使用 MSAL.NET 登录用户、获取令牌并在国家/地区云中调用 Microsoft Graph API。

以下教程演示了如何生成 .NET Core 2.2 MVC Web 应用。 此应用使用 OpenID Connect，通过隶属于某个国家/地区云的组织中的工作和学校帐户来登录用户。

- 若要登录用户和获取令牌，请按照本教程操作：[生成 ASP.NET Core Web 应用，通过 Microsoft 标识平台在主权云中登录用户](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 若要调用 Microsoft Graph API，请按照本教程操作：[使用 Microsoft 标识平台，代表使用其在 Microsoft 国家/地区云中的工作和学校帐户登录的用户，从 ASP.NET Core 2.x Web 应用调用 Microsoft Graph ASP](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="javascript"></a>[JavaScript](#tab/javascript)

若要启用适用于主权云的 MSAL.js 应用程序，请执行以下操作：

- 在特定门户中注册应用程序，具体取决于云。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 使用存储库中的任何[示例](https://github.com/Azure-Samples/ms-identity-javascript-tutorial)并对配置进行适当更改，具体取决于云（如后文所述）。
- 根据应用程序注册所在的云，使用特定的颁发机构。 有关不同云对应颁发机构的详细信息，请参阅 [Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。
- 调用 Microsoft Graph API 时，需要一个特定于所用云的终结点 URL。 若要查找适合于所有国家/地区云的 Microsoft Graph 终结点，请参阅 [Microsoft Graph 和 Graph 资源管理器服务根终结点](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

以下是示例颁发机构：

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

下面是 Microsoft Graph 终结点的示例，范围如下：

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

下面是使用主权云对用户进行身份验证并调用 Microsoft Graph 的最小代码：

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        redirectUri: "/",
    }
};

// Initialize MSAL
const msalObj = new PublicClientApplication(msalConfig);

// Get token using popup experience
try {
    const graphToken = await msalObj.acquireTokenPopup({
        scopes: ["User.Read"]
    });
} catch(error) {
    console.log(error)
}

// Call the Graph API
const headers = new Headers();
const bearer = `Bearer ${graphToken}`;

headers.append("Authorization", bearer);

fetch("https://graph.microsoft.us/v1.0/me", {
    method: "GET",
    headers: headers
})
```


## <a name="python"></a>[Python](#tab/python)

若要启用适用于主权云的 MSAL Python 应用程序，请执行以下操作：

- 在特定门户中注册应用程序，具体取决于云。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 使用存储库中的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample)并对配置进行适当更改，具体取决于云（如后文所述）。
- 根据应用程序注册所在的云，使用特定的颁发机构。 有关不同云对应颁发机构的详细信息，请参阅 [Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

    以下是示例颁发机构：

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- 调用 Microsoft Graph API 时，需要一个特定于所用云的终结点 URL。 若要查找适合于所有国家/地区云的 Microsoft Graph 终结点，请参阅 [Microsoft Graph 和 Graph 资源管理器服务根终结点](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

    下面是 Microsoft Graph 终结点的示例，范围如下：

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

若要为主权云启用适用于 Java 的 MSAL 应用程序，请执行以下操作：

- 在特定门户中注册应用程序，具体取决于云。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 使用存储库中的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)并对配置进行适当更改，具体取决于云（如后文所述）。
- 根据应用程序注册所在的云，使用特定的颁发机构。 有关不同云对应颁发机构的详细信息，请参阅 [Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

以下是示例颁发机构：

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- 调用 Microsoft Graph API 时，需要一个特定于所用云的终结点 URL。 若要查找适合于所有国家/地区云的 Microsoft Graph 终结点，请参阅 [Microsoft Graph 和 Graph 资源管理器服务根终结点](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

下面是 Graph 终结点的示例，范围如下：

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

适用于 iOS 和 macOS 的 MSAL 可用于在国家/地区云中获取令牌，但在创建 `MSALPublicClientApplication` 时需要进行额外配置。

例如，如果希望应用程序成为国家/地区云（此处为美国政府）的多租户应用程序，可以编写：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

适用于 iOS 和 macOS 的 MSAL 可用于在国家/地区云中获取令牌，但在创建 `MSALPublicClientApplication` 时需要进行额外配置。

例如，如果希望应用程序成为国家/地区云（此处为美国政府）的多租户应用程序，可以编写：

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>后续步骤

有关每种云对应的 Azure 门户 URL 和令牌终结点的列表，请参阅[国家/地区云身份验证终结点](authentication-national-cloud.md)。

国家云文档：

- [Azure Government](../../azure-government/index.yml)
- [Azure 中国世纪互联](/azure/china/)
- [Azure 德国（于 2021 年 10 月 29 日关闭）](../../germany/index.yml)