---
title: 关于使用 Azure Site Recovery 进行 Azure VM 灾难恢复的常见问题解答
description: 本文介绍了关于使用 Azure Site Recovery 进行 Azure VM 灾难恢复的常见问题解答。
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397939"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常见问题：Azure 到 Azure 的灾难恢复

本文解答了有关使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 Azure vm 灾难恢复到另一个 azure 区域的常见问题。

## <a name="general"></a>常规

### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？

了解 Azure VM 灾难恢复的 [成本](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) 。

### <a name="how-does-the-free-tier-work"></a>免费层如何工作？

每个受 Site Recovery 保护的实例在前31天内都是免费的。 在该时间段后，对每个实例的保护将按 [定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中汇总的费率计算。 可以使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)估算成本。

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>是否在前31天内产生其他 Azure 费用？

是的。 尽管 Azure Site Recovery 在实例受保护的前 31 天是免费的，但你可能需要支付 Azure 存储、存储交易和数据传输费用。 恢复的 VM 也可能产生 Azure 计算费用。 G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>如何实现 Azure VM 灾难恢复入门？

1. [了解](azure-to-azure-architecture.md) Azure VM 灾难恢复体系结构。
2. [查看](azure-to-azure-support-matrix.md)支持要求。
3. 为 Azure Vm[设置](azure-to-azure-how-to-enable-replication.md)灾难恢复。
4. 使用测试故障转移[运行灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)。
5. 对辅助 Azure 区域[运行完全故障转移](azure-to-azure-tutorial-failover-failback.md)。
6. [故障回复](azure-to-azure-tutorial-failback.md) 到主要区域的次要区域。

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划足够的基础结构容量。 当你开始故障转移时，团队还有助于确保受 Site Recovery 保护的 VM 实例部署到目标区域。

## <a name="replication"></a>复制

### <a name="can-i-replicate-vms-with-disk-encryption"></a>是否可以复制具有磁盘加密的 Vm？

是的。 Site Recovery 支持启用了 Azure 磁盘加密 (ADE) Vm 的灾难恢复。 启用复制时，Azure 会将所有所需的磁盘加密密钥和机密从源区域复制到用户上下文中的目标区域。 如果没有所需的权限，安全管理员可以使用脚本来复制密钥和机密。

