---
title: 跨 VM 共享 Azure 托管磁盘
description: 了解如何跨多个 Linux VM 共享 Azure 托管磁盘。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 80491f2d9169bcd5c9d4054eb8329c89fd778ba9
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252576"
---
# <a name="share-an-azure-managed-disk"></a>共享 Azure 托管磁盘

Azure 共享磁盘是 Azure 托管磁盘的一项功能，可同时将托管磁盘附加到多个虚拟机 (VM)。 通过将托管磁盘附加到多个 VM，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。

## <a name="how-it-works"></a>工作原理

群集中的 VM 可以根据群集应用程序使用 [SCSI 永久预留](https://www.t10.org/members/w_spc3.htm) (SCSI PR) 选择的预留来读取或写入其附加的磁盘。 SCSI PR 是一种行业标准，可供本地存储区域网络 (SAN) 上运行的应用程序使用。 在托管磁盘上启用 SCSI PR，可以将这些应用程序按原样迁移到 Azure。

共享托管磁盘提供了可以从多个 VM 进行访问的共享块存储，这些存储作为逻辑单元号 (LUN) 公开。 然后，会将 LUN 从目标（磁盘）提供给发起程序 (VM)。 这些 LUN 看起来像直接附加存储 (DAS) 或 VM 的本地驱动器。

共享托管磁盘本身并不提供可以使用 SMB/NFS 访问的完全托管的文件系统。 需要使用群集管理器（如 Windows Server 故障转移群集 (WSFC) 或 Pacemaker）来处理群集节点通信和写入锁定。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>操作系统要求

共享磁盘支持多个操作系统。 有关支持的操作系统，请参阅 [Windows](#windows) 或 [Linux](#linux) 部分。

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>示例工作负载

### <a name="windows"></a>Windows

Windows Server 2008 和更高版本支持 Azure 共享磁盘。 大多数基于 Windows 的群集基于 WSFC 进行构建，它处理群集节点通信的所有核心基础结构，使应用程序能够利用并行访问模式。 WSFC 根据 Windows Server 的版本启用 CSV 和非 CSV 的选项。 有关详细信息，请参阅[创建故障转移群集](/windows-server/failover-clustering/create-failover-cluster)。

WSFC 上运行的热门应用程序包括：

- [使用 Azure 共享磁盘创建 FCI（Azure VM 上的 SQL Server）](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
    - [将故障转移群集实例迁移到具有共享磁盘的 Azure VM 上的 SQL Server](../azure-sql/migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)
- 横向扩展文件服务器 (SoFS) [模板] (https://aka.ms/azure-shared-disk-sofs-template) )
- SAP ASCS/SCS [模板] (https://aka.ms/azure-shared-disk-sapacs-template) )
- 常规用途的文件服务器（IW 工作负载）
- 远程桌面服务器用户配置文件磁盘 (RDS UPD)

### <a name="linux"></a>Linux

支持 Azure 共享磁盘的版本包括：
- [SUSE SLE for SAP 和 SUSE SLE HA 15 SP1 及更高版本](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18.04 和更高版本](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [任何 RHEL 8 版本上的 RHEL 开发人员预览版](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Linux 群集可以使用群集管理器，例如 [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker)。 Pacemaker 基于 [Corosync](http://corosync.github.io/corosync/) 构建，可为部署在高可用环境中的应用程序启用群集通信。 一些常见的群集文件系统包括 [ocfs2](https://oss.oracle.com/projects/ocfs2/) 和 [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)。 可以使用基于 SCSI 永久预留 (SCSI PR) 和/或 STONITH 块设备 (SBD) 的聚类分析模型，以便仲裁对磁盘的访问。 使用 SCSI PR 时，可以使用 [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) 和 [sg_persist](https://linux.die.net/man/8/sg_persist) 之类的实用工具来处理预留和注册。

## <a name="persistent-reservation-flow"></a>永久预留流

下图演示了一个示例 2 节点群集数据库应用程序，该应用程序使用 SCSI PR 启用从一个节点到另一个节点的故障转移。

![双节点群集。 群集上运行的应用程序正在处理对磁盘的访问](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

流程如下所示：

1. 在 Azure VM1 和 VM2 上运行的群集应用程序均注册了其读取或写入磁盘的意图。
1. 然后，VM1 上的应用程序实例将使用独占预留以写入磁盘。
1. 已在 Azure 磁盘上强制执行此预留，并且数据库现在可以独占方式写入磁盘。 从 VM2 上的应用程序实例进行的任何写入都不会成功。
1. 如果 VM1 上的应用程序实例关闭，则 VM2 上的实例现在可以启动数据库故障转移和磁盘接管。
1. 现在，已在 Azure 磁盘上强制执行此预留，并且该磁盘将不再接受来自 VM1 的写入。 它将只接受来自 VM2 的写入。
1. 群集应用程序可以完成数据库故障转移并处理来自 VM2 的请求。

下图说明了另一个常见的群集工作负载，该工作负载由多个节点组成，这些节点从磁盘读取数据以运行并行进程，例如机器学习模型训练。

![四节点 VM 群集，每个节点注册写入意图，应用程序进行独占预留以正确处理写入结果](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

流程如下所示：

1. 所有 VM 上运行的群集应用程序均注册了其读取或写入磁盘的意图。
1. 然后，VM1 上的应用程序实例将使用独占预留以写入磁盘，同时开放其他 VM 的磁盘读取。
1. 将在 Azure 磁盘上强制执行此预留。
1. 群集中的所有节点现在都可以从磁盘中读取。 只有一个节点代表群集中的所有节点将结果写回磁盘。

### <a name="ultra-disks-reservation-flow"></a>超级磁盘预留流

超级磁盘提供附加限制，总共有两个限制。 因此，超级磁盘预留流可以按前面部分所述工作，也可以更精细地限制和分配性能。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="描述预留持有者、注册者和其他人的 `ReadOnly` 或 `Read/Write` 访问权限的表的图像。":::

## <a name="performance-throttles"></a>性能限制

### <a name="premium-ssd-performance-throttles"></a>高级 SSD 性能限制

对于高级 SSD，磁盘 IOPS 和吞吐量是固定的，例如，P30 的 IOPS 为 5000。 无论磁盘是在 2 个 VM 还是 5 个 VM 之间共享，此值都保持不变。 磁盘限制可以通过单个 VM 达到，也可以在两个或多个 VM 中进行划分。 

### <a name="ultra-disk-performance-throttles"></a>超级磁盘性能限制

超级磁盘具有独特的功能，允许你通过公开可修改的属性并允许对其进行修改来设置性能。 默认情况下，只有两个可修改的属性，但共享的超级磁盘具有两个附加属性。


|Attribute  |说明  |
|---------|---------|
|DiskIOPSReadWrite     |所有装载具有写入访问权限的共享磁盘的 VM 所允许的 IOPS 总数。         |
|DiskMBpsReadWrite     |所有装载具有写入访问权限的共享磁盘的 VM 所允许的总吞吐量 (MB/s)。         |
|DiskIOPSReadOnly*     |所有以 `ReadOnly` 方式装载共享磁盘的 VM 所允许的 IOPS 总数。         |
|DiskMBpsReadOnly*     |所有以 `ReadOnly` 方式装载共享磁盘的 VM 所允许的总吞吐量 (MB/s)。         |

\* 仅适用于共享的超级磁盘

以下公式说明了如何设置性能属性，因为这些属性是用户可修改的：

- DiskIOPSReadWrite/DiskIOPSReadOnly： 
    - 300 IOPS/GiB 的 IOPS 限制，每个磁盘最高可达 160 K IOPS
    - 最小值为 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly 至少为 2 IOPS/GiB
- DiskMBpsRead    Write/DiskMBpsReadOnly：
    - 单个磁盘对应于每个预配 IOPS 的吞吐量限制为 256 KiB/秒，每个磁盘的最大吞吐量为 2000 MBps。
    - 对于每个预配的 IOPS，每个磁盘的最低保证吞吐量为 4KiB/s，总体基线最低为 1 MBps

#### <a name="examples"></a>示例

以下示例描述了一些方案，这些方案具体说明了限制如何作用于共享超级磁盘。

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>使用群集共享卷的双节点群集

以下是使用群集共享卷的 2 节点 WSFC 的示例。 使用此配置，两个 VM 都可以同时对磁盘进行写入访问，这将导致 `ReadWrite` 限制将由两个 VM 共享，且不使用 `ReadOnly` 限制。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 双节点超级性能示例":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>无群集共享卷的双节点群集

以下是未使用群集共享卷的 2 节点 WSFC 的示例。 使用此配置时，只有一个 VM 对磁盘具有写入访问权限。 这会导致 `ReadWrite` 限制专用于主 VM，`ReadOnly` 限制专用于辅助 VM。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 双节点无 csv 超级磁盘示例":::

##### <a name="four-node-linux-cluster"></a>四节点 Linux 群集

下面是具有一个编写器和三个横向扩展读取器的 4 节点 Linux 群集的示例。 使用此配置时，只有一个 VM 对磁盘具有写入访问权限。 这会导致将 `ReadWrite` 限制专用于主 VM，而 `ReadOnly` 限制则由辅助 VM 共享。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="四节点超级限制示例":::

#### <a name="ultra-pricing"></a>超级共享磁盘定价

超级共享磁盘的定价依据为：预配的容量、总预配 IOPS (diskIOPSReadWrite + diskIOPSReadOnly) 和总预配吞吐量 MBps (diskMBpsReadWrite + diskMBpsReadOnly)。 每次额外的 VM 装载均不收取额外费用。 例如，配置为 diskSizeGB：1024、DiskIOPSReadWrite：10000、DiskMBpsReadWrite：600、DiskIOPSReadOnly：100、DiskMBpsReadOnly：1 的超级共享磁盘按 1024 GiB、10100 IOPS 和 601 MBps 进行收费，而不管它是装载到 2 个 VM 还是 5 个 VM。

## <a name="next-steps"></a>后续步骤

如果你有兴趣为托管磁盘启用和使用共享磁盘，请参阅我们的文章：[启用共享磁盘](disks-shared-enable.md)