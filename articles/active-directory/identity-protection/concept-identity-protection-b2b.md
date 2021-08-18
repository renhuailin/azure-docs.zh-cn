---
title: 身份保护和 B2B 用户 - Azure Active Directory
description: 对 B2B 用户使用标识保护
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/03/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1d275c4fe093992f1bf87f7d4b8c5965e448a41
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737294"
---
# <a name="identity-protection-and-b2b-users"></a>标识保护和 B2B 用户

标识保护检测到 Azure AD 用户的凭据已泄露。 如果检测到你的凭据已泄露，则表示其他人可能拥有你的密码，并且正在非法使用它。 若要防止帐户面临进一步的风险，请务必安全地重置密码，使不良参与者无法再使用你已泄露的密码。 标识保护会将可能被泄露的帐户标记为“存在风险”。

你可以使用组织凭据以来宾身份登录到其他组织。 这个过程称为[企业到企业或 B2B 协作](../external-identities/what-is-b2b.md)。 组织可以配置策略，以便在用户的凭据[存在风险](concept-identity-protection-risks.md)时阻止用户登录。 如果你的帐户存在风险，且被阻止以来宾身份登录到另一个组织，则可以使用以下步骤自行修正你的帐户。 如果你的组织尚未启用自助式密码重置，则管理员将需要手动修正你的帐户。

## <a name="how-to-unblock-your-account"></a>如何取消阻止帐户 

如果尝试以来宾身份登录到另一个组织，但因风险而被阻止登录，你将看到以下阻止消息：“你的帐户已被阻止。 我们在你的帐户中检测到可疑活动。” 

![已阻止来宾帐户，请与组织的管理员联系](./media/concept-identity-protection-b2b/risky-guest-user-blocked.png)

如果你的组织启用了此功能，则可以使用自助式密码重置取消阻止帐户，并将凭据恢复为安全状态。
1. 转到[密码重置门户](https://passwordreset.microsoftonline.com/)，并启动密码重置。 如果没有为你的帐户启用自助式密码重置，并且无法继续操作，请联系你的 IT 管理员，并提供[以下](#how-to-remediate-a-users-risk-as-an-administrator)信息。
2. 如果为你的帐户启用了自助式密码重置，则在更改密码之前，系统将提示你使用安全方法验证你的标识。 有关帮助信息，请参阅[重置工作或学校帐户密码](../user-help/active-directory-passwords-update-your-own-password.md)一文。
3. 成功并安全地重置密码后，你的用户风险将得以修正。 你现在能够与以来宾用户身份重试登录。

如果重置密码后，你仍因风险而被阻止作为来宾登录，请联系你组织的 IT 管理员。

## <a name="how-to-remediate-a-users-risk-as-an-administrator"></a>如何以管理员身份修正用户的风险

标识保护会自动检测 Azure AD 租户的风险用户。 如果你之前未检查过标识保护报告，则可能存在大量存在风险的用户。 由于资源租户可以向来宾用户应用用户风险策略，因此你的用户可能会因为风险而被阻止（即使他们之前不知道他们存在风险的状态）。 如果用户报告他们在另一个租户中作为来宾用户登录因风险而被阻止，请务必修正用户以保护其帐户并实现协作。 

### <a name="reset-the-users-password"></a>重置用户密码

在“Azure AD 安全”菜单的[风险用户报表](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers)中，使用“用户”筛选器搜索受影响的用户。 在报表中选择受影响的用户，然后单击顶部工具栏中的“重置密码”。 用户将分配到一个临时密码，该密码必须在下一次登录时进行更改。 此过程将修正其用户风险，并将其凭据恢复为安全状态。

### <a name="manually-dismiss-users-risk"></a>手动消除用户的风险

如果不在 Azure AD 门户中选择“密码重置”，则可以选择手动消除用户风险。 此过程可让用户不再处于风险之中，但对现有密码没有任何影响。 务必要使用任何可用的方法更改用户密码，才能将标识恢复到安全状态。 

若要消除用户风险，请在“Azure AD 安全”菜单中转到[风险用户报表](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers)。 使用“用户”筛选器搜索受影响的用户，然后单击该用户。 单击顶部工具栏中的“消除用户风险”选项。 此操作可能需要几分钟才能完成和更新报表中的用户风险状态。

若要了解有关标识保护的详细信息，请参阅[什么是标识保护](overview-identity-protection.md)。

## <a name="how-does-identity-protection-work-for-b2b-users"></a>标识保护如何为 B2B 用户工作？

B2B 协作用户的用户风险在其主目录中进行评估。 用户尝试访问资源时，会在资源目录中评估这些用户的实时登录风险。 通过 Azure AD B2B 协作，组织可以为使用身份保护的 B2B 用户实施基于风险的策略。 这些策略有两种配置方式：

- 管理员可以配置内置身份保护的基于风险的策略，适用于所有应用，包括来宾用户。
- 管理员可以配置其条件访问策略使用登录风险作为条件，包括来宾用户。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 协作用户的身份保护限制

由于 B2B 协作用户的身份存在于其主目录中，因此在对资源目录中的 B2B 协作用户实施身份保护时存在一定的限制。 主要限制如下：

- 如果来宾用户触发身份保护用户风险策略来强制重置密码，则“将阻止用户访问”。 阻止的原因是无法在资源目录中重置密码。
- “来宾用户不会出现在风险用户报表中”。 这种限制是因为 B2B 用户主目录中发生的风险评估。
- 管理员无法在其资源目录中 **消除或修正有风险的 B2B 协作用户**。 这种限制是因为资源目录中的管理员无法访问 B2B 用户的主目录。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>为什么无法在我的目录中修正有风险的 B2B 协作用户？

B2B 用户的风险评估和修正发生在其主目录中。 因此，来宾用户不会显示于资源目录中的风险用户报表，并且资源目录中的管理员不能强制用户进行安全密码重置。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 协作用户由于组织中基于风险的策略而被阻止，我该怎么办？

如果基于风险的策略阻止了目录中有风险的 B2B 用户，则用户需要在其主目录中修正此风险。 用户可以通过在其主目录中执行安全的密码重置来修正其风险，[如上文所述](#how-to-unblock-your-account)。 如果用户的主目录中没有启用自助式密码重置，则用户需要联系自己组织的 IT 人员，请求管理员手动消除风险或重置密码。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何防止 B2B 协作用户受到基于风险的策略的影响？

从组织基于风险的条件访问策略中排除 B2B 用户，能够防止 B2B 用户受风险评估的影响或被阻止。 若要排除这些 B2B 用户，请在 Azure AD 中创建一个包含组织的所有来宾用户的组。 然后，将此组添加为内置标识保护用户风险和登录风险策略的排除项，以及使用登录风险作为条件的所有条件访问策略的排除项。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](../external-identities/what-is-b2b.md)