- Site Recovery 支持运行 Windows 的 Azure Vm 的 ADE。
- Site Recovery 支持：
    - ADE 版本0.1，其架构需要 Azure Active Directory (Azure AD) 。
    - ADE 版本1.1，不需要 Azure AD。 对于版本1.1，Windows Azure Vm 必须具有托管磁盘。
    - [了解详细信息](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。 有关扩展架构的信息。

[详细了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何为加密 VM 启用复制。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>是否可以从其他资源组中选择自动化帐户？

当你允许 Site Recovery 管理在复制的 Azure Vm 上运行的移动服务扩展的更新时，它将通过 Azure automation 帐户部署 Azure 服务) 使用的全局 runbook (。 可以使用 Site Recovery 创建的自动化帐户，也可以选择使用现有的自动化帐户。 

目前，在门户中，只能在与保管库相同的资源组中选择一个自动化帐户。 你可以使用 PowerShell 从不同的资源组中选择一个自动化帐户。 [了解详细信息](azure-to-azure-autoupdate.md#enable-automatic-updates)。

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>如果我使用的客户自动化帐户不在保管库资源组中，是否可以删除默认 runbook？

是的，如果不需要，可以将其删除。 

### <a name="can-i-replicate-vms-to-another-subscription"></a>是否可将 VM 复制到另一个订阅？

是的，可以将 Azure Vm 复制到同一 Azure AD 租户中的任何订阅。 为 Vm 启用灾难恢复时，默认情况下，显示的目标订阅为源 VM 的。 您可以修改目标订阅，并从所选订阅中自动填充资源组和虚拟网络) 等 (的其他设置。

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>是否可以将可用性区域中的 Vm 复制到另一个区域？

是的，可以将可用性区域中的 Vm 复制到其他 Azure 区域。 可以将目标 VM 部署为可用性集中的单个实例，也可以部署在可用性区域（如果在目标区域中受支持）。 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>是否可以将非区域 Vm 复制到同一区域内的区域？ 

门户不支持此项。 你可以使用 REST API/PowerShell 来执行此操作。

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>是否可以将分区 Vm 复制到同一区域中的其他区域？

对此的支持仅限于几个区域。 [了解详细信息](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

### <a name="can-i-exclude-disks-from-replication"></a>是否可从复制中排除磁盘？

是的，使用 PowerShell 设置复制时，可以排除磁盘。 [了解详细信息](azure-to-azure-exclude-disks.md)。

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>是否可以复制添加到复制 Vm 的新磁盘？

对于包含托管磁盘的复制 Vm，你可以添加新磁盘并为其启用复制。 添加新磁盘时，复制的 VM 会显示一条警告消息，指出 VM 上的一个或多个磁盘可用于保护。 

- 如果为添加的磁盘启用复制，则在初始复制后会出现警告。
- 如果你不想为磁盘启用复制，则可以关闭此警告。
- 如果使用添加的磁盘对 VM 进行故障转移，复制点将显示可用于恢复的磁盘。 例如，如果将第二个磁盘添加到包含一个磁盘的 VM，则在添加之前创建的复制点会显示为 "1 个，共2个磁盘"。

Site Recovery 不支持复制的 VM 中的磁盘 "热删除"。 如果删除 VM 磁盘，则需要先禁用 VM 的复制，然后再重新启用它。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？

将 Azure VM 复制到另一个 Azure 区域时，复制是持续性的。 [详细了解](./azure-to-azure-architecture.md#replication-process) 复制的工作原理。

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>是否可以在某个区域中复制虚拟机？ 

不能使用 Site Recovery 来复制区域内的磁盘。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>能否将 VM 实例复制到任意 Azure 区域？

可以在同一地理群集中的任意两个区域之间复制和恢复 VM。 地理群集的定义考虑到了数据延迟和主权。 [详细了解](./azure-to-azure-support-matrix.md#region-support) 区域支持。

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery 是否需要 internet 连接？

不需要，但 Vm 需要访问 Site Recovery Url 和 IP 范围。 [了解详细信息](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>能否跨资源组复制应用程序？

是的，可以复制应用，并将灾难恢复配置保留在单独的资源组中。

例如，如果应用在不同资源组中的应用程序/数据库/web)  (三个层，则需要启用复制三次，以保护所有层。 Site Recovery 将三个层复制到三个不同的资源组中。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>能否跨资源组移动存储帐户？

否，不支持这种情况。 如果意外将存储帐户移动到不同的资源组并删除原始资源组，则可以创建一个与旧资源组同名的新资源组，然后将该存储帐户移到此资源组。

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？

复制策略定义恢复点的保留历史记录和应用一致快照的频率。  Site Recovery 创建默认复制策略，如下所示：

- 保留恢复点24小时。
- 每隔四小时获取应用一致性快照。

[了解](azure-to-azure-how-to-enable-replication.md#customize-target-resources) 有关复制设置的详细信息。

### <a name="whats-a-crash-consistent-recovery-point"></a>崩溃一致的恢复点是什么？

崩溃一致恢复点包含磁盘上的数据，就像在快照期间从服务器中拔下电源线一样。 它不包括创建快照时内存中存在的任何数据。

如今，大多数应用都可以从崩溃一致的快照中很好地进行恢复。 崩溃一致的恢复点通常足以满足非数据库操作系统的情况，以及文件服务器、DHCP 服务器和打印服务器等应用。

Site Recovery 每五分钟自动创建一次崩溃一致的恢复点。

### <a name="whats-an-application-consistent-recovery-point"></a>什么是应用程序一致的恢复点？

应用一致性恢复点是基于应用一致性快照创建的。 它们捕获与崩溃一致的快照相同的数据，此外还捕获内存中的数据和所有正在处理的事务。

由于有额外的内容，因此最常涉及到与应用一致的快照，并花费的时间最长。 建议为数据库操作系统和应用（如 SQL Server）应用一致的恢复点。 对于 Windows，应用一致的快照使用 (VSS) 卷影复制服务。

### <a name="do-app-consistent-recovery-points-impact-performance"></a>应用一致的恢复点是否会影响性能？

 由于应用一致的恢复点捕获内存和进程中的所有数据，如果这些数据经常捕获，则它可能会影响工作负荷已繁忙时的性能。 对于非数据库工作负荷，建议不要过于频繁地捕获。 即使对于数据库工作负荷，一小时也应该足以满足需要。

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>生成应用一致的恢复点的最小频率是多少？

Site Recovery 可以创建最小频率为1小时的应用一致的恢复点。

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>能否为 Linux Vm 启用应用一致性复制？

是的。 适用于 Linux 的移动代理支持用于应用一致性的自定义脚本。 代理使用带有 pre 和 post 选项的自定义脚本。 [了解详细信息](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？

要了解 Site Recovery 如何生成恢复点，请使用示例。 

- 复制策略将恢复点保留24小时，并每隔一小时获取一个应用一致性频率快照。
- Site Recovery 每五分钟创建一个崩溃一致的恢复点。 你无法更改此频率。
- Site Recovery 在一小时后修剪恢复点，每小时保存一个点。

因此，在过去的一个小时内，可以选择12个故障一致性点和一个应用一致的点，如图中所示。

   ![生成的恢复点列表](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？

可以使用的最早恢复点是 72 小时。

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>如果 Site Recovery 不能生成超过24小时的恢复点，会发生什么情况？ 

如果复制策略为24小时，并且 Site Recovery 无法生成超过24小时的恢复点，则旧恢复点将保留。 如果生成新点，Site Recovery 仅替换最早的点。 在出现新的恢复点之前，你会在到达保留期窗口后保留所有旧点。

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>是否可以在启用复制后更改复制策略？

是的。 在保管库 > **Site Recovery 基础结构**  >  **复制策略** 中，选择并编辑策略。 更改也适用于现有策略。

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>所有恢复点都是完整的 VM 副本？

生成的第一个恢复点包含完整副本。 连续恢复点有增量更改。

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>恢复点保留期增加会增加存储成本吗？

是的。 例如，如果将保留时间从24小时增加到72，Site Recovery 会将恢复点保存额外48小时。 添加的时间会导致存储更改。 例如，如果单个恢复点有 10 GB 的增量更改，每月每 GB 的成本为 $0.16，则额外费用将是每月 $1.60 ×48。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什么是多 VM 一致性？

多 VM 一致性可确保跨复制虚拟机的恢复点保持一致。

- 启用多 VM 一致性时，Site Recovery 会创建启用了选项的所有计算机的复制组。 
- 当你对复制组中的计算机进行故障转移时，它们具有共享的崩溃一致和应用一致的恢复点。

[了解](azure-to-azure-tutorial-enable-replication.md#enable-replication) 如何启用多 VM 一致性。

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>能否故障转移复制组中的单个 VM？

不是。 启用多 VM 一致性后，它将推断应用程序依赖于复制组中的所有 Vm，不允许单个 VM 故障转移。

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>可以在一个组中同时复制多少个 VM？

可在复制组中将16个 Vm 复制到一起。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何时应启用多 VM 一致性？

多 VM 一致性是 CPU 密集型，启用此一致性可能会影响工作负荷性能。 仅当 Vm 运行相同的工作负荷并且需要跨多台计算机的一致性时，才启用。 例如，如果应用程序中有两个 SQL Server 实例和两个 web 服务器，则只为 SQL Server 实例启用多 VM 一致性。

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>是否可以将复制 VM 添加到复制组？

为 VM 启用复制时，可以将其添加到新的复制组或现有组中。 无法添加已复制到组中的 VM。 
 
## <a name="failover"></a>故障转移

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划足够的基础结构容量。 启动故障转移时，团队还有助于确保受 Site Recovery 保护的 VM 实例可以部署到目标区域。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

故障转移不是自动的。 可以在门户中单击一次，或使用  [PowerShell](azure-to-azure-powershell.md) 来触发故障转移。

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>故障转移后是否可以保留公共 IP 地址？

故障转移后，不能保留生产应用的公共 IP 地址。

当你在故障转移过程中引入工作负荷时，需要为其分配一个 Azure 公共 IP 地址资源。 该资源必须在目标区域中可用。 你可以手动分配 Azure 公共 IP 地址资源，或者可以使用恢复计划自动执行。 [了解](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) 如何在故障转移后设置公共 IP 地址。

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>故障转移后是否可以保留专用 IP 地址？

是的。 默认情况下，当你启用 Azure Vm 的灾难恢复时，Site Recovery 会根据源资源设置创建目标资源。 对于配置有静态 IP 地址的 Azure VM，Site Recovery 将尝试对目标 VM 预配相同的 IP 地址（如果未占用）。
[详细了解如何](site-recovery-retain-ip-azure-vm-failover.md) 在故障转移后保留 IP 地址。

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>为什么 VM 在故障转移后分配了新的 IP 地址？

故障转移时，Site Recovery 会尽量提供 IP 地址。 如果其他 VM 使用该地址，Site Recovery 会将下一个可用 IP 地址设置为目标。

[详细了解如何](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) 设置虚拟网络的网络映射和 IP 寻址。

### <a name="whats-the-latest-recovery-point"></a>*最近* 的恢复点是什么？

*最新 (最低 RPO)* 恢复点 "选项执行以下操作：

1. 它首先处理已发送到 Site Recovery 的所有数据。
2. 该服务处理数据后，会在故障转移到 VM 之前为每个 VM 创建一个恢复点。 此选项提供了最低恢复点目标 (RPO)。
3. 故障转移后创建的 VM 会在触发故障转移时，将所有数据复制到 Site Recovery。

### <a name="do-latest-recovery-points-impact-failover-rto"></a>*最近* 的恢复点是否会影响故障转移 RTO？

是的。 Site Recovery 在故障转移之前处理所有挂起的数据，因此此选项的恢复时间目标 (RTO) 比其他选项更高。

### <a name="whats-the-latest-processed-recovery-option"></a>*最新* 的已处理恢复选项是什么？

*最新处理* 的选项将执行以下操作：

1. 它会将所有 Vm 故障转移到 Site Recovery 处理的最新恢复点。 此选项提供低的 RTO，因为无需费时处理未经处理的数据。

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>如果主要区域发生意外中断，会发生什么情况？

可以启动故障转移。 Site Recovery 不需要从主要区域进行连接，即可执行故障转移。

### <a name="what-is-the-rto-of-a-vm-failover"></a>VM 故障转移的 RTO 是什么？

Site Recovery 的 RTO SLA 为 [两小时](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 大多数情况下，Site Recovery 会在几分钟内对 Vm 进行故障转移。 若要计算 RTO，请查看故障转移作业，其中显示了启动 VM 所需的时间。 

## <a name="recovery-plans"></a>恢复计划

### <a name="whats-a-recovery-plan"></a>什么是恢复计划？

Site Recovery 中的 [恢复计划](site-recovery-create-recovery-plans.md) 将对 vm 的故障转移和恢复进行协调。 它有助于使恢复的准确性、可重复性和自动化。 此选项执行以下操作：

- 定义一组一起故障转移的 Vm
- 定义 Vm 之间的依赖关系，以便应用程序能够正确地提供。
- 自动恢复，并为除 VM 故障转移以外的其他任务选择自定义手动操作。 


### <a name="how-does-sequencing-work"></a>序列化的工作原理

在恢复计划中，可以为排序创建多组虚拟机。 组一次发生故障转移，以便同一组中的 Vm 一起进行故障转移。 [了解详细信息](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到恢复计划的 RTO？

若要检查恢复计划的 RTO，请对恢复计划执行测试故障转移。 在 **Site Recovery 作业** 中，检查 "测试故障转移持续时间"。 在示例屏幕截图中， **SAPTestRecoveryPlan** 测试故障转移作业花费了8分钟到59秒。

![列出用于显示 RTO 的测试故障转移持续时间的作业](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>能否将自动化 runbook 添加到恢复计划？

是的。 [了解详细信息](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保护和故障回复

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>故障转移后，辅助区域中的 Vm 是否自动受到保护？ 

不是。 将 Vm 从一个区域故障转移到另一个区域时，Vm 在目标灾难恢复区域中启动时处于不受保护的状态。 若要重新保护次要区域中 [的 vm，](./azure-to-azure-how-to-reprotect.md) 请启用复制回主要区域。

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>重新保护时，是否将所有数据从次要区域复制到主要区域？ 

它依赖于。如果源区域 VM 存在，则只同步源磁盘与目标磁盘之间的更改。 Site Recovery 将磁盘与不同的磁盘进行比较，然后传输数据。 此过程通常需要几个小时。 [了解详细信息](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)。

### <a name="how-long-does-it-take-fail-back"></a>故障回复需要多长时间？

重新保护之后，故障回复所需的时间与从主要区域故障转移到次要区域花费的时间相同。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划了足够的基础结构容量。 启动故障转移时，团队还有助于确保受 Site Recovery 保护的 VM 实例可以部署到目标区域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 是否适用于预留实例？

是的，你可以在灾难恢复区域中购买 [保留的 Azure vm](https://azure.microsoft.com/pricing/reserved-vm-instances/) ，Site Recovery 故障转移操作使用它们。 不需要任何其他配置。

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
