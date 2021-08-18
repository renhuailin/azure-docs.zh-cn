---
title: 高可用性、灾难恢复、业务连续性
description: 了解适用于 Azure VM 上的 SQL Server 的高可用性、灾难恢复 (HADR) 和业务连续性选项，例如 AlwaysOn 可用性组、故障转移群集实例、数据库镜像、日志传送以及对 Azure 存储的备份和还原。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: a5fe3157d419b199f1e83f44485e507812c0b80c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434791"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>适用于 Azure 虚拟机上的 SQL Server 的业务连续性和 HADR
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

业务连续性意味着在发生灾难时继续你的业务、规划恢复以及确保数据高度可用。 Azure 虚拟机上的 SQL Server 可以帮助降低高可用性和灾难恢复 (HADR) 数据库解决方案的成本。 

虚拟机 (VM) 支持大多数 SQL Server HADR 解决方案，这些解决方案既可以仅限于 Azure，也可以是混合解决方案。 在仅包含 Azure 的解决方案中，整个 HADR 系统都在 Azure 中运行。 而在混合配置中，解决方案的一部分在 Azure 中运行，另一部分则在组织的本地运行。 Azure 环境具有灵活性，允许部分或完全迁移至 Azure，以满足 SQL Server 数据库系统对于预算和 HADR 的要求。

本文比较和对比了适用于 Azure VM 上的 SQL Server 的业务连续性解决方案。 

## <a name="overview"></a>概述

你有责任确保数据库系统拥有服务级别协议 (SLA) 要求的 HADR 功能。 Azure 提供了高可用性机制，例如云服务的服务修复和虚拟机的故障恢复检测，但这一事实自身并不保证你能够达到 SLA 的要求。 虽然这些机制可以帮助保护虚拟机的高可用性，但不能保护在 VM 内部运行的 SQL Server 的可用性。 

VM 联机并正常运行时，SQL Server 实例也可能会出故障。 即便是 Azure 提供的高可用性机制，也会在 VM 遇到从软件或硬件故障进行恢复、操作系统升级等事件时，为其留出一定的停机时间。

