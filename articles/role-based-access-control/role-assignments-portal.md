---
title: 使用 Azure 门户分配 Azure 角色 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 向用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: e25bbe4e1a96e4efaaa13732aea571d26d4b006e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555265"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 门户分配 Azure 角色

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文介绍如何使用 Azure 门户分配角色。

如需在 Azure Active Directory 中分配管理员角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](../active-directory/roles/manage-roles-portal.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>步骤 1：识别所需的范围

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] 有关范围的详细信息，请参阅[了解范围](scope-overview.md)。

![Azure RBAC 的范围级别](../../includes/role-based-access-control/media/scope-levels.png)

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部的“搜索”框中，搜索要授予对其的访问权限的范围。 例如，搜索“管理组”、“订阅”、“资源组”或某个特定资源  。

    ![针对资源组的 Azure 门户搜索](./media/shared/rg-portal-search.png)

1. 单击该范围的特定资源。

    下面展示了一个示例资源组。

    ![资源组概述](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>步骤 2：打开“添加角色分配”窗格

“访问控制(IAM)”是一个页面，通常用于分配角色以授予对 Azure 资源的访问权限。 该功能也称为标识和访问管理 (IAM)，会显示在 Azure 门户中的多个位置。

1. 单击“访问控制(IAM)”。

    下面显示了资源组的“访问控制(IAM)”页的示例。

    ![资源组的“访问控制(IAM)”页](./media/shared/rg-access-control.png)

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

1. 单击“添加” > “角色分配”。
   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![“添加角色分配”菜单](./media/shared/add-role-assignment-menu.png)

    将打开“添加角色分配”窗格。

   ![“添加角色分配”窗格](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-appropriate-role"></a>步骤 3：选择合适的角色

1. 在“角色”列表中，搜索或滚动查找要分配的角色。

    为了确定合适的角色，你可以将鼠标指针悬停在信息图标上，以显示角色的说明。 有关更多信息，可以查看 [Azure 内置角色](built-in-roles.md)一文。

   ![在“添加角色分配”中选择角色](./media/role-assignments-portal/add-role-assignment-role.png)

1. 单击以选择角色。

## <a name="step-4-select-who-needs-access"></a>步骤 4：选择需要访问权限的人员

1. 在“将访问权限分配给”列表中，选择要为其分配访问权限的安全主体的类型。

    | 类型 | 说明 |
    | --- | --- |
    | 用户、组或服务主体 | 如果要将角色分配给用户、组或服务主体（应用程序），请选择此类型。 |
    | **用户分配的托管标识** | 如果要将角色分配给[用户分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md)，请选择此类型。 |
    | *系统分配托管标识* | 如果要将角色分配给[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md)，请选择托管标识所在的 Azure 服务实例。 |

   ![在“添加角色分配”中选择安全主体类型](./media/role-assignments-portal/add-role-assignment-type.png)

1. 如果你选择了用户分配的托管标识或系统分配的托管标识，请选择该托管标识所在的“订阅”。

1. 在“选择”部分，通过输入字符串或滚动浏览列表来搜索安全主体。

   ![在“添加角色分配”中选择用户](./media/role-assignments-portal/add-role-assignment-user.png)

1. 找到安全主体后，单击以将其选中。

## <a name="step-5-assign-role"></a>步骤 5：分配角色

1. 若要分配角色，请单击“保存”。

   片刻之后，会在所选范围内为安全主体分配角色。

1. 在“角色分配”选项卡上，验证列表是否显示了该角色分配。

    ![添加角色分配 - 保存](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>后续步骤

- [将用户分配为 Azure 订阅的管理员](role-assignments-portal-subscription-admin.md)
- [删除 Azure 角色分配](role-assignments-remove.md)
- [排查 Azure RBAC 问题](troubleshooting.md)
