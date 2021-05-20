---
title: 配置 MFA 注册策略 - Azure Active Directory 标识保护
description: 了解如何配置“Azure AD 标识保护”多重身份验证注册策略。
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
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835860"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>如何：配置 Azure AD 多重身份验证注册策略

Azure AD 标识保护帮助你管理 Azure AD 多重身份验证 (MFA) 注册的实施，方法是：将条件访问策略配置为，无论你登录到哪个新式身份验证应用，都需要注册 MFA。

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>什么是 Azure AD 多重身份验证注册策略？

Azure AD 多重身份验证提供了一种不仅仅是验证用户名和密码的方法来验证用户的身份。 它为用户登录提供了附加的安全层。为了使用户能够响应 MFA 提示，他们必须首先注册 Azure AD 多重身份验证。

建议要求对用户登录执行 Azure AD 多重身份验证，因为这种身份验证方法可以：

- 通过一系列验证选项提供强身份验证。
- 在使组织做好准备以从“标识保护”的风险检测中自我修正方面起关键作用。

有关 Azure AD 多重身份验证的详细信息，请参阅[什么是 Azure AD 多重身份验证？](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>策略配置

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory” > “安全” > “标识保护” > “MFA 注册策略”。
   1. 在“分配”下
      1. 用户 - 选择“所有用户”或“选择个人和组”（如果你限制推出的话）。
         1. （可选）可以选择从策略中排除用户。
   1. 在“控件”下
      1. 请确保选中“需要 Azure AD MFA 注册”复选框，然后选择“选择”。
   1. “强制执行策略” - “开”
   1. **保存**

## <a name="user-experience"></a>用户体验

Azure Active Directory 标识保护将在用户下次以交互方式登录时提示他们进行注册，并且他们将有 14 天的时间完成注册。 在此 14 天内，他们可以绕过注册，但在此期间结束时，他们将需要注册，然后才能完成登录过程。

如需相关用户体验的概述，请参阅：

- [Azure AD 标识保护中的登录体验](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>后续步骤

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)

- [启用 Azure AD 自助式密码重置](../authentication/howto-sspr-deployment.md)

- [启用 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)
