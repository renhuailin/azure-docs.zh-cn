---
title: Azure AD 角色的最佳做法 - Azure Active Directory
description: 使用 Azure Active Directory 角色的最佳做法。
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48d424a64df215a7506130a44d57fc45d638255d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739944"
---
# <a name="best-practices-for-azure-ad-roles"></a>Azure AD 角色的最佳做法

本文介绍了使用 Azure Active Directory 基于角色的访问控制 (Azure AD RBAC) 的一些最佳做法。 这些最佳做法源自我们的 Azure AD RBAC 经验和客户经验。 建议同时阅读[确保 Azure AD 中混合部署和云部署的特权访问安全性](security-planning.md)中的详细安全指南。

## <a name="1-manage-to-least-privilege"></a>1. 争取最小特权

规划访问控制策略时，最佳做法是争取最小特权。 最小特权是指向管理员授予恰好完成其工作所需的权限。 向管理员分配角色时，需要考虑三个方面：一组特定的权限、在特定的范围内、在特定的时间段内。 即使最初看起来更方便操作，也应避免在更广泛的范围内分配更广泛的角色。 通过限制角色和范围，可以对在安全主体受到入侵的情况下会面临风险的具体资源进行限制。 Azure AD RBAC 支持超过 65 个[内置角色](permissions-reference.md)。 有用于管理目录对象（例如用户、组和应用程序）的 Azure AD 角色，也有用于管理 Microsoft 365 服务（例如 Exchange、SharePoint 和 Intune）的 Azure AD 角色。 若要更好地了解 Azure AD 内置角色，请参阅[了解 Azure Active Directory 中的角色](concept-understand-roles.md)。 如果没有满足你的需求的内置角色，可以创建自己的[自定义角色](custom-create.md)。  
 
### <a name="finding-the-right-roles"></a>查找适当的角色

遵循以下步骤可帮助你找到适当的角色。

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”以查看 Azure AD 角色。

1. 使用“服务”筛选器缩小角色列表的范围。

    ![Azure AD 中的“角色和管理员”页，其中“服务”筛选器处于打开状态](./media/best-practices/roles-administrators.png)

1. 请参阅 [Azure AD 内置角色](permissions-reference.md)文档。 与每个角色相关联的权限将一起列出，以提高可读性。 若要了解角色权限的结构和含义，请参阅[如何理解角色权限](permissions-reference.md#how-to-understand-role-permissions)。

1. 请参阅[任务的最小特权角色](delegate-by-task.md)文档。

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. 使用 Privileged Identity Management 授予实时访问权限

最小特权原则之一是仅应在特定时间段内授予访问权限。 通过 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 可以向管理员授予实时访问权限。 Microsoft 建议在 Azure AD 中启用 PIM。 使用 PIM，用户可以成为 Azure AD 角色的合格成员，然后在需要时激活该角色一段有限的时间。 该时间范围到期后，将自动删除特权访问。 还可以[配置 PIM 设置](../privileged-identity-management/pim-how-to-change-default-settings.md)以便在用户需要激活其角色分配时要求审批或接收通知电子邮件。 向高特权角色添加新用户时，通知会发出警报。 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. 为所有管理员帐户启用多重身份验证

[根据我们的调查](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)，如果使用多重身份验证 (MFA)，有 99.9% 的概率可避免帐户信息泄露。 
 
可以使用两种方法为 Azure AD 角色启用 MFA：
- Privileged Identity Management 中的[角色设置](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [条件访问](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. 配置重复访问评审，以随时间推移撤销不需要的权限

“访问评审”使组织能够定期评审管理员的访问权限，以确保只有正确的人员才能继续访问。 定期审核管理员至关重要，原因如下：
- 恶意参与者可能会入侵帐户。
- 人员在公司内移动团队。 如果不进行审核，随时间推移，他们可能会进行许多不必要的访问。
 
有关角色的访问评审的信息，请参阅[在 PIM 中创建 Azure AD 角色的访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)。 有关分配了角色的组的访问评审的信息，请参阅[在 Azure AD 访问评审中创建组和应用程序的访问评审](../governance/create-access-review.md)。

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. 将全局管理员的数量限制为少于 5

Microsoft 建议将全局管理员角色分配给组织中五个以下的人员，这是最佳做法。 全局管理员持整个组织范围的权限，因此出于利益考虑有必要将攻击面保持在较低水平。 如前所述，所有这些帐户都应通过多重身份验证进行保护。

默认情况下，当用户注册 Microsoft 云服务时，系统会创建一个 Azure AD 租户，并使该用户成为全局管理员角色的成员。 已分配有全局管理员角色的用户可以读取和修改 Azure AD 组织中的每项管理设置。 除少数例外，全局管理员还可以读取和修改 Microsoft 365 组织中的所有配置设置。 全局管理员还可以提升其对读取数据的访问权限。

Microsoft 建议保留两个永久分配给全局管理员角色的紧急访问帐户。 请确保这些帐户不需要使用与普通管理帐户相同的多重身份验证机制进行登录，如[在 Azure AD 中管理紧急访问帐户](../roles/security-emergency-access.md)中所述。 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. 将组用于 Azure AD 角色分配并委派角色分配

如果你有一个利用组的外部治理系统，应考虑将角色分配给 Azure AD 组，而不是单个用户。 还可以在 PIM 中管理可接受角色分配的组，以确保这些特权组中不存在长期所有者或成员。 有关详细信息，请参阅[特权访问 Azure AD 组的管理功能](../privileged-identity-management/groups-features.md)。

可以将所有者分配给角色可分配的组。 该所有者可决定要添加到组中或从组中删除的人员，因此可间接决定谁将获取角色分配。 通过这种方式，全局管理员或特权角色管理员可以通过使用组按角色委托角色管理。 有关详细信息，请参阅[使用 Azure AD 组来管理角色分配](groups-concept.md)。

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. 使用特权访问组一次性激活多个角色

可能存在这样一种情况，一个人通过 PIM 具有五到六个有效分配的 Azure AD 角色。 他们将不得不单独激活每个角色，这会降低工作效率。 更糟糕的是，他们还可能为这些角色分配数十或数百个 Azure 资源，这会使问题更加严重。
 
在这种情况下，应使用[特权访问组](../privileged-identity-management/groups-features.md)。 创建特权访问组，并向其授予对多个角色（Azure AD 和/或 Azure）的永久访问权限。 使该用户成为此组的合格成员或所有者。 只需一次激活，他们就可访问所有链接的资源。

![显示同时激活多个角色的特权访问组关系图](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. 将云本机帐户用于 Azure AD 角色

避免将本地同步帐户用于 Azure AD 角色分配。 如果本地帐户遭到入侵，则还可能会入侵 Azure AD 资源。

## <a name="next-steps"></a>后续步骤

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](security-planning.md)
