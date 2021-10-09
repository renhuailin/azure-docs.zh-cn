---
title: 在 PIM 中配置 Azure 特权访问组设置 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中配置可分配角色的组设置。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/14/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c47530e3fd7626674297a2d910ff9c39722c228b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650831"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中配置特权访问组设置（预览）

角色设置是应用于 Privileged Identity Management (PIM) 中的组所有者和组成员的特权访问分配的默认设置。 使用以下步骤设置审批工作流，以指定谁可以批准或拒绝提升特权的请求。

## <a name="open-role-settings"></a>打开角色设置

遵循以下步骤打开 Azure 特权访问组角色的设置。

1. 以[全局管理员](../roles/permissions-reference.md#global-administrator)角色或者被指定为组所有者的角色登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“特权访问组(预览)”。

1. 选择要管理的组。

    ![按组名称筛选的特权访问组](./media/groups-role-settings/group-select.png)

1. 选择“设置”。

    ![“设置”页列出所选组的组设置](./media/groups-role-settings/group-settings-select-role.png)

1. 选择要查看或更改其设置的所有者或成员角色。 你可以在“角色设置详细信息”页中查看角色的当前设置。

    ![列出多个分配和激活设置的“角色设置详细信息”页](./media/groups-role-settings/group-role-setting-details.png)

1. 选择“编辑”，打开“编辑角色设置”页 。 “激活”选项卡允许你更改角色激活设置，包括是否允许永久有效作业。

    ![“编辑角色设置”页，其中的“激活”选项卡已打开](./media/groups-role-settings/role-settings-activation-tab.png)

1. 选择“分配”选项卡打开分配设置选项卡。这些设置控制此角色的 Privileged Identity Management 分配设置。

    ![角色设置页中的角色分配表](./media/groups-role-settings/role-settings-assignment-tab.png)

1. 使用“通知”选项卡或页面底部的  “下一步:  激活”按钮即可转到此角色的通知设置选项卡。 这些设置控制与此角色相关的所有电子邮件通知。

    ![角色设置页中的角色“通知”选项卡](./media/groups-role-settings/role-settings-notification-tab.png)

1. 随时选择“更新”按钮，对角色设置进行更新。

在角色设置页上的“通知”选项卡上，Privileged Identity Management 允许对接收通知的人员及其收到的通知进行精细控制。

- **关闭电子邮件**<br>可以通过清除“默认收件人”复选框并删除任何其他收件人来关闭特定电子邮件。  
- **将电子邮件限制为指定的电子邮件地址**<br>可以通过清除“默认收件人”复选框来关闭发送给默认收件人的电子邮件。 然后，可以添加其他电子邮件地址作为其他收件人。 如果要添加多个电子邮件地址，请使用分号 (;) 分隔它们。
- **向默认收件人和其他收件人发送电子邮件**<br>可以通过选择“默认收件人”复选框并添加其他收件人的电子邮件地址，将电子邮件发送给默认收件人和其他收件人。
- **仅限关键电子邮件**<br>对于每种类型的电子邮件，可以选择该复选框以仅接收关键电子邮件。 这意味着，仅当电子邮件需要即时操作时，Privileged Identity Management 才会继续向配置的收件人发送电子邮件。 例如，将要触发要求管理员批准扩展请求的电子邮件时，不会触发要求用户扩展其角色分配的电子邮件。

## <a name="assignment-duration"></a>分配持续时间

配置角色的设置时，可以从用于每种分配类型（合格和活动）的两个分配持续时间选项中进行选择·。 在 Privileged Identity Management 中将用户分配到角色时，这些选项将成为默认的最大持续时间。

可以选择其中一个合格的  分配持续时间选项：

| | 说明 |
| --- | --- |
| **允许永久的合格分配** | 资源管理员可以分配永久的合格分配。 |
| **使合格分配在以下时间后过期** | 资源管理员可以要求所有合格分配都具有指定的开始和结束日期。 |

并且，可以选择其中一个活动  分配持续时间选项：

| | 说明 |
| --- | --- |
| **允许永久的活动分配** | 资源管理员可以分配永久的活动分配。 |
| **使活动分配在以下时间后过期** | 资源管理员可以要求所有活动分配都具有指定的开始和结束日期。 |

> [!NOTE]
> 资源管理员可续订具有特定结束日期的所有分配。 此外，用户也可启动自助服务请求来[扩展或续订角色分配](pim-resource-roles-renew-extend.md)。

## <a name="require-multifactor-authentication"></a>要求多重身份验证

Privileged Identity Management 提供了两种不同的可选 Azure AD 多重身份验证强制执行方案。

### <a name="require-multifactor-authentication-on-active-assignment"></a>要求在活动分配时进行多重身份验证

此选项要求管理员必须在创建活动角色分配（而不是合格角色分配）之前完成多重身份验证。 Privileged Identity Management 无法在用户使用其角色分配时强制实施多重身份验证，因为从分配角色时起，用户就已经在角色中处于活动状态。

若要在创建活动角色分配时要求多重身份验证，请选中“要求在活动分配时进行多重身份验证”复选框。

### <a name="require-multifactor-authentication-on-activation"></a>要求在激活时进行多重身份验证

可以要求符合角色条件的用户通过 Azure AD 多重身份验证来证明其身份，然后才允许其激活。 多重身份验证可以在相当程度上确保用户是本人。 强制执行此选项可以在用户帐户可能已遭入侵的情况下保护关键资源。

若要在激活前要求进行多重身份验证，请选中“要求在激活时进行多重身份验证”框。

有关详细信息，请参阅[多重身份验证和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>最长激活持续时间

使用“最长激活持续时间”滑块是角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可以是 1 到 24 个小时。

## <a name="require-justification"></a>需要理由

你可以要求用户在激活时输入业务理由。 若需要理由，请选中“在活动分配时需要理由”  框或“在激活时需要理由”  框。

## <a name="require-approval-to-activate"></a>需要批准才能激活

如果要求批准以激活角色，请按照以下步骤操作。

1. 选中“需要批准以激活”复选框。

1. 选择“选择审批者”打开“选择成员或组”页   。

    ![用于选择审批者的“选择用户或组”窗格](./media/groups-role-settings/group-settings-select-approvers.png)

1. 至少选择一个用户或组，然后单击“选择”  。 可以添加任何用户和组的组合。 必须至少选择 1 个审批者。 没有默认的审批者。

    所选项将出现在所选审批者列表中。

1. 在指定所有角色设置后，选择“更新”  以保存更改。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配特权访问组成员资格或所有权](groups-assign-member-owner.md)
