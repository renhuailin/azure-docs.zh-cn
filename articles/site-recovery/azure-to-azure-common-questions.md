---
title: 关于使用 Azure Site Recovery 进行 Azure VM 灾难恢复的常见问题解答
description: 本文介绍了关于使用 Azure Site Recovery 进行 Azure VM 灾难恢复的常见问题解答。
author: sideeksh
manager: rochakm
ms.date: 07/25/2021
ms.topic: conceptual
ms.openlocfilehash: c2fb5175b077275d0c2ef5b0a37f6d4ead76b9d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728579"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常见问题：Azure 到 Azure 的灾难恢复

本文解答有关如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务进行到另一 Azure 区域的 Azure VM 灾难恢复的常见问题。

## <a name="general"></a>常规

### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？

了解 Azure VM 灾难恢复的[费用](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-does-the-free-tier-work"></a>免费层如何工作？

每个使用 Site Recovery 保护的实例在前 31 天均可免费享受保护。 在该时间段后，对每个实例的保护将按照[定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中汇总的费率进行收费。 你可以使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)估算费用。

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>在前 31 天内是否会产生其他 Azure 费用？

是的。 尽管 Azure Site Recovery 在实例受保护的前 31 天是免费的，但你可能需要支付 Azure 存储、存储交易和数据传输费用。 恢复后的 VM 也可能会产生 Azure 计算费用。 G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>VM 灾难恢复如何入门？

1. [了解](azure-to-azure-architecture.md) Azure VM 灾难恢复体系结构。
2. [查看](azure-to-azure-support-matrix.md)支持要求。
3. [设置](azure-to-azure-how-to-enable-replication.md) Azure VM 灾难恢复。
4. 使用测试故障转移[运行灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)。
5. 对次要 Azure 区域[运行完全故障转移](azure-to-azure-tutorial-failover-failback.md)。
6. 从次要区域[故障转移回](azure-to-azure-tutorial-failback.md)主要区域。

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队规划了足够的基础结构容量。 启动故障转移时，团队还会帮助确保将受 Site Recovery 保护的 VM 实例部署到目标区域。

## <a name="replication"></a>复制

### <a name="can-i-replicate-vms-with-disk-encryption"></a>是否可以复制包含磁盘加密的 VM？

是的。 Site Recovery 支持对已启用 Azure 磁盘加密 (ADE) 的 VM 进行灾难恢复。 启用复制后，Azure 会将所有必需的磁盘加密密钥和机密从用户上下文中的源区域复制到目标区域。 如果你没有所需的权限，则安全管理员可以使用脚本来复制密钥和机密。

- Site Recovery 支持运行 Windows 的 Azure VM 的 ADE。
- Site Recovery 支持：
    - ADE 版本 0.1，该版本包含需要 Azure Active Directory (Azure AD) 的架构。
    - ADE 版本 1.1，该版本无需 Azure AD。 对于版本 1.1，Windows Azure VM 必须具有托管磁盘。
    - [了解详细信息](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。 有关扩展架构的信息。

[详细了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何为加密 VM 启用复制。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>是否可以从不同的资源组中选择自动化帐户？

当允许 Site Recovery 为在复制的 Azure VM 上运行的出行服务扩展管理更新时，它会通过 Azure 自动化帐户部署全局 runbook（由 Azure 服务使用）。 可以使用 Site Recovery 创建的自动化帐户，也可以选择使用现有的自动化帐户。

当前，在门户中，只能选择与保管库位于同一资源组中的自动化帐户。 你可以使用 PowerShell 从不同的资源组中选择自动化帐户。 [了解详细信息](azure-to-azure-autoupdate.md#enable-automatic-updates)。

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>如果我使用的客户自动化帐户不在保管库资源组中，是否可以删除默认的 runbook？

是，如果不需要它，可以将其删除。

### <a name="can-i-replicate-vms-to-another-subscription"></a>是否可将 VM 复制到另一个订阅？

是，可以将 Azure VM 复制到同一 Azure AD 租户中的任何订阅。 为 VM 启用灾难恢复时，默认情况下显示的目标订阅是源 VM 的订阅。 你可以修改目标订阅，其他设置（如资源组和虚拟网络）将根据所选的订阅自动填充。

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>是否可以将可用性区域中的 Vm 复制到另一个区域？

是的，可以将可用性区域中的 Vm 复制到另一个 Azure 区域。

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>是否可以将非分区 Vm 复制到同一区域内的区域？

门户中不支持此操作。 可以使用 REST API/PowerShell 来执行此操作。

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>是否可以将分区 Vm 复制到同一区域中的其他区域？

对此操作的支持仅限于几个区域。 [了解详细信息](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

### <a name="can-i-exclude-disks-from-replication"></a>是否可从复制中排除磁盘？

是，你可以在使用 PowerShell 设置复制时排除磁盘。 [了解详细信息](azure-to-azure-exclude-disks.md)。

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>是否可以复制添加到复制的 VM 的新磁盘？

对于具有托管磁盘的复制的 VM，可以添加新磁盘，并为其启用复制。 添加新磁盘时，复制的 VM 将显示一条警告消息，指出 VM 上的一个或多个磁盘可用于保护。

- 如果为添加的磁盘启用复制，此警告会在初次复制后消失。
- 如果你不想为磁盘启用复制，则可以消除警告。
- 如果对添加了磁盘的 VM 进行故障转移，复制点将显示可用于恢复的磁盘。 例如，如果向包含一个磁盘的 VM 添加第二个磁盘，则在添加之前创建的复制点会显示为“第 1 个磁盘，共 2 个”。

Site Recovery 不支持将磁盘从复制的 VM 中“热删除”。 如果删除某个 VM 磁盘，则需先禁用该 VM 的复制，然后为其重新启用复制。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？

将 Azure VM 复制到另一个 Azure 区域时，复制是持续性的。 [详细了解](./azure-to-azure-architecture.md#replication-process)复制的工作原理。

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>是否可以在某个区域中复制虚拟机？

不能使用 Site Recovery 在区域中复制磁盘。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>能否将 VM 实例复制到任意 Azure 区域？

可以在任意两个区域之间复制和恢复 VM。 

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery 是否需要建立 Internet 连接？

不需要，但 VM 需要访问 Site Recovery URL 和 IP 范围。 [了解详细信息](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>是否可以跨资源组分层复制应用程序？

是，你可以复制应用并在另一个资源组中保留灾难恢复配置。

例如，如果应用在不同的资源组中有三个层（应用程序/数据库/web），则需要启用三次复制，才能保护所有层。 Site Recovery 会将这三个层复制到三个不同的资源组中。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>能否跨资源组移动存储帐户？

否，不支持这种情况。 如果你意外地将存储帐户移到不同的资源组并删除了原始资源组，则可以创建一个与旧资源组同名的新资源组，然后将该存储帐户移到此资源组。

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？

复制策略定义恢复点的保留历史记录，以及应用一致性快照的频率。  Site Recovery 创建默认复制策略，如下所示：

- 将恢复点保留 24 小时。
- 每 4 小时捕获一次应用一致性快照。

[详细了解](azure-to-azure-how-to-enable-replication.md#customize-target-resources)复制设置。

### <a name="whats-a-crash-consistent-recovery-point"></a>什么是崩溃一致性恢复点？

崩溃一致性恢复点包含创建快照期间拔下服务器的电源线时磁盘上的数据。 它不包括创建快照时内存中存在的任何数据。

目前，大多数应用都可以从崩溃一致性快照正常恢复。 对于无数据库的操作系统以及应用（如文件服务器、DHCP 服务器、打印服务器），崩溃一致性恢复点通常已足够。

Site Recovery 每隔五分钟自动创建一个崩溃一致性恢复点。

### <a name="whats-an-application-consistent-recovery-point"></a>什么是应用程序一致性恢复点？

应用一致性恢复点是基于应用一致性快照创建的。 它们捕获的数据与崩溃一致性快照相同，此外还会捕获内存中的数据，以及进程中的所有事务。

由于包含额外的内容，应用一致性快照涉及的操作最多，且花费的时间最长。 我们建议对数据库操作系统以及 SQL Server 等应用使用应用一致性恢复点。 对于 Windows，应用一致性快照使用卷影复制服务 (VSS)。

### <a name="do-app-consistent-recovery-points-impact-performance"></a>应用一致性恢复点是否会影响性能？

 因为应用一致性恢复点会捕获内存和进程中的所有数据，如果它们频繁地捕获，则在工作负载已经很繁忙的情况下可能会影响性能。 对于无数据库工作负载，我们建议不要太频繁地进行捕获。 即使对于数据库工作负载，一小时也应该足以满足需要。

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>生成应用一致性恢复点的最小频率是多少？

Site Recovery 可以创建应用一致性恢复点，其最小频率为一小时。

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>是否可以为 Linux VM 启用应用一致性复制？

是的。 适用于 Linux 的移动代理支持用于应用一致性的自定义脚本。 该代理使用带有前置和后置选项的自定义脚本。 [了解详细信息](site-recovery-faq.yml)

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？

为了了解 Site Recovery 如何生成恢复点，让我们使用一个示例。

- 复制策略将恢复点保留 24 小时，并每小时获取一个应用一致性频率快照。
- Site Recovery 每隔五分钟创建一个崩溃一致性恢复点。 你无法更改此频率。
- Site Recovery 在一小时后删除恢复点，每小时保存一个点。

因此，在过去一小时，可以从 12 个崩溃一致性恢复点和 1 个应用一致性恢复点中进行选择，如图所示。

   ![生成的恢复点列表](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？

可以使用的最早恢复点是 72 小时。

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>如果 Site Recovery 无法生成恢复点超过 24 小时，会发生什么情况？

如果复制策略为 24 小时，并且 Site Recovery 无法生成恢复点超过 24 小时，则将保留旧恢复点。 Site Recovery 仅在生成新点时替换最旧的点。 到达保留期之后，在出现新的恢复点之前，所有旧恢复点将会保留。

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>启用复制后，是否可以更改复制策略？

是的。 在保管库 >“Site Recovery 基础结构” > “复制策略”中，选择并编辑策略 。 更改也适用于现有策略。

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>是否所有恢复点都是完整的 VM 副本？

生成的第一个恢复点包含完整副本。 后续恢复点包含增量更改。

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>恢复点保留期的增加是否会增加存储费用？

是的。 例如，如果将保留期从 24 小时增大到 72 小时，则 Site Recovery 会保存额外 48 小时的恢复点。 添加的时间会导致存储更改。 例如，如果单个恢复点包含 10 GB 的增量更改，每 GB 费用为 0.16 美元/月，则每月会产生 1.60 美元 × 48 的额外费用。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什么是多 VM 一致性？

多 VM 一致性可以确保恢复点在复制的虚拟机之间保持一致。

- 当启用多 VM 一致性时，Site Recovery 会创建启用了该选项并包含所有计算机的复制组。
- 对复制组中的计算机进行故障转移时，它们共享崩溃一致性恢复点和应用一致性恢复点。

[了解](azure-to-azure-tutorial-enable-replication.md#enable-replication)如何启用多 VM 一致性。

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>是否可以对复制组中的单个 VM 进行故障转移？

不是。 启用多 VM 一致性时，它会推断应用依赖于复制组中的所有 VM，并且不允许对单个 VM 进行故障转移。

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>可以在一个组中同时复制多少个 VM？

在一个复制组中，可以一同复制 16 个 VM。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何时应启用多 VM 一致性？

由于多 VM 一致性会占用大量 CPU 资源，启用它可能会影响工作负载性能。 仅当 VM 运行相同的工作负载并且需要在多个计算机之间保持一致时才启用。 例如，如果应用程序中有两个 SQL Server 实例和两个 Web 服务器，则只为 SQL Server 实例启用多 VM 一致性。

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>是否可以将复制的 VM 添加到复制组？

为 VM 启用复制时，可以将其添加到新的复制组或现有组中。 无法添加已复制到组中的 VM。

## <a name="failover"></a>故障转移

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队规划了足够的基础结构容量。 启动故障转移时，团队还会帮助确保受 Site Recovery 保护的 VM 实例可以部署到目标区域。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

故障转移不是自动的。 可以在门户中单击一下鼠标来启动故障转移，也可以使用 [PowerShell](azure-to-azure-powershell.md) 来触发故障转移。

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>是否可以在故障转移后保留公共 IP 地址？

无法在故障转移后保留生产应用的公共 IP 地址。

在故障转移过程中启动某个工作负载时，需要为其分配一个 Azure 公共 IP 地址资源。 该资源必须在目标区域中可用。 可以手动分配 Azure 公共 IP 地址资源，也可以使用恢复计划来自动分配。 [了解](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)如何设置故障转移后的公共 IP 地址。

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>是否可以在故障转移后保留专用 IP 地址？

是的。 默认情况下，为 Azure VM 启动灾难恢复时，Site Recovery 将根据源资源设置创建目标资源。 对于配置有静态 IP 地址的 Azure VM，Site Recovery 将尝试对目标 VM 预配相同的 IP 地址（如果未占用）。
[详细了解](site-recovery-retain-ip-azure-vm-failover.md)如何在故障转移后保留 IP 地址。

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>为什么在故障转移后为 VM 分配了新的 IP 地址？

故障转移时，Site Recovery 会尽量提供 IP 地址。 如果该地址被另一个 VM 使用，则 Site Recovery 会将下一个可用 IP 地址设为目标。

[详细了解](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)如何设置虚拟网络的网络映射和 IP 寻址。

### <a name="whats-the-latest-recovery-point"></a>什么是最新恢复点？

“最新(最低 RPO)”恢复点选项执行以下操作：

1. 它首先处理已发送到 Site Recovery 的所有数据。
2. 服务处理数据后，将为每个 VM 创建恢复点，然后故障转移到该 VM。 此选项提供了最低恢复点目标 (RPO)。
3. 故障转移后创建的 VM 包含触发故障转移时复制到 Site Recovery 的所有数据。

### <a name="do-latest-recovery-points-impact-failover-rto"></a>“最新”恢复点是否会影响故障转移 RTO？

是的。 Site Recovery 在故障转移之前需要处理所有挂起的数据，因此，此选项的恢复时间目标 (RTO) 比其他选项更高。

### <a name="whats-the-latest-processed-recovery-option"></a>什么是“最新已处理”恢复选项？

“最新已处理”选项将执行以下操作：

1. 它将所有 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低的 RTO，因为无需费时处理未经处理的数据。

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>如果主要区域发生意外中断怎么办？

可以启动故障转移。 Site Recovery 不需要从主要区域进行连接，即可执行故障转移。

### <a name="what-is-the-rto-of-a-vm-failover"></a>什么是 VM 故障转移的 RTO？

Site Recovery 的 [RTO SLA 为两小时](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 大多数情况下，Site Recovery 会在几分钟内对 VM 进行故障转移。 若要计算 RTO，请查看故障转移作业，该作业显示启动 VM 所需的时间。

## <a name="recovery-plans"></a>恢复计划

### <a name="whats-a-recovery-plan"></a>什么是恢复计划？

Site Recovery 中的[恢复计划](site-recovery-create-recovery-plans.md)可以协调 VM 的故障转移和恢复。 它有助于实现恢复的一致准确性、可重复性和自动化。 此选项执行以下操作：

- 定义一组一起进行故障转移的 VM
- 定义 VM 之间的依赖关系，使应用程序能够适时启动。
- 自动恢复，可以选择对 VM 故障转移以外的任务执行自定义手动操作。


### <a name="how-does-sequencing-work"></a>排序如何工作？

在恢复计划中，可以创建多个 VM 组进行排序。 一次只能对一个组进行故障转移，因此属于同一组的 VM 会一起进行故障转移。 [了解详细信息](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到恢复计划的 RTO？

若要检查恢复计划的 RTO，请对恢复计划执行测试故障转移。 在“Site Recovery 作业”中，检查测试故障转移持续时间。 在示例屏幕截图中，“SAPTestRecoveryPlan”测试故障转移作业耗时 8 分 59 秒。

![列出显示 RTO 的测试故障转移持续时间的作业](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>是否可以将自动化 runbook 添加到恢复计划？

是的。 [了解详细信息](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保护和故障回复

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>故障转移后，次要区域中的 VM 是否自动受到保护？

不是。 将 VM 从一个区域故障转移到另一个区域后，VM 将在目标灾难恢复区域中启动，但处于不受保护状态。 若要[重新保护](./azure-to-azure-how-to-reprotect.md)次要区域中的 VM，可以启用到主要区域的复制。

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>重新保护时，是否将所有数据从次要区域复制到主要区域？

不一定。如果源区域 VM 存在，则只会同步源磁盘与目标磁盘之间的更改。 Site Recovery 将磁盘与不同的磁盘进行比较，然后传输数据。 此过程通常需要几个小时。 [了解详细信息](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)。

### <a name="how-long-does-it-take-fail-back"></a>故障回复需要多长时间？

完成重新保护后，故障回复所需的时间类似于从主要区域故障转移到次要区域所需的时间。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划了足够的基础结构容量。 启动故障转移时，团队还会帮助确保受 Site Recovery 保护的 VM 实例可以部署到目标区域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 是否适用于预留实例？

是，可以在灾难恢复区域中购买[预留 Azure VM](https://azure.microsoft.com/pricing/reserved-vm-instances/)，Site Recovery 故障转移操作使用它们。 不需要任何其他配置。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？

否。Site Recovery 不会截获已复制数据，也不包含 VM 上运行的组件的任何相关信息。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。

Site Recovery 获得了 ISO 27001:2013、27018、HIPAA 和 DPA 认证。 此服务正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？

是，Azure 中的传输中加密和[静态加密](../storage/common/storage-service-encryption.md)均受支持。

## <a name="next-steps"></a>后续步骤

- [查阅 Azure 到 Azure 支持要求](azure-to-azure-support-matrix.md)。
- [设置 Azure 到 Azure 复制](azure-to-azure-tutorial-enable-replication.md)。
- 如果在阅读本文后有任何疑问，请将它们发布在[关于 Azure 恢复服务的 Microsoft Q&A 问题页](/answers/topics/azure-site-recovery.html)上。
