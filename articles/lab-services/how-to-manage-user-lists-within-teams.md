---
title: 从 Teams 管理 Azure 实验室服务用户列表
description: 了解如何从 Teams 管理 Azure 实验室服务用户列表。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946599"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>从 Teams 管理实验室服务用户列表

在 Teams 内创建实验室时 (参阅“[开始使用并从 Teams 创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)”) ，实验室用户列表将自动填充并与团队成员身份同步。 团队中的每个人（包括所有者、成员和来宾）都将自动添加到实验室用户列表中。 Azure 实验室服务保持与团队成员身份同步，每24小时触发一次自动同步。 

## <a name="sync-users"></a>同步用户

团队成员身份更新后，教师可以使用“**同步**”按钮来触发手动同步。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="同步用户":::

自动或手动同步完成后，根据实验室是否已发布，会存在以下情况。

* 如果实验室尚未至少发布一次：
    * 每次对团队成员身份进行更改时，系统都会在实验室用户列表中添加或删除用户。 
* 如果实验室至少已发布一次，则除了添加或删除用户外，实验室容量也会自动更新。
    * 如果团队有任何新的添加件，则会创建新的 VM。
    * 如果从团队中删除任何用户，则关联的 VM 也将删除。

## <a name="next-steps"></a>后续步骤

配置好模板 VM 后，当教师选择发布模板时，系统将创建数量与实验室用户列表中的用户数等效的 VM。 发布实验室并创建 VM 后，用户将自动注册到实验室，他们在首次登录到 Azure 实验室服务时——即，当首次访问具有 **Azure 实验室服务** 应用程序的选项卡时——，将获得系统分配的 VM。 

若要发布模板 VM，请转到“团队实验室服务”窗口，选择“**模板**”选项卡-> **...**  -> **发布**。

若要管理 VM 池，请参阅“[从 Teams 管理实验室服务中的 VM 池](how-to-manage-vm-pool-within-teams.md)”。

### <a name="also-review"></a>另请参阅

请参阅以下文章：

- [在 Teams 内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [开始使用并从 Teams 创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)
- [从 Teams 创建实验室服务计划](how-to-create-schedules-within-teams.md)
- [从 Teams 访问实验室服务中的 VM（学生视图）](how-to-access-vm-for-students-within-teams.md)

