---
title: 使用 Site Recovery 预览版将 VMware VM 故障转移到 Azure
description: 了解如何在 Azure Site Recovery 预览版中将 VMware VM 故障转移到 Azure
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 42ccd96287fefed0a6c7ef4cee8ddf776541c8ba
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446997"
---
# <a name="fail-over-vmware-vms---preview"></a>对 VMware VM 进行故障转移 - 预览版

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 将本地 VMware 虚拟机 (VM) 故障转移到 Azure - 预览版。

有关经典版中故障转移的信息，请参阅[此文章](vmware-azure-tutorial-failover-failback.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 验证 VMware VM 属性是否符合 Azure 要求。
> * 将特定 VM 故障转移到 Azure。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 它们尽可能地使用默认选项，并且不显示所有可能的设置和路径。 若要详细了解故障转移，请参阅[对 VM 和物理服务器进行故障转移](site-recovery-failover.md)。

[了解](failover-failback-overview.md#types-of-failover)不同类型的故障转移。 如果要在恢复计划中对多个 VM 进行故障转移，请查看[本文](site-recovery-failover.md)。

## <a name="before-you-start"></a>开始之前

完成前一篇教程：

1. 确保已[设置 Azure](tutorial-prepare-azure.md)，以便实现 VMware VM 的本地灾难恢复。
2. 准备本地 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 环境，以实现灾难恢复。
3. 为 [VMware VM](vmware-azure-set-up-replication-tutorial-preview.md) 设置灾难恢复。
4. 运行[灾难恢复演练](tutorial-dr-drill-azure.md)，以确保一切按预期方式进行。

## <a name="verify-vm-properties"></a>验证 VM 属性

运行故障转移之前，请检查 VM 属性，确保 VM 符合 [Azure 要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

按如下所述检查属性：

1. 在“受保护的项”中选择“复制的项”，然后选择要验证的 VM   。

2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要  。 选择“属性”以查看更多详细信息  。

3. 可以在“计算和网络”中根据需要修改这些属性  ：
    * Azure 名称
    * 资源组
    * 目标大小
    * 托管磁盘设置

4. 可以查看和修改网络设置，包括：

    * 故障转移后用于放置 Azure VM 的网络和子网。
    * 要分配给它的 IP 地址。

5. 在“磁盘”  中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="run-a-failover-to-azure"></a>运行到 Azure 的故障转移

1. 在“设置” > “复制的项”中选择要故障转移的 VM，然后选择“故障转移”  。
2. 在“故障转移”  中，选择要故障转移到的“恢复点”  。 可以使用以下选项之一：
   * **最新**：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的恢复点目标 (RPO)，因为故障转移后创建的 Azure VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
   * **最新处理**：此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供较低的 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
   * **最新的应用一致**：此选项将 VM 故障转移到由 Site Recovery 处理的最新应用一致恢复点。
   * **自定义**：使用此选项可以指定恢复点。

3. 选择“在开始故障转移之前关闭计算机”，在触发故障转移之前尝试关闭源 VM  。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。 

  在某些情况下，故障转移需要大约 8 到 10 分钟的时间完成其他进程。 对于以下情况，你可能会发现测试故障转移会持续较长时间：

  * VMware Linux VM。
  * VMware VM 未启用 DHCP 服务。
  * VMware VM 不包含以下启动驱动程序：storvsc、vmbus、storflt、intelide、atapi。

  > [!WARNING]
  > 不会取消正在进行的故障转移。 在故障转移开始前，VM 复制已停止。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

## <a name="connect-to-failed-over-vm"></a>连接到故障转移的 VM

1. 如果想在故障转移后通过使用远程桌面协议 (RDP) 和安全外壳 (SSH) 连接到 Azure VM，请[验证是否符合要求](failover-failback-overview.md#connect-to-azure-after-failover)。
2. 故障转移后，请转到该 VM，并通过与它建立[连接](../virtual-machines/windows/connect-logon.md)来进行验证。
3. 若要在故障转移后使用不同的恢复点，请使用“更改恢复点”。  在下一步骤中提交故障转移后，此选项不再可用。
4. 验证后，选择“提交”以确认故障转移后的 VM 恢复点  。
5. 提交后，系统会删除其他所有可用的恢复点。 完成此步骤，就完成了故障转移。

>[!TIP]
> 如果故障转移后遇到任何连接问题，请遵循[故障排除指南](site-recovery-failover-to-azure-troubleshoot.md)予以解决。

## <a name="planned-failover-from-azure-to-on-premises"></a>从 Azure 到本地的计划内故障转移

你可以执行从 Azure 到本地的计划内故障转移。 因为是计划内的故障转移活动，所以是在触发计划内故障转移作业后生成恢复点。

>[!NOTE]
> 在继续操作之前，请确保计算机的复制操作正常。 另请确保设备及其所有组件也正常。

触发计划内故障转移时，系统将挂起的更改复制到本地，生成 VM 的最新恢复点并关闭 Azure VM。 在此之后，本地计算机将打开。

成功完成计划内故障转移后，计算机将在本地环境中处于活动状态。

> [!NOTE]
> 如果受保护的计算机有 iSCSI 磁盘，故障转移时会将该配置保留在 Azure 中。 在进行从 Azure 到本地的计划内故障转移后，无法保留 iSCSI 配置。 所以在本地计算机上创建了 vmdk 磁盘。 若要删除重复磁盘，请删除 iSCSI 磁盘，因为数据的替换由 vmdk 磁盘执行。


### <a name="failed-over-vm-to-azure---requirements"></a>将 VM 故障转移到 Azure 后的要求

将 VM 故障转移到 Azure 后，确保 VM 符合以下要求：

1. 应始终打开 Azure 中的 VM。
2. 确保移动代理服务“服务 1”和“服务 2”均在 VM 上运行 。 这是为了确保 VM 中的移动代理可与 Azure 中的 Azure Site Recovery 服务通信。
3. [这里](vmware-azure-architecture-preview.md#set-up-outbound-network-connectivity)提到的 URL 可从 VM 访问。

## <a name="cancel-planned-failover"></a>取消计划内故障转移

如果本地环境未就绪，或遇到了任何困难，可取消计划内故障转移。以后，一旦本地条件允许，可随时执行此操作。

**取消计划内故障转移**：

1. 导航到恢复服务保管库中的计算机，然后选择“取消故障转移”。
2. 单击 **“确定”** 。
3. 确保已阅读有关如何取消故障转移的信息。

如果有任何妨碍 Azure Site Recovery 成功取消失败的作业的问题，请按照作业中的建议步骤操作。 执行建议的操作后，请重试取消作业。

先前的计划内故障转移操作将被取消。 Azure 中的计算机会返回到触发计划内故障转移之前的状态。

对于计划内故障转移，从设备拆除 VM 磁盘后，我们会在开机前获取其快照。

如果 VM 或某些应用程序未正常启动；或者出于某种原因你决定先取消计划内故障转移再重试，那么：

1. 我们将还原做过的所有更改。

2. 使用之前获取的快照，可将磁盘恢复为开机前的状态。

3. 最后，将磁盘装回设备并继续复制。

此行为不同于旧版/经典版体系结构中的行为。

- 使用预览版，你可以在以后再次执行故障回复操作。

- 在旧版体系结构中，如果 VM 或应用程序未启动，或有任何其他原因，你不能取消并重试故障回复。  


> [!NOTE]
> 只能取消从 Azure 到本地的计划内故障转移。 无法取消从本地到 Azure 的故障转移。

### <a name="planned-failover---failure"></a>计划内故障转移 - 失败

如果计划内故障转移失败，Azure Site Recovery 会自动启动一个作业来取消失败的作业，然后检索计算机在执行计划内故障转移操作前的状态。

如果取消上次计划内故障转移作业失败，Azure Site Recovery 会提示你手动启动取消操作。

计划内故障转移操作失败和复制项存在运行状况问题时，会提供此信息。

如果问题持续出现，请联系 Microsoft 支持部门。 请勿禁用复制。

## <a name="re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover"></a>成功完成计划内故障转移后为 Azure 重新保护本地计算机

成功完成计划内故障转移后，计算机在本地处于活动状态。 若要在将来保护计算机，请确保将计算机复制到 Azure（重新保护）。

为此，请转到“计算机”>“重新保护”，选择想选的设备，再选择复制策略，然后继续操作。

成功启用复制和初始复制后，系统将生成恢复点来提供业务连续性，防止不必要的中断。

## <a name="next-steps"></a>后续步骤

故障转移后，在本地重新保护 Azure VM。 重新保护 VM 并将其复制到本地站点后，如果已准备就绪，请从 Azure 故障回复。

> [!div class="nextstepaction"]
> [重新保护 Azure VM](vmware-azure-reprotect.md)
> [从 Azure 故障回复](vmware-azure-failback.md)
