---
title: Windows Server 故障转移群集概述
description: '了解 Windows Server 故障转移群集技术与 Azure VM（例如可用性组和故障转移群集实例）上的 SQL Server 之间的差异。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: mathoma
ms.openlocfilehash: f85c603f7bfa4113edaeaed1eb92d907d06fe09d
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581918"
---
# <a name="windows-server-failover-cluster-with-sql-server-on-azure-vms"></a>Windows Server 故障转移群集与 Azure VM 上的 SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了 Windows Server 故障转移群集功能与 Azure VM 上的 SQL Server 之间的差异，以实现高可用性和灾难恢复 (HADR)，例如 Always On 可用性组 (AG) 或故障转移群集实例 (FCI)。 

若要了解有关 Windows 功能本身的详细信息，请参阅 [Windows Server 故障转移群集文档](/windows-server/failover-clustering/failover-clustering-overview)。

## <a name="overview"></a>概述

Windows 上的 SQL Server 高可用性解决方案（例如 Always On 可用性组 (AG) 或故障转移群集实例 (FCI)）依赖于基础 Windows Server 故障转移群集 (WSFC) 服务。

群集服务监视群集中节点的网络连接和运行状况。 此外，还监视 SQL Server 作为可用性组或故障转移群集实例功能的一部分而执行的运行状况检查。 如果群集服务无法访问节点，或者如果群集中的 AG 或 FCI 角色变得不正常，则群集服务会启动相应的恢复操作，以恢复应用程序和服务并使其联机，无论是在群集的同一节点上，还是在群集的其他节点上。

## <a name="cluster-health-monitoring"></a>群集运行状况监视

为了提供高可用性，群集必须确保组成群集解决方案的不同组件的运行状况。 群集服务根据多个系统和网络参数监视群集的运行状况，以便检测和响应故障。 

设置用于声明故障的阈值很重要，目的是为了在快速响应故障与避免误故障之间达到平衡。

用于监视的策略有两种：

| 监视  | 说明 |
|-|-|
| 激进 | 提供对硬故障的快速故障检测和恢复，从而提供最高级别的可用性。 群集服务和 SQL Server 都不太能容忍暂时性故障，在某些情况下，可能会在发生暂时性中断时提前故障转移资源。 检测到故障后，相应的纠正措施可能需要额外的时间。 |
| 已放宽 | 提供更多包容性故障检测，以便更能容忍短暂的暂时性网络问题。 避免暂时性故障，但也带来了延迟检测真故障的风险。 |

