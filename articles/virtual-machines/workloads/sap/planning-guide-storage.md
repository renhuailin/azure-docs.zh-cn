---
title: 适用于 SAP 工作负载的 Azure 存储类型
description: 规划适用于 SAP 工作负载的 Azure 存储类型
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0af6df954dda4e5af6335776b1f93f929da5834e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323723"
---
# <a name="azure-storage-types-for-sap-workload"></a>适用于 SAP 工作负载的 Azure 存储类型
Azure 具有多种存储类型，它们在功能、吞吐量、延迟和价格上各不相同。 某些存储类型不可用于 SAP 方案，或者只能在此类方案中受限使用。 但是，有几种 Azure 存储类型非常适合特定的 SAP 工作负载方案或针对特定的 SAP 工作负载方案进行了优化。 尤其是对于 SAP HANA，某些 Azure 存储类型经认证可在 SAP HANA 中使用。 本文档将介绍不同类型的存储、其功能以及在 SAP 工作负载和 SAP 组件中的可用性。

有关整篇文章中使用的单位的注解。 公有云供应商已改用 GiB ([Gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) 或 TiB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte))（而不是 GB 或 TB）作为大小单位。 因此，所有 Azure 文档和定价都使用这些单元。  在整篇文档中，我们只会提到这些 MiB、GiB 和 TiB 大小单位。 你可能需要使用 MB、GB 和 TB 进行规划。 因此，如果需要适用于 400 MiB/秒吞吐量（而不是 250 MiB/秒吞吐量）的大小，请注意计算中的一些细微差别。

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure 存储复原

Microsoft Azure 的标准 HDD 和标准 SSD 存储、Azure 高级存储和超级磁盘将基本 VHD（含 OS）和 VM 附加的数据磁盘或 VHD 在三个不同存储节点上保留三个副本。 在存储节点发生故障时，故障转移到另一个副本并为新副本设定种子是透明的。 由于这种冗余，不需要跨多个 Azure 磁盘使用任何类型的存储冗余层。 这称为本地冗余存储 (LRS)。 LRS 是 Azure 中此类存储的默认设置。 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)提供足够的冗余来实现与其他本机 Azure 存储相同的 SLA。

还有其他多种冗余方法，[Azure 存储复制](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)一文中介绍了所有这些方法，它们适用于 Azure 提供的某些不同存储类型。 

