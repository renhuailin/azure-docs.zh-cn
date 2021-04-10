---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007880"
---
| 平台          | 项目<br/>GitHub                                                                          | 包                                                                               | 获取<br/>started                    | 用户登录                                         | 访问 Web API                                                 | 正式发布 (GA) 或<br/>公共预览版<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [快速入门](../articles/active-directory/develop/quickstart-v2-android.md) | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| iOS (Swift/Obj-C) | [适用于 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [教程](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![库可以为用户登录请求 ID令牌。][y] | ![库可以为受保护的 Web API 请求访问令牌。][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览版的补充使用条款][preview-tos]适用于公共预览版中的库。

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/