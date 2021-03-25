---
title: 教程：使用 Azure Site Recovery 运行 Azure VM 灾难恢复演练
description: 在本教程中，使用 Site Recovery 对其他区域运行 Azure VM 灾难恢复演练。
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395572"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>教程：针对 Azure VM 运行灾难恢复演练

了解如何针对使用 [Azure Site Recovery](site-recovery-overview.md) 进行复制的 Azure VM 对其他 Azure 区域运行灾难恢复演练。 本文内容：

> [!div class="checklist"]
> * 验证先决条件
> * 在进行演练之前，请检查 VM 设置
> * 运行测试故障转移
> * 演练完成之后进行清理


> [!NOTE]
> 本教程提供了运行灾难恢复演练所需的最基本步骤。 如果要通过完整的基础架构测试来运行演练，请了解 Azure VM [网络](azure-to-azure-about-networking.md)、[自动化](azure-to-azure-powershell.md)和[故障排除](azure-to-azure-troubleshoot-errors.md)。

## <a name="prerequisites"></a>先决条件

开始本教程之前，必须为一个或多个 Azure VM 启用灾难恢复。 为此，请完成本教程系列的[第一个教程](azure-to-azure-tutorial-enable-replication.md)。

## <a name="verify-vm-settings"></a>验证 VM 设置

1. 在保管库中 >“复制的项”，选择 VM  。

    ![用于在 VM 属性中打开“灾难恢复”页面的选项](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. 在“概述”页面上，检查 VM 是否受保护且运行正常。
3. 运行测试故障转移时，请在目标区域中选择一个 Azure 虚拟网络。 故障转移后创建的 Azure VM 将置于此网络中。 

    - 在本教程中，在运行测试故障转移时选择一个现有网络。
    - 建议为演练选择非生产网络，以便 IP 地址和网络组件在生产网络中保持可用。
   - 你也可以预配置要用于测试故障转移的网络设置。 可以为每个 NIC 分配的精细设置包括子网、专用 IP 地址、公用 IP 地址、负载均衡器和网络安全组。 此处未使用此方法，但你可[查看本文](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations)了解详细信息。


## <a name="run-a-test-failover"></a>运行测试故障转移


1. 在“概述”页面上，选择“测试故障转移” 。

    
    ![复制项的测试故障转移按钮](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. 在“测试故障转移”中，选择一个恢复点。 使用来自此恢复点的数据在目标区域中创建 Azure VM。
  
   - **最新处理**：使用由 Site Recovery 处理的最新恢复点。 将显示时间戳。 无需费时处理数据，因此恢复时间目标 (RTO) 会较低。
   -  **最新**：处理发送到 Site Recovery 的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 提供最低的恢复点目标 (RPO)，因为触发故障转移时，所有数据都将复制到 Site Recovery 中。
   - **最新的应用一致**：此选项将 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
   - **自定义**：故障转移到特定的恢复点。 自定义仅在对单个 VM 进行故障转移且不使用恢复计划时可用。

3. 在 Azure 虚拟网络中，选择将故障转移后创建的 Azure VM 放置在其中的目标网络。 如果可以，请选择非生产网络，而不是启用复制时创建的网络。

    ![“测试故障转移设置”页](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. 若要启动故障转移，请选择“确定”。
5. 在通知中监视测试故障转移。

    ![进度通知](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![成功通知](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. 故障转移完成后，在目标区域中创建的 Azure VM 将显示在 Azure 门户“虚拟机”中。 确保 VM 正在运行、大小合适且已连接到所选网络。

## <a name="clean-up-resources"></a>清理资源

1. 在“基本功能”页面中，选择“清除测试故障转移” 。

    ![用于开始清理过程的按钮](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. 在“测试故障转移清理” > “说明”中，记录并保存与测试故障转移相关的任何观测结果 。 
3. 选择“测试完成”，删除在测试故障转移过程中创建的 VM。

    ![带有清理选项的页面](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. 在通知中监视清理进度。

    ![清理进度通知](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![清理成功通知](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>后续步骤

在本教程中，运行了灾难恢复演练来检查故障转移是否按预期方式工作。 现在，可以尝试进行完整的故障转移。

> [!div class="nextstepaction"]
> [运行生产故障转移](azure-to-azure-tutorial-failover-failback.md)
