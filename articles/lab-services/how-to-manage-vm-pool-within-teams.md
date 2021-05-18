---
title: 从 Teams 管理 Azure 实验室服务中的 VM 池
description: 了解如何从 Teams 管理 Azure 实验室服务中的 VM 池。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91946627"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>从 Teams 管理实验室服务中的 VM 池

在首次发布模板虚拟机 (VM) 时，会立即开始创建 VM。 将会创建数量等于实验室用户列表中用户数量的 VM。 在学生首次登录到 Azure 实验室服务时，会自动将 VM 分配给学生。 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>发布模板并管理 VM 池

若要发布模板，请转到“Teams 实验室服务”窗口，选择“模板”选项卡->“...” -> “发布”  。

在配置好模板 VM 后，并且在教师选择发布模板时，系统将创建数量与实验室用户列表中的用户数相等的 VM。 在发布实验室并创建 VM 后，用户将自动注册到实验室，他们在首次登录到 Azure 实验室服务时（即，在首次访问具有 **Azure 实验室服务** 应用的选项卡时）将获得系统分配的 VM。 

在触发用户列表同步时，实验室容量（实验室中 VM 的数量）将会根据对团队成员身份的变化自动更新。 在添加新用户时将会创建新 VM，对于从团队中删除的用户，分配给该用户的 VM 也将会被删除。 有关详细信息，请参阅[如何在 Teams 中管理用户](how-to-manage-user-lists-within-teams.md)。 

教师可以直接从“VM 池”选项卡继续访问学生 VM。并且教师可以从“虚拟机池”选项卡或通过单击“我的虚拟机”按钮（屏幕的右上角）来访问分配给他们自己的 VM 。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM 池":::

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [在 Teams 内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [开始使用并从 Teams 创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)
- [从 Teams 管理实验室服务用户列表](how-to-manage-user-lists-within-teams.md)
- [从 Teams 创建实验室服务计划](how-to-create-schedules-within-teams.md)
- [从 Teams 访问实验室服务中的 VM（学生视图）](how-to-access-vm-for-students-within-teams.md)


