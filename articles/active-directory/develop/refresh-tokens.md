---
title: Microsoft 标识平台刷新令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Azure AD 发出的刷新令牌。
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 8b8bda71c637419dbd5b2bf7b181288abd9b965c
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112075234"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft 标识平台刷新令牌

当客户端获取访问令牌来访问受保护资源时，它同时还会收到一个刷新令牌。 当前访问令牌过期时，可以使用刷新令牌获取新的访问/刷新令牌对。 刷新令牌还用于获取其他资源的额外访问令牌。 刷新令牌绑定到用户和客户端的组合，但不绑定到资源或租户。 这使客户端可以使用刷新令牌来获取任何资源和租户组合的访问令牌，前提是客户端有权这样做。 刷新令牌是经过加密的，只有 Microsoft 标识平台可以读取它们。

## <a name="prerequisites"></a>先决条件

在阅读本文之前，建议浏览以下文章：

* Microsoft 标识平台中的 [ID 令牌](id-tokens.md)。
* Microsoft 标识平台中的[访问令牌](access-tokens.md)。

## <a name="refresh-token-lifetime"></a>刷新令牌生存期

刷新令牌的生存期比访问令牌要长得多。 该令牌的默认生存期为 90 天，每次使用后都会用新的刷新令牌替换。 这意味着，每当使用刷新令牌获取新的访问令牌时，也会发出一个新的刷新令牌。 Microsoft 标识平台不会在使用旧刷新令牌提取新的访问令牌时将旧刷新令牌撤销。 获取新的刷新令牌后，才会安全地将旧的刷新令牌删除。 刷新令牌需要像访问令牌或应用程序凭据一样安全地存储。 

## <a name="refresh-token-expiration"></a>刷新令牌过期

刷新令牌可以因超时和吊销而随时撤销。 当发生这种情况时，应用必须妥善处理登录服务的拒绝。 解决方法是向用户发送交互式登录提示以重新登录。 

### <a name="token-timeouts"></a>令牌超时

使用[令牌生存期配置](active-directory-configurable-token-lifetimes.md#refresh-and-session-token-lifetime-policy-properties)，可以缩短或延长刷新令牌的生存期。 此设置可更改刷新令牌在未使用的情况下可以存留的时长。 例如，假设用户超过 90 天没有打开应用。 当应用尝试使用 90 天以上的旧刷新令牌时，它会发现该令牌已过期。 此外，管理员还可以要求定期使用第二个因素，强制用户按特定的时间间隔手动登录。 这些方案包括：

* 非活动：刷新令牌仅在 `MaxInactiveTime` 所指示的时间段有效。  如果令牌在该时间段内未使用（并且未替换为新令牌），则它将不再可用。
* 会话到期：如果 `MaxAgeSessionMultiFactor` 或 `MaxAgeSessionSingleFactor` 已设置为其默认值（“Until-revoked”）以外的值，则在经过 MaxAgeSession* 中设置的时间后，需要重新进行身份验证。  这用于强制用户定期使用第一个或第二个因素重新进行身份验证。 
* 示例:
  * 租户的 MaxInactiveTime 为 5 天，用户去度假一周， 因此 Azure AD 在 7 天内未看到用户发出新令牌请求。 下次用户请求新令牌时，他们将看到其刷新令牌已被吊销，他们必须重新输入其凭据。
  * 敏感应用程序的 `MaxAgeSessionMultiFactor` 为 1 天。 如果用户在一天的某个时段后再次登录，则需要通过交互式提示再次完成 MFA。 例如，如果用户在星期一登录，并在经过 25 小时后的星期二再次登录。 

并非所有刷新令牌都遵循令牌生存期策略中设置的规则。 具体而言，在[单页应用](reference-third-party-cookies-spas.md)中使用的刷新令牌的活动期上限始终为 24 小时，就好像它们应用的 `MaxAgeSessionSingleFactor` 策略为 24 小时。 

### <a name="revocation"></a>撤销

服务器可能会因为凭据更改、用户操作或管理员操作而撤销刷新令牌。  刷新令牌分为两类：颁发给机密客户端的令牌（最右侧的列）以及颁发给公共客户端的令牌（所有其他列）。

| 更改 | 基于密码的 Cookie | 基于密码的令牌 | 不基于密码的 Cookie | 不基于密码的令牌 | 机密客户端令牌 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 密码过期 | 一直有效 | 一直有效 | 一直有效 | 一直有效 | 一直有效 |
| 用户更改了密码 | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 用户执行 SSPR | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 管理员重置密码 | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 用户[通过 PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 撤销刷新令牌 | 已撤销 | 已撤销 | 已撤销 | 已撤销 | 已撤销 |
| 管理员[通过 PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 撤销用户的所有刷新令牌 | 已撤销 | 已撤销 |已撤销 | 已撤销 | 已撤销 |
| [在 Web 上](v2-protocols-oidc.md#single-sign-out)单一注销 | 已撤销 | 一直有效 | 已撤销 | 一直有效 | 一直有效 |

## <a name="next-steps"></a>后续步骤

* 了解[可配置的令牌生存期](active-directory-configurable-token-lifetimes.md)
* 请查看[主刷新令牌](../devices/concept-primary-refresh-token.md)，了解有关主刷新令牌的更多详细信息。
