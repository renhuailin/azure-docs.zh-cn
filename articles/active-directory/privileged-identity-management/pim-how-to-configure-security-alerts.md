---
title: PIM 中 Azure AD 角色的安全警报 - Azure AD | Microsoft Docs
description: 在 Azure Active Directory 中为 Azure AD 角色 Privileged Identity Management 配置安全警报。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/30/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761995f33d5688e5864640a0e6e2f864f5aa44a3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766039"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中为 Azure AD 角色配置安全警报

当 Azure Active Directory (Azure AD) 组织中存在可疑或不安全活动时，Privileged Identity Management (PIM) 会生成警报。 触发警报时，它显示在 Privileged Identity Management 仪表板上。 选择警报以查看列出了已触发该警报的用户或角色的报告。

![显示包含警报及其严重性列表的“警报”页面的屏幕截图。](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>安全警报

本部分列出 Azure AD 角色的所有安全警报，以及如何修复和防止这些警报。 严重性的含义如下：

- **高**：因策略冲突需要立即采取措施。
- **中**：不需要立即采取措施但有潜在的策略冲突。
- **低**：不需要立即采取措施，但建议考虑可取的策略更改。

### <a name="administrators-arent-using-their-privileged-roles"></a>管理员不使用其特权角色

严重级别：低

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 为用户分配他们不需要的特权角色会增大受攻击的可能性。 攻击者更容易忽略不经常使用的帐户。 |
| **如何修复？** | 查看列表中的用户，将其从不需要的特权角色中删除。 |
| **防护** | 特权角色只分配给有业务需要的用户。 </br>安排定期的[访问评审](./pim-create-azure-ad-roles-and-resource-roles-review.md)，以确认用户是否仍需要访问权限。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果用户在指定天数过后未激活角色，将触发此警报。 |
| **天数** | 此设置指定用户可以不激活角色的最大天数（0 到 100）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色无需多重身份验证进行激活

严重级别：低

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 如果不执行多重身份验证，则遭到入侵的用户可以激活特权角色。 |
| **如何修复？** | 查看角色列表，[要求执行多重身份验证](pim-how-to-change-default-settings.md)（针对每个角色）。 |
| **防护** | 针对每个角色[要求执行 MFA](pim-how-to-change-default-settings.md)。  |
| **门户中的缓解措施** | 要求在激活特权角色时执行多重身份验证。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>组织没有 Azure AD Premium P2

严重级别：低

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 当前 Azure AD 组织没有 Azure AD Premium P2。 |
| **如何修复？** | 查看有关 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的信息。 升级到 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>可能有过时的帐户充当特权角色

严重级别：中

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 具有特权角色的帐户在过去 90 天内未更改密码。 这些帐户可能是未维护且易受攻击者攻击的服务帐户或共享帐户。 |
| **如何修复？** | 请检查列表中的帐户。 如果它们不再需要访问权限，请将其从特权角色中删除。 |
| **防护** | 确保当知道密码的用户有变化时，共享的帐户会轮换使用强密码。 </br>使用[访问评审](./pim-create-azure-ad-roles-and-resource-roles-review.md)定期审查具有特权角色的帐户，并删除不再需要的角色分配。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **最佳实践** | 使用密码进行身份验证并分配给高特权管理角色（如全局管理员或安全管理员）的共享帐户、服务帐户和紧急访问帐户应针对以下情况轮换其密码：<ul><li>发生涉及误用或泄露管理访问权限的安全事件后</li><li>任何用户的权限被更改而导致他们不再是管理员之后（例如，一名曾是管理员的员工离开了 IT 或组织）</li><li>固定时间间隔（例如，每季度或每年），即使没有任何已知的安全漏洞或 IT 人员变动</li></ul>由于多个用户有权限访问这些帐户的凭据，因此应轮换这些凭据以确保已失去其角色的人员无法再访问帐户。 [详细了解如何保护帐户](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>在 Privileged Identity Management 之外分配角色

严重级别：高

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 在 Privileged Identity Management 外部进行的特权角色分配未受到正确的监视，可能表示正遭到攻击。 |
| **如何修复？** | 查看列表中的用户，将其从 Privileged Identity Management 外部分配的特权角色中删除。 你还可以在警报设置中启用或禁用警报及其附带的电子邮件通知。 |
| **防护** | 调查在 Privileged Identity Management 外部的哪个位置为用户分配了特权角色，禁止将来在该位置进行分配。 |
| **门户中的缓解措施** | 从用户的特权角色中删除用户。 |

### <a name="there-are-too-many-global-administrators"></a>全局管理员过多

严重级别：低

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 全局管理员是特权最高的角色。 如果全局管理员遭到入侵，则攻击者可以获取其所有访问权限，使整个系统面临风险。 |
| **如何修复？** | 查看列表中的用户，删除不是绝对需要“全局管理员”角色的所有用户。 </br>改为这些用户分配特权更低的角色。 |
| **防护** | 为用户分配他们所需的最低特权角色。 |
| **门户中的缓解措施** | 从用户的特权角色中删除其帐户。 |
| **触发器** | 如果满足两个不同的条件并且可以同时配置这两个条件，将触发此警报。 首先，需要达到全局管理员角色分配的某个特定阈值。 其次，总角色分配的特定百分比必须是全局管理员。 如果只满足其中一个度量，不会显示该警报。 |
| **全局管理员的最少数目** | 此设置指定的全局管理员角色分配数量（2 到 100）你认为对你的 Azure AD 组织来说太少。 |
| **全局管理员百分比** | 此设置指定属于全局管理员的管理员的最小百分比（0% 到 100%），你不希望你的 Azure AD 组织降到该值之下。 |

### <a name="roles-are-being-activated-too-frequently"></a>角色激活过于频繁

严重级别：低

| | 说明 |
| --- | --- |
| **为何收到此警报？** | 同一用户多次激活同一特权角色是受到攻击的迹象。 |
| **如何修复？** | 检查列表中的用户，并确保用户特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使他们能够执行任务。 |
| **防护** | 确保特权角色的[激活持续时间](pim-how-to-change-default-settings.md)设置得足够长，使用户能够执行其任务。</br>针对其帐户由多个管理员共享的特权角色[要求执行多重身份验证](pim-how-to-change-default-settings.md)。 |
| **门户中的缓解措施** | 不适用 |
| **触发器** | 如果用户在指定期限内多次激活同一特权角色，将触发此警报。 可以同时配置时间段和激活次数。 |
| **激活续订时间范围** | 此设置以天、小时、分钟和秒为单位指定要用于跟踪可疑续订的时间段。 |
| **激活续订次数** | 此设置指定你希望在所选时间范围内获得通知的激活次数（2 到 100）。 可通过移动滑块或在文本框中键入数字更改此设置。 |

## <a name="customize-security-alert-settings"></a>自定义安全警报设置

在“警报”页，选择“设置”。 

![突出显示了“设置”的“警报”页](media/pim-how-to-configure-security-alerts/alert-settings.png)

在各个警报上自定义设置以适应你的环境和安全目标。

![警报的“设置”页，用于启用和配置设置](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)