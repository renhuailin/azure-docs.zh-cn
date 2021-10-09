---
title: 为 VM 配置自动启动设置
description: 了解如何在实验室中为 VM 配置自动启动设置。 此设置使实验室中的 VM 能够根据计划自动启动。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 828350cb130e990d6a6ce3f16f084d5629518293
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642940"
---
# <a name="auto-startup-lab-virtual-machines"></a>自动启动实验室虚拟机  
通过 Azure 开发测试实验室可以在实验室中配置虚拟机，使其根据计划自动启动和关闭。 有关配置自动关闭设置的信息，请参阅[管理 Azure 开发测试实验室中的某个实验室的自动关闭策略](devtest-lab-auto-shutdown.md)。 

启用自动关闭策略时，所有 VM 都包含在内，与自动关闭不同，自动启动策略要求实验室用户显式选择 VM 并选择加入此计划。 这样一来，就不容易遇到不需要的 VM 意外自动启动并导致意外支出的情况。

本文介绍如何配置实验室的自动启动策略。

## <a name="configure-autostart-settings-for-a-lab"></a>为实验室配置自动启动设置 
1. 导航到实验室的主页。 
2. 在左侧菜单中选择“配置和策略”。 

    ![显示开发测试实验室中“配置和策略”菜单的屏幕截图。](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 在“配置和策略”页上，执行以下步骤：
    
    1. 为“允许将虚拟机计划为自动启动”选择“打开”，以为此实验室启用自动启动功能 。 
    2. 为“计划启动”字段选择启动时间（例如：上午 8:00:00）。 
    3. 选择要使用的时区。 
    4. 选择需要自动启动 VM 的一周中的某些天。 
    5. 然后在工具栏上选择“保存”以保存设置。 

        ![“自动启动”设置](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 此策略不会自动将自动启动应用于实验室中的任何虚拟机。 若要选择加入单独的虚拟机，请转到虚拟机页，然后为该 VM 启用“自动启动” 。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>在实验室中为 VM 启用自动启动
以下过程提供了将虚拟机选择加入实验室的自动启动策略的步骤。 

1. 在实验室的主页上，在“我的虚拟机”列表中选择“VM” 。 

    ![“配置和策略”菜单](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 在“虚拟机”页上，从左侧菜单或在“计划”列表中选择“自动启动”  。 

    ![选择“自动启动”菜单](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 在“自动启动”页上，为“允许将此虚拟机计划为自动启动”选项选择“打开”  。

    ![为 VM 启用自动启动](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 然后在工具栏上选择“保存”以保存设置。 


## <a name="next-steps"></a>后续步骤
若要了解为实验室配置自动关闭策略的信息，请参阅[管理 Azure 开发测试实验室中的某个实验室的自动关闭策略](devtest-lab-auto-shutdown.md)
