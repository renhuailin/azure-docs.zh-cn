---
title: Microsoft 标识 Web 身份验证库概述
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识 Web，这是一个与 Azure Active Directory、Azure AD B2C 和 Microsoft Graph 以及其他 Web API 集成的适用于 ASP.NET Core 应用程序的身份验证和授权库。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b82e300128a41f8315132e1ff93af33c853edb15
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173509"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft 标识 Web 身份验证库

Microsoft 标识 Web 是一组 ASP.NET Core 库，可简化向与 Microsoft 标识平台集成的 Web 应用和 Web API 添加身份验证和授权支持的过程。 它提供了一个单面 API 便利层，该层将 ASP.NET Core、其身份验证中间件以及[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](https://github.com/azuread/microsoft-authentication-library-for-dotnet) 绑定在一起。

## <a name="supported-application-scenarios"></a>支持的应用程序方案

如果要构建 ASP.NET Core Web 应用或 Web API，并希望使用 Azure Active Directory (Azure AD) 或 Azure AD B2C 进行标识和访问管理 (IAM)，建议在所有这些情况下使用 Microsoft 标识 Web：

- [用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)
- [将用户登录并以用户身份调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)
- [只有经身份验证的用户可以访问的受保护 Web API](scenario-protected-web-api-overview.md)
- [代表已登录用户调用其他（下游）Web API 的受保护 Web API](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>获取该库

你可以从 [NuGet](#nuget)、[.NET Core 项目模板](#project-templates)和 [GitHub](#github) 获取 Microsoft 标识 Web。

#### <a name="nuget"></a>NuGet

Microsoft 标识 Web 在 NuGet 上以一组包的形式提供，这些包基于应用的需求提供模块化功能。 使用 .NET CLI 的 `dotnet add` 命令或 Visual Studio 的 **NuGet 包管理器** 安装适用于项目的包：

- [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) - 主要包。 使用 Microsoft 标识 Web 的所有应用所需的。
- [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) - 可选。 为 Web 应用添加进行用户登录和注销的 UI，以及关联的控制器。
- [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) - 可选。 提供与 Microsoft Graph API 的简化交互。
- [Microsoft.Identity.Web.MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) - 可选。 提供与 Microsoft Graph API [beta 版本终结点](/graph/api/overview?view=graph-rest-beta&preserve-view=true)的简化交互。

#### <a name="project-templates"></a>项目模板

Microsoft 标识 Web 项目模板包含在 .NET 5.0 中，可以在下载后用于 ASP.NET Core 3.1 项目。

如果你使用的是 ASP.NET Core 3.1，请通过 .NET CLI 安装模板：

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

下图显示了受支持的应用类型及其相关参数的概要视图：

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="适用于 Microsoft 标识 Web 的 .NET CLI 项目模板图示":::
<br /><sup><b>*</b></sup> `MultiOrg` 不支持与 `webapi2` 一起使用，但你可以通过将租户设置为 `common` 或 `organizations` 在 appsettings.json 中启用它。
<br /><sup><b>**</b></sup> `--calls-graph` 不支持用于 Azure AD B2C

此示例 .NET CLI 命令取自我们的 [Blazor Server 教程](tutorial-blazor-server.md)，它生成一个新的 Blazor Server 项目，其中包含正确的包和起始代码（已显示占位符值）：

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft 标识 Web 是 GitHub 上承载的一个开源项目：<a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/microsoft-identity-web<span class="docon docon-navigate-external x-hidden-focus"></span></a>

[存储库 wiki](https://github.com/AzureAD/microsoft-identity-web/wiki) 包含其他文档，如果你需要帮助或发现了 bug，可以[提交问题](https://github.com/AzureAD/microsoft-identity-web/issues)。

## <a name="features"></a>功能

如果你使用默认的 ASP.NET 3.1 项目模板，则 Microsoft 标识 Web 包括多项未提供的功能。

| 功能                                                                                  | ASP.NET Core 3.1                                                     | Microsoft 标识 Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 在 Web 应用中[将用户登录](scenario-web-app-sign-user-app-configuration.md)             | <li>工作或学校帐户<li>社交标识（使用 Azure AD B2C） | <li>工作或学校帐户<li>Microsoft 个人帐户<li>社交标识（使用 Azure AD B2C）     |
| [保护 Web API](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>工作或学校帐户<li>社交标识（使用 Azure AD B2C） | <li>工作或学校帐户<li>Microsoft 个人帐户<li>社交标识（使用 Azure AD B2C）     |
| 多租户应用中的颁发者验证                                                   | 否                                                                   | 是，适用于[所有云](authentication-national-cloud.md)和 [Azure AD B2C](../../active-directory-b2c/index.yml) |
| Web 应用/API [调用 Microsoft Graph][scenario-api-call-graph]                             | 否                                                                   | 是                                                                                                     |
| Web 应用/API [调用 Web API][scenario-api-call-api]                                       | 否                                                                   | 是                                                                                                     |
| 支持证书凭据                                                         | 否                                                                   | 是，包括 Azure Key Vault                                                                          |
| Web 应用中的增量同意和条件访问支持                           | 否                                                                   | 是，在 MVC、Razor 页面和 Blazor 中                                                                    |
| Web API 中的令牌加密证书                                                | 否                                                                   | 是                                                                                                     |
| Web API 中的[作用域/应用角色验证][scenario-api-validation]                        | 否                                                                   | 是                                                                                                     |
| Web API 中的 `WWW-Authenticate` 标头生成                                         | 否                                                                   | 是                                                                                                     |

## <a name="next-steps"></a>后续步骤

若要查看操作中的 Microsoft 标识 Web，请尝试我们的 Blazor Server 教程：

[教程：创建使用 Microsoft 标识平台进行身份验证的 Blazor Server 应用](tutorial-blazor-server.md)

GitHub上的 Microsoft 标识 Web Wiki 包含了广泛的参考文档，涉及此库的各个方面。 例如，可以在下面找到证书用法、增量同意和条件访问参考：

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">将证书用于 Microsoft.Identity.Web<span class="docon docon-navigate-external x-hidden-focus"></span></a> (GitHub)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">增量同意和条件访问<span class="docon docon-navigate-external x-hidden-focus"></span></a> (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenario-api-call-api]: scenario-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk [scenario-api-call-graph]: scenario-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk [scenario-api-validation]: scenario-protected-web-api-verification-scope-app-roles.md