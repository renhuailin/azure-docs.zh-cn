---
ms.openlocfilehash: 7a496a480ed176cb0e4316ab53f265c6d1c40a84
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122263393"
---
| 语言/框架 | 项目<br/>GitHub                                                                                           | 包                                                                                                    | 获取<br/>started                                                                                | 用户登录                                                        | 访问 Web API                                                                | 正式发布 (GA) 或<br/>公共预览版<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------:|:--------------------------------------------------------------------:|:------------------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                              | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                                                                  | ![库无法为用户登录请求 ID令牌。][n]             | ![库可以为受保护的 Web API 请求访问令牌。][y]                | GA                                                           |
| .NET                 | [Microsoft.IdentityModel](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Microsoft.IdentityModel](https://www.nuget.org/packages?q=Microsoft.IdentityModel)                        | —                                                                                                  | ![库无法为用户登录请求 ID 令牌。][n]<sup>2</sup> | ![库无法为受保护的 Web API 请求访问令牌。][n]<sup>2</sup> | GA                                                           |
| ASP.NET Core         | [ASP.NET 安全性](/aspnet/core/security/)                                                                      | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | [快速入门](../articles/active-directory/develop/quickstart-v2-aspnet-core-webapp-calls-graph.md) | ![库可以为用户登录请求 ID令牌。][y]                | ![库无法为受保护的 Web API 请求访问令牌。][n]             | GA                                                           |
| ASP.NET Core         | [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web)                                     | [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | [快速入门](../articles/active-directory/develop/quickstart-v2-aspnet-core-webapp-calls-graph.md) | ![库可以为用户登录请求 ID令牌。][y]                | ![库可以为受保护的 Web API 请求访问令牌。][y]                | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                                  | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [快速入门](../articles/active-directory/develop/quickstart-v2-java-webapp.md)                    | ![库可以为用户登录请求 ID令牌。][y]                | ![库可以为受保护的 Web API 请求访问令牌。][y]                | GA                                                           |
| Node.js              | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)          | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                                                | [快速入门](../articles/active-directory/develop/quickstart-v2-nodejs-webapp-msal.md)             | ![库可以为用户登录请求 ID令牌。][y]                | ![库可以为受保护的 Web API 请求访问令牌。][y]                | GA                                                              |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                           | [msal](https://pypi.org/project/msal)                                                                      | [快速入门](../articles/active-directory/develop/quickstart-v2-python-webapp.md)                  | ![库可以为用户登录请求 ID令牌。][y]                | ![库可以为受保护的 Web API 请求访问令牌。][y]                | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览版的补充使用条款][preview-tos]适用于公共预览版中的库。

<sup>2</sup> [Microsoft.IdentityModel](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 库仅验证令牌，它不能请求 ID 或访问令牌。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
