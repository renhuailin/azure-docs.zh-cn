---
title: 身份保护和 B2B 用户 - Azure Active Directory
description: 对 B2B 用户使用身份保护的工作原理和已知限制
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "88949190"
---
# <a name="identity-protection-and-b2b-users"></a>标识保护和 B2B 用户

通过 Azure AD B2B 协作，组织可以为使用身份保护的 B2B 用户实施基于风险的策略。 这些策略有两种配置方式：

- 管理员可以配置内置身份保护的基于风险的策略，适用于所有应用，包括来宾用户。
- 管理员可以配置其条件访问策略使用登录风险作为条件，包括来宾用户。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>如何对 B2B 协作用户评估风险

B2B 协作用户的用户风险在其主目录中进行评估。 用户尝试访问资源时，会在资源目录中评估这些用户的实时登录风险。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 协作用户的身份保护限制

由于 B2B 协作用户的身份存在于其主目录中，因此在对资源目录中的 B2B 协作用户实施身份保护时存在一定的限制。 主要限制如下：

- 如果来宾用户触发身份保护用户风险策略来强制重置密码，则“将阻止用户访问”。 阻止的原因是无法在资源目录中重置密码。
- “来宾用户不会出现在风险用户报表中”。 这种失去可见性的原因在 B2B 用户主目录中发生的风险评估。
- 管理员无法在其资源目录中 **消除或修正有风险的 B2B 协作用户**。 这种功能损失是因为资源目录中的管理员无法访问 B2B 用户的主目录。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>为什么无法在我的目录中修正有风险的 B2B 协作用户？

B2B 用户的风险评估和修正发生在其主目录中。 因此，来宾用户不会显示于资源目录中的风险用户报表，并且资源目录中的管理员不能强制用户进行安全密码重置。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 协作用户由于组织中基于风险的策略而被阻止，我该怎么办？

如果基于风险的策略阻止了目录中有风险的 B2B 用户，则用户需要在其主目录中修正此风险。 用户可以通过在其主目录中执行安全密码重置来修正风险。 如果用户的主目录中没有启用自助式密码重置，则用户需要联系自己组织的 IT 人员，请求管理员手动消除风险或重置密码。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何防止 B2B 协作用户受到基于风险的策略的影响？

从组织基于风险的条件访问策略中排除 B2B 用户，能够防止 B2B 用户受风险评估的影响或被阻止。 若要排除这些 B2B 用户，请在 Azure AD 中创建一个包含组织的所有来宾用户的组。 然后，将此组添加为内置身份保护用户风险和登录风险策略的排除项，并添加为将登录风险作为条件的任何条件访问策略的排除项。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](../external-identities/what-is-b2b.md)