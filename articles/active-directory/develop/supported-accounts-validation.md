---
title: 受支持帐户类型的验证差异 | Azure
titleSuffix: Microsoft identity platform
description: 了解向 Microsoft 标识平台注册应用时，不同受支持帐户类型的各种属性的验证差异。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 09/29/2021
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: manrath
ms.openlocfilehash: 3a143a40f6c6faa4f846a5d77a39a0afdb52068e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355673"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>按受支持帐户类型列出的验证差异 (signInAudience)

向开发人员的 Microsoft 标识平台注册应用程序时，系统会要求你选择应用程序支持的帐户类型。 在应用程序对象和清单中，此属性是 `signInAudience`。

选项包括以下值：

- AzureADMyOrg：仅应用已注册的组织目录中的帐户（单租户）。
- AzureADMultipleOrgs：任何组织目录中的帐户（多租户）。
- **AzureADandPersonalMicrosoftAccount**：任何组织目录（多租户）中的帐户和个人 Microsoft 帐户（例如，Skype、Xbox 和 Outlook.com）。

对于已注册的应用程序，可以在应用程序的“身份验证”部分找到受支持帐户类型值。 也可以在 **清单** 中的 `signInAudience` 属性下找到它。

为此属性选择的值已影响其他应用对象属性。 因此，如果更改此属性，则可能需要先更改其他属性。

请参阅下表，了解不同受支持帐户类型的各种属性的验证差异。

| 属性                                                     | `AzureADMyOrg`                                                                                                                                                                                                                                      | `AzureADMultipleOrgs`                                                                                                                                                                                                                          | `AzureADandPersonalMicrosoftAccount` 和 `PersonalMicrosoftAccount`                                                                                                                                                |
| ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 应用程序 ID URI (`identifierURIs`)                        | 在租户中必须唯一 <br><br> 支持 urn:// 方案 <br><br> 不支持通配符 <br><br> 支持查询字符串和片段 <br><br> 最大长度为 255 个字符 <br><br> 对 identifierURI 数没有限制\* | 必须全局唯一 <br><br> 支持 urn:// 方案 <br><br> 不支持通配符 <br><br> 支持查询字符串和片段 <br><br> 最大长度为 255 个字符 <br><br> 对 identifierURI 数没有限制\* | 必须全局唯一 <br><br> 不支持 urn:// 方案 <br><br> 不支持通配符、片段和查询字符串 <br><br> 最大长度为 120 个字符 <br><br> 最多 50 个 identifierURI |
| 证书 (`keyCredentials`)                              | 对称签名密钥                                                                                                                                                                                                                               | 对称签名密钥                                                                                                                                                                                                                          | 加密和非对称签名密钥                                                                                                                                                                              |
| 客户端密码 (`passwordCredentials`)                       | 无限制\*                                                                                                                                                                                                                                          | 无限制\*                                                                                                                                                                                                                                     | 如果启用 liveSDK：最多 2 个客户端密码                                                                                                                                                               |
| 重定向 URI (`replyURLs`)                                  | 有关详细信息，请参阅[重定向 URI/回复 URL 的局限性和限制](reply-url.md)。                                                                                                                                                              |                                                                                                                                                                                                                                                |                                                                                                                                                                                                                    |
| API 权限 (`requiredResourceAccess`)                   | 无限制\*                                                                                                                                                                                                                                          | 无限制\*                                                                                                                                                                                                                                     | 每个应用程序最多 50 个资源，且每个资源 30 个权限（例如 Microsoft Graph）。 每个应用程序的总限制为 200（资源 x 权限）。                                              |
| 此 API 定义的作用域 (`oauth2Permissions`)             | 最大作用域名称长度为 120 个字符 <br><br> 对定义的作用域数没有限制\*                                                                                                                                                     | 最大作用域名称长度为 120 个字符 <br><br> 对定义的作用域数没有限制\*                                                                                                                                                | 最大作用域名称长度为 40 个字符 <br><br> 最多定义 100 个作用域                                                                                                                                  |
| 授权客户端应用程序 (`preAuthorizedApplications`) | 无限制\*                                                                                                                                                                                                                                          | 无限制\*                                                                                                                                                                                                                                     | 总最大值为 500 <br><br> 最多定义 100 个客户端应用 <br><br> 每个客户端最多定义 30 个作用域                                                                                                  |
| appRoles                                                     | 支持 <br> 无限制\*                                                                                                                                                                                                                           | 支持 <br> 无限制\*                                                                                                                                                                                                                      | 不支持                                                                                                                                                                                                      |
| 前通道注销 URL                                     | 允许 https://localhost <br><br> 不允许使用 `http` 方案 <br><br> 最大长度为 255 个字符                                                                                                                                         | 允许 https://localhost <br><br> 不允许使用 `http` 方案 <br><br> 最大长度为 255 个字符                                                                                                                                    | 允许使用 https://localhost ， http://localhost 失败 <br><br> 不允许使用 `http` 方案 <br><br> 最大长度为 255 个字符 <br><br> 不支持通配符                                            |

\* 应用对象的所有集合属性有大约 1000 项的全局限制。

## <a name="next-steps"></a>后续步骤

有关应用程序注册及其 JSON 清单的详细信息，请参阅：

- [应用程序注册](app-objects-and-service-principals.md)
- [应用程序清单](reference-app-manifest.md)
