---
title: Azure VM 上的托管磁盘的备份和灾难恢复
description: 本文介绍如何规划 Azure 中的 IaaS 虚拟机和托管磁盘的备份与灾难恢复。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a2c060362a74400a1356d96fb85a4e62d20cac57
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101794"
---
# <a name="backup-and-disaster-recovery-for-azure-managed-disks"></a>Azure 托管磁盘的备份和灾难恢复

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集

本文介绍如何规划 Azure 托管磁盘的备份与灾难恢复。

首先介绍 Azure 平台内置的容错功能，此功能可预防本地故障的发生。 然后介绍内置功能未全面涵盖的灾难恢复方案。 此外，本文档演示了几个工作负荷方案示例，它们的备份和灾难恢复注意事项各不相同。 还介绍了托管磁盘的一些灾难恢复解决方案。

## <a name="introduction"></a>简介

Azure 使用各种方法实现冗余和容错，以帮助客户避免本地硬件故障。 本地故障可能包括存储部分虚拟磁盘数据的 Azure 存储服务器计算机出现问题，或此服务器上的固态硬盘 (SSD) 或硬盘驱动器 (HDD) 发生故障。 隔离的硬件组件故障可能会在正常操作期间发生。

Azure 旨在从这些故障中复原。 重大灾难可能会导致大量存储服务器甚至整个数据中心发生故障或无法访问。 尽管本地故障通常不会对虚拟机 (VM) 和磁盘造成影响，但有必要采取额外措施，以保护工作负荷不受整个区域内的灾难性故障（如重大灾难）影响，此类故障可能会影响 VM 和磁盘。

除了可能出现的平台故障外，客户应用程序或数据也可能会出现问题。 例如，应用程序的新版本可能会在无意间对数据进行更改，导致应用程序中断。 在这种情况下，我们建议将应用程序和数据还原到上一版已知的良好状态。 这需要维护定期备份。

对于区域性灾难恢复，必须在不同区域中备份基础结构即服务 (IaaS) VM 磁盘。 

在了解备份和灾难恢复选项之前，先来回顾一下本地故障的一些处理方法。

### <a name="azure-iaas-resiliency"></a>Azure IaaS 复原

复原是指对硬件组件中发生的常见故障实现容错。 通过复原，可以从故障中恢复，并继续正常运行。 复原并不旨在避免故障发生，而是通过响应故障来避免故障时间或数据丢失。 复原的目标是在故障发生后将应用程序恢复到可完全正常运行的状态。 Azure VM 和托管磁盘旨在从常见硬件故障中复原。 让我们来看看 Azure IaaS 平台是如何提供这种复原功能的。

VM 主要由两部分组成：计算服务器和永久性磁盘。 两者都会影响 VM 的容错能力。

如果托管 VM 的 Azure 计算主机服务器遇到硬件故障（极少数情况），那么 Azure 会在另一台服务器上自动还原 VM。 如果发生这种情况，计算机会重新启动，并在一段时间后备份 VM。 为了确保客户 VM 尽快可用，Azure 自动检测此类硬件故障，并开始恢复。

对于托管磁盘，数据持续性对永久性存储平台来说至关重要。 Azure 客户在 IaaS 上运行重要的商业应用程序，这些应用程序依赖数据的永久性。 Azure 针对这些 IaaS 磁盘设计的保护措施是在本地存储数据的三个冗余副本。 这些副本提供高持续性来应对本地故障。 如果托管磁盘的硬件组件之一出现故障，VM 不会受到影响，因为有两个额外的副本支持磁盘请求。 即使两个支持磁盘的不同硬件组件同时出现故障（这比较罕见），VM 也能正常运行。 

为了确保始终维护三个副本，Azure 在后台自动创建一个新数据副本，以防三个副本中有一个无法使用。 因此，不应通过结合使用 RAID 与 Azure 磁盘来实现容错。 简单的 RAID 0 配置应足以对磁盘进行必要分区，以创建更大的卷。

