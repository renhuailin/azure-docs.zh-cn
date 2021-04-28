---
title: 自定义 Azure AD 租户应用声明 (PowerShell)
titleSuffix: Microsoft identity platform
description: 本页介绍 Azure Active Directory 声明映射。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598884"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>如何：为租户中的特定应用自定义在令牌中发出的声明（预览版）

> [!NOTE]
> 此功能替换并取代了当前通过门户提供的[声明自定义](active-directory-saml-claims-customization.md)。 在同一应用程序中，如果使用门户以及本文档中详细介绍的 Graph/PowerShell 方法自定义声明，则为该应用程序颁发的令牌会忽略门户中的配置。 通过本文档中详细介绍的方法进行的配置不会在门户中进行反映。

> [!NOTE]
> 此功能目前以公共预览版提供。 应准备好还原或删除所做的任何更改。 在公共预览版推出期间，可在任何 Azure Active Directory (Azure AD) 订阅中使用此功能。 但是，在正式版推出后，某些功能可能需要使用 Azure AD Premium 订阅。 此功能支持配置适用于 WS-Fed、SAML、OAuth 和 OpenID Connect 协议的声明映射策略。

此功能由租户管理员用来自定义以令牌形式针对其租户中的特定应用程序发出的声明。 可以使用声明映射策略执行以下操作：

- 选择在令牌中包含的声明。
- 创建尚未存在的声明类型。
- 选择或更改在特定声明中发出的数据的源。

在本文中，我们会演练几个常见方案，它们可帮助你理解如何使用[声明映射策略类型](reference-claims-mapping-policy-type.md)。

创建声明映射策略时，还可以根据令牌中的目录架构扩展属性发出声明。 使用与扩展属性对应的 ExtensionID，而不是 `ClaimsSchema` 元素中的 ID。  有关扩展属性的更多信息，请参阅[使用目录架构扩展属性](active-directory-schema-extensions.md)。

## <a name="prerequisites"></a>先决条件

以下示例将创建、更新、链接和删除服务主体的策略。 声明映射策略只能分配给服务主体对象。 如果你是 Azure AD 新手，我们建议在继续学习这些示例之前，先[了解如何获取 Azure AD 租户](quickstart-create-new-tenant.md)。

若要开始，请执行以下步骤：

1. 首先请下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 运行 Connect 命令，登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 若要查看组织中创建的所有策略，请运行以下命令。 在以下方案中，建议在大多数操作之后运行此命令，以检查是否按预期创建策略。

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>省略令牌中的基本声明

在此示例中创建一个策略，它会从颁发给链接的服务主体的令牌中删除[基本声明集](reference-claims-mapping-policy-type.md#claim-sets)。

1. 创建声明映射策略。 此策略（链接到特定服务主体）会从令牌中删除基本声明集。
   1. 若要创建该策略，请运行以下命令：

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：

      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 Microsoft Graph API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到你的 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>在令牌中包括 EmployeeID 和 TenantCountry 作为声明

在此示例中创建一个策略，它会向颁发给链接的服务主体的令牌添加 EmployeeID 和 TenantCountry。 EmployeeID 在 SAML 令牌和 JWT 中都作为名称声明类型发出。 TenantCountry 在 SAML 令牌和 JWT 中都作为国家/地区声明类型发出。 在此示例中，我们继续在令牌中包含基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）向令牌添加 EmployeeID 和 TenantCountry 声明。
   1. 若要创建该策略，请运行以下命令：

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：

      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 Microsoft Graph API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到你的 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>在令牌中使用声明转换

在此示例中创建一个策略，它会向颁发给链接的服务主体的 JWT 发出自定义声明“JoinedData”。 此声明包含通过将用户对象的 extensionattribute1 属性中存储的数据与“.sandbox”联接所创建的值。 在此示例中，我们在令牌中排除基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）向令牌添加 EmployeeID 和 TenantCountry 声明。
   1. 若要创建该策略，请运行以下命令：

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：

      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 Microsoft Graph API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到你的 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>安全注意事项

接收令牌的应用程序依赖于这样一个事实：即声明值是由 Azure AD 权威颁发的，不能篡改。 但当你通过声明映射策略修改令牌内容时，上述事实可能不再适用。 应用程序必须明确承认令牌已被声明映射策略的创建者修改，才能防止被恶意参与者创建的声明映射策略破坏。 通过以下方式可实现此目的：

- 配置自定义签名密钥
- 更新应用程序清单，以接受映射的声明。
 
否则，Azure AD 将返回 [`AADSTS50146` 错误代码](reference-aadsts-error-codes.md#aadsts-error-codes)。

### <a name="custom-signing-key"></a>自定义签名密钥

若要将自定义签名密钥添加到服务主体对象，可使用 Azure PowerShell cmdlet [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) 为应用程序对象创建证书密钥凭据。

启用了声明映射的应用必须通过将 `appid={client_id}` 追加到其 [OpenID Connect 元数据请求](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)来验证令牌签名密钥。 下面是你应该使用的 OpenID 连接元数据文档的格式：

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>更新应用程序清单

或者，可以在[应用程序清单](reference-app-manifest.md)中将 `acceptMappedClaims` 属性设置为 `true`。 如 [apiApplication 资源类型](/graph/api/resources/apiapplication#properties)中所述，这样可以让应用程序使用声明映射，而无需指定自定义签名密钥。

这确实会要求请求令牌的受众使用经过验证的 Azure AD 租户域名，这意味着应确保设置 `Application ID URI`（在应用程序清单中由 `identifierUris` 表示），例如将其设置为 `https://contoso.com/my-api` 或（仅使用默认租户名）`https://contoso.onmicrosoft.com/my-api`。

如果不使用经过验证的域，Azure AD 将返回 `AADSTS501461` 错误代码以及消息“AcceptMappedClaims 仅支持与应用程序 GUID 匹配的令牌受众或经过验证的租户域中的受众。请更改资源标识符，或使用特定于应用程序的签名密钥。”

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[声明映射策略类型](reference-claims-mapping-policy-type.md)参考文章。
- 若要了解如何通过 Azure 门户自定义 SAML 令牌中颁发的声明，请参阅[如何：为企业应用程序自定义 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)
- 若要详细了解扩展属性，请参阅[在声明中使用目录架构扩展属性](active-directory-schema-extensions.md)。
