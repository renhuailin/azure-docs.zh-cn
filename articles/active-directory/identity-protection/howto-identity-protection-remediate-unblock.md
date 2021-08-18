---
title: 在 Azure AD 标识保护中修正风险和解除阻止用户
description: 了解用于关闭活动风险检测的选项。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc03e619903ae3fb4065dfa1d7f1478c8e08942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727648"
---
# <a name="remediate-risks-and-unblock-users"></a>修正风险并对用户解除阻止

完成[调查](howto-identity-protection-investigate-risk.md)后，将需要采取措施来修正风险或解除阻止用户。 组织还可以选择使用其[风险策略](howto-identity-protection-configure-risk-policies.md)来启用自动修正。 组织应尝试关闭在组织能够接受的时间段内出现的所有风险检测。 Microsoft 建议尽快关闭事件，因为在处理风险时，时间很重要。

## <a name="remediation"></a>补救

计算称作“用户风险级别”的值时，会考虑到所有活动风险检测。 用户风险级别是反映帐户泄露可能性的一个指标（低、中、高）。 管理员希望能够关闭所有风险检测，以便受影响的用户不再面临风险。

某些风险检测可能会被身份保护标记为“已关闭（系统）”，因为这些事件不再被确定为具有风险。

管理员可以使用以下选项进行修正：

- 利用风险策略自行修正
- 手动重置密码
- 消除用户风险
- 手动关闭单个风险检测

### <a name="self-remediation-with-risk-policy"></a>利用风险策略自行修正

如果允许用户自行修正，且风险策略中有 Azure AD 多重身份验证 (MFA) 和自助式密码重置 (SSPR)，则可以在检测到风险时对这些用户解除阻止。 然后，这些检测将被视为已解决。 用户必须已注册 Azure AD MFA 和 SSPR，才能在检测到风险时使用。

某些检测可能不会将风险提高到用户需要进行自我修正的级别，但管理员仍应评估这些检测。 管理员可以确定是否需要执行其他措施，如[阻止来自位置的访问](../conditional-access/howto-conditional-access-policy-location.md)，或降低其策略中可接受的风险。

### <a name="manual-password-reset"></a>手动重置密码

如果无法要求使用用户风险策略重置密码，可以使用手动密码重置关闭用户的所有风险检测。

为用户重置密码时，管理员有两个选项：

- **生成临时密码** - 生成临时密码可以立即让标识恢复安全状态。 此方法需要联系受影响的用户，因为这些用户需要知道临时密码。 由于密码是临时的，因此，在下一次登录期间，系统会提示用户将密码更改为新密码。

- **要求用户重置密码** - 要求用户重置密码可以实现自助恢复，而无需联系支持人员或管理员。 此方法仅适用于注册 Azure AD MFA 和 SSPR 的用户。 对于尚未注册的用户，此选项不可用。

### <a name="dismiss-user-risk"></a>消除用户风险

如果由于用户已被删除而无法进行密码重置，那么可以选择取消用户风险检测。

单击“消除用户风险”时，所有事件都会关闭，受影响的用户不再有风险。 但是，此方法不会对现有密码产生影响，因为它不能将相关的标识恢复安全状态。 

### <a name="close-individual-risk-detections-manually"></a>手动关闭单个风险检测

可以手动关闭单个风险检测。 手动关闭风险检测可以降低用户风险级别。 通常，在响应相关调查时需手动关闭风险检测。 例如，在与用户沟通时发现不再需要某个活动风险检测。 
 
手动关闭风险检测时，可以选择执行以下任一操作来更改风险检测的状态：

- 确认用户是否遭到入侵
- 消除用户风险
- 确认登录是否安全
- 确认登录是否遭到入侵

## <a name="unblocking-users"></a>对用户取消阻止

管理员可以选择基于其风险策略或调查阻止登录。 可能会基于登录或用户风险进行阻止。

### <a name="unblocking-based-on-user-risk"></a>基于用户风险取消阻止

若要取消因用户风险而被阻止的帐号，管理员可选择以下选项：

1. **重置密码** - 可以重置用户的密码。
1. **消除用户风险** - 如果已达到配置的阻止访问的用户风险级别，用户风险策略将会阻止用户。 可以通过消除用户风险或手动关闭报告的风险检测来降低用户的风险级别。
1. **从策略中排除用户** -如果你认为登录策略的当前配置导致特定用户出现问题，则可以从策略中排除用户。 有关详细信息，请参阅[如何配置和启用风险策略](howto-identity-protection-configure-risk-policies.md#exclusions)一文中的“排除项”部分。
1. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 有关详细信息，请参阅[如何配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)一文。

### <a name="unblocking-based-on-sign-in-risk"></a>基于登录风险取消阻止

若要基于登录风险对帐户取消阻止，管理员可以使用以下选项：

1. 从熟悉的位置或设备登录 - 已阻止可疑登录的常见原因是尝试从不熟悉的位置或设备登录。 用户可通过尝试从熟悉的位置或设备登录来快速确定这是否是阻止原因。
1. **从策略中排除用户** -如果你认为登录策略的当前配置导致特定用户出现问题，则可以从策略中排除用户。 有关详细信息，请参阅[如何配置和启用风险策略](howto-identity-protection-configure-risk-policies.md#exclusions)一文中的“排除项”部分。
1. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 有关详细信息，请参阅[如何配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)一文。

## <a name="powershell-preview"></a>PowerShell 预览

通过 Microsoft Graph PowerShell SDK 预览模块，组织可以使用 PowerShell 来管理风险。 可在 [Azure AD GitHub 存储库](https://github.com/AzureAD/IdentityProtectionTools)中找到预览模块和示例代码。 

存储库中包含的 `Invoke-AzureADIPDismissRiskyUser.ps1` 脚本使组织可以在其目录中消除所有有风险的用户。

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview-identity-protection.md)。
