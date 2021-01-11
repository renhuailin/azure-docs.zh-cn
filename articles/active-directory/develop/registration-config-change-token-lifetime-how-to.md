---
title: 更改自定义 Azure AD 应用的令牌生存期默认值
description: 如何为在 Azure AD 上开发的应用程序更新令牌生存期策略
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5dec4958c3a1d955d8f8c1dce90e27696fbebfe5
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063342"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何为自定义开发的应用程序更改令牌生存期默认设置

本文介绍如何使用 Azure AD PowerShell 来设置访问令牌生存期策略。 Azure AD Premium 允许应用程序开发人员和租户管理员配置为非机密客户端颁发的令牌的生存期。 根据租户范围或要访问的资源设置令牌生存期策略。

> [!IMPORTANT]
> 在5月 2020 5 日后，租户将无法再配置刷新和会话令牌生存期。  在2021年1月30日后，Azure Active Directory 将停止在策略中遵守现有的刷新和会话令牌配置。 在弃用之后，你仍然可以配置访问令牌生存期。 有关详细信息，请参阅 [Azure AD 中的可配置令牌生存期](./active-directory-configurable-token-lifetimes.md)。
> 已在 Azure AD 条件访问中实现 [身份验证会话管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)   。 你可以使用此新功能，通过设置登录频率来配置刷新令牌生存期。  

若要设置访问令牌生存期策略，需要下载 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureADPreview)。
运行 **Connect-AzureAD -Confirm** 命令。

下面是一个示例策略，要求用户在 web 应用中更频繁地进行身份验证。 此策略设置对 web 应用的服务主体的访问生存期。 创建策略并将其分配给服务主体。 还需要获取服务主体的 ObjectId。

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure AD 中的可配置令牌生存期](./active-directory-configurable-token-lifetimes.md)，了解如何配置 Azure AD 颁发的令牌生存期，包括如何为组织中的所有应用、多租户应用或组织中的特定服务主体设置令牌生存期。 
* [Azure AD 令牌引用](./id-tokens.md)
