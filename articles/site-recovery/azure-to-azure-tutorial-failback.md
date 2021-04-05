---
title: 教程：在灾难恢复过程中使用 Azure Site Recovery 将 Azure VM 故障回复到主要区域。
description: 教程：了解有关使用 Azure Site Recovery 将 Azure VM 故障回复到主要区域的信息。
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393859"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>教程：将 Azure VM 故障回复到主要区域

将 Azure VM 故障转移到次要 Azure 区域后，请按照本教程操作，使用 [Azure Site Recovery](site-recovery-overview.md) 将 VM 故障转移到主要 Azure 区域。  在本文中，学习如何：

> [!div class="checklist"]
> 
> * 查看先决条件。
> * 对次要区域中的 VM 进行故障回复。
> * 重新保护主 VM，以便它复制回次要区域。
> 
> [!NOTE]
> 本教程介绍如何通过最少的步骤对 VM 进行故障回复。 如果要使用完整设置运行故障转移，请了解 Azure VM [网络](azure-to-azure-about-networking.md)、[自动化](azure-to-azure-powershell.md)和[故障排除](azure-to-azure-troubleshoot-errors.md)。



## <a name="prerequisites"></a>先决条件

开始本教程之前，应完成以下操作：

1. 至少为一个 Azure VM [设置复制](azure-to-azure-tutorial-enable-replication.md)，并为此尝试进行[灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)。
2. [将 VM](azure-to-azure-tutorial-failover-failback.md) 从主要区域故障转移到次要区域，然后对其进行重新保护，使其从次要区域复制到主要区域。 
3. 检查主要区域是否可用，并且你是否能够在其中创建和访问新资源。

## <a name="fail-back-to-the-primary-region"></a>故障回复到主要区域

重新保护 VM 后，可根据需要故障回复到主要区域。

1. 在保管库中 >“复制的项”，选择 VM  。

2. 在运行故障转移之前，请在 VM 概述页面上，检查虚拟机是否正常运行以及同步是否完成。 VM 应处于受保护状态。

    ![显示处于受保护状态的 VM 的 VM 概述页面](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. 在概述页面上，选择“故障转移”。 由于这次未进行测试故障转移，系统会提示进行验证。

    [显示同意在不进行测试故障转移的情况下运行故障转移的页面](./media/azure-to-azure-tutorial-failback/no-test.png)

4. 在“故障转移”中，记下从次要区域到主要区域的方向，然后选择某个恢复点。 使用来自此点的数据在目标（主要区域）中创建 Azure VM。
   - **最新处理**：使用由 Site Recovery 处理的最新恢复点。 将显示时间戳。 无需费时处理数据，因此恢复时间目标 (RTO) 会较低。
   -  **最新**：处理发送到 Site Recovery 的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 提供最低的恢复点目标 (RPO)，因为触发故障转移时，所有数据都将复制到 Site Recovery 中。
   - **最新的应用一致**：此选项将 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
   - **自定义**：故障转移到特定的恢复点。 自定义仅在对单个 VM 进行故障转移且不使用恢复计划时可用。

    > [!NOTE]
    > 如果在为 VM 启用复制后对添加了磁盘的 VM 进行故障转移，则复制点将显示可用于恢复的磁盘。 例如，在添加第二个磁盘之前创建的复制点会显示为“第 1 个磁盘，共 2 个”。

4. 如果希望 Site Recovery 在开始故障转移之前尝试关闭源 VM，请选择“在开始故障转移之前关闭计算机”。 关闭有助于确保没有数据丢失。 即使关机失败，故障转移也仍会继续。 

    ![“故障转移设置”页](./media/azure-to-azure-tutorial-failback/failover.png)    

3. 若要启动故障转移，请选择“确定”。
4. 在通知中监视故障转移。

    ![故障转移进度通知](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![故障转移成功通知](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>重新保护 VM

将 VM 故障回复到主要区域后，需要重新保护它们，以便它们再次开始复制到次要区域。

1. 在 VM 的“概述”页面上，选择“重新保护” 。

    ![用于重新保护主要区域的按钮](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. 查看主要区域的目标设置。 标记为“新”的资源由 Site Recovery 在重新保护操作中创建。
3. 选择“确定”以开始重新保护过程。 该过程将初始数据发送到目标位置，然后将 VM 的增量信息复制到目标。

     ![显示复制设置的页面](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. 在通知中监视重新保护进度。 

    ![重新保护进度通知](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Reprotect progress notification](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>清理资源

对于具有托管磁盘的 VM，在完成故障回复并重新保护 VM 以实现从主要区域复制到次要区域之后，Site Recovery 会自动清理次要灾难恢复区域中的计算机。 无需手动删除次要区域中的 VM 和 NIC。 不清理具有非托管磁盘的 VM。

如果在故障回复后完全禁用复制，则 Site Recovery 会清理受其保护的计算机。 在这种情况下，它还会为不使用托管磁盘的 VM 清理磁盘。 
 
## <a name="next-steps"></a>后续步骤

本教程已将 VM 从次要区域故障回复到了主要区域。 这是该过程的最后一步，其中包括为 VM 启用复制、尝试进行灾难恢复演练、从主要区域故障转移到次要区域，最后进行故障回复。

> [!div class="nextstepaction"]
> 现在，尝试将[本地 VM ](vmware-azure-tutorial-prepare-on-premises.md) 灾难恢复到 Azure

