---
title: 本教程介绍如何使用 Azure Site Recovery 将 Azure VM 故障转移到次要区域，以实现灾难恢复。
description: 本教程介绍如何使用 Azure Site Recovery 服务对复制到 Azure 次要区域的 Azure VM 进行故障转移和重新保护，以实现灾难恢复。
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392711"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>教程：将 Azure VM 故障转移到次要区域

了解如何使用 [Azure Site Recovery](site-recovery-overview.md) 将启用了灾难恢复的 Azure VM 故障转移到次要 Azure 区域。 故障转移后，请在目标区域中重新保护 VM，使这些 VM 复制回主要区域。 在本文中，学习如何：

> [!div class="checklist"]
> * 检查先决条件
> * 验证 VM 设置
> * 运行到次要区域的故障转移
> * 开始将 VM 复制回主要区域。


> [!NOTE]
> 本教程介绍如何通过最少的步骤对 VM 进行故障转移。 如果要使用完整设置运行故障转移，请了解 Azure VM [网络](azure-to-azure-about-networking.md)、[自动化](azure-to-azure-powershell.md)和[故障排除](azure-to-azure-troubleshoot-errors.md)。



## <a name="prerequisites"></a>先决条件

开始本教程之前，应完成以下操作：

1. 为一个或多个 Azure VM 设置复制。 如果尚未执行此操作，请完成本教程系列中的[第一个教程](azure-to-azure-tutorial-enable-replication.md)。
2. 建议为复制的 VM [运行灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)。 在运行完整的故障转移之前运行演练有助于确保一切按预期运行，而不会影响生产环境。 


## <a name="verify-the-vm-settings"></a>验证 VM 设置

1. 在保管库中 >“复制的项”，选择 VM  。

    ![在概述页上打开 VM 属性的选项](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. 运行故障转移之前，请在 VM“概述”页面上检查 VM 是否受保护且运行正常。
    ![用于验证 VM 属性和状态的页面](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. 在故障转移之前，请检查：
    - VM 正在运行支持的 [Windows](azure-to-azure-support-matrix.md#windows) 或 [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) 操作系统。
    - VM 符合[计算](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)、[存储](azure-to-azure-support-matrix.md#replicated-machines---storage)和[网络](azure-to-azure-support-matrix.md#replicated-machines---networking)要求。

## <a name="run-a-failover"></a>运行故障转移


1. 在 VM“概述”页中，选择“故障转移” 。

    ![复制项的“故障转移”按钮](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. 在“故障转移”中，选择一个恢复点。 使用来自此恢复点的数据在目标区域中创建 Azure VM。
  
   - **最新处理**：使用由 Site Recovery 处理的最新恢复点。 将显示时间戳。 无需费时处理数据，因此恢复时间目标 (RTO) 会较低。
   -  **最新**：处理发送到 Site Recovery 的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 提供最低的恢复点目标 (RPO)，因为触发故障转移时，所有数据都将复制到 Site Recovery 中。
   - **最新的应用一致**：此选项将 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
   - **自定义**：故障转移到特定的恢复点。 自定义仅在对单个 VM 进行故障转移且不使用恢复计划时可用。

    > [!NOTE]
    > 如果在启用复制后将磁盘添加到了 VM，则复制点将显示可用于恢复的磁盘。 例如，在添加第二个磁盘之前创建的复制点会显示为“第 1 个磁盘，共 2 个”。

4. 如果希望 Site Recovery 在开始故障转移之前尝试关闭源 VM，请选择“在开始故障转移之前关闭计算机”。 关闭有助于确保没有数据丢失。 即使关机失败，故障转移也仍会继续。 

    ![“故障转移设置”页](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. 若要启动故障转移，请选择“确定”。
4. 在通知中监视故障转移。

    ![进度通知](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![成功通知](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. 故障转移后，在目标区域中创建的 Azure VM 将显示在“虚拟机”中。 请确保 VM 正在运行，且大小合适。 如果要为 VM 使用不同的恢复点，请在“Essentials”页上选择“更改恢复点” 。
6. 如果对已故障转移的 VM 感到满意，请在“概述”页上选择“提交”，以完成故障转移。

    ![“提交”按钮](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. 在“提交”中，选择“确定”以确认 。 提交会删除 Site Recovery 中 VM 的所有可用恢复点，并且你将无法更改恢复点。

8. 在通知中监视提交进度。

    ![提交进度通知](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![提交成功通知](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. 在故障转移后，Site Recovery 不会清理源 VM。 你需要手动执行此操作。


## <a name="reprotect-the-vm"></a>重新保护 VM

故障转移后，请在次要区域中重新保护 VM，使其复制回主要区域。 

1. 在开始之前，请确保 VM“状态”为“已提交故障转移”。
2. 请检查你是否可以访问主要区域，以及你是否有权在其中创建 VM。
3. 在 VM“概述”页中，选择“重新保护” 。

   ![用于为 VM 启用重新保护的按钮。](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. 在“重新保护”中，验证复制方向（次要区域到主要区域），并查看主要区域的目标设置。 标记为“新”的资源由 Site Recovery 在重新保护操作中创建。

     ![“重新保护设置”页](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. 选择“确定”以开始重新保护过程。 该过程将初始数据发送到目标位置，然后将 VM 的增量信息复制到目标。
7. 在通知中监视重新保护进度。 

    ![重新保护进度通知](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![重新保护成功通知](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>后续步骤

在本教程中，你从主要区域故障转移到了次要区域，并开始将 VM 复制回主要区域。 现在，可以从次要区域故障转移回主要区域。

> [!div class="nextstepaction"]
> [故障回复到主要区域](azure-to-azure-tutorial-failback.md)
