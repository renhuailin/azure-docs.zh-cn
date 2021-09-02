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
ms.openlocfilehash: e4d2721905d1e344cd0825466e0b0eb08578ef47
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688122"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft 标识平台刷新令牌

当客户端获取访问令牌来访问受保护资源时，它同时还会收到一个刷新令牌。 当前访问令牌过期时，可以使用刷新令牌获取新的访问/刷新令牌对。 刷新令牌还用于获取其他资源的额外访问令牌。 刷新令牌绑定到用户和客户端的组合，但不绑定到资源或租户。 这样，客户端就可以使用刷新令牌来获取任何资源和租户组合的访问令牌，但前提是客户端有权这样做。 刷新令牌是经过加密的，只有 Microsoft 标识平台可以读取它们。

## <a name="prerequisites"></a>先决条件

在阅读本文之前，建议浏览以下文章：

* Microsoft 标识平台中的 [ID 令牌](id-tokens.md)。
* Microsoft 标识平台中的[访问令牌](access-tokens.md)。

## <a name="refresh-token-lifetime"></a>刷新令牌生存期

刷新令牌的生存期比访问令牌更长。 该令牌的默认生存期为 90 天，每次使用后都会用新的刷新令牌替换。 这意味着，每当使用刷新令牌获取新的访问令牌时，都会发出一个新的刷新令牌。 Microsoft 标识平台不会在使用旧刷新令牌提取新的访问令牌时将旧刷新令牌撤销。 获取新的刷新令牌后，才会安全地将旧的刷新令牌删除。 刷新令牌需要像访问令牌或应用程序凭据一样安全地存储。 

## <a name="refresh-token-expiration"></a>刷新令牌过期

刷新令牌可以因超时和吊销而随时撤销。 当发生这种情况时，应用必须妥善处理登录服务的拒绝。 解决方法是向用户发送交互式登录提示以重新登录。 

### <a name="token-timeouts"></a>令牌超时

无法配置刷新令牌的生存期。 不能缩短或延长生存期。 配置“条件访问”中的登录频率，以定义要求用户重新登录之前的时间段。 详细了解[使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

并非所有刷新令牌都遵循令牌生存期策略中设置的规则。 具体而言，[单页应用](reference-third-party-cookies-spas.md)中使用的刷新令牌的活动时间固定为 24 小时，就像已应用 24 小时的 `MaxAgeSessionSingleFactor` 策略一样。 

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
