---
title: 如何将其他所有者添加到 Azure 实验室服务中的实验室
description: 本文介绍管理员如何将用户作为所有者添加到 Azure 实验室服务中的实验室。
ms.topic: article
ms.date: 08/03/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: e8dd21a140e1c519f09d661c776e5c84c47da925
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635009"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>如何将其他所有者添加到 Azure 实验室服务中的现有实验室
本文介绍如何以管理员身份向现有实验室添加其他所有者。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>将用户添加到实验室帐户的读取者角色
1. 返回“实验室帐户”页，选择左侧菜单中的“所有实验室”。
2. 选择要将用户添加为其所有者的实验室。 

    ![选择实验室 ](./media/how-to-add-user-lab-owner/select-lab.png)  
1. 在导航菜单中，选择“访问控制 (IAM)”。

1. 选择“添加” > “+ 添加角色分配（预览版）”。 

    ![打开了“添加角色分配”菜单的“访问控制(IAM)”页。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. 在“角色”选项卡上，选择“读者”角色。 

    ![选中了“角色”选项卡的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. 在“成员”选项卡上，选择要添加到“读者”角色的用户。

1. 在“查看 + 分配”选项卡上，选择“查看 + 分配”，以分配角色 。
## <a name="add-user-to-the-owner-role-for-the-lab"></a>将用户添加到实验室的所有者角色

> [!NOTE]
> 如果用户只有某个实验室的“读取者”访问权限，则 labs.azure.com 中不会显示该实验室。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。


1. 在“实验室帐户”页上，选择“访问控制 (IAM)” 

1. 选择“添加” > “+ 添加角色分配（预览版）”。 

    ![打开了“添加角色分配”菜单的“访问控制(IAM)”页。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. 在“角色”选项卡上，选择“所有者”角色。 

    ![选中了“角色”选项卡的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. 在“成员”选项卡上，选择要添加到“所有者”角色的用户

1. 在“查看 + 分配”选项卡上，选择“查看 + 分配”，以分配角色 。


## <a name="next-steps"></a>后续步骤
确认用户在登录到[实验室服务门户](https://labs.azure.com)后看到了实验室。