鉴于此体系结构，Azure 为 IaaS 磁盘不间断提供企业级数据持续性，[年化故障率](https://en.wikipedia.org/wiki/Annualized_failure_rate)为 0%，达到行业领先水平。

计算主机或存储平台上的本地硬件故障有时可能会导致 VM 暂时不可用，有关 VM 可用性的 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 对此做了介绍。 Azure 还提供了有关使用 Azure 高级 SSD 的单 VM 实例的行业领先 SLA。

为了保护应用程序工作负荷不受磁盘或 VM 暂时不可用带来的故障时间影响，客户可以使用[可用性集](./availability.md)。 可用性集中的两个或多个虚拟机为应用程序提供冗余。 然后，Azure 在电源、网络和服务器组件不同的单独容错域中创建这些 VM 和磁盘。

由于这些单独的容错域，本地硬件故障通常不会同时影响可用性集中的多个 VM。 单独的容错域为应用程序提供了高可用性。 如果需要高可用性，最好使用可用性集。

### <a name="backup-and-disaster-recovery"></a>备份和灾难恢复

灾难恢复是指能够从罕见但非常重大的事件中恢复。 这些事件包括非暂时性的大规模故障，如影响整个区域的服务中断。 灾难恢复包括数据备份和存档，并且可能包括手动干预，如通过备份还原数据库。

如果出现可能导致大规模中断的重大灾难，Azure 平台内置的本地故障保护功能可能无法完全保护 VM/磁盘。 这些大规模中断事件包括数据中心遭遇飓风、地震、火灾或大规模硬件单元故障等灾难性事件。 此外，可能还会遇到应用程序或数据问题导致的故障。

若要保护 IaaS 工作负荷不受中断影响，应计划冗余和备份，以便能够进行恢复。 对于灾难恢复，应该在远离主站点的不同地理位置备份。 该方式有助于确保最初影响 VM 或磁盘的相同事件不会对备份造成影响。 有关详细信息，请参阅 [Azure 应用程序的灾难恢复](/azure/architecture/resiliency/disaster-recovery-azure-applications)。

灾难恢复注意事项可能包括以下方面：

- 高可用性：应用程序能够以正常状态继续运行，而没有显著增加停机时间。 “正常状态”是指，应用程序有响应，用户可以连接到应用程序，并与之交互。 某些任务关键型应用程序和数据库可能需要始终可用，即使平台上有故障，也不例外。 对于这些工作负荷，可能需要为应用程序和数据计划冗余。

- 数据持续性：在某些情况下，主要注意事项是确保在灾难发生时保留数据。 因此，可能需要在不同站点中备份数据。 对于此类工作负荷，可能不需要为应用程序计划完全冗余，只需定期备份磁盘即可。

## <a name="backup-and-disaster-recovery-scenarios"></a>备份和灾难恢复方案

让我们来看几个典型的应用程序工作负荷方案示例，以及规划灾难恢复时的注意事项。

### <a name="scenario-1-major-database-solutions"></a>应用场景 1：主要数据库解决方案

假设为 SQL Server 或 Oracle 等生产数据库服务器，可以支持高可用性。 关键生产应用程序和用户依赖此类数据库。 此系统的灾难恢复计划可能需要满足以下要求：

- 数据必须受保护且可恢复。
- 服务器必须可用。

灾难恢复计划可能需要将不同区域中的数据库副本作为备份进行维护。 解决方案包括主动-主动或主动-被动副本站点、定期脱机备份数据，具体视服务器可用性和数据恢复要求而定。 SQL Server 和 Oracle 等关系型数据库提供各种复制选项。 对于 SQL Server，可以使用 [SQL Server AlwaysOn 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)实现高可用性。

为了实现冗余，MongoDB 等 NoSQL 数据库也支持[副本](https://docs.mongodb.com/manual/replication/)。 可以使用实现高可用性的副本。

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>应用场景 2：冗余 VM 群集

假设由提供冗余和负载均衡的 VM 群集处理工作负载。 例如，在区域中部署的 Cassandra 群集。 此类体系结构已在相应区域提供了高水平冗余。 不过，为了保护工作负荷免受区域级故障影响，应考虑在两个区域分布群集，或定期备份到另一个区域。

### <a name="scenario-3-iaas-application-workload"></a>应用场景 3：IaaS 应用程序工作负荷

让我们探讨一下 IaaS 应用程序工作负荷。 例如，该应用程序可能是 Azure VM 上运行的典型生产工作负荷。 它可能是保存内容和其他站点资源的 Web 服务器或文件服务器。 也可能是在 VM 上运行的专门定制的商业应用程序，将数据、资源和应用程序状态存储到 VM 磁盘上。 在这种情况下，请务必定期进行备份。 应根据 VM 工作负载的性质确定备份频率。 例如，如果应用程序每天都运行，并且修改数据，那么应每小时备份一次。

再例如，报表服务器从其他数据源拉取数据，并生成聚合报表。 如果丢失此 VM 或磁盘，可能导致报表丢失。 不过，可以重新运行报表进程，并重新生成输出。 在这种情况下，即使报表服务器遭遇灾难，也不会真正丢失数据。 因此，可以有高水平的容错，允许报表服务器上丢失部分数据。 在这种情况下，不太频繁地进行备份可以降低成本。

### <a name="scenario-4-iaas-application-data-issues"></a>应用场景 4：IaaS 应用程序数据问题

IaaS 应用程序数据问题是另一种可能的情况。 假设有一个应用程序，用于计算、维护和提供关键商业数据（如定价信息）。 新版应用程序有一个软件 bug，不仅错误地计算了定价，还破坏了平台提供的现有商业数据。 在这种情况下，最好还原到旧版应用程序和数据。 若要能够进行还原，请定期备份系统。

## <a name="disaster-recovery-solution-azure-disk-backup"></a>灾难恢复解决方案：Azure 磁盘备份 

Azure 磁盘备份是基于云的本机备份解决方案，可保护托管磁盘中的数据。 它是一个简单、安全且经济高效的解决方案，使你能够通过几个步骤为托管磁盘配置保护。 它确保你可以在灾难情况下恢复数据。

Azure 磁盘备份提供了一个统包式解决方案，通过自动定期创建快照并使用备份策略在配置的时间段内保留快照，为托管磁盘提供快照生命周期管理。 无需对基础结构投入任何资金，无需编写任何自定义脚本，无需产生任何管理开销，就能管理磁盘快照。 它是一个使用增量快照创建托管磁盘时间点备份的崩溃一致性备份解决方案，并支持每日创建多个备份。 它也是一个无代理解决方案，不会影响生产应用程序的性能。 它支持备份和还原 OS 磁盘与数据磁盘（包括共享磁盘），不管这些磁盘当前是否已附加到正在运行的 Azure VM。

有关 Azure 磁盘备份的详细信息，请参阅 [Azure 磁盘备份概述](../backup/disk-backup-overview.md)。

## <a name="alternative-solution-consistent-snapshots"></a>替代解决方案：一致性快照

如果无法使用 Azure 备份，可以使用快照实现自己的备份机制。 为 VM 使用的所有磁盘创建一致性快照，再将这些快照复制到另一个区域的过程比较复杂。 因此，Azure 认为相对于生成自定义解决方案，使用备份服务是更好的选择。

如果将本地冗余存储用于磁盘，需要自行复制数据。

快照表示处于特定时间点的对象。 快照因保留数据的大小递增而产生费用。 有关详细信息，请参阅[为托管磁盘创建增量快照](disks-incremental-snapshots.md)。

### <a name="create-snapshots-while-the-vm-is-running"></a>当 VM 正在运行时创建快照

尽管可以随时生成快照，但如果 VM 正在运行，就仍有数据流式传输到磁盘上。 快照可能包含部分正在测试的操作。 此外，如果涉及到多个磁盘，各个磁盘的快照可能在不同时间生成。 这些情况可能导致快照不协调。 如果在备份期间发生了更改，带分区的卷文件就会被破坏，因此对此类文件来说，缺乏协调性导致的问题尤为严重。

为了避免这种情况发生，必须在备份过程中执行以下步骤：

1.  冻结所有磁盘。

1.  刷新所有挂起写入。

1.  [为所有磁盘的托管磁盘创建增量快照](disks-incremental-snapshots.md)。

某些 Windows 应用程序（如 SQL Server）通过卷影服务提供协调的备份机制，以创建应用程序一致性备份。 在 Linux 上，可以使用 fsfreeze 等工具来协调磁盘。 此工具提供文件一致性备份，而不是应用程序一致性快照。 此过程比较复杂。因此，应考虑使用 [Azure 磁盘备份](../backup/disk-backup-overview.md)或已实施此过程的第三方备份解决方案。

上述过程会生成所有 VM 磁盘的协调快照集合，用于表示处于特定时间点的 VM。 这就是 VM 的备份还原点。 可以按原定时间间隔重复执行此过程，从而创建定期备份。 请参阅[将备份复制到另一个区域](#copy-the-snapshots-to-another-region)，了解将快照复制到另一个区域进行灾难恢复的步骤。

### <a name="create-snapshots-while-the-vm-is-offline"></a>当 VM 脱机时创建快照

创建一致性备份的另一种做法是，关闭 VM，再为每个磁盘生成快照。 生成脱机快照比协调正在运行的 VM 的快照更为简单，但会出现几分钟的停机时间。

### <a name="copy-the-snapshots-to-another-region"></a>将快照复制到另一个区域

仅仅创建快照可能不足以执行灾难恢复。 还必须将快照复制到另一个区域。 请查看[将 Azure 托管磁盘备份复制到具有增量快照差异功能的另一区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)中的说明

## <a name="other-options"></a>其他选项

### <a name="sql-server"></a>SQL Server

在 VM 中运行的 SQL Server 有自己的内置功能，可将 SQL Server 数据库备份到 Azure Blob 存储或文件共享。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的备份和还原](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md)。 除了备份和还原外，[SQL Server AlwaysOn 可用性组](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md)还可以维护数据库的次要副本。 这种功能可以大大减少灾难恢复时间。

## <a name="next-steps"></a>后续步骤

请参阅[使用增量快照备份 Azure 非托管虚拟机磁盘](linux/incremental-snapshots.md)。

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png