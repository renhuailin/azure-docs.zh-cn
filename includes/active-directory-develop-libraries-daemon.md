---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007986"
---
| 语言/框架 | 项目<br/>GitHub                                                                 | 包                                                                                | 获取<br/>started                           | 用户登录                                            | 访问 Web API                                                 | 正式发布 (GA) 或<br/>公共预览版<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [快速入门](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![库无法为用户登录请求 ID令牌。][n] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![库无法为用户登录请求 ID令牌。][n] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| 节点               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)  | [快速入门](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![库无法为用户登录请求 ID令牌。][n] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [快速入门](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![库无法为用户登录请求 ID令牌。][n] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览版的补充使用条款][preview-tos]适用于公共预览版中的库。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/