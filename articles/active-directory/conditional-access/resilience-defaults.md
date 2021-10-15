---
title: Azure AD 条件访问的复原能力默认值
description: 复原能力默认值和 Azure AD 备份身份验证服务
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a8e61f296d430713812007b93f1b34cea8588a
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811541"
---
# <a name="conditional-access-resilience-defaults"></a>条件访问：复原能力默认值

如果主身份验证服务中断，Azure Active Directory (Azure AD) 备份身份验证服务可能会为现有会话自动向应用程序颁发访问令牌。 此功能可能会显著提高 Azure AD 复原能力，因为对现有会话的重新身份验证占 Azure AD 身份验证的 90% 以上。 备份身份验证服务不支持来宾用户的新会话或身份验证。

对于受条件访问保护的身份验证，在颁发访问令牌之前重新评估策略以确定：

1.  应用哪些条件访问策略？
1.  对于应用的策略，是否满足所需的控制？

在中断期间，备份身份验证服务无法实时评估所有条件来确定是否应该应用条件访问策略。 条件访问复原能力默认值是一个新的会话控制，允许管理员决定是当无法实时评估策略条件时在中断期间阻止身份验证，还是允许使用在用户会话开始时收集的数据评估策略。 

> [!IMPORTANT]
> 所有新策略和现有策略都自动启用复原能力默认值，Microsoft 强烈建议保留启用复原能力默认值，以缓解服务中断的影响。 管理员可以禁用单个条件访问策略的复原能力默认值。 

## <a name="how-does-it-work"></a>它是如何工作的？

在中断期间，备份身份验证服务会自动为某些会话重新颁发访问令牌：

| 会话描述 | 授予访问权限 |
| --- | --- |
| 新会话 | 否 |
| 现有会话 - 未配置条件访问策略 | 是 |
| 现有会话 - 配置的条件访问策略和所需的控制（如 MFA）以前已得到满足 | 是 |
| 现有会话 - 配置的条件访问策略和所需的控制（如 MFA）以前未得到满足 | 由复原能力默认值确定 |

当现有会话在 Azure AD 中断期间过期时，新访问令牌的请求将路由到备份身份验证服务，并重新评估所有条件访问策略。 如果没有条件访问策略，或者以前在会话开始时满足了所需的所有控制（如 MFA），则备份身份验证服务会颁发新的访问令牌来延长会话。 

如果以前未满足策略所需的控制，则重新评估策略以确定是应授予还是拒绝访问权限。 但是，并非所有条件都可以在中断期间实时重新评估。 这些情况包括： 

- 组成员身份
- 角色成员身份
- 登录风险
- 用户风险
- 国家/地区位置（解析新的 IP 或 GPS 坐标）

## <a name="resilience-defaults-enabled"></a>已启用复原能力默认值

启用复原能力默认值后，备份身份验证服务可能会使用在会话开始时收集的数据来评估在缺少实时数据的情况下是否应该应用策略。 默认情况下，所有策略都将启用复原能力默认值。 当中断期间需要实时策略评估才能访问敏感应用程序时，可能会为个别策略禁用该设置。

示例：已启用复原能力默认值的策略要求所有访问 Azure 门户的全局管理员执行 MFA。 在中断之前，如果不是全局管理员的用户访问 Azure 门户，则不会应用策略，并且将授予用户访问权限，而不会提示用户执行 MFA。 在中断期间，备份身份验证服务将重新评估策略，以确定是否应该提示用户执行 MFA。 由于备份身份验证服务无法实时评估角色成员身份，因此它将使用在用户会话开始时收集的数据来确定是否仍不应应用策略。因此，将授予用户访问权限，而不会提示用户执行 MFA。

## <a name="resilience-defaults-disabled"></a>已禁用复原能力默认值

禁用复原能力默认值后，备份身份验证服务不会使用在会话开始时收集的数据来评估条件。 在中断期间，如果无法实时评估策略条件，则访问将被拒绝。

