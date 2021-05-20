---
title: 风险策略 - Azure Active Directory 标识保护
description: 在 Azure Active Directory 标识保护中启用和配置风险策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997624"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>操作说明：配置和启用风险策略

正如前一篇文章[标识保护策略](concept-identity-protection-policies.md)中所述，有两种风险策略可以在目录中启用。 

- 登录风险策略
- 用户风险策略

![启用用户和登录风险策略的安全“概述”页](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

这两个策略都可用于自动响应环境中的风险检测，让用户可以在检测到风险时自行修正。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>先决条件 

如果你的组织想让用户在检测到风险时能够自行修正，则用户必须同时注册自助式密码重置和 Azure AD 多重身份验证。 为了获得最佳体验，建议[启用组合式安全信息注册体验](../authentication/howto-registration-mfa-sspr-combined.md)。 允许用户自行修正，可以让他们更快地恢复高效状态，而无需管理员干预。 管理员仍可以看到这些事件并进行事后调查。 

## <a name="choosing-acceptable-risk-levels"></a>选择可接受的风险级别

组织必须决定他们愿意接受的风险级别，以平衡用户体验和安全态势。 

Microsoft 建议将用户风险策略阈值设置为“高”，将登录风险策略设置为“中等及以上”。

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。 但它会从策略中排除标记为“低”和“中等”的风险检测，这样就无法阻止攻击者利用遭到泄露的标识。 选择“低”阈值会引入额外的用户中断，但会改进安全态势。

## <a name="exclusions"></a>排除项

所有策略都允许排除部分用户（如[应急访问或紧急情况管理员帐户](../roles/security-emergency-access.md)）。 组织可能会根据帐户的使用方式确定他们是否需要将其他帐户排除在特定策略之外。 应定期检查所有排除项，查看它们是否仍然适用。

在某些风险检测中，标识保护使用已配置的受信任[网络位置](../conditional-access/location-condition.md)来减少假正。

## <a name="enable-policies"></a>启用策略

若要启用用户风险策略和登录风险策略，请完成以下步骤。

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 依次转到“Azure Active Directory” > “安全性” > “标识保护” > “概述”。
1. 选择“用户风险策略”。
   1. 在“分配”下
      1. 用户 - 选择“所有用户”或“选择个人和组”（如果你限制推出的话）。
         1. （可选）可以选择从策略中排除用户。
      1. “条件” - “用户风险”：Microsoft 建议将此选项设置为“高”。
   1. 在“控制”下
      1. “访问”：Microsoft 建议设置“允许访问”和“需要更改密码”。
   1. “强制执行策略” - “开”
   1. “保存”：此操作将返回到“概述”页。
1. 选择“登录风险策略”。
   1. 在“分配”下
      1. 用户 - 选择“所有用户”或“选择个人和组”（如果你限制推出的话）。
         1. （可选）可以选择从策略中排除用户。
      1. “条件” - “登录风险”：Microsoft 建议将此选项设置为“中等以上”。
   1. 在“控制”下
      1. “访问”：Microsoft 建议设置“允许访问”和“需要多重身份验证”。
   1. “强制执行策略” - “开”
   1. **保存**

## <a name="next-steps"></a>后续步骤

- [启用 Azure AD 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [什么是风险](concept-identity-protection-risks.md)

- [调查风险检测](howto-identity-protection-investigate-risk.md)

- [模拟风险检测](howto-identity-protection-simulate-risk.md)