Azure 中的异地冗余存储 (GRS) 是通过一个名为异地复制的功能实现的。 GRS 可能不是适合你的数据库的灾难恢复解决方案。 因为异地复制异步发送数据，所以最近的更新可能会在灾难中丢失。 有关异地复制限制的详细信息，请阅读[异地复制支持](#geo-replication-support)部分。

> [!NOTE]
> 现在，可以使用 Azure Migrate 将[故障转移群集实例](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)和[可用性组](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)解决方案直接迁移到 Azure VM 上的 SQL Server。 


## <a name="deployment-architectures"></a>部署体系结构
Azure 支持以下 SQL Server 技术以实现业务连续性：

* [AlwaysOn 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On 故障转移群集实例 (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [日志传送](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [使用 Azure Blob 存储执行 SQL Server 备份和还原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [数据库镜像](/sql/database-engine/database-mirroring/database-mirroring-sql-server) - SQL Server 2016 中已弃用
* [Azure Site Recovery](../../../site-recovery/site-recovery-sql.md)

可将多种技术配合使用，以实现具有高可用性和灾难恢复功能的 SQL Server 解决方案。 根据所用技术的不同，混合部署可能需要使用 VPN 隧道连接 Azure 虚拟网络。 以下部分显示了某些部署体系结构的示例。

## <a name="azure-only-high-availability-solutions"></a>仅限 Azure：高可用性解决方案

可针对在数据库级别具有 Always On 可用性组的 SQL Server 提供高可用性解决方案。 还可以在具有 Always On 故障转移群集实例的实例级别创建高可用性解决方案。 对于其他保护措施，可以通过在故障转移群集实例上创建可用性组，在两个级别上创建冗余。 

| 技术 | 示例体系结构 |
| --- | --- |
| **可用性组** |在同一区域的 Azure VM 中运行的可用性副本提供高可用性。 需要配置域控制器 VM，因为 Windows 故障转移群集需要 Active Directory 域。<br/><br/> 为了实现更高的冗余性和可用性，可以按照[可用性组概述](availability-group-overview.md)中的说明，将 Azure VM 部署在不同的[可用性区域](../../../availability-zones/az-overview.md)中。 如果已在可用性区域中部署可用性组中的 SQL Server VM，则将 [Azure 标准负载均衡器](../../../load-balancer/load-balancer-overview.md)用于侦听器，如 [Azure SQL VM CLI](./availability-group-az-commandline-configure.md) 和 [Azure 快速入门模板](availability-group-quickstart-template-configure.md)文章中所述。<br/> ![示意图中在“WSFC 群集”（由“主要副本”、“次要副本”和“文件共享见证”组成）上方显示了“域控制器”。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>有关详细信息，请参阅[在 Azure 中配置可用性组 (GUI)](./availability-group-quickstart-template-configure.md)。 |
| **故障转移群集实例** |SQL Server VM 支持故障转移群集实例。 由于 FCI 功能需要共享存储，因此，可以在 Azure VM 上的 SQL Server 中使用五个解决方案： <br/><br/> - 对于 Windows Server 2019，使用 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)。 共享托管磁盘是一种允许同时将托管磁盘附加到多个虚拟机的 Azure 产品。 群集中的 VM 可以根据群集应用程序通过 SCSI 永久预留 (SCSI PR) 选择的预留在附加的磁盘中进行读取或写入。 SCSI PR 是一种行业标准的存储解决方案，可供在本地存储区域网络 (SAN) 上运行的应用程序使用。 在托管磁盘上启用 SCSI PR，可以将这些应用程序按原样迁移到 Azure。 <br/><br/>- 对于 Windows Server 2016 及更高版本，使用[存储空间直通\( S2D\)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 提供基于软件的虚拟 SAN。<br/><br/>- 对于 Windows Server 2012 及更高版本，使用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)。 高级文件共享由 SSD 提供支持，延迟稳定且较低，完全支持用于 FCI。<br/><br/>- 对于群集功能，使用合作伙伴解决方案支持的存储。 有关使用 SIOS DataKeeper 的具体示例，请参阅博客文章：[故障转移群集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。<br/><br/>- 对于经由 Azure ExpressRoute 的远程 iSCSI 目标，使用共享块存储。 例如，NetApp 专用存储 (NPS) 使用 Equinix 通过 ExpressRoute 向 Azuer VM 公开 iSCSI 目标。<br/><br/>对于 Microsoft 合作伙伴提供的共享存储和数据复制解决方案，如有任何关于在故障转移时访问数据的问题，请联系供应商。<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>仅限 Azure：灾难恢复解决方案
可将可用性组、数据库镜像或备份和还原与存储 Blob 配合使用，为 Azure 中的 SQL Server 数据库提供灾难恢复解决方案。

| 技术 | 示例体系结构 |
| --- | --- |
| **可用性组** |可用性副本在 Azure VM 中跨多个数据中心运行以实现灾难恢复。 这种跨区域解决方案可以帮助防止站点完全中断。 <br/> ![示意图中显示了两个区域，其中包含通过“异步提交”连接的“主要副本”和“次要副本”。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>在一个区域内，所有副本应该位于同一云服务和同一虚拟网络中。 由于每个区域会有单独的虚拟网络，因此这些解决方案需要网络间连接。 有关详细信息，请参阅[使用 Azure 门户配置网络间连接](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 有关详细说明，请参阅[在不同的 Azure 区域中配置 SQL Server Always On 可用性组](availability-group-manually-configure-multiple-regions.md)。|
| **数据库镜像** |主体和镜像以及服务器在不同数据库中运行以实现灾难恢复。 必须使用服务器证书对它们进行部署。 Azure VM 上的 SQL Server 2008 和 SQL Server 2008 R2 都不支持 SQL Server 数据库镜像。 <br/>![示意图显示了一个区域中的“主体”通过“高性能”模式连接到另一个区域中的“镜像”。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **使用 Azure Blob 存储进行备份和还原** |生产数据库直接备份到不同数据中心内的 Blob 存储以实现灾难恢复。<br/>![示意图显示了一个区域中的“数据库”备份到另一个区域中的“Blob 存储”。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>有关详细信息，请参阅 [Azure VM 中 SQL Server 的备份和还原](../../../azure-sql/virtual-machines/windows/backup-restore.md)。 |
| 使用 Azure Site Recovery 将 SQL Server 复制和故障转移到 Azure |一个 Azure 数据中心的生产 SQL Server 实例直接复制到其他 Azure 数据中心中的 Azure 存储以实现灾难恢复。<br/>![示意图显示了一个 Azure 数据中心内的“数据库”使用“ASR 复制”在另一个数据中心内进行灾难恢复。 ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>有关详细信息，请参阅[使用 SQL Server 灾难恢复和 Azure Site Recovery 来保护 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="hybrid-it-disaster-recovery-solutions"></a>混合 IT：灾难恢复解决方案
可使用可用性组、数据库镜像、日志传送以及备份和还原与 Azure Blob 存储配合使用，在混合 IT 环境中为 SQL Server 数据库提供灾难恢复解决方案。

| 技术 | 示例体系结构 |
| --- | --- |
| **可用性组** |某些可用性副本运行在 Azure VM 中，另一些则在本地运行，以实现跨站点灾难恢复。 生产站点可位于本地，也可以位于 Azure 数据中心。<br/>![可用性组](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>由于所有可用性副本必须在同一故障转移群集中，因此该群集必须同时跨越这两个网络（多子网故障转移群集）。 此配置需要在 Azure 与本地网络之间使用 VPN 连接。<br/><br/>为了成功地对数据库进行灾难恢复，还应在灾难恢复站点上安装副本域控制器。|
| **数据库镜像** |一个合作伙伴在 Azure VM 中运行，另一个则在本地运行，以实现使用服务器证书进行跨站点灾难恢复。 合作伙伴不必在同一 Active Directory 域中，并且不需要 VPN 连接。<br/>![数据库镜像](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>另一种数据库镜像方案是一个合作伙伴在 Azure VM 中运行，另一个则在同一 Active Directory 域中本地运行，以实现跨站点灾难恢复。 需要[在 Azure 虚拟网络与本地网络之间使用 VPN 连接](../../../vpn-gateway/tutorial-site-to-site-portal.md)。<br/><br/>为了成功地对数据库进行灾难恢复，还应在灾难恢复站点上安装副本域控制器。 Azure VM 上的 SQL Server 2008 和 SQL Server 2008 R2 都不支持 SQL Server 数据库镜像。 |
| **日志传送** |一个服务器在 Azure VM 中运行，另一个则在本地运行，以实现跨站点灾难恢复。 日志传送依赖于 Windows 文件共享，因此需要在 Azure 虚拟网络与本地网络之间使用 VPN 连接。<br/>![日志传送](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>为了成功地对数据库进行灾难恢复，还应在灾难恢复站点上安装副本域控制器。 |
| **使用 Azure Blob 存储进行备份和还原** |本地生产数据库直接备份到 Azure Blob 存储以实现灾难恢复。<br/>![备份和还原](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>有关详细信息，请参阅 [Azure 虚拟机上 SQL Server 的备份和还原](../../../azure-sql/virtual-machines/windows/backup-restore.md)。 |
| 使用 Azure Site Recovery 将 SQL Server 复制和故障转移到 Azure |本地生产 SQL Server 实例直接复制到 Azure 存储以实现灾难恢复。<br/>![使用 Azure Site Recovery 进行复制](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>有关详细信息，请参阅[使用 SQL Server 灾难恢复和 Azure Site Recovery 来保护 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="free-dr-replica-in-azure"></a>Azure 中的免费 DR 副本

如果具有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，可以使用 SQL Server 实现混合灾难恢复 (DR) 计划，且不会因被动灾难恢复实例产生额外的许可费用。

例如，可以拥有两个免费被动次要副本，前提是所有三个副本全在 Azure 中托管： 

![拥有两个免费被动副本，前提是所有副本都托管在 Azure 中](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

也可以在 Azure 中配置一个混合故障转移环境，其中包含一个许可的主要本地副本、一个用于实现 HA 的免费被动副本、一个用于 DR 的免费本地被动副本和一个用于 DR 的免费被动副本：

![环境是混合环境且具有一个主要本地副本时配置三个免费被动副本](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

有关详细信息，请参阅[产品许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 

要启用此权益，请转到 [SQL Server 虚拟机资源](manage-sql-vm-portal.md#access-the-resource)。 在“设置”下选择“配置”，然后在“SQL Server 许可证”下选择“灾难恢复”   。 选中复选框以确认此 SQL Server VM 将用作被动副本，然后选择“应用”以保存设置。 

![在 Azure 中配置灾难恢复副本](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>有关 Azure 中的 SQL Server HADR 的重要注意事项
Azure VM、存储和网络的运行特征与本地非虚拟化的 IT 基础结构不同。 需要了解这些区别并设计可适应这些区别的解决方案，才能成功地在 Azure 中实现 HADR SQL Server 解决方案。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性集中的高可用性节点
使用 Azure 中的可用性集，可以将高可用性节点放置在单独的容错域和更新域中。 Azure 平台为可用性集中的每个虚拟机分配一个更新域和一个容错域。 数据中心内的这种配置可以确保在发生计划内或计划外维护事件时，至少有一个虚拟机可用，并满足 99.95% 的 Azure SLA 要求。 

若要配置高可用性设置，请将所有参与的 SQL Server 虚拟机放在同一可用性集中，以避免在维护事件期间丢失应用程序或数据。 只有同一云服务中的节点可加入同一可用性集。 有关详细信息，请参阅[管理虚拟机的可用性](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="high-availability-nodes-in-an-availability-zone"></a>可用性区域中的高可用性节点
可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源以及散热和网络设备。 区域内可用性区域的物理隔离通过确保至少有一个虚拟机可用且 Azure SLA 的可用性达 99.99%，来帮助保护应用程序和数据免受数据中心故障的影响。 

若要配置高可用性，请将参与的 SQL Server 虚拟机分散到该区域中的各个可用性区域。 可用性区域之间的网络到网络传输会产生额外的费用。 有关详细信息，请参阅[可用性区域](../../../availability-zones/az-overview.md)。 

### <a name="network-latency-in-hybrid-it"></a>混合 IT 环境中的网络延迟
在部署 HADR 解决方案时，请假设本地网络和 Azure 之间有时可能会存在很高的网络延迟。 将副本部署到 Azure 时，同步模式应使用异步提交而非同步提交。 当你同时在本地和 Azure 中部署数据库镜像服务器时，请使用高性能模式，而非高安全模式。

请参阅 [HADR 配置最佳做法](hadr-cluster-best-practices.md)，了解有助于适应云环境的群集和 HADR 设置。 

### <a name="geo-replication-support"></a>异地复制支持
Azure 磁盘中的异地复制不支持将同一数据库的数据文件和日志文件各自存储在不同的磁盘上。 GRS 独立并异步地复制对每个磁盘的更改。 此机制可保证单个磁盘中异地复制副本上的写顺序，但不能保证多个磁盘的异地复制副本上的写顺序。 如果将数据库配置为将其数据文件和日志文件各自存储在不同的磁盘上，则灾难后恢复的磁盘所含的数据文件副本可能比日志文件副本新，而这违反了 SQL Server 中的预写日志以及事务的 ACID 属性（原子性、一致性、隔离性和持久性）。 

如果无法对存储帐户禁用异地复制，请将数据库的所有数据和日志文件都保留在同一磁盘上。 如果因数据库较大而必须使用多个磁盘，请部署之前列出的某个灾难恢复解决方案以确保数据冗余。

## <a name="next-steps"></a>后续步骤

确定适合你的业务的最佳业务连续性解决方案是[可用性组](availability-group-overview.md)还是[故障转移群集实例](failover-cluster-instance-overview.md)。 然后，查看针对高可用性和灾难恢复配置环境的[最佳做法](hadr-cluster-best-practices.md)。