另请注意，不同的 Azure 存储类型会影响[虚拟机 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 中发布的单一 VM 可用性 SLA。


### <a name="azure-managed-disks"></a>Azure 托管磁盘

托管磁盘是 Azure 资源管理器中的一种资源类型，可用来代替 Azure 存储帐户中存储的 VHD。 托管磁盘会自动适应其所附加到的虚拟机 [可用性集][virtual-machines-manage-availability]，因而提高了虚拟机和虚拟机上所运行服务的可用性。 有关详细信息，请阅读[概述文章](../../managed-disks-overview.md)。

与复原能力相关，此示例演示托管磁盘的优点：

- 你将在 Azure 可用性集中为 SAP 系统部署两个 DBMS VM 
- 当 Azure 部署 VM 时，含 OS 映像的磁盘将放置在其他存储群集中。 这可避免两个 VM 都受到单个 Azure 存储群集问题的影响
- 当你创建新的托管磁盘以分配给这些 VM 来存储数据库的数据和日志文件时，这两个 VM 分配到的新磁盘也将分别部署在不同存储群集中，因此第一个 VM 的所有磁盘均不会与第二个 VM 的磁盘共享存储群集

如果在客户定义的存储帐户中不使用托管磁盘进行部署，则磁盘分配是任意的，并且不会意识到将 VM 部署在 AvSet 内是为了实现复原。

> [!NOTE]
> 出于此原因以及专门通过托管磁盘实现的其他一些改进，我们要求全新部署的、对磁盘使用 Azure 块存储（除 Azure NetApp 文件以外的其他所有 Azure 存储）的 VM 为基本 VHD/OS 磁盘以及包含 SAP 数据库文件的数据磁盘使用 Azure 托管磁盘。 不管你是通过可用性集、跨可用性区域还是独立于集和区域部署 VM，都需要满足此要求。 用于存储备份目的的磁盘不一定需要是托管磁盘。

> [!NOTE]
> Azure 托管磁盘仅提供本地冗余 (LRS)。 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP 工作负载的存储方案
Azure 中部署的各种堆栈组件中的 SAP 工作负载需要持久性存储。 这些方案最起码需要满足如下所述的要求：

- 持久保存 VM 的基本 VHD，其中包含操作系统以及在该磁盘中安装的其他软件。 该磁盘/VHD 是 VM 的根目录。 对它所做的任何更改都需要持久保存， 以便下次停止再重启 VM 时，以前所做的所有更改都仍会保留。 在 Azure 将 VM 部署到其他主机（而不是最初运行该 VM 的主机）时，这一点尤其重要
- 永久性数据磁盘。 这些磁盘是附加的 VHD，用于存储应用程序数据。 此应用程序数据可以是数据库的数据和日志/重做文件、备份文件或软件安装。 这表示除包含操作系统的基本 VHD 以外的任何磁盘
- 包含 NetWeaver 或 S/4HANA 全局传输目录的文件共享或共享磁盘。 这些共享内容要么由在多个 VM 中运行的软件使用，要么用于构建高可用性故障转移群集方案
- /sapmnt 目录或 EDI 进程的公用文件共享，或类似内容。 这些共享内容要么由在多个 VM 中运行的软件使用，要么用于构建高可用性故障转移群集方案

接下来的几个部分将介绍适用于 SAP 工作负载和上述四种方案的不同 Azure 存储类型及其可用性。 [Azure 有哪些可用的磁盘类型？](../../disks-types.md)一文中介绍了不同 Azure 存储类型的一般用法分类。 有关使用适用于 SAP 工作负载的不同 Azure 存储类型的建议没有太大的差别。

有关适用于 S/4HANA SAP NetWeaver/应用层的 Azure 存储类型的支持限制，请参阅 [SAP 支持说明 2015553](https://launchpad.support.sap.com/#/notes/2015553)。有关 SAP HANA 已认证的和支持的 Azure 存储类型，请参阅 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)一文。

介绍不同 Azure 存储类型的部分将提供有关使用 SAP 支持的存储时的限制和各种可行性的更多背景信息。 

### <a name="storage-choices-when-using-dbms-replication"></a>使用 DBMS 复制时的存储选择
我们的引用体系结构预见到了 DBMS 功能的使用，如 SQL Server Always On、HANA 系统复制、Db2 HADR 或 Oracle 数据防护。 如果在两个或多个 Azure 虚拟机之间使用这些技术，则为每个 VM 选择的存储类型都必须相同。 这意味着，如果为 DBMS 系统选择的重做日志卷的存储是某个 VM 上的 Azure 高级存储，则相同的卷需要基于 Azure 高级存储，并且所有其他 VM 都处于相同的高可用性同步配置。 对于用于数据库文件的数据卷也是如此。
  

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP 存储方案的存储建议
在深入到细节之前，我们先根据本文档开头部分的内容提供汇总和建议。 特定 Azure 存储类型的详细信息将在本文档部分结束后提供。 表格中汇总了适用于 SAP 存储方案的存储建议：

| 使用方案 | 标准 HDD | 标准 SSD | 高级存储 | 超级磁盘 | Azure NetApp 文件 |
| --- | --- | --- | --- | --- | --- |
| OS 磁盘 | 不适用 |  受限适用（非生产） | 建议 | 不可用 | 不可用 |
| 全局传输目录 | 不支持 | 不支持 | 建议 | 建议 | 建议 |
| /sapmnt | 不适用 | 受限适用（非生产） | 建议 | 建议 | 建议 |
| DBMS 数据卷 SAP HANA M/Mv2 VM 系列 | 不支持 | 不支持 | 建议 | 建议 | 建议<sup>2</sup> |
| DBMS 日志卷 SAP HANA M/Mv2 VM 系列 | 不支持 | 不支持 | 建议<sup>1</sup> | 建议 | 建议<sup>2</sup> | 
| DBMS 数据卷 SAP HANA Esv3/Edsv4 VM 系列 | 不支持 | 不支持 | 建议 | 建议 | 建议<sup>2</sup> |
| DBMS 日志卷 SAP HANA Esv3/Edsv4 VM 系列 | 不支持 | 不支持 | 不支持 | 建议 | 建议<sup>2</sup> | 
| DBMS 数据卷非 HANA | 不支持 | 受限适用（非生产） | 建议 | 建议 | 仅适用于 Oracle Linux 上的特定 Oracle 版本和 Linux 上的 Db2 |
| DBMS 日志卷非 HANA M/Mv2 VM 系列 | 不支持 | 受限适用（非生产） | 建议<sup>1</sup> | 建议 | 仅适用于 Oracle Linux 上的特定 Oracle 版本和 Linux 上的 Db2 |
| DBMS 日志卷非 HANA 非 M/Mv2 VM 系列 | 不支持 | 受限适用（非生产） | 适用于最大为中型的工作负载 | 建议 | 仅适用于 Oracle Linux 上的特定 Oracle 版本和 Linux 上的 Db2 |


<sup>1</sup> 对 M/Mv2 VM 系列的日志/重做日志卷使用 [Azure 写入加速器](../../how-to-enable-write-accelerator.md)。<sup>2</sup> 使用 ANF 要求 /hana/data 和 /hana/log 位于 ANF 上 

下面列出了不同存储类型的预期特征：

| 使用方案 | 标准 HDD | 标准 SSD | 高级存储 | 超级磁盘 | Azure NetApp 文件 |
| --- | --- | --- | --- | --- | --- |
| 吞吐量/IOPS SLA | 否 | 否 | 是 | 是 | 是 |
| 延迟读取 | high | 中到高 | low | 亚毫秒级 | 亚毫秒级 |
| 延迟写入 | high | 中到高  | 低（亚毫秒级<sup>1</sup>） | 亚毫秒级 | 亚毫秒级 |
| HANA 支持 | 否 | 否 | 是<sup>1</sup> | 是 | 是 |
| 可以使用磁盘快照 | 是 | 是 | 是 | 否 | 是 |
| 使用可用性集时在不同存储群集上分配磁盘 | 通过托管磁盘 | 通过托管磁盘 | 通过托管磁盘 | 磁盘类型在通过可用性集部署的 VM 上不受支持 | 否<sup>3</sup> |
| 适应可用性区域 | 是 | 是 | 是 | 是 | 需要 Microsoft 介入 |
| 区域冗余 | 不适用于托管磁盘 | 不适用于托管磁盘 | 不适用于托管磁盘 | 否 | 否 |
| 异地冗余 | 不适用于托管磁盘 | 不适用于托管磁盘 | 否 | 否 | 否 |


<sup>1</sup> 对 M/Mv2 VM 系列的日志/重做日志卷使用 [Azure 写入加速器](../../how-to-enable-write-accelerator.md)

<sup>2</sup> 成本取决于预配的 IOPS 和吞吐量

<sup>3</sup> 创建不同的 ANF 容量池并不保证容量池部署到不同的存储单元


> [!IMPORTANT]
> 若要使用 Azure NetApp 文件 (ANF) 实现 1 毫秒以下的 I/O 延迟，需要与 Microsoft 协作，以在 VM 与基于 ANF 的 NFS 共享之间正确定位。 目前没有任何现有机制可以在部署的 VM 与托管在 ANF 上的 NFS 卷之间提供自动邻近定位。 假设不同的 Azure 区域采用不同的设置，在未根据邻近性原则分配 VM 和 NFS 共享的情况下，增大的网络延迟可能会导致 I/O 延迟超过 1 毫秒。


> [!IMPORTANT]
> 当前提供的基于 Azure 块存储的托管磁盘和 Azure NetApp 文件都不提供任何区域冗余或异地冗余。 因此，需要确保你的高可用性和灾难恢复体系结构不依赖于对这些托管磁盘、NFS 或 SMB 共享使用任何类型的 Azure 原生存储复制。


## <a name="azure-premium-storage"></a>Azure 高级存储
现已推出 Azure 高级 SSD 存储，此类存储旨在提供：

* 低 I/O 延迟
* IOPS 和吞吐量的 SLA
* 更小的 I/O 延迟变化性

此类存储适用于 DBMS 工作负载以及要求延迟低于 10 毫秒的存储流量，它还可以满足有关 IOPS 的 SLA。使用 Azure 高级存储时，吞吐量成本基础不在于此类磁盘中存储的实际数据量，而是此类磁盘的大小类别，它不考虑磁盘中存储的数据量。 还可在高级存储中创建不直接映射到[高级 SSD](../../disks-types.md#premium-ssd) 一文中所示大小类别的磁盘。 此文中的结论如下：

- 存储按范围组织的。 例如，513 GiB 到 1024 GiB 容量范围内的磁盘的功能和每月成本相同
- 每 GiB 的 IOPS 在各种大小类别中不是呈线性趋势。 低于 32 GiB 的较小磁盘的每 GiB IOPS 速率较高。 对于超过 32 GiB 到 1024 GiB 的磁盘，每 GiB 的 IOPS 速率为每 GiB 4-5 IOPS。 对于最大 32,767 GiB 的较大磁盘，每 GiB 的 IOPS 速率将低于 1
- 此存储的 I/O 吞吐量不会根据磁盘类别大小线性变化。 对于较小的磁盘，例如容量为 65 GiB 到 128 GiB 的类别，吞吐量大约为 780KB/GiB。 对于极大型磁盘（例如 32,767 GiB 磁盘），吞吐量大约为 28KB/GiB
- 在不更改磁盘容量的情况下无法更改 IOPS 和吞吐量 SLA


下面是 SAP 工作负载的功能矩阵：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 适用 | 所有系统 |
| 数据磁盘 | 适用 | 所有系统 - [特别适用于 SAP HANA](../../how-to-enable-write-accelerator.md) |
| SAP 全局传输目录 | YES | [支持](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 适用 | 所有系统 |
| 备份存储 | 适用 | 用于短期存储备份 |
| 共享/共享磁盘 | 不可用 | 需要 Azure 高级文件存储或第三方存储 |
| 复原能力 | LRS | 不为磁盘提供 GRS 或 ZRS |
| 延迟 | 低到中 | - |
| IOPS SLA | YES | - |
| IOPS 根据容量线性变化 | 托架中为半线性变化  | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 每个磁盘的最大 IOPS | 20,000 [与磁盘大小相关](https://azure.microsoft.com/pricing/details/managed-disks/) | 还要考虑 [VM 限制](../../sizes.md) |
| 吞吐量 SLA | YES | - |
| 吞吐量根据容量线性变化 | 托架中为半线性变化 | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 认证 | YES | [特别适用于 SAP HANA](../../how-to-enable-write-accelerator.md) |
| 可以使用磁盘快照 | YES | - |
| 可以使用 Azure 备份 VM 快照 | YES | [写入加速器](../../how-to-enable-write-accelerator.md)缓存磁盘除外  |
| 成本 | MEDIUM | - |

Azure 高级存储不使用 Azure 高级存储提供的常用缓存类型来实现 SAP HANA 存储延迟 KPI。 若要实现 SAP HANA 日志写入的存储延迟 KPI，需要根据[启用写入加速器](../../how-to-enable-write-accelerator.md)一文中所述使用 Azure 写入加速器缓存。 Azure 写入加速器可为所有其他 DBMS 系统的事务日志写入和重写日志写入带来好处。 因此，我们建议在所有 SAP DBMS 部署中使用它。 对于 SAP HANA，Azure 写入加速器必须与 Azure 高级存储结合使用。



摘要：Azure 高级存储是建议用于 SAP 工作负载的 Azure 存储类型之一。 此建议适用于非生产系统和生产系统。 Azure 高级存储适合处理数据库工作负载。 使用 Azure 写入加速器将显著降低针对 Azure 高级磁盘的写入延迟。 但是，对于具有高 IOPS 和高吞吐率的 DBMS 系统，要么需要过度预配存储容量，要么需要使用功能（例如 Windows 存储空间或 Linux 中的逻辑卷管理器）来构建条带集，以便一方面为你提供所需的容量，另一方面提供经济高效的必要 IOPS 或吞吐量。


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
- 执行事务日志或重做日志备份时读取突发


## <a name="azure-ultra-disk"></a>Azure 超级磁盘
Azure 超级磁盘为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 超级磁盘的其他一些优势包括能够动态更改磁盘以及工作负载的 IOPS 和吞吐量，而无需重启虚拟机 (VM)。 超级磁盘适用于数据密集型工作负载，例如 SAP DBMS 工作负载。 超级磁盘只能用作数据磁盘，而不能用作存储操作系统的基本 VHD 磁盘。 我们建议使用 Azure 高级存储作为基本 VHD 磁盘。

创建超级磁盘时，可以定义三个维度：

- 磁盘的容量。 范围为 4 GiB 到 65,536 GiB
- 磁盘的预配 IOPS。 不同的最大值适用于磁盘容量。 有关更多详细信息，请参阅[超级磁盘](../../disks-types.md#ultra-disk)一文
- 预配的存储带宽。 根据磁盘容量应用不同的最大带宽。 有关更多详细信息，请参阅[超级磁盘](../../disks-types.md#ultra-disk)一文

单个磁盘的成本根据可为特定磁盘单独定义的三个维度来确定。 


下面是 SAP 工作负载的功能矩阵：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 不可用 | - |
| 数据磁盘 | 适用 | 所有系统  |
| SAP 全局传输目录 | YES | [支持](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 适用 | 所有系统 |
| 备份存储 | 适用 | 用于短期存储备份 |
| 共享/共享磁盘 | 不可用 | 需要第三方组件 |
| 复原能力 | LRS | 不为磁盘提供 GRS 或 ZRS |
| 延迟 | 极低 | - |
| IOPS SLA | YES | - |
| IOPS 根据容量线性变化 | 托架中为半线性变化  | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 每个磁盘的最大 IOPS | 1,200 到 160,000 | 与磁盘容量相关 |
| 吞吐量 SLA | YES | - |
| 吞吐量根据容量线性变化 | 托架中为半线性变化 | [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 认证 | YES | - |
| 可以使用磁盘快照 | 是 | - |
| 可以使用 Azure 备份 VM 快照 | 是 | - |
| 成本 | 高于高级存储 | - |



摘要：Azure 超级磁盘是适合各种 SAP 工作负载的低延迟存储。 到目前为止，超级磁盘只能与通过可用性区域（区域部署）部署的 VM 结合使用。 超级磁盘目前不支持存储快照。 与所有其他存储不同，超级磁盘不能用作基本 VHD 磁盘。 超级磁盘非常适合 I/O 工作负载极不稳定且你想让已部署的存储吞吐量或 IOPS 适应存储工作负载模式（而不是调整大小以最大程度地利用带宽和 IOPS）的情况。


## <a name="azure-netapp-files-anf"></a>Azure NetApp 文件 (ANF)
[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)是 Microsoft 与 NetApp 合作的成果，旨在提供高性能的 Azure 原生 NFS 和 SMB 共享。 其重心是提供高带宽、低延迟的存储以实现 DBMS 部署方案，并最终通过 Azure 实现 NetApp 存储的典型操作功能。 NFS/SMB 共享在区分存储吞吐量和价格的三个不同服务级别中提供。 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)一文中介绍了服务级别。 对于不同类型的 SAP 工作负载，强烈建议使用以下服务级别：

- SAP DBMS 工作负载：“性能”级别，最好是使用“超级”级别
- SAPMNT 共享：“性能”级别，最好是使用“超级”级别
- 全局传输目录：“性能”级别，最好是使用“超级”级别

> [!NOTE]
> 最小预配大小为一个 4 TiB 单位（称为容量池）。 然后，在此容量池中创建卷。 可以构建的最小卷为 100 GiB。 可以 TiB 为增量扩展容量池。 有关定价信息，请查看 [Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/)一文

目前有多种 SAP 工作负载方案支持 ANF 存储：

- 为 SAP 的全局传输目录提供 SMB 或 NFS 共享
- 高可用性方案中的共享 sapmnt，如以下文档中所述：
    - [使用适用于 SAP 应用程序的 Azure NetApp 文件 (SMB) 实现 Windows 的 Azure VM 上的 SAP NetWeaver 高可用性](./high-availability-guide-windows-netapp-files-smb.md)
    - [带有适用于 SAP 应用程序的 Azure NetApp 文件的 SUSE Linux Enterprise Server 上 Azure VM 上的 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)
    - [使用适用于 SAP 应用程序的 Azure NetApp 文件实现 Red Hat Enterprise Linux 上的 SAP NetWeaver 的 Azure 虚拟机高可用性](./high-availability-guide-rhel-netapp-files.md)
- 为 /hana/data 和 /hana/log 卷使用 NFS v4.1 共享和/或为 /hana/shared 卷使用 NFS v4.1 或 NFS v3 卷的 SAP HANA 部署，如 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)一文中所述
- Suse 或 Red Hat Linux 来宾 OS 中的 IBM Db2
- 为 Oracle 数据和重做日志卷使用 [dNFS](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA) 的 Oracle Linux 来宾 OS 中的 Oracle 部署。 有关更多详细信息，请参阅[适用于 SAP 工作负载的 Azure 虚拟机 Oracle DBMS 部署](./dbms_guide_oracle.md)一文

> [!NOTE]
> 基于 Azure NetApp 文件的 NFS 或 SMB 共享不支持其他 DBMS 工作负载。 如果今后在此方面有变化，我们将提供更新和更改。

与 Azure 高级存储一样，如果需要遵守吞吐量的某些下限，则每 GB 的固定或线性吞吐量大小可能是个问题。 SAP HANA 就属于这种情况。 使用 ANF 时，此问题可能比使用 Azure 高级磁盘时更明显。 对于 Azure 高级磁盘，可以采用每 GiB 吞吐量相对较高的多个较小磁盘，并条带化这些磁盘，使其更具经济效益，并以更低的容量实现更高的吞吐量。 此类条带化不适用于 ANF 上托管的 NFS 或 SMB 共享。 此项限制会导致部署容量过多，如下所述：

- 例如，若要在 ANF 上托管的 NFS 卷中实现 250 MiB/秒的吞吐量，需要部署“超级”服务级别的 1.95 TiB 容量。 
- 若要实现 400 MiB/秒的吞吐量，需要部署 3.125 TiB 容量。 但是，可能需要过度预配容量才能实现所需的卷吞吐量。 这种容量过度预配会影响较小 HANA 实例的定价。 
- 在 ANF 顶层为 SAP /sapmnt 目录使用 NFS 期间，通常远远还达不到 Azure NetApp 文件强制实施的下限容量（100 GiB 到 150 GiB）。 但是，客户体验表明，12.8 MiB/秒的相关吞吐量（使用“超级”服务级别）可能不足，并可能对 SAP 系统的稳定性产生负面影响。 在这种情况下，客户可以通过增大 /sapmnt 卷的容量来避免出现问题，以便为该卷提供更高的吞吐量。

下面是 SAP 工作负载的功能矩阵：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 不可用 | - |
| 数据磁盘 | 适用 | 仅限 SAP HANA  |
| SAP 全局传输目录 | YES | SMB 和 NFS |
| SAP sapmnt | 适用 | 所有系统 SMB（仅适用于 Windows）或 NFS（仅适用于 Linux） |
| 备份存储 | 适用 | - |
| 共享/共享磁盘 | YES | SMB 3.0、NFS v3 和 NFS v4.1 |
| 复原能力 | LRS | 不为磁盘提供 GRS 或 ZRS |
| 延迟 | 极低 | - |
| IOPS SLA | YES | - |
| IOPS 根据容量线性变化 | 严格线性  | 取决于[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| 吞吐量 SLA | YES | - |
| 吞吐量根据容量线性变化 | 托架中为半线性变化 | 取决于[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA 认证 | YES | - |
| 可以使用磁盘快照 | YES | - |
| 可以使用 Azure 备份 VM 快照 | 是 | - |
| 成本 | 高于高级存储 | - |


ANF 存储的其他内置功能：

- 执行卷快照的功能
- 从快照中克隆 ANF 卷
- 从快照还原卷（快照还原）

摘要：Azure NetApp 文件是经过 HANA 认证的低延迟存储，可用于部署 NFS 和 SMB 卷或共享。 该存储具有三个不同的服务级别，以线性方式为卷的每 GiB 容量提供不同的吞吐量和 IOPS。 通过 ANF 存储，可以使用备用节点部署 SAP HANA 横向扩展方案。 该存储适用于根据需要为 /sapmnt 或 SAP 全局传输目录提供文件共享。 ANF 存储附带可作为本机 NetApp 功能提供的功能可用性。  



## <a name="azure-standard-ssd-storage"></a>Azure 标准 SSD 存储
与 Azure 标准 HDD 存储相比，Azure 标准 SSD 存储提供更高的可用性、一致性、可靠性和更低的延迟。 它已针对需要在较低 IOPS 级别保持一致性能的工作负载进行优化。 此存储是 IOPS 和吞吐量需求较低的非生产 SAP 系统使用的最低配存储。 下面是 SAP 工作负载的功能矩阵：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 受限适用 | 非生产系统 |
| 数据磁盘 | 受限适用 | IOPS 和延迟需求较低的某些非生产系统 |
| SAP 全局传输目录 | 是 | [不支持](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 受限适用 | 非生产系统 |
| 备份存储 | 适用 | - |
| 共享/共享磁盘 | 不可用 | 需要第三方组件 |
| 复原能力 | LRS、GRS | 不为磁盘提供 ZRS |
| 延迟 | high | 对于 SAP 全局传输目录或生产系统过高 |
| IOPS SLA | 是 | - |
| 每个磁盘的最大 IOPS | 500 | 与磁盘大小无关 |
| 吞吐量 SLA | 是 | - |
| HANA 认证 | 是 | - |
| 可以使用磁盘快照 | YES | - |
| 可以使用 Azure 备份 VM 快照 | YES | - |
| 成本 | LOW | - |



摘要：对于非生产 VM 的基本 VHD，以及对延迟相对不敏感和/或 IOPS 和吞吐率较低的最终 DBMS 部署，建议的最低配置是 Azure 标准 SSD 存储。 此 Azure 存储类型不受支持，不再用于托管 SAP 全局传输目录。 



## <a name="azure-standard-hdd-storage"></a>Azure 标准 HDD 存储
在 2014 年当 Azure 基础结构经认证适用于 SAP NetWeaver 工作负载时，Azure 标准 HDD 存储是唯一的存储类型。 在 2014 年，Azure 虚拟机都比较小，而且存储吞吐量较低。 因此，此存储类型刚好能够满足需求。 该存储非常适合对延迟不敏感的工作负载，而在 SAP 空间中很难遇到这种工作负载。 随着 Azure VM 吞吐量以及这些 VM 生成的工作负载不断增长，我们不再认为此存储类型适合在 SAP 方案中使用。 下面是 SAP 工作负载的功能矩阵：

| 功能| 评论| 备注/链接 | 
| --- | --- | --- | 
| OS 基本 VHD | 不适用 | - |
| 数据磁盘 | 不适用 | - |
| SAP 全局传输目录 | 是 | [不支持](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 是 | 不支持 |
| 备份存储 | 适用 | - |
| 共享/共享磁盘 | 不可用 | 需要 Azure 文件存储或第三方存储 |
| 复原能力 | LRS、GRS | 不为磁盘提供 ZRS |
| 延迟 | high | 对于使用 DBMS 的方案、SAP 全局传输目录或 sapmnt/saploc 过高 |
| IOPS SLA | 是 | - |
| 每个磁盘的最大 IOPS | 500 | 与磁盘大小无关 |
| 吞吐量 SLA | 是 | - |
| HANA 认证 | 是 | - |
| 可以使用磁盘快照 | YES | - |
| 可以使用 Azure 备份 VM 快照 | YES | - |
| 成本 | LOW | - |



摘要：标准 HDD 是只能用于存储 SAP 备份的 Azure 存储类型。 仅应将其用作相当不活跃的系统（例如用于在此处查找数据的停用系统）的基本 VHD。 但是，活动的开发、QA 或生产 VM 不应基于该存储， 数据库文件也不应在该存储上托管


## <a name="azure-vm-limits-in-storage-traffic"></a>存储流量中的 Azure VM 限制
与本地方案相反，所选的单个 VM 类型在可以实现的存储带宽方面发挥着重要作用。 对于不同的存储类型，需要考虑：

| 存储类型| Linux | Windows | 注释 |
| --- | --- | --- | --- |
| 标准 HDD | [Azure 中 Linux VM 的大小](../../sizes.md) | [Azure 中 Windows VM 的大小](../../sizes.md) | 可能很难改变中大型 VM 的存储限制 |
| 标准 SSD | [Azure 中 Linux VM 的大小](../../sizes.md) | [Azure 中 Windows VM 的大小](../../sizes.md) | 可能很难改变中大型 VM 的存储限制 |
| 高级存储 | [Azure 中 Linux VM 的大小](../../sizes.md) | [Azure 中 Windows VM 的大小](../../sizes.md) | 很容易达到存储配置中的 IOPS 或存储吞吐量 VM 限制 |
| 超级磁盘存储 | [Azure 中 Linux VM 的大小](../../sizes.md) | [Azure 中 Windows VM 的大小](../../sizes.md) | 很容易达到存储配置中的 IOPS 或存储吞吐量 VM 限制 |
| Azure NetApp 文件 | [Azure 中 Linux VM 的大小](../../sizes.md) | [Azure 中 Windows VM 的大小](../../sizes.md) | 存储流量使用的是网络吞吐量带宽，而不是存储带宽！ |

至于限制，你可以注意到：

- VM 越小，可以附加的磁盘就越少。 此限制不适用于 ANF。 由于装载了 NFS 或 SMB 共享，因此你不会遇到要附加的共享卷数的限制
- VM 具有 I/O 吞吐量和 IOPS 方面的限制，高级存储磁盘和超级磁盘很容易超出该限制
- 对于 ANF，流到共享卷的流量将使用 VM 的网络带宽（而不是存储带宽）
- 对于两位数 TiB 容量空间中的大型 NFS 卷，基于 Linux 对与共享卷交互的单个会话的限制，从单个 VM 中访问此类卷的吞吐量将趋于平稳。 

在 SAP 系统的生命周期中增大 Azure VM 的大小时，应评估新的和更大 VM 类型的 IOPS 和存储吞吐量限制。 在某些情况下，根据 Azure VM 的新功能调整存储配置也很合理。 


## <a name="striping-or-not-striping"></a>条带化或不条带化
通过将包含多个 Azure 磁盘的条带集创建为一个更大的卷，可以将各磁盘的 IOPS 和吞吐量累积到一个卷中。 它仅用于 Azure 标准存储和 Azure 高级存储。 Azure 超级磁盘无需使用条带集，因为你可以在其中不受磁盘容量的影响配置吞吐量和 IOPS。 基于 NFS 或 SMB 的共享卷无法条带化。 由于 Azure 高级存储吞吐量和 IOPS 具有非线性特性，因此你可以使用相同的 IOPS 和吞吐量来预配比大型单个 Azure 高级存储磁盘更小的容量。 这就是使用 Azure 高级存储以更低成本实现更高吞吐量或 IOPS 的方法。 例如，跨两个 P15 高级存储磁盘进行条带化可提供： 

- 250 MiB/秒的吞吐量。此类卷将具有 512 GiB 的容量。 如果要使用单个磁盘来提供每秒 250 MiB 的吞吐量，则需要选择具有 2 TiB 容量的 P40 磁盘。 
- 400 MiB/秒的吞吐量，这是通过条带化四个 P10 高级存储磁盘（条带化后总容量为 512 GiB）实现的。 如果要使单个磁盘的每秒最低吞吐量为 500 MiB，则需要选择具有 8 TiB 容量的 P60 高级存储磁盘。 由于高级存储的成本与容量几乎成线性关系，可以使用条带化来节省成本。

条带化时需要遵循一些规则：

- 不应使用在 VM 中配置的存储，因为 Azure 存储已经使数据保持冗余
- 应用条带集的磁盘必须具有相同的大小

跨多个较小的磁盘进行条带化是使用 Azure 高级存储实现良好性价比的最佳方法。 据了解，条带化具有一些额外的部署和管理开销。

有关具体的条带大小建议，请参阅不同 DBMS 的文档，例如 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)。




## <a name="next-steps"></a>后续步骤
阅读以下文章：

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md)
- [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)
