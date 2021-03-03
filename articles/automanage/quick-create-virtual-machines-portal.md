---
title: 快速入门 - 在 Azure 门户中启用适用于 VM 的 Azure 自动管理
description: 了解如何在 Azure 门户中对新的或现有 VM 快速启用适用于虚拟机的自动管理。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 6e0e582ed37230ba3f379f193a229cfec06f066c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648027"
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

## <a name="enable-automanage-for-a-single-vm"></a>对单个 VM 启用 Automanage

1. 浏览到要启用的虚拟机。

2. 在“操作”下的目录中单击“Automanage (预览版)”项 。

3. 选择“入门”。

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-GetStarted.png" alt-text="启动单个 VM。":::

4. 选择 Automanage 设置（环境、首选项、Automanage 帐户）并点击“启用”。

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-Enable.png" alt-text="在单个 VM 上启用。":::

## <a name="enable-automanage-for-multiple-vms"></a>对多个 VM 启用 Automanage

1. 在搜索栏中，搜索并选择“Automanage - Azure 计算机最佳做法”。

2. 选择“在现有 VM 上启用”。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="在现有 VM 上启用。":::

3. 在“选择计算机”边栏选项卡上：
    1. 按“订阅”和“资源组”筛选 VM 列表 。
    1. 选中要加入的每个虚拟机的复选框。
    1. 单击“选择”按钮。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="从可用 VM 列表中选择现有 VM。":::

4. 在“配置文件”下，单击“浏览并更改配置文件和首选项” 。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="浏览并更改配置文件和首选项。":::

5. 在“选择配置文件 + 首选项”边栏选项卡上：
    1. 在左侧选择配置文件：“开发/测试”用于测试，“生产”用于生产 。
    1. 单击“选择”按钮。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="浏览生产配置文件。":::

6. 单击“启用”按钮。


## <a name="enable-automanage-for-a-new-vm"></a>对新 VM 启用 Automanage

在[此处](https://aka.ms/AutomanagePortal-Ignite21)登录到 Azure 门户，以创建新的 VM 并启用自动管理。

1. 在 Azure 门户的左上角，选择“创建资源”。

2. 在 Azure 市场资源列表上方的搜索框中，搜索并选择要使用的映像，然后选择“创建”。

> [!NOTE]
> 选中 Automanage 支持的 [Linux 发行版](automanage-linux.md#supported-linux-distributions-and-versions)和 [Windows Server 版本](automanage-windows-server.md#supported-windows-server-versions)。

3. 在“基本信息”选项卡中，填写 VM 详细信息。

> [!NOTE]
> 选中 Automanage [支持的区域](automanage-virtual-machines#supported-regions)。

4. 浏览到“管理”选项卡，并选择“Automanage 环境” 。

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMCreate-Management-Tab.png" alt-text="在“管理”选项卡中启用 Automanage。":::

5. 保留剩余的默认值，然后选择页面底部的“查看 + 创建”按钮。

6. 显示验证通过的消息时，选择“创建”。

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
> [适用于 VM 的 Azure Automanage - 自定义配置文件](virtual-machines-custom-preferences.md)