示例：已停用复原能力默认值的策略要求所有访问 Azure 门户的全局管理员执行 MFA。 在中断之前，如果不是全局管理员的用户访问 Azure 门户，则不会应用策略，并且将授予用户访问权限，而不会提示用户执行 MFA。 在中断期间，备份身份验证服务将重新评估策略，以确定是否应该提示用户执行 MFA。 由于备份身份验证服务无法实时评估角色成员身份，因此会阻止用户访问 Azure 门户。

> [!WARNING]
> 禁用应用于组或角色的策略的复原能力默认值将降低租户中所有用户的复原能力。 由于在中断期间无法实时评估组和角色成员身份，因此，即使不属于策略分配中的组或角色的用户访问策略范围内的应用程序也会被拒绝。 要避免降低不在策略范围内的所有用户的复原能力，请考虑将策略应用于单个用户，而不是组或角色。 

## <a name="testing-resilience-defaults"></a>测试复原能力默认值

目前无法使用备份身份验证服务进行试运行，或模拟启用或禁用复原能力默认值的策略的结果。 Azure AD 将使用备份身份验证服务进行每月练习，并且登录日志将显示是否使用了备份身份验证服务来颁发访问令牌。

## <a name="configuring-resilience-defaults"></a>配置复原能力默认值

可以从 Azure 门户、MS Graph API 或 PowerShell 配置条件访问复原能力默认值。 

### <a name="azure-portal"></a>Azure 门户

1.  导航到 Azure 门户 > 安全 > 条件访问
1.  创建新策略或选择现有策略
1.  打开“会话控制”设置
1.  选择“禁用复原能力默认值”以禁用此策略的设置。 在 Azure AD 中断期间，将阻止策略范围内的登录
1.  保存对策略的更改

### <a name="ms-graph-apis"></a>MS Graph API

还可使用 MS Graph API 和 [Microsoft Graph Explorer](/graph/graph-explorer/graph-explorer-overview) 管理条件访问策略的复原能力默认值。 

示例请求 URL： 

`PATCH https://graph.microsoft.com/beta/identity/conditionalAccess/policies/policyId`

示例请求正文： 

```json

{
"sessionControls": {
"disableResilienceDefaults": true
}
}
```

### <a name="powershell"></a>PowerShell

安装 Microsoft.Graph.Authentication 模块后，可能会使用 Microsoft PowerShell 部署此补丁操作。 要安装此模块，请打开提升的 PowerShell 提示符并执行以下操作

`Install-Module Microsoft.Graph.Authentication`

连接到 Microsoft Graph，请求所需的范围 –

`Connect-MgGraph -Scopes Policy.Read.All,Policy.ReadWrite.ConditionalAccess,Application.Read.All -TenantId <TenantID>`

在出现提示时进行身份验证。

为 PATCH 请求创建 JSON 正文 –

`$patchBody = '{"sessionControls": {"disableResilienceDefaults": true}}'`

执行补丁操作 –

`Invoke-MgGraphRequest -Method PATCH -Uri https://graph.microsoft.com/beta/identity/conditionalAccess/policies/<PolicyID> -Body $patchBody`

## <a name="recommendations"></a>建议

Microsoft 建议启用复原能力默认值。 虽然没有直接安全问题，但客户应评估他们是否希望允许备份身份验证服务使用在会话开始时收集的数据（而不是实时）在中断期间评估条件访问策略。 

自会话开始以来，用户的角色或组成员身份可能已更改。 使用[连续访问评估 (CAE)](concept-continuous-access-evaluation.md)，访问令牌的有效期为 24 小时，但受即时吊销事件限制。 备份身份验证服务订阅相同的吊销事件 CAE。 如果将用户的令牌作为 CAE 的一部分吊销，则用户无法在中断期间登录。 启用复原能力默认值后，将延长在中断期间过期的现有会话。 即使为策略配置了会话控制来强制实施登录频率，会话也会延长。 例如，启用了复原能力默认值的策略可能要求用户每隔一小时重新进行身份验证，以访问 SharePoint 站点。 在中断期间，即使 Azure AD 可能无法重新验证用户身份，用户的会话也会延长。 

## <a name="next-steps"></a>后续步骤

- [连续访问评估 (CAE)](concept-continuous-access-evaluation.md)
