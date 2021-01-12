---
title: 将用户分配为 Azure 订阅的管理员-Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) ，使用户成为 Azure 订阅的管理员。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: b15238a4308fe055f914d1a684b79b38a2c64870
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122023"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>将用户分配为 Azure 订阅的管理员

若要使某个用户成为 Azure 订阅的管理员，请在订阅范围内为其分配[所有者](built-in-roles.md#owner)角色。 “所有者”角色授予用户对订阅中所有资源的完全访问权限，包括将访问权限授予其他用户的权限。 这些步骤与任何其他角色分配是相同的。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>步骤1：打开订阅

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部的搜索框中，搜索 "订阅"。

    ![Azure 门户搜索资源组](./media/shared/sub-portal-search.png)

1. 单击要使用的订阅。

    下面显示了一个示例订阅。

    ![资源组概述](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>步骤2：打开 "添加角色分配" 窗格

“访问控制(IAM)”是一个页面，通常用于分配角色以授予对 Azure 资源的访问权限。 它也称为标识和访问管理 (IAM) 并出现在 Azure 门户的多个位置中。

1. 单击“访问控制(IAM)”。

    下面显示了订阅的“访问控制(IAM)”页面的示例。

    ![资源组的访问控制 (IAM) 页](./media/shared/sub-access-control.png)

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

1. 单击“添加” > “角色分配”。
   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![“添加角色分配”菜单](./media/shared/add-role-assignment-menu.png)

    此时会打开“添加角色分配”窗格。

   ![“添加角色分配”窗格](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>步骤3：选择所有者角色

[所有者](built-in-roles.md#owner)角色授予完全访问权限，以管理所有资源，包括在 Azure RBAC 中分配角色的能力。 最多只能有 3 个订阅所有者，这样可降低被入侵的所有者做出违规行为的可能性。

- 在 " **角色** " 列表中，选择 " **所有者** " 角色。

   ![在 "添加角色分配" 窗格中选择所有者角色](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>步骤4：选择需要访问的人员

1. 在 " **分配访问权限** " 列表中，选择 **用户、组或服务主体**。

1. 在 " **选择** " 部分中，通过输入字符串或滚动列表来搜索用户。

   ![在添加角色分配中选择用户](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. 找到用户后，单击将其选中。

## <a name="step-5-assign-role"></a>步骤5：分配角色

1. 若要分配角色，请单击 " **保存**"。

   几分钟后，用户将被分配到所选作用域中的角色。

1. 在 " **角色分配** " 选项卡上，验证是否在列表中看到角色分配。

    ![添加角色分配 - 保存](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)
- [使用 Azure 门户列出 Azure 角色分配](role-assignments-list-portal.md)
- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)
