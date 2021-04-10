---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007953"
---
| 语言/框架 | 项目<br/>GitHub                                                                                     | 包                                                                               | 获取<br/>started                        | 用户登录                                         | 访问 Web API                                                 | 正式发布 (GA) 或<br/>公共预览版<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | 公共预览版                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [适用于 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [教程](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [教程](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [教程](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览版的补充使用条款][preview-tos]适用于公共预览版中的库。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/