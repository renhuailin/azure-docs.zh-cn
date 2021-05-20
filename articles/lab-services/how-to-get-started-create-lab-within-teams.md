---
title: 开始在 Teams 内创建 Azure 实验室服务实验室
description: 了解如何开始在 Teams 内创建 Azure 实验室服务实验室。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433965"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>开始在 Teams 内创建实验室服务实验室

本文介绍如何向团队添加 Azure 实验室服务应用，以及如何在 MS Teams 环境内创建实验室。

## <a name="prerequisites"></a>先决条件

在本教程中，使用虚拟机为你的团队设置一个实验室。 只有在实验室帐户中充当下述角色之一的成员才能在实验室帐户中设置实验室：所有者、实验室创建者或参与者。 用来创建实验室帐户的帐户会自动添加到所有者角色。 因此，可使用创建实验室帐户所用的用户帐户创建一个实验室。

下面是在 Teams 中使用 Azure 实验室服务时的典型工作流

1. 用户在 Azure 门户上[创建实验室帐户](tutorial-setup-lab-account.md#create-a-lab-account)。
1. [实验室帐户创建者将其他用户添加到“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。 例如，实验室帐户创建者/管理员将教师添加到“实验室创建者”角色，这样教师就可以创建课堂实验室。
1. 然后，教师创建实验室、预先配置模板 VM 并发布实验室，为团队中的每个人预配 VM。
1. 发布实验室后，当团队成员身份列表上的每个人通过在 Teams 内单击包含 Azure 实验室服务应用的选项卡 (SSO) 或通过访问[实验室网站](https://labs.azure.com)第一次登录 Azure 实验室服务时，就会被分配一个 VM。 然后，用户可使用该 VM 来完成课堂作业和家庭作业。

> [!IMPORTANT]
> 只有当实验室帐户创建在与 Teams 相同的租户中时，Azure 实验室服务才能在 Teams 中使用。

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>将 Azure 实验室服务应用作为一个选项卡添加到团队

作为团队所有者，你可以直接在你的 Teams 通道中添加 Azure 实验室服务应用，然后团队中的每个人都可使用该应用。 遵循以下三个步骤：

1. 导航到想要添加该应用的 Teams 通道，并选择“+”以添加选项卡。 
1. 在选项卡选项中搜索“Azure 实验室服务”，并添加此应用。 

    > [!NOTE]
    > 只有团队所有者才能为团队创建实验室。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="添加选项卡":::
1. 选择要用于在此团队中创建实验室的实验室服务帐户。 

    Azure 实验室服务使用单一登录进入 [Azure 实验室服务网站](https://labs.azure.com)，并拉取你有权访问的所有实验室帐户。 

    将显示与 Teams 处于同一租户的帐户，以及你具有其“所有者”、“参与者”或“创建者”访问权限的帐户  。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="欢迎使用 ALS":::
1. 按“保存”，即可将该选项卡添加到通道。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="已创建 ALS 选项卡":::

    现在，可从你的通道中选择“Azure 实验室服务”选项卡，开始按照以下文章中所述的方式管理实验室。

选择实验室帐户后，团队所有者将能够为团队创建实验室。 整个实验室创建过程以及实验室级别的所有任务都可在 Teams 中执行。 用户可以选择在同一个团队内创建多个实验室，并且在实验室帐户级别具有适当的访问权限的团队所有者只能查看与特定团队关联的实验室。

## <a name="next-steps"></a>后续步骤

在 Teams 内创建实验室时，实验室用户列表将自动填充，并与团队成员身份同步。 团队中的每个人（包括所有者、成员和来宾）都将自动添加到实验室用户列表中。 Azure 实验室服务将保持与团队成员身份同步，每 24 小时触发一次自动同步。 有关详细信息，请参阅：

[在 Teams 内管理实验室服务用户列表](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>另请参阅

另请参阅以下文章：

- [在 Teams 中使用 Azure 实验室服务的概述](lab-services-within-teams-overview.md)
- [在 Teams 中管理实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在 Teams 中创建和管理实验室计划](how-to-create-schedules-within-teams.md)
- [在 Teams 中访问 VM -“学生”视图](how-to-access-vm-for-students-within-teams.md)
- [在 Teams 中删除实验室](how-to-delete-lab-within-teams.md)
