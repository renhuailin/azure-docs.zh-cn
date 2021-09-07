---
title: 在 PIM 中配置 Azure AD 角色设置 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中配置 Azure AD 角色设置。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/25/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 558f8b720992f44a747689cb0e69436507b685c4
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866992"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>在 Privileged Identity Management 中配置 Azure AD 角色设置

特权角色管理员可以在其 Azure Active Directory (Azure AD) 组织中自定义 Privileged Identity Management (PIM)，包括更改激活合格角色分配的用户的体验。

## <a name="open-role-settings"></a>打开角色设置

遵循以下步骤打开 Azure AD 角色的设置。

1. 使用具有[特权角色管理员](../roles/permissions-reference.md#privileged-role-administrator)角色的用户登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”&gt;“Azure AD 角色”&gt;“角色设置”。

    ![列出 Azure AD 角色的“角色设置”页](./media/pim-how-to-change-default-settings/role-settings.png)

1. 选择要配置其设置的角色。

    ![列出多个分配和激活设置的“角色设置详细信息”页](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. 选择“编辑”打开“角色设置”页。

    ![“编辑角色设置”页，其中包含用于更新分配和激活设置的选项](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    在每个角色的“角色设置”窗格上，有多个可以配置的设置。

## <a name="assignment-duration"></a>分配持续时间

配置角色的设置时，可以从用于每种分配类型（合格和活动）的两个分配持续时间选项中进行选择·。 在 Privileged Identity Management 中将用户分配到角色时，这些选项将成为默认的最大持续时间。

可以选择其中一个合格的  分配持续时间选项：

| 设置 | 说明 |
| --- | --- |
| 允许永久的合格分配 | 全局管理员和特权角色管理员可以分配永久的合格分配。 |
| 使合格分配在以下时间后过期 | 全局管理员和特权角色管理员可以要求所有合格分配都具有指定的开始和结束日期。 |

并且，可以选择其中一个活动  分配持续时间选项：

| 设置 | 说明 |
| --- | --- |
| 允许永久的活动分配 | 全局管理员和特权角色管理员可以分配永久的活动分配。 |
| 使活动分配在以下时间后过期 | 全局管理员和特权角色管理员可以要求所有活动分配都具有指定的开始和结束日期。 |

> [!NOTE]
> 全局管理员和特权角色管理员可续订具有特定结束日期的所有分配。 此外，用户也可启动自助服务请求来[扩展或续订角色分配](pim-resource-roles-renew-extend.md)。

## <a name="require-multifactor-authentication"></a>要求多重身份验证

Privileged Identity Management 提供针对激活以及针对活动分配强制执行 Azure AD 多重身份验证的功能。

### <a name="on-activation"></a>针对激活

可以要求符合角色条件的用户通过 Azure AD 多重身份验证来证明其身份，然后才允许其激活。 多重身份验证可以在相当程度上确保用户是本人。 强制执行此选项可以在用户帐户可能已遭入侵的情况下保护关键资源。

如果要求使用多重身份验证来激活角色分配，请在“编辑角色设置”的“激活”选项卡中选择“激活时，需要 Azure MFA”选项 。

### <a name="on-active-assignment"></a>针对活动分配

在某些情况下，你可能希望为用户分配短期（例如，一天）角色。 在这种情况下，分配的成员不需要请求激活。 在这种情况下，Privileged Identity Management 无法在用户使用其角色分配时强制实施多重身份验证，因为从分配角色时起，用户就已经在角色中处于活动状态。

若要在分配处于活动状态时要求多重身份验证，请在“编辑角色设置”的“分配”选项卡中选择“激活分配时必须进行 Azure 多重身份验证”选项 。

有关详细信息，请参阅[多重身份验证和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>最长激活持续时间

使用“最长激活持续时间”滑块是角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可以是 1 到 24 个小时。

## <a name="require-justification"></a>需要理由

你可以要求用户在激活时输入业务理由。 若需要理由，请选中“在活动分配时需要理由”  框或“在激活时需要理由”  框。

## <a name="require-approval-to-activate"></a>需要批准才能激活

如果设置多个审批者，则审批会在其中一项批准或拒绝后立即完成。 不能强制由第二个或后续审批者进行批准。 若要求批准以激活角色，请按照以下步骤操作。

1. 选中“需要批准以激活”复选框。

1. 选择“选择审批者”。

    ![用于选择审批者的“选择用户或组”窗格](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 至少选择一个用户，然后单击“选择”。 请至少选择一个审批者。 如果未选择任何特定审批者，则特权角色管理员和全局管理员将成为默认审批者。

1. 选择“更新”以保存更改。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中为 Azure AD 角色配置安全警报](pim-how-to-configure-security-alerts.md)
