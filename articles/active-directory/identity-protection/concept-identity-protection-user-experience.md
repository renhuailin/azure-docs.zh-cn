---
title: Azure AD 标识保护中的用户体验
description: Azure AD 标识保护的用户体验
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 484136b1c01cf93515971a42030eacfdda51715e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784159"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD 标识保护中的用户体验

使用 Azure Active Directory 标识保护时，可以：

* 要求用户注册 Azure AD 多重身份验证 (MFA)
* 自动修正有风险的登录和遭到入侵的用户

所有标识保护策略都会影响用户的登录体验。 允许用户注册并使用 Azure AD MFA 和自助式密码重置等工具可减轻影响。 这些工具以及相应的策略选择在用户需要时为其提供自我修正选项。

## <a name="multi-factor-authentication-registration"></a>多重身份验证注册

启用需要注册多重身份验证并面向所有用户的标识保护策略，将确保他们能够在以后使用 Azure AD MFA 进行自我修正。 配置此策略后，用户可选择在 14 天的期限内进行注册，也可在最后强制用户进行注册。 用户体验如下所述。 有关详细信息，请参阅文章中的最终用户文档：[双因素验证概述以及工作或学校帐户](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)。

### <a name="registration-interrupt"></a>注册中断

1. 登录到任何 Azure AD 集成的应用程序时，用户会收到有关要求设置用于多重身份验证的帐户的通知。 使用新设备的新用户在 Windows 10 全新安全体验中也会触发此策略。
   
    ![需要更多信息](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 完成指导步骤注册 Azure AD 多重身份验证并完成登录。

## <a name="risky-sign-in-remediation"></a>有风险的登录修正

管理员针对登录风险配置策略后，受影响的用户在尝试登录时将收到通知并触发策略风险级别。 

### <a name="risky-sign-in-self-remediation"></a>有风险的登录自我修正

1. 通知用户在其登录时检测到异常情况，例如从新的位置、设备或应用登录。
   
    ![出现异常提示](./media/concept-identity-protection-user-experience/120.png)

1. 用户需要通过使用之前注册的一种方法完成 Azure AD MFA 来证明其身份。 

### <a name="risky-sign-in-administrator-unblock"></a>有风险的登录管理员取消阻止

管理员可选择根据风险级别阻止用户登录。 若要取消阻止，最终用户必须联系 IT 人员，或者可以尝试从熟悉的位置或设备登录。 在此情况下，无法通过执行多重身份验证来自我修正。

![已被登录风险策略阻止](./media/concept-identity-protection-user-experience/200.png)

IT 人员可以按照[取消阻止用户](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk)部分中的说明操作以允许用户重新登录。

## <a name="risky-user-remediation"></a>有风险的用户修正

配置用户风险策略后，符合入侵的用户风险级别概率的用户必须完成用户入侵恢复流程才能登录。 

### <a name="risky-user-self-remediation"></a>有风险的用户自我修正

1. 通知用户，由于出现可疑活动或凭据泄露，其帐户的安全性面临风险。
   
    ![补救](./media/concept-identity-protection-user-experience/101.png)

1. 用户需要通过使用之前注册的一种方法完成 Azure AD MFA 来证明其身份。 
1. 最后，用户必须使用自助式密码重置更改其密码，因为其他人可能已获取了其帐户的访问权限。

## <a name="risky-sign-in-administrator-unblock"></a>有风险的登录管理员取消阻止

管理员可选择根据风险级别阻止用户登录。 若要取消阻止，最终用户必须联系 IT 人员。 在此情况下，无法通过执行多重身份验证和自助式密码重置来自我修正。

![被用户风险策略阻止](./media/concept-identity-protection-user-experience/104.png)

IT 人员可以按照[取消阻止用户](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk)部分中的说明操作以允许用户重新登录。

## <a name="see-also"></a>另请参阅

- [修正风险并对用户解除阻止](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory 标识保护](./overview-identity-protection.md)