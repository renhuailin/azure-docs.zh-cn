---
title: 快速入门 - 在 Azure 门户中启用适用于 VM 的 Azure 自动管理
description: 了解如何在 Azure 门户中对新的或现有 VM 快速启用适用于虚拟机的自动管理。
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 7836e15c307a0dcfcf4bfa4b8c56fbeea869d315
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709765"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>快速入门：在 Azure 门户中启用适用于虚拟机的 Azure 自动管理

使用 Azure 门户在新的或现有虚拟机上启用自动管理，从而开始使用适用于虚拟机的 Azure 自动管理。


## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级为即用即付订阅。

> [!IMPORTANT]
> 必须在包含 VM 的资源组上具有“参与者”角色，才能使用现有自动管理帐户启用自动管理。 如果要使用新的自动管理帐户启用自动管理，则需要具有以下权限：订阅上的“所有者”角色或“参与者”以及“用户访问管理员”角色  。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://aka.ms/AutomanagePortal-Ignite21)。

## <a name="enable-automanage-on-existing-machines"></a>在现有计算机上启用 Automanage

1. 在搜索栏中，搜索并选择“Automanage - Azure 计算机最佳做法”。

2. 选择“在现有 VM 上启用”。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="在现有 VM 上启用。":::

3. 在“选择计算机”边栏选项卡上：
    1. 按“订阅”和“资源组”筛选列表。
    1. 选中要加入的每个虚拟机的复选框。
    1. 单击“选择”按钮。
    > [!NOTE]
    > 可同时选择 Azure VM 和已启用 Azure Arc 的服务器。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="从可用 VM 列表中选择现有 VM。":::

4. 在“环境”下，选择环境类型：“开发/测试”或“生产”  。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="选择环境。":::

   单击“比较环境详细信息”，以查看环境之间的差异。
    1. 在下拉列表中选择环境：“开发/测试”环境用于测试，“生产”环境用于生产 。
    1. 单击“确定”按钮。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="浏览生产环境。":::

5. 默认情况下会选择“Azure 最佳做法”首选项作为配置首选项。 若要更改此选择，请创建新的首选项或选择现有的首选项。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="创建首选项。":::

6. 单击“启用”按钮。


## <a name="enable-automanage-for-a-new-vm"></a>对新 VM 启用 Automanage

在[此处](https://aka.ms/AzureAutomanagePreview)登录到 Azure 门户，以创建新的 VM 并启用自动管理。

1. 在“基本信息”选项卡中，填写 VM 详细信息。

> [!NOTE]
> 请查看 Automanage [支持的区域](automanage-virtual-machines.md#supported-regions)以及 Automanage 支持的 [Linux 发行版](automanage-linux.md#supported-linux-distributions-and-versions)和 [Windows Server 版本](automanage-windows-server.md#supported-windows-server-versions)。

2. 浏览到“管理”选项卡，并选择“Automanage 环境” 。

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="在“管理”选项卡中启用 Automanage。":::

3. 保留剩余的默认值，然后选择页面底部的“查看 + 创建”按钮。

4. 显示验证通过的消息时，选择“创建”。

## <a name="disable-automanage-for-vms"></a>禁用适用于 VM 的自动管理

可通过禁用自动管理来快速停止适用于虚拟机的 Azure 自动管理。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="在虚拟机上禁用自动管理。":::

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

在本快速入门中，你启用了适用于 VM 的 Azure 自动管理。

了解如何在对虚拟机启用自动管理时创建和应用自定义首选项。

> [!div class="nextstepaction"]
> [适用于 VM 的 Azure Automanage - 自定义配置首选项](virtual-machines-custom-preferences.md)
