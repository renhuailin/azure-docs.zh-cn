---
title: 通过 Azure Policy 为虚拟机启用 Automanage
description: 了解如何通过 Azure 门户中的内置 Azure Policy 为 VM 启用 Azure Automanage。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91714906"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>通过 Azure Policy 为虚拟机启用 Automanage

如果要为许多 VM 启用 Automanage，可以使用内置 [Azure Policy](..\governance\azure-management.md) 来实现。 本文将指导你查找正确的策略并对其进行分配，以便在 Azure 门户中启用 Automanage。


## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级为即用即付订阅。

> [!IMPORTANT]
> 启用 Automanage 需要以下 Azure RBAC 权限：“所有者”角色或“参与者”角色，以及“用户访问管理员”角色。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com/)。


## <a name="locate-and-assign-the-policy"></a>找到并分配策略

1. 在 Azure 门户中导航到“策略”
1. 转到“定义”窗格
1. 单击“类别”下拉列表以查看可用选项
1. 选择“启用 Automanage - Azure 虚拟机最佳做法”选项
1. 此时列表会进行更新，以显示名称以“启用 Automanage…”开头的内置策略
1. 单击“启用 Automanage - Azure 虚拟机最佳做法”内置策略名称
1. 单击该策略后，可以看到“定义”选项卡

    > [!NOTE]
    > Azure Policy 定义用于设置 Automanage 参数（例如配置文件或帐户）。 它还设置筛选器，以确保策略仅适用于正确的 VM。

1. 单击“分配”按钮以创建分配
1. 在“基本信息”选项卡下，通过设置“订阅”和“资源组”来填写“范围”

    > [!NOTE]
    > “范围”可让你定义此策略适用于哪些 VM。 可以在订阅级别或资源组级别设置应用程序。 如果设置资源组，则该资源组中当前存在的所有 VM 或我们添加到该资源组中的任何将来的 VM 都会自动启用 Automanage。 

1. 单击“参数”选项卡，然后设置“Automanage 帐户”和所需的“配置文件” 
1. 在“查看 + 创建”选项卡下，查看设置
1. 通过单击“创建”应用分配
1. 在“定义”旁边的“分配”选项卡中查看分配

> [!NOTE]
> 一段时间过后，该策略才会对当前在资源组或订阅中的 VM 生效。


## <a name="next-steps"></a>后续步骤 

了解通过 Azure 门户为虚拟机启用 Azure Automanage 的另一种方法。 

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)