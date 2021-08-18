---
title: 将用户分配为 Azure 订阅的管理员 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 将用户分配为 Azure 订阅的管理员。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/25/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: af88edbb788afa3576d3317d4e1cd54dea6f4deb
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988060"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>将用户分配为 Azure 订阅的管理员

若要使某个用户成为 Azure 订阅的管理员，请在订阅范围内为其分配[所有者](built-in-roles.md#owner)角色。 “所有者”角色授予用户对订阅中所有资源的完全访问权限，包括将访问权限授予其他用户的权限。 这些步骤与任何其他角色分配是相同的。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>步骤 1：打开订阅

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部的“搜索”框中，搜索订阅。

1. 单击想要使用的订阅。

    下面显示了一个示例订阅。

    ![订阅概述的屏幕截图](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>步骤 2：打开“添加角色分配”页面

“访问控制(IAM)”是一个页面，通常用于分配角色以授予对 Azure 资源的访问权限。 该功能也称为标识和访问管理 (IAM)，会显示在 Azure 门户中的多个位置。

1. 单击“访问控制(IAM)”。

    下面显示了订阅的“访问控制(IAM)”页面的示例。

    ![订阅的“访问控制(IAM)”页面的屏幕截图。](./media/shared/sub-access-control.png)

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

1. 单击“添加” > “添加角色分配(预览)”。

   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

    ![预览体验的“添加”>“添加角色分配”菜单的屏幕截图。](./media/shared/add-role-assignment-menu-preview.png)

    随即打开“添加角色分配”窗格。

## <a name="step-3-select-the-owner-role"></a>步骤 3：选择“所有者”角色

[所有者](built-in-roles.md#owner)角色授予管理所有资源的完全访问权限，包括允许在 Azure RBAC 中分配角色。 最多只能有 3 个订阅所有者，这样可降低被入侵的所有者做出违规行为的可能性。

1. 在“角色”选项卡上，选择“所有者”角色 。

    可按名称或说明搜索角色。 还可按类型和类别筛选角色。

   ![预览版体验的“添加角色分配”页面的屏幕截图，其中显示了“角色”选项卡。](./media/shared/roles.png)

1. 单击“下一步”。 

## <a name="step-4-select-who-needs-access"></a>步骤 4：选择需要访问权限的人员

1. 在“成员”选项卡上，选择“用户、组或服务主体” 。

   ![预览体验的“添加角色分配”页面的屏幕截图，其中显示了“添加成员”选项卡。](./media/shared/members.png)

1. 单击“选择成员”。

1. 查找并选择用户。

    可以在“选择”框中键入，以在目录中搜索显示名称或电子邮件地址。

   ![预览体验的“选择成员”窗格的屏幕截图。](./media/shared/select-members.png)

1. 单击“保存”，将用户添加到“成员”列表。

1. 在“说明”框中，输入此角色分配的可选说明。

    稍后，可以在“角色分配”列表中显示此说明。

1. 单击“下一步”。 

## <a name="step-5-assign-role"></a>步骤 5：分配角色

1. 在“查看 + 分配”选项卡上，查看角色分配设置。

1. 单击“查看 + 分配”以分配角色。

   片刻之后，会为用户分配订阅的“所有者”角色。

    ![分配角色后预览体验的角色分配列表屏幕截图。](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户分配 Azure 角色](role-assignments-portal.md)
- [使用 Azure 门户列出 Azure 角色分配](role-assignments-list-portal.md)
- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)