云中群集环境的主动设置可能导致过早的故障和更长的中断，因此建议对 Azure VM 上的故障转移群集使用已放宽的监视策略。 若要调整阈值设置，请参阅[群集最佳做法](hadr-cluster-best-practices.md#relaxed-monitoring)以了解更多详细信息。 

## <a name="cluster-heartbeat"></a>群集检测信号

影响节点之间的群集检测信号和运行状况检测的主要设置：

| 设置 | 说明 |
|-|-|
| 延迟 | 这会定义在节点之间发送群集检测信号的频率。 延迟是发送下一个检测信号之前等待的秒数。 在同一群集中，可以在同一子网上的节点之间以及在不同子网上的节点之间配置不同的延迟设置。 |
| 阈值 | 阈值是在群集执行恢复操作之前可以丢失的检测信号数。 在同一群集中，可以在同一子网上的节点之间以及在不同子网上的节点之间配置不同的阈值设置。 |

对于云环境，这些设置的默认值可能太小，可能会由于暂时性网络问题而导致不必要的故障。 若要提高包容性，请对 Azure VM 中的故障转移群集使用已放宽的阈值设置。 有关更多详细信息，请参阅[群集最佳做法](hadr-cluster-best-practices.md#heartbeat-and-threshold)。 

## <a name="quorum"></a>Quorum

尽管双节点群集即使没有[仲裁资源](/windows-server/storage/storage-spaces/understand-quorum)也能正常工作，但我们严格要求客户使用仲裁资源来获得生产支持。 没有仲裁资源，群集验证将不会通过任何群集。 

从技术上讲，在没有仲裁资源的情况下，三节点群集可以承受单个节点丢失（减少到两个节点）。 但在群集减少到两个节点后，如果节点丢失或节点之间发生通信故障，则群集资源会处于脱机状态以防出现拆分情况。 配置仲裁资源，将使群集资源在只有一个节点联机时可以继续联机。

磁盘见证是最具弹性的仲裁选项，但若要在 Azure VM 中的 SQL Server 上使用磁盘见证，必须使用 Azure 共享磁盘，这会对高可用性解决方案施加一些限制。 因此，在使用 Azure 共享磁盘配置故障转移群集实例时请使用磁盘见证，否则尽量使用云见证。 

下表列出了 Azure VM 上的 SQL Server 可用的仲裁选项： 

|  |[云见证](/windows-server/failover-clustering/deploy-cloud-witness) |[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**支持的 OS**| Windows Server 2016 及更高版本 |全部 | 全部|
| **说明** | 云见证是一种故障转移群集仲裁见证，它使用 Microsoft Azure 提供对群集仲裁的投票。 默认大小约为 1 MB，仅包含时间戳。 云见证是在多个站点、多个区域和多个地区中进行部署的理想选择。 尽可能使用云见证，除非你有包含共享存储的故障转移群集解决方案。 | 磁盘见证是群集可用存储组中的小型群集磁盘。 此磁盘具有高可用性，并且可在节点之间故障转移。 它包含群集数据库的副本，其默认大小小于 1 GB。 对于使用 Azure 共享磁盘（或任何共享磁盘解决方案，如共享 SCSI、iSCSI 或光纤信道 SAN）的任何群集，磁盘见证都是首选的仲裁选项。  群集共享卷不能用作磁盘见证。 将 Azure 共享磁盘配置为磁盘见证。  | 文件共享见证是一个 SMB 文件共享，通常在运行 Windows Server 的文件服务器上配置。 它在 witness.log 文件中维护群集信息，但不存储群集数据库的副本。 在 Azure 中，你可以在同一虚拟网络中的单独虚拟机上配置文件共享。 如果磁盘见证或云见证在你的环境中不可用，则使用文件共享见证。 | 

若要开始，请参阅[配置群集仲裁](hadr-cluster-quorum-configure-how-to.md)。 


## <a name="virtual-network-name-vnn"></a>虚拟网络名称 (VNN)

在传统的本地环境中，群集资源（例如故障转移群集实例或 Always On 可用性组）依赖于虚拟网络名称将流量路由到相应的目标 - 故障转移群集实例或 Always On 可用性组的侦听器。 虚拟名称绑定 DNS 中的 IP 地址，客户端可以使用虚拟名称或 IP 地址来连接到其高可用性目标，而不考虑哪个节点当前拥有资源。 VNN 是由群集托管的网络名称和地址，群集服务在故障转移事件期间将网络地址从节点移动到节点。 在发生故障时，该地址将在原始主副本上脱机，并在新的主副本上联机。

在 Azure 虚拟机上，需要额外的组件将流量从客户端路由到群集资源（故障转移群集实例或可用性组的侦听器）的虚拟网络名称。 在 Azure 中，负载均衡器保留群集 SQL Server 资源所依赖的 VNN 的 IP 地址，并且是将流量路由到相应的高可用性目标所必需的。 负载均衡器还检测网络组件故障，并将该地址移动到新主机。 

负载均衡器分会配到达前端的入站流，然后将该流量路由到后端池定义的实例。 可以使用负载均衡规则和运行状况探测来配置流量。 对于 SQL Server FCI，后端池实例是运行 SQL Server 的 Azure 虚拟机，对于可用性组，后端池是侦听器。 使用负载均衡器时存在轻微的故障转移延迟，因为默认情况下，运行状况探测每 10 秒执行一次活动检查。 

首先，了解如何为[故障转移群集实例](failover-cluster-instance-vnn-azure-load-balancer-configure.md)或[可用性组](availability-group-vnn-azure-load-balancer-configure.md)配置 Azure 负载均衡器。 

**支持的操作系统**：All   
**支持的 SQL 版本**：All   
**支持的 HADR 解决方案**：故障转移群集实例与可用性组   

VNN 的配置可能很繁琐，它是另一故障源，可能会导致故障检测的延迟，并且会产生与管理其他资源相关的开销和成本。 为了解决其中一些限制，SQL Server 2019 引入了对分布式网络名称功能的支持。 

## <a name="distributed-network-name-dnn"></a>分布式网络名称 (DNN)

从 SQL Server 2019 开始，分布式网络名称功能为 SQL Server 客户端提供了一种替代方法，使其无需使用负载均衡器即可连接到 SQL Server 故障转移群集实例或可用性组侦听器。 

创建 DNN 资源后，群集将 DNS 名称与群集中所有节点的 IP 地址绑定。 客户端将尝试连接到此列表中的每个 IP 地址，以查找要连接到的资源。 可以通过在连接字符串中指定 `MultiSubnetFailover=True` 来加快此过程。 此设置会让提供程序并行尝试所有 IP 地址，以便客户端可以立即连接到 FCI 或侦听程序。 

建议尽可能通过负载均衡器使用分布式网络名称，原因是： 
- 端到端解决方案更加可靠，因为不再需要维护负载均衡器资源。 
- 消除负载均衡器探测可最大程度地缩短故障转移时间。 
- DNN 使用 Azure VM 上的 SQL Server 简化故障转移群集实例或可用性组侦听程序的配置和管理。 

使用 DNN 时，大多数 SQL Server 功能透明地适用于 FCI 和可用性组，但是某些功能可能需要特殊考虑。 

**支持的操作系统**：Windows Server 2016 及更高版本   
**支持的 SQL 版本**：SQL Server 2019 CU2 (FCI) 和 SQL Server 2019 CU8 (AG)   
**支持的 HADR 解决方案**：故障转移群集实例与可用性组   

首先，了解如何为[故障转移群集实例](failover-cluster-instance-distributed-network-name-dnn-configure.md)或[可用性组](availability-group-distributed-network-name-dnn-listener-configure.md)配置分布式网络名称资源。

在将 DNN 与其他 SQL Server 功能一起使用时，还有其他注意事项。 请参阅 [FCI 和 DNN 互操作性](failover-cluster-instance-dnn-interoperability.md) 以及 [AG 和 DNN 互操作性](availability-group-dnn-interoperability.md)了解详细信息。 

## <a name="recovery-actions"></a>恢复操作

检测到故障时，群集服务会采取纠正措施。 这可能会重新启动现有节点上的资源，或将资源故障转移到另一个节点。 启动纠正措施后，它们需要一些时间才能完成。 

例如，重新启动的可用性组按以下顺序联机： 

1. 侦听器 IP 联机
1. 侦听器网络名称联机
1. 可用性组联机
1. 各个数据库会经历恢复过程，这可能需要一些时间，具体取决于许多因素，例如重做日志的长度。 只有在数据库完全恢复后，侦听器才会路由连接。 有关详细信息，请参阅[估计故障转移时间 (RTO)](/sql/database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups)。 

由于恢复可能需要一些时间，因此，如果发生暂时性事件（例如内存保留 [Azure VM 维护](#azure-platform-maintenance)），则设置为在 20 秒内检测故障的主动监视可能会导致中断几分钟。 将监视设置为更放宽的值（即 40 秒）有助于避免更长的服务中断。 

若要调整阈值设置，请参阅[群集最佳做法](hadr-cluster-best-practices.md)以了解更多详细信息。 


## <a name="node-location"></a>节点位置

Azure 中虚拟机上的 Windows 群集中的节点可能在同一 Azure 区域内以物理方式分隔，也可以位于不同的区域中。 此距离可能会引起网络延迟，就像将群集节点分散在你自己的设施中的各个位置之间一样。 在云环境中，不同之处在于，在一个区域内，你可能不知道节点之间的距离。  此外，一些其他因素（如物理和虚拟组件、跃点数等）也会导致延迟增加。 如果节点之间的延迟构成问题，请考虑将群集的节点置于[邻近放置组](../../../virtual-machines/co-location.md)中，以确保网络邻近性。

## <a name="resource-limits"></a>资源限制

配置 Azure VM 时，需要确定 CPU、内存和 IO 的计算资源限制。 工作负荷需要的资源多于已购买的 Azure VM，或磁盘限制可能会导致 VM 性能问题。 性能下降可能会导致群集服务或 SQL Server 高可用性功能的运行状况检查失败。 资源瓶颈可能会导致节点或资源显示在群集或 SQL Server 之下。 

密集型 SQL IO 操作或维护操作（例如备份、索引或统计信息维护）可能会导致 VM 或磁盘达到 IOPS 或 MBPS 吞吐量限制，这可能会导致 SQL Server 对 IsAlive/LooksAlive 检查无响应。 

如果 SQL Server 发生意外故障转移，请检查以确保遵循所有[性能最佳做法](performance-guidelines-best-practices-checklist.md)，并监视服务器的磁盘或 VM 级别上限。 

## <a name="azure-platform-maintenance"></a>Azure 平台维护

与任何其他云服务一样，Azure 定期更新平台，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 此类更新的目的包括修补托管环境中的软件组件、升级网络组件以及硬件解除授权，等等。

大多数平台更新不会影响客户 VM。 如果无法避免更新产生影响，Azure 会选择对客户 VM 影响最小的更新机制。 大多数有影响的维护只会导致 VM 暂停 10 秒以下。 在某些情况下，Azure 使用内存预留维护机制。 这些机制最长会将 VM 暂停 30 秒，并在 RAM 中预留内存。 VM 随后会恢复，其时钟会自动同步。

内存保留维护适用于 90% 以上的 Azure VM。 它不适用于 G、M、N 和 H 系列。 Azure 越来越多地使用实时迁移技术并改进内存保留维护机制以减少暂停持续时间。 将 VM 实时迁移到不同的主机时，某些敏感的工作负荷（如 SQL Server）可能会在导致 VM 暂停的几分钟内出现略微的性能下降。

平台维护期间的资源瓶颈可能导致 AG 或 FCI 显示在群集服务之下。 有关详细信息，请参阅本文的[资源限制](#resource-limits)部分。 

如果使用主动群集监视，则延长的 VM 暂停可能会触发故障转移。 故障转移通常会导致比维护暂停更多的停机时间，因此建议使用已放宽的监视，以避免在 VM 暂停进行维护时触发故障转移。 有关在 Azure VM 中设置群集阈值的详细信息，请参阅[群集最佳做法](hadr-cluster-best-practices.md)。

## <a name="limitations"></a>限制

在 Azure 虚拟机上使用 FCI 或可用性组以及 SQL Server 时，请注意以下限制。 

### <a name="msdtc"></a>MSDTC 

Azure 虚拟机支持 Windows Server 2019 上的 Microsoft 分布式事务处理协调器 (MSDTC)，其中存储位于群集共享卷 (CSV) 和[标准负载均衡器](../../../load-balancer/load-balancer-overview.md)上，或者位于正在使用 Azure 共享磁盘的 SQL Server VM 上。 

在 Azure 虚拟机上，具有群集共享卷的 Windows Server 2016 或更早版本不支持 MSDTC，因为：

- 无法将群集 MSDTC 资源配置为使用共享存储。 在 Windows Server 2016 上，如果创建 MSDTC 资源，即使存储可用，它也不会显示任何可用的共享存储。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不处理 RPC 端口。



## <a name="next-steps"></a>后续步骤

现在，你已了解 Windows 故障转移群集与 Azure VM 上的 SQL Server 之间的差异，了解高可用性功能[可用性组](availability-group-overview.md)或[故障转移群集实例](failover-cluster-instance-overview.md)。 如果已准备好开始，请务必查看[最佳做法](hadr-cluster-best-practices.md)以获取配置建议。 
