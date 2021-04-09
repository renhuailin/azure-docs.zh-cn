---
title: 将用户分配为 Azure 订阅的管理员 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 将用户分配为 Azure 订阅的管理员。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556837"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>将用户分配为 Azure 订阅的管理员

若要使某个用户成为 Azure 订阅的管理员，请在订阅范围内为其分配[所有者](built-in-roles.md#owner)角色。 “所有者”角色授予用户对订阅中所有资源的完全访问权限，包括将访问权限授予其他用户的权限。 这些步骤与任何其他角色分配是相同的。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>步骤 1：打开订阅

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部的“搜索”框中，搜索订阅。

    ![Azure 门户搜索资源组](./media/shared/sub-portal-search.png)

1. 单击想要使用的订阅。

    下面显示了一个示例订阅。

    ![资源组概述](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>步骤 2：打开“添加角色分配”窗格

“访问控制(IAM)”是一个页面，通常用于分配角色以授予对 Azure 资源的访问权限。 该功能也称为标识和访问管理 (IAM)，会显示在 Azure 门户中的多个位置。

1. 单击“访问控制(IAM)”。

    下面显示了订阅的“访问控制(IAM)”页面的示例。

    ![资源组的“访问控制(IAM)”页](./media/shared/sub-access-control.png)

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

1. 单击“添加” > “角色分配”。
   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![“添加角色分配”菜单](./media/shared/add-role-assignment-menu.png)

    将打开“添加角色分配”窗格。

   ![“添加角色分配”窗格](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>步骤 3：选择“所有者”角色

[所有者](built-in-roles.md#owner)角色授予管理所有资源的完全访问权限，包括允许在 Azure RBAC 中分配角色。 最多只能有 3 个订阅所有者，这样可降低被入侵的所有者做出违规行为的可能性。

- 在“角色”列表中，选择“所有者”角色 。

   ![在“添加角色分配”窗格中选择“所有者”角色](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>步骤 4：选择需要访问权限的人员

1. 在“分配访问权限至”列表中，选择“用户、组或服务主体” 。

1. 在“选择”部分，通过输入字符串或滚动列表来搜索用户。

   ![在“添加角色分配”中选择用户](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. 找到用户后，单击以选中。

## <a name="step-5-assign-role"></a>步骤 5：分配角色

1. 单击“保存”以分配该角色。

   片刻之后，就会在所选范围为用户分配角色。

1. 在“角色分配”选项卡上，验证列表是否显示了角色分配。

    ![添加角色分配 - 保存](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户分配 Azure 角色](role-assignments-portal.md)
- [使用 Azure 门户列出 Azure 角色分配](role-assignments-list-portal.md)
- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)
