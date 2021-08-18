---
title: 在 Azure Automanage 中为 VM 创建自定义首选项
description: 了解如何在 Azure Automanage 中调整环境配置，以及如何设置自己的首选项。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: edd82086dba6f603c50edd23c3f3757b95dcdb9d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468357"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>在 Azure Automanage 中为 VM 创建自定义首选项

适合虚拟机最佳做法的 Azure Automanage 是默认环境，可根据需要进行调整。 本文将介绍在新的或现有虚拟机 (VM) 上启用 Automanage 时如何设置自己的首选项。

目前支持在 [Azure 备份](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy)和 [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration) 上进行自定义。


> [!NOTE]
> 启用 Automanage 后，无法更改 VM 上的环境或首选项。 你需要为该 VM 禁用 Automanage，然后使用所需的配置环境和首选项重新启用 Automanage。


## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级为即用即付订阅。

> [!IMPORTANT]
> 除了“用户访问管理员”角色，还需要以下 Azure RBAC 权限才能启用 Automanage：“所有者”角色或“参与者”角色  。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com/)。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>在现有 VM 上启用适用于 VM 的自动管理

1. 在搜索栏中，搜索并选择“Automanage - Azure 计算机最佳做法”。

2. 选择“在现有 VM 上启用”。

3. 在“选择计算机”边栏选项卡上：
    1. 按“订阅”和“资源组”筛选 VM 列表 。
    1. 选中要加入的每个虚拟机的复选框。
    1. 单击“选择”按钮。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="从可用 VM 列表中选择现有 VM。":::

    > [!NOTE]
    > 单击“显示不合格的计算机”，查看包含不受支持的计算机及其原因的列表。 

4. 在“配置”下，单击“比较环境” 。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="比较环境。":::

5. 在“环境详细信息”边栏选项卡上，从下拉菜单中选择一个环境：选择“开发/测试”用于测试，选择“生产”用于生产环境，然后单击“确定” 

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="浏览生产环境。":::

6. 选择环境后，可选择“配置首选项”。 默认情况下，将使用“Azure 最佳做法”首选项。 此首选项包含使用每个服务的推荐设置。 可创建自定义首选项来修改这些设置： 
    1. 单击“新建首选项”。
    1. 在“创建配置首选项”边栏选项卡中，填写“基本信息”选项卡：
        1. 订阅
        1. 资源组
        1. 首选项名称
        1. 区域

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="填写配置首选项。":::

7. 调整所需的配置首选项。
        
    > [!NOTE]
    > 更改环境配置时，仅允许仍在最佳做法上限和下限范围内的调整。

8. 查看配置详细信息。
9. 单击“创建”  按钮。

10. 单击“启用”按钮。


## <a name="disable-automanage-for-vms"></a>禁用适用于 VM 的自动管理

可通过禁用自动管理来快速停止适用于虚拟机的 Azure 自动管理。

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="在虚拟机上禁用自动管理。":::

1. 请转到“自动管理 – Azure 虚拟机最佳做法”页，其中列出了自动管理的所有 VM。
1. 选中要禁用的虚拟机旁的复选框。
1. 单击“禁用自动管理”按钮。
1. 在同意“禁用”之前，请仔细阅读生成的弹出窗口中的消息。


## <a name="clean-up-resources"></a>清理资源

如果创建了新资源组来尝试适用于虚拟机的 Azure 自动管理，但不再需要它，则可以删除该资源组。 删除组时也会删除资源组中包含的 VM 和所有其他资源。

Azure 自动管理会创建用于存储资源的默认资源组。 检查具有命名约定“DefaultResourceGroupRegionName”和“AzureBackupRGRegionName”的资源组，以清理所有资源。

1. 选择“资源组”。
1. 在资源组页上，选择“删除”。
1. 出现提示时，确认资源组的名称，然后选择“删除”。


## <a name="next-steps"></a>后续步骤 

获取在常见问题解答中已解答的常见问题。 

> [!div class="nextstepaction"]
> [常见问题解答](faq.yml)