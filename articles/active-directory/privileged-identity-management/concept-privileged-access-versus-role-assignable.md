---
title: 特权访问组和可分配角色的组之间有何差异 - Azure AD | Microsoft Docs
description: 了解如何区分 Azure AD Privileged Identity Management (PIM) 中的特权访问组和可分配角色的组。
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
ms.date: 07/02/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5f9d968c114c28dcff8e247435c40960040c4e9
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396778"
---
# <a name="whats-the-difference-between-privileged-access-groups-and-role-assignable-groups"></a>特权访问组和可分配角色的组之间有何差异？

Privileged Identity Management (PIM) 支持在可分配角色的组上启用特权访问。 但是，由于创建特权访问组的先决条件是有可用的可分配角色，因此本文将介绍二者之间的差异及如何利用这些差异。

## <a name="what-are-azure-ad-role-assignable-groups"></a>什么是 Azure AD 可分配角色的组？

Azure AD 允许将云 Azure AD 安全组分配到 Azure AD 角色。 全局管理员和特权角色管理员必须创建新的安全组，并在创建时使该组的角色可分配。 仅全局管理员、特权角色管理员或组所有者角色中的用户可以更改该组内的成员身份。 此外，其他任何用户均不可重置该组内成员用户的密码。 此功能有助于防止管理员在未经请求和审批的情况下提升为更高特权的角色。

## <a name="what-are-privileged-access-groups"></a>什么是特权访问组？

特权访问组让用户能够提升为 Azure AD 安全组的所有者或成员角色。 通过此功能不仅可以为 Azure AD 和 Azure 角色批量设置实时工作流，还可以为 Azure SQL、Azure Key Vault、Intune 或其他应用程序角色等用例启用实时方案。 有关详细信息，请参阅[特权访问组的管理功能](groups-features.md)。

>[!Note]
>对于那些用于提升为 Azure AD 角色的特权访问组，Microsoft 建议你要求对符合条件的成员的分配执行审批流程。 如果分配未经批准即可激活，你可能容易遭受特权较低的管理员带来的安全风险。 例如，支持管理员有权重置符合条件的用户的密码。

## <a name="when-to-use-each-type-of-group"></a>何时使用每个类型的组

你可以设置对 Azure AD 和 Azure 资源以外的权限和角色的实时访问。 如果有其他资源的授权可以连接到某个 Azure AD 安全组（针对 Azure Key Vault、Intune、Azure SQL 或其他应用和服务），则应启用对该组的特权访问权限，并将用户分配为符合组内成员条件的角色。

如果要将组分配到 Azure AD 角色或 Azure 资源角色，并且需要通过 PIM 过程提升权限，可通过两种方法实现：

- 将组永久分配到一个角色。 然后在 PIM 中授予用户对组内符合条件成员的访问权限。 而且，符合条件的用户须激活各自的成员身份，才能进入永久分配给该角色的组。 此路径要求在 PIM 中启用可分配角色的组，并将该组作为特权访问组分配给 Azure AD 角色。
- 通过 PIM 分配该组，使其符合成为某个角色的条件。 组内每个成员均须激活各自的角色分配，才能获得对相应角色的访问权限。 此路径要求将可分配角色的组分配给 Azure AD 角色及将安全组分配给 Azure 资源。

    ![此图显示了在 PIM 中使用特权访问组分配角色的两种方法。](./media/concept-privileged-access-versus-role-assignable/concept-privileged-access.png)

这些方法均适用于端到端方案。 但我们建议在大多数情况下使用第一种方法。 仅在尝试执行以下操作时，才应使用第二种方法：

- 为多个 Azure AD 或 Azure 资源角色分配一个组，并让用户激活一次来访问多个角色。
- 为不同用户组保留访问 Azure AD 或 Azure 资源角色所需的不同激活策略。 例如，若要在某些用户成为全局管理员之前审批这些用户，同时允许自动审批其他用户，则可以设置两个特权访问组并将它们永久（Privileged Identity Management 中的“永久”分配）分配到全局管理员角色，然后为每组成员角色使用不同的激活策略。

## <a name="next-steps"></a>后续步骤

- [批准或拒绝 Azure AD 角色的请求](azure-ad-pim-approval-workflow.md)
- [批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [批准特权访问组成员和所有者的激活请求（预览）](groups-approval-workflow.md)
