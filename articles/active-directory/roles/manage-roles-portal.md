---
title: 向用户分配 Azure AD 角色 - Azure Active Directory
description: 了解如何通过分配 Azure AD 角色向 Azure Active Directory 中的用户授予访问权限。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d62e723f42838343798603517ca430d727ff517
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787828"
---
# <a name="assign-azure-ad-roles-to-users"></a>向用户分配 Azure AD 角色

现在，可在 Azure AD 管理中心查看和管理管理员角色的所有成员。 如果你经常需要管理角色分配，也许更乐意使用此体验。 本文介绍如何使用 Azure AD 管理中心分配 Azure AD 角色。

## <a name="prerequisites"></a>先决条件

- 特权角色管理员或全局管理员
- 使用 Privileged Identity Management (PIM) 时需要 Azure AD Premium P2 许可证

## <a name="assign-a-role"></a>分配角色

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory”  。

1. 选择“角色和管理员”以查看所有可用角色列表。

    ![“角色和管理员”页的屏幕截图](./media/manage-roles-portal/roles-and-administrators.png)

1. 选择一个角色，查看其分配情况。

    为了帮助你找到需要的角色，Azure AD 可以根据角色类别显示角色子集。 查看“类型”筛选器，其中只显示所选类型中的角色。

1. 选择“添加分配”，然后选择要分配给此角色的用户。

    如果你看到与下图不同的内容，请阅读 [Privileged Identity Management (PIM)](#privileged-identity-management-pim) 中的“说明”，以确认你是否正在使用 PIM。

    ![管理员角色的权限列表](./media/manage-roles-portal/add-assignments.png)

1. 选择“添加”以分配角色。

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

可以使用 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 为其他管理功能选择“在 PIM 中管理”。 特权角色管理员可将“永久”（始终充当该角色）分配更改为“符合条件”（仅当提升了权限时才充当该角色）。 如果没有 Privileged Identity Management，仍可以选择“在 PIM 中管理”来注册试用版。 Privileged Identity Management 需要 [Azure AD Premium P2 许可证](../privileged-identity-management/subscription-requirements.md)。

![显示“用户管理员 - 分配”页的屏幕截图，其中选择了“在 PIM 中管理”操作](./media/manage-roles-portal/member-list-pim.png)

全局管理员或特权角色管理员可以轻松添加或删除成员、筛选列表，或选择某个成员以查看为其分配的有效角色。

> [!Note]
> 如果你有 Azure AD Premium P2 许可证，并且已使用 Privileged Identity Management，则所有角色管理任务都会在 Privilege Identity Management 中执行，而不会在 Azure AD 中执行。
>
> ![在 PIM 中为已使用 PIM 且具有 Premium P2 许可证的用户管理的 Azure AD 角色](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色的详细信息，请参阅 [Azure AD 内置角色](permissions-reference.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
