---
title: SAP HANA Azure 虚拟机存储配置 | Microsoft Docs
description: 针对已部署 SAP HANA 的 VM 的存储建议。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, Storage Ultra disk, Premium storage
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/09/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5eb1ecce79e8b98478eb1957086f23d49c7c5b14
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968210"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

对于正在运行 SAP HANA 的 Azure VM，Azure 提供其他适用的存储类型。 下面列出了一些可用于 SAP HANA 部署的 SAP HANA 认证的 Azure 存储类型： 

- Azure 高级 SSD 或高级存储 
- [超级磁盘](../../disks-enable-ultra-ssd.md)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

若要了解这些磁盘类型，请参阅[适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)一文和[选择磁盘类型](../../disks-types.md)一文

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 建议利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)进行 Azure 块存储部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 无论选择何种 Azure 存储类型，对于特定操作系统和 DBMS，都需要 SAP 支持该存储所使用的文件系统。 [SAP 支持说明 #2972496](https://launchpad.support.sap.com/#/notes/2972496) 列出了不同操作系统和数据库支持的文件系统，包括 SAP HANA。 这适用于 SAP HANA 可能访问的所有卷（以便为任何任务进行读取和写入）。 若为 SAP HANA 专门使用 Azure 上的 NFS，NFS 版本的其他限制将按本文后面的说明适用 


不同存储类型的最低 SAP HANA 认证条件为： 

- Azure 高级存储 - 需要 /hana/log 以便获得 Azure [写入加速器](../../how-to-enable-write-accelerator.md)支持。 /hana/data 卷可以放置在高级存储上（无需 Azure 写入加速器），也可以放置在超级磁盘上
- 至少将 Azure 超级磁盘用于 /hana/log 卷。 /hana/data 卷可以放置在高级存储上（无需 Azure 写入加速器），也可以放置在超级磁盘上（旨在以更快的速度重启）
- 对于 /hana/log 和 /hana/data，需要基于 Azure NetApp 文件的 NFS v4.1 卷 。 /hana/shared 的卷可以使用 NFS v3 或 NFS v4.1 协议

可以组合某些存储类型。 例如，可以将 /hana/data 放在高级存储上，同时 /hana/log 可放置在超级磁盘存储上，以获得所需的低延迟 。 如果将基于 ANF 的卷用于 /hana/data，则 /hana/log 卷也需要基于 ANF 之上的 NFS 。 不支持将基于 ANF 的 NFS 用于其中的一个卷（如 /hana/data）并将 Azure 高级存储或超级磁盘用于另一个卷（如 /hana/log） 。

在本地环境中，很少需要考虑 I/O 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 在自行构建 Azure 基础结构时，你应该注意其中的一些 SAP 颁发的要求。 SAP 所建议的某些最低吞吐量特征如下：

- 在 I/O 大小为 1 MB、速率为 250 MB/秒的 /hana/log 上读/写
- 对于 16 MB 和 64 MB I/O 大小的 /hana/data，读取活动的速率至少 400 MB/秒
- 对于 16 MB 和 64 MB I/O 大小的 /hana/data，写入活动的速率至少 250 MB/秒

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此，必须对 /hana/data 和 /hana/log 卷使用 Azure 高级存储、超级磁盘或 ANF  。 


下面列出了有关选择 HANA 存储配置的一些指导原则：

- 根据[适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)一文和[选择磁盘类型](../../disks-types.md)一文，决定存储类型
- 在调整 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量和 IOPS 限制。 [内存优化虚拟机大小](../../sizes-memory.md)一文中记录了总体 VM 存储吞吐量
- 在决定存储配置时，请尽量使 /hana/data 卷配置低于 VM 的总体吞吐量。 通过写入保存点，SAP HANA 可以积极地发出 I/O。 写入保存点时，可能很容易达到 /hana/data 卷的吞吐量限制。 如果生成 /hana/data 卷的磁盘的吞吐量高于 VM 允许的吞吐量，则可能会出现以下情况：保存点写入所利用的吞吐量会干扰重做日志写入操作的吞吐量需求。 这种情况可能会影响应用程序吞吐量
- 如果考虑使用 HANA 系统复制，则需要为参与 HANA 系统复制配置的所有 VM 的 /hana/data 和 /hana/log 使用完全相同的 Azure 存储类型 。 例如，在同一 HANA 系统复制配置内，不支持对一个 VM 的 /hana/data 使用 Azure 高级存储，而对另一个 VM 的 /hana/log 使用 Azure 超级磁盘 


> [!IMPORTANT]
> 存储配置的建议旨在指导如何开始使用。 运行工作负载并分析了存储利用率模式后，你可能会意识到未利用完提供的全部存储带宽或 IOPS。 可以考虑缩减存储大小。 也或者相反，工作负载所需的存储吞吐量可能比配置的存储吞吐量更多。 这样就可能需要部署更多容量、IOPS 或吞吐量。 为了帮助用户在所需的存储容量、所需的存储延迟、所需的存储吞吐量和 IOPS 以及最低成本配置之间尽可能实现平衡（往往不易），Azure 提供了足够多的具有不同功能和不同价格的不同存储类型，以便用户为其 HANA 工作负载找到并调整到最合适的方案。


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>条带集与 SAP HANA 数据卷分区
使用 Azure 高级存储时，有助于获得最佳性价比的方法是将 /hana/data 和/或 /hana/log 卷条带化到多个 Azure 磁盘上 。 而不是部署更大的磁盘卷来提供更多所需 IOPS 或吞吐量。 到目前为止，此方法是通过 LVM 和 MDADM 卷管理器（Linux 的一部分）实现的。 条带化磁盘的方法已有数十年历史，并且广为人知。 尽管这些条带化卷可达到所需的 IOPS 或吞吐量功能，但也因需要管理这些条带化卷而增加了复杂性。 尤其是在卷需要扩展容量的情况下。 至少对于 /hana/data，SAP 引入了一种替代方法，同样可实现跨多个 Azure 磁盘进行条带化的目标。 从 SAP HANA 2.0 SPS03 开始，HANA indexserver 可以将其 I/O 活动条带化到位于不同 Azure 磁盘上的多个 HANA 数据文件中。 优点是无需负责创建和管理不同 Azure 磁盘上的条带化卷。 以下文章详细说明了 SAP HANA 的数据卷分区功能：

- [HANA 管理员指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [有关 SAP HANA 的博客 - 分区数据卷](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP 说明 #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP 说明 #2700123](https://launchpad.support.sap.com/#/notes/2700123)

通过这些详细信息可知，利用此功能可消除条带集使用卷管理器时的复杂性。 你还会认识到，HANA 数据卷分区不仅仅适用于 Azure 数据块存储（如 Azure 高级存储）。 利用此功能还可在 NFS 共享设有 IOPS 或吞吐量限制的情况下跨这些共享进行条带化。  


## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 Linux 供应商和 SAP 的常规建议是重新配置磁盘卷的 I/O 调度程序模式，即从“mq-deadline”或“kyber”模式配置为“noop”(non-multiqueue) 或“none”(multiqueue) 模式（如果 SLES saptune 配置文件尚未如此设置）   。 有关详细信息，请参阅： 

- [SAP 说明 #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 说明 #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [SLES 12 SP4 中的 noop 设置问题](https://www.suse.com/support/kb/doc/?id=000019547)

在 Red Hat 上，原样保留由不同 SAP 应用程序的特定优化配置文件所设定的设置。


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>对 Azure M 系列虚拟机使用包含高级存储和 Azure 写入加速器的解决方案
Azure 写入加速器是可用于 Azure M 系列 VM 的一项功能。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此，/hana/data 和 /hana/log 是单独的卷，其中 Azure 写入加速器仅支持 /hana/log 卷  。 

> [!IMPORTANT]
> 使用 Azure 高级存储时，必须使用 Azure [写入加速器](../../how-to-enable-write-accelerator.md)或 /hana/log 卷。 写入加速器仅适用于高级存储以及 M 系列和 Mv2 系列 VM。 写入加速器不能与其他 Azure VM 系列（如 Esv3 或 Edsv4）结合使用。

以下针对 Azure 高级磁盘的缓存建议假设 SAP HANA 的 I/O 特征如下所示：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。 
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

建议：对于这些观察到的 SAP HANA 的 I/O 模式，使用 Azure 高级存储的不同卷的缓存应设置为如下形式：

- **/hana/data** - 无缓存或读取缓存
- **/hana/log** - 无缓存；M 和 Mv2 系列 VM 除外，这两个系列应启用 Azure 写入加速器 
- **/hana/shared** - 读取缓存
- **OS 磁盘** - 不要更改 Azure 在创建 VM 时设置的默认缓存


如果使用 LVM 或 mdadm 跨多个 Azure 高级磁盘生成条带集，则需要定义条带大小。 这些大小在 /hana/data 和 /hana/log 之间有所不同 。 建议：对于条带大小，建议使用：

- 256 KB 的 /hana/data
- 64 KB 的 /hana/log

> [!NOTE]
> 根据在较新 Linux 版本方面的客户体验，/hana/data 的条带大小已从之前要求 64 KB 或 128 KB 这一建议更改为 256 KB。 256 KB 的大小提供的性能略佳。 我们还将建议的 /hana/log 条带大小从 32 KB 更改为 64 KB，以通过更大的 I/O 获得足够吞吐量。

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 块存储为一个 VHD 保留三个映像。 使用 Azure 高级磁盘的条带集纯粹是为了配置可提供足够 IOPS 和/或 I/O 吞吐量的卷。

在条带集下面累积多个 Azure VHD 可以提高 IOPS 和存储吞吐量。 因此，如果使用 3 倍的 P30 Azure 高级存储磁盘构建条带集，则单个 Azure 高级存储 P30 磁盘的 IOPS 和存储吞吐量会提高 3 倍。

> [!IMPORTANT]
> 如果使用 LVM 或 mdadm 作为卷管理器跨多个 Azure 高级磁盘创建条带集，则不能将这三个 SAP HANA 文件系统 /data、/log 和 /shared 置于默认或根卷组。 强烈建议遵循 Linux 供应商指南，该指南通常用于为 /data、/log 和 /shared 创建各自的卷组。


### <a name="azure-burst-functionality-for-premium-storage"></a>适用于高级存储的 Azure 突发功能
我们为容量低于或等于 512 GiB 的 Azure 高级存储磁盘提供突发功能。 [磁盘突发](../../disk-bursting.md)一文中介绍了磁盘突发的具体工作原理。 阅读该文章可以了解当 I/O 工作负载低于磁盘的额定 IOPS 和吞吐量时 IOPS 和吞吐量累积的概念（有关额定吞吐量的详细信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)）。 磁盘当前用量与额定值之间的 IOPS 和吞吐量增量将会累积。 突发限制为最长 30 分钟。

可以计划使用此突发功能的理想情况可能是卷或磁盘包含不同 DBMS 的数据文件。 这些卷（尤其是包含中小型系统的卷）所需的 I/O 工作负载预期类似于：

- 低至中等的读取工作负载，因为理想状态下数据缓存在内存中，或者像 HANA 一样，数据应该完全存储在内存中
- 由定期发出的数据库检查点或保存点引起的写入突发
- 在不通过存储快照执行备份的情况下，以连续流方式读取的备份工作负载
- 对于 SAP HANA，在实例重启后将数据加载到内存中

尤其是在工作负载每秒仅处理几百个事务的小型 DBMS 系统上，此类突发功能对于存储事务或重做日志的磁盘或卷也很有用。 此类磁盘或卷的预期工作负载类似于：

- 定期写入磁盘，这取决于工作负载和工作负载性质，因为应用程序每次发出的提交都可能触发 I/O 操作
- 执行操作任务（如创建或重新生成索引）时，吞吐量的工作负载会更高
- 执行事务日志或重做日志备份时的读取突发


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>建议用于生产场景、基于 Azure 高级存储的存储解决方案

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 在涉及 Azure 高级存储的方案中，我们将在配置中实现突发功能。 无论使用什么形状或形式的存储测试工具，请随时牢记 [Azure 高级磁盘突发的工作方式](../../disk-bursting.md)。 运行通过 SAP HWCCT 或 HCMT 工具实现的存储测试时，不是所有测试都能通过标准，因为某些测试会超出你可以累积的突发额度。 尤其是在所有测试都按顺序运行而不中断时。

> [!NOTE]
> 对于 M32ts 和 M32ls VM，使用 HCMT/HWCCT 磁盘测试时，磁盘吞吐量可能低于预期。 即使在磁盘突发或基础磁盘的 I/O 吞吐量预配充足的情况下，也是如此。 观察到的行为的根本原因在于，HCMT/HWCCT 存储测试文件已完全缓存在高级存储数据磁盘的读取缓存中。 此缓存位于托管虚拟机的计算主机上，可以完全缓存 HCMT/HWCCT 的测试文件。 在这种情况下，[M 系列](../../m-series.md)文章中的“最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（缓存大小以 GiB 为单位）”列中列出的配额是相关的。 特别是对于 M32ts 和 M32ls，针对读取缓存的吞吐量配额仅为 400MB/秒。由于测试文件已完全缓存，因此尽管磁盘突发或预配的 I/O 吞吐量较高，这些测试仍可能略低于 400MB/秒的最大吞吐量。 作为替代方法，可以在 Azure 高级存储数据磁盘上不启用读取缓存的情况下进行测试。


> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

建议：建议在生产场景中使用类似以下的 Azure 高级存储配置：

SAP /hana/data 卷的配置：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | 预配的吞吐量 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M64ls | 512 GiB | 1,000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2,000 | 14,000 |
| M32dms_v2、M32ms_v2 | 875 GiB  | 500 MBps | 4 x P15 | 500 MBps | 680 MBps | 4,400 | 14,000 |
| M64s、M64ds_v2、M64s_v2 | 1,024 GiB | 1,000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4,400 | 14,000 |
| M64ms、M64dms_v2、M64ms_v2 | 1,792 GiB | 1,000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200 | 14,000 |  
| M128s、M128ds_v2、M128s_v2 | 2,048 GiB | 2,000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200| 14,000 | 
| M192ids_v2、M192is_v2 | 2,048 GiB | 2,000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200| 14,000 | 
| M128ms、M128dms_v2、M128ms_v2 | 3,892 GiB | 2,000 MBps | 4 x P30 | 800 MBps | 无突发 | 20,000 | 无突发 | 
| M192ims、M192idms_v2 | 4,096 GiB | 2,000 MBps | 4 x P30 | 800 MBps | 无突发 | 20,000 | 无突发 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 4 x P30 | 800 MBps | 无突发 | 20,000| 无突发 | 
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 4 x P40 | 1,000 MBps | 无突发 | 30,000 | 无突发 |
| M416s_v2 | 5,700 GiB | 2,000 MBps | 4 x P40 | 1,000 MBps | 无突发 | 30,000 | 无突发 |
| M416ms_v2 | 11,400 GiB | 2,000 MBps | 4 x P50 | 1,000 MBps | 无突发 | 30,000 | 无突发 |


对于 /hana/log 卷， 配置如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/log 卷 | 预配的吞吐量 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 | 
| M64ls | 512 GiB | 1,000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 | 
| M32dms_v2、M32ms_v2 | 875 GiB | 500 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M64s、M64ds_v2、M64s_v2 | 1,024 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M64ms、M64dms_v2、M64ms_v2 | 1,792 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M128s、M128ds_v2、M128s_v2 | 2,048 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500| 
| M192ids_v2、M192is_v2 | 2,048 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500| 
| M128ms、M128dms_v2、M128ms_v2 | 3,892 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |
| M192idms_v2、M192ims_v2 | 4,096 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M416s_v2 | 5,700 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M416ms_v2 | 11,400 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 


对于其他卷，配置如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M32dms_v2、M32ms_v2 | 875 GiB | 500 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64s、M64ds_v2、M64s_v2 | 1,024 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms、M64dms_v2、M64ms_v2 | 1,792 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s、M128ds_v2、M128s_v2 | 2,048 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M192ids_v2、M192is_v2  | 2,048 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms、M128dms_v2、M128ms_v2 | 3,892 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M192idms_v2、M192ims_v2  | 4,096 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850 GiB | 1,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700 GiB | 2,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


检查建议的不同卷的存储吞吐量是否满足所要运行的工作负载。 如果工作负载要求对 /hana/data 和 /hana/log 使用更高规格的卷，则需要增加 Azure 高级存储 VHD 数量 。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，至少需要将构成 /hana/log 卷的 Azure 高级存储磁盘部署为托管磁盘。 在[写入加速器](../../how-to-enable-write-accelerator.md)一文中可以找到有关 Azure 写入加速器的更多详细说明和限制。

对于 HANA 认证的 Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) 系列和 [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series) 系列 VM，需要对 /hana/data 和 /hana/log 卷使用 ANF 。 或者，需要仅对 /hana/log 卷使用 Azure 超级磁盘存储而不是 Azure 高级存储。 因此，Azure 高级存储上的 /hana/data 卷的配置可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | 预配的吞吐量 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1,500 | 10,500|
| E48ds_v4 | 384 GiB | 1,152 MBps | 3 x P15 |  375 MBps |510 MBps | 3,300  | 10,500 | 
| E64ds_v4 | 504 GiB | 1,200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3,300 | 10,500 | 
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 3 x P15 |  375 MBps | 510 MBps | 3,300 | 10,500 | 

对于其他卷（包括超级磁盘上的 /hana/log），配置可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1,152 MBps | 192 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200 MBps | 256 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1,200 MBps | 220 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure 超级磁盘存储配置
其他 Azure 存储类型称为 [Azure 超级磁盘](../../disks-types.md#ultra-disk)。 目前提供的 Azure 存储与超级磁盘之间的显著区别在于，磁盘功能不再受到磁盘大小的约束。 作为客户，你可以为超级磁盘定义以下功能：

- 磁盘大小范围（从 4 GiB 到 65,536 GiB）
- IOPS 范围（从 100 IOPS 到 160K IOPS，最大值也取决于 VM 类型）
- 存储吞吐量（从 300 MB/秒到 2,000 MB/秒）

超级磁盘使你能够定义一个磁盘来满足大小、IOPS 和磁盘吞吐量范围。 无需使用基于 Azure 高级存储的 LVM 或 MDADM 等逻辑卷管理器来构造可满足 IOPS 和存储吞吐量要求的卷。 可以在超级磁盘和高级存储之间运行配置组合。 因此，你可以将超级磁盘的使用限制为性能关键型 /hana/data 和 /hana/log 卷，并覆盖使用 Azure 高级存储的其他卷 

与高级存储相比，超级磁盘的其他优点可能是读取延迟更低。 如果希望减少 HANA 启动时间以及向内存中后续加载数据，速度较快的读取延迟可能会有优势。 如果 HANA 在编写保存点，超级磁盘存储的优势也可以体现出来。 

> [!NOTE]
> 超级磁盘并非在所有 Azure 区域都提供，并且还尚不完全支持下面列出的 VM 类型。 有关超级磁盘的可用区域以及受支持的 VM 系列的详细信息，请参阅文章 [Azure 中提供哪些可用磁盘类型？](../../disks-types.md#ultra-disk)。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生产推荐的使用纯超级磁盘配置的存储解决方案
在此配置中，可以单独保留 /hana/data 和 /hana/log 卷 。 建议的值派生自 KPI，即 SAP 必须根据 [SAP TDI 存储白皮书](https://blogs.saphana.com/wp-content/uploads/2015/02/Storage-Whitepaper-2-54.pdf)中建议的对 SAP HANA 和存储配置的 VM 类型进行认证。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

> [!NOTE]
> Azure 超级磁盘强制要求每 GB 磁盘容量至少 2 IOPS


| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 卷 | /hana/data I/O 吞吐量 | /hana/data IOPS | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 200 GB | 400 MBps | 2,500 | 80 GB | 250 MB | 1,800 |
| E32ds_v4 | 256 GiB | 768 MB/秒 | 300 GB | 400 MBps | 2,500 | 128 GB | 250 MBps | 1,800 |
| E48ds_v4 | 384 GiB | 1152 MB/秒 | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1,800 |
| E64ds_v4 | 504 GiB | 1200 MB/秒 | 610 GB | 400 MBps | 3,500 |  256 GB | 250 MBps | 1,800 |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 610 GB | 400 MBps | 3,500 | 220 GB | 250 MB | 1,800 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 2,500 | 96 GB | 250 MBps  | 1,800 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 2,500 | 256 GB | 250 MBps  | 1,800 |
| M64ls | 512 GiB | 1,000 MB/秒 | 620 GB | 400 MBps | 3,500 | 256 GB | 250 MBps  | 1,800 |
| M32dms_v2、M32ms_v2 | 875 GiB | 500 MB/秒 |  1,200 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M64s、M64ds_v2、M64s_v2 | 1,024 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M64ms、M64dms_v2、M64ms_v2 | 1,792 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M128s、M128ds_v2、M128s_v2 | 2,048 GiB | 2,000 MB/秒 |2,400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 |
| M192ids_v2、M192is_v2 | 2,048 GiB | 2,000 MB/秒 |2,400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M128ms、M128dms_v2、M128ms_v2 | 3,892 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |9,600 | 512 GB | 250 MBps  | 2,500 | 
| M192idms_v2、M192ims_v2 | 4,096 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |9,600 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 750 MBps | 14,400 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,000 MBps | 14,400 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 28,800 | 512 GB | 400 MBps  | 4,000 |   

**列出的值旨在作为起点，并需要根据实际需求进行评估。** Azure 超级磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。   

> [!NOTE]
> 到目前为止，超级磁盘存储的存储快照不可用。 这会阻止 Azure 备份服务对 VM 快照的使用


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 文件上的 NFS v4.1 卷
有关适用于 HANA 的 ANF 的详细信息，请阅读[适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)文档




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure 高级存储的成本意识解决方案
到目前为止，本文档中[对 Azure M 系列虚拟机使用包含高级存储和 Azure 写入加速器的解决方案](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines)一节中所述的 Azure 高级存储解决方案适用于 SAP HANA 生产支持的方案。 生产支持的配置的特征之一是将 SAP HANA 数据和重做日志的卷分离成两个不同的卷。 进行这种分离的原因是卷上的工作负载特征不同。 而且对于建议的生产配置，可能需要不同类型的缓存，或甚至需要不同类型的 Azure 块存储。 对于非生产方案，生产系统需考虑的一些注意事项可能不适用于更低端的非生产系统。 因此，可以合并 HANA 数据和日志卷， 尽管最终会出现一些问题，例如最终无法满足生产系统所需的特定吞吐量或延迟 KPI。 降低此类环境成本的另一个方面就是使用 [Azure 标准 SSD 存储](./planning-guide-storage.md#azure-standard-ssd-storage)。 请记住，选择标准 SSD 或标准 HDD Azure 存储会对你的单个 VM SLA 产生影响，如[虚拟机 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 一文中所述。

对于此类配置，降低成本的替代方法可能如下所示：


| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM 类型未通过 HANA 认证 <br /> 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5,000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM 类型未通过 HANA 认证 <br /> 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5,000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1,152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| E64v3 | 432 GiB | 1,200 MB/秒 | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/秒 |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会实现低于 1 毫秒的存储延迟<sup>1</sup> |
| M64ls | 512 GiB | 1,000 MB/秒 | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10,000<sup>2</sup> |
| M32dms_v2、M32ms_v2 | 875 GiB | 500 MB/秒 | 6 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5,000<sup>2</sup> |
| M64s、M64ds_v2、M64s_v2 | 1,024 GiB | 1,000 MB/秒 | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10,000<sup>2</sup> |
| M64ms、M64dms_v2、M64ms_v2| 1,792 GiB | 1,000 MB/秒 | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10,000<sup>2</sup> |
| M128s、M128ds_v2、M128s_v2 | 2,048 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20,000<sup>2</sup> |
| M192ids_v2、M192is_v2 | 2,048 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20,000<sup>2</sup> |
| M128ms、M128dms_v2、M128ms_v2  | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20,000<sup>2</sup> |
| M192idms_v2、M192ims_v2  | 4,096 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20,000<sup>2</sup> |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10,000<sup>2</sup> |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10,000<sup>2</sup> |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20,000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20,000<sup>2</sup> |


<sup>1</sup> [Azure 写入加速器](../../how-to-enable-write-accelerator.md)不能与 Ev4 和 Ev4 VM 系列一起使用。 由于使用 Azure 高级存储，I/O 延迟不会低于 1 毫秒

<sup>2</sup> VM 系列支持 [Azure 写入加速器](../../how-to-enable-write-accelerator.md)，但写入加速器的 IOPS 限制可能会限制磁盘配置 IOPS 功能

如果组合 SAP HANA 的数据和日志卷，生成条带化卷的磁盘不应启用读取缓存或读/写缓存。

列出的 VM 类型未通过 SAP 认证，因此未在所谓的 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中列出。 客户的反馈是这些未列出的 VM 类型已在某些非生产任务中成功使用。


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [Azure 虚拟机的 SAP HANA 高可用性指南](./sap-hana-availability-overview.md)。