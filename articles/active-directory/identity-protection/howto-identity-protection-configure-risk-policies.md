---
title: 风险策略 - Azure Active Directory 标识保护
description: 在 Azure Active Directory 标识保护中启用和配置风险策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 05/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11751323d1341cbcde19451bc101197c7d714368
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739980"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>操作说明：配置和启用风险策略

正如前一篇文章[标识保护策略](concept-identity-protection-policies.md)中所述，有两种风险策略可以在目录中启用。 

- 登录风险策略
- 用户风险策略

![启用用户和登录风险策略的安全“概述”页](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

这两个策略都可用于自动响应环境中的风险检测，让用户可以在检测到风险时自行修正。 

## <a name="choosing-acceptable-risk-levels"></a>选择可接受的风险级别

组织必须决定他们愿意接受的风险级别，以平衡用户体验和安全态势。 

Microsoft 建议将用户风险策略阈值设置为“高”，并将登录风险策略设置为“中等以上”并允许自我修正选项。 选择阻止访问，而不是允许自我修正选项（如密码更改和多重身份验证）会影响用户和管理员。 配置策略时，请权衡此选择。

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。 但它会从策略中排除标记为“低”和“中等”的风险检测，这样就无法阻止攻击者利用遭到泄露的标识。 选择“低”阈值会引入更多的用户中断。

在某些风险检测中，标识保护使用已配置的受信任[网络位置](../conditional-access/location-condition.md)来减少假正。

### <a name="risk-remediation"></a>风险修正

组织可以选择在检测到风险时阻止访问。 阻止访问有时会妨碍合法用户执行所需的操作。 更好的解决方案是允许使用 Azure AD 多重身份验证 (MFA) 和自助式密码重置 (SSPR) 进行自我修正。 

- 触发用户风险策略时： 
   - 管理员可以要求进行安全密码重置，也就是要求在用户使用 SSPR 创建新密码之前完成 Azure AD MFA，从而消除用户风险。 
- 触发登录风险策略时： 
   - 可能会触发 Azure AD MFA，允许用户通过使用他们注册的一种身份验证方法来验明自己的身份，从而消除登录风险。 

> [!WARNING]
> 用户必须注册 Azure AD MFA 和 SSPR 才会遇到需要修正的情况。 未注册的用户将被阻止，需要管理员干预。
> 
> 危险用户策略修正流程之外的密码更改（我知道我的密码并希望将其更改为新密码）不符合安全密码重置的要求。

## <a name="exclusions"></a>排除项

策略允许排除部分用户（如[应急访问或紧急情况管理员帐户](../roles/security-emergency-access.md)）。 组织可能会根据帐户的使用方式而需要将其他帐户排除在特定策略之外。 应定期检查排除项，以确定它们是否仍适用。

## <a name="enable-policies"></a>启用策略

可以在两个位置配置这些策略：条件访问和标识保护。 首选方法是采用条件访问策略的配置，可提供更多上下文，包括： 

   - 增强的诊断数据
   - 仅报告模式集成
   - 图形 API 支持
   - 在策略中使用更多条件访问属性

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

### <a name="user-risk-with-conditional-access"></a>采用条件性访问时的用户风险

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
1. 在“条件” > “用户风险”下，将“配置”设置为“是”。 在“配置强制执行策略所需的用户风险级别”下，选择“高”，然后选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要更改密码”和“选择”。
1. 确认设置并将“启用策略”设置为“打开”。
1. 选择“创建”  ，以便创建启用策略所需的项目。

### <a name="sign-in-risk-with-conditional-access"></a>采用条件性访问时的登录风险

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
1. 在“条件” > “登录风险”下，将“配置”设置为“是”   。 在“选择适用于此策略的登录风险级别”下 
   1. 选“高”和“中等” 。
   1. 选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“授予访问权限”、“需要多重身份验证”、“选择”。  
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

- [启用 Azure AD 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [什么是风险](concept-identity-protection-risks.md)

- [调查风险检测](howto-identity-protection-investigate-risk.md)

- [模拟风险检测](howto-identity-protection-simulate-risk.md)
