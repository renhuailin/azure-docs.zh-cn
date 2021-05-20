---
title: 将大型机存储转移到 Azure 存储
description: Azure 存储资源支持大规模缩放，可帮助基于大型机的组织迁移并实现 IBM z14 应用程序现代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954354"
---
# <a name="move-mainframe-storage-to-azure"></a>将大型机存储转移到 Azure

要在 Microsoft Azure 上运行大型机工作负载，需要知道大型机功能与 Azure 相比如何。 可大规模缩放的存储资源可帮助组织开始实现现代化，而无需放弃他们所依赖的应用程序。

Azure 提供与大型机类似的功能和存储容量，与基于 IBM z14 （截至撰写本文时最新型号）的系统不相上下。 本文将介绍如何在 Azure 上获得相当的结果。

## <a name="mainframe-storage-at-a-glance"></a>大型机存储概览

IBM 大型机的存储按特征可分为两种。 第一种是直接访问存储设备 (DASD)。 第二种是顺序存储。 为便于管理存储，大型机提供数据设施存储管理子系统 (DFSMS)。 该系统可管理对各种存储设备的数据访问。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) 是指单独的辅助存储设备（非内存中），允许使用唯一地址直接访问数据。 最初，DASD 一词应用于旋转磁盘、磁头或数据单元。 但是，现在该术语也适用于固态硬盘 (SSD)、存储区域网络 (SAN)、网络连接存储 (NAS) 和光学驱动器。 就本文档来说，DASD 是指旋转磁盘、SAN 和 SSD。

相比 DASD 存储，大型机上的顺序存储是指磁带驱动器等设备，即先从某个起点访问数据，然后再以行读取或写入数据。

存储设备通常使用光纤连接 (FICON) 进行连接，或者使用 [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm) 直接在大型机的 IO 总线上访问。HiperSockets 是 IBM 为在服务器上的分区与虚拟机监控程序之间实现高速通信而推出的一项技术。

大多数大型机系统都将存储分为两种类型：

- 联机存储（也称为热存储）用于满足日常运营需求。 DASD 存储通常用于此目的。 不过，每日磁带备份（逻辑或物理）等顺序存储也可用于实现此目的。

- 存档存储（也称为冷存储）不保证能够在给定时间装载。 相反，我们根据需要来装载和访问它们。 存档存储通常使用顺序磁带备份存储来实现（逻辑或物理）。

## <a name="mainframe-versus-io-latency-and-iops"></a>大型机与 IO 延迟和 IOPS

大型机通常用于需要高性能 IO 和低 IO 延迟的应用程序。 他们通过 FICON 连接到 IO 设备和 HiperSockets 来实现此目的。 当使用 HiperSockets 将应用程序和设备直接连接到大型机的 IO 通道时，可以实现微秒级延迟。

## <a name="azure-storage-at-a-glance"></a>Azure 存储概览

适用于存储的 Azure 基础结构即服务 ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) 选项提供与大型机相当的容量。

Microsoft 为 Azure 中托管的应用程序提供 PB 级存储空间，并且可以使用多种存储选项。 这些选项的范围广泛，从面向高性能应用的 SSD 存储到面向批量存储和存档的低成本 blob 存储，应有尽有。 此外，Azure 还为存储提供了数据冗余选项，而在大型机环境中需要投入更多精力才能实现该功能。

Azure 存储提供 [Azure 磁盘](../../../managed-disks-overview.md)、[Azure 文件](../../../../storage/files/storage-files-introduction.md)和 [Azure Blob](../../../../storage/blobs/storage-blobs-overview.md)，如下表所示。 详细了解[每种选项的适用情况](../../../../storage/common/storage-introduction.md)。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>类型</th><th>说明</th><th>适用于以下任务:</th></tr>
</thead>
<tbody>
<tr><td>Azure 文件
</td>
<td>
提供 SMB 接口、客户端库以及 <a href="/rest/api/storageservices/file-service-rest-api">REST</a> 接口，借助该接口可随时随地访问存储的文件。
</td>
<td><ul>
<li>当应用程序使用本机文件系统 API 在自身与 Azure 中运行的其他应用程序之间共享数据时，可将该应用程序即时转移到云中。</li>
<li>存储需要通过许多 VM 访问的开发和调试工具。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供客户端库和 <a href="/rest/api/storageservices/blob-service-rest-api">REST</a> 接口，借助该接口可在块 blob 中大规模存储和访问非结构化数据。 还支持 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>，用于企业大数据分析解决方案。
</td>
<td><ul>
<li>支持应用程序中的流式处理和随机访问方案。</li>
<li>可以从任何位置访问应用程序数据。</li>
<li>在 Azure 上生成企业数据湖并执行大数据分析。</li>
</ul></td>
</tr>
<tr><td>Azure 磁盘
</td>
<td>提供客户端库和 <a href="/rest/api/compute/disks">REST</a> 接口，借助该接口可通过附加的虚拟硬盘永久地存储和访问数据。
</td>
<td><ul>
<li>直接迁移使用本机文件系统 API 在持久性磁盘中读取和写入数据的应用程序。</li>
<li>存储不需要从磁盘附加到的虚拟机外部访问的数据。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 热（联机）和冷（存档）存储

给定系统的存储类型取决于系统要求，包括存储大小、吞吐量和 IOPS。 对于大型机上的 DASD 类型存储，Azure 上的应用程序通常改为使用 Azure 磁盘驱动器存储。 对于大型机存档存储，Azure 上使用 Blob 存储。

在 Azure 上，SSD 提供的存储性能最高。 截至撰写本文档之时，可用的选项如下所示：

| 类型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| 超级 SSD    | 4 GB 至 64 TB  | 1,200 至 160,000 IOPS |
| 高级·SSD  | 32 GB 至 32 TB | 12 至 15,000 IOPS     |
| 标准 SSD | 32 GB 至 32 TB | 12 至 2,000 IOPS      |

Azure 上 Blob 存储提供的存储量最大。 除存储大小之外，Azure 还提供托管存储以及非托管存储。 使用托管存储时，Azure 负责管理基础存储帐户。 使用非托管存储时，用户负责设置适当大小的 Azure 存储帐户以满足存储要求。

## <a name="next-steps"></a>后续步骤

- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [在 Azure 虚拟机上重新托管大型机](../overview.md)
- [将大型机计算转移到 Azure](mainframe-compute-Azure.md)
- [确定何时使用 Azure Blob、Azure 文件或 Azure 磁盘](../../../../storage/common/storage-introduction.md)
- [适用于 Azure VM 工作负载的标准 SSD 托管磁盘](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM 资源

- [Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)（IBM Z 上的并行系统联合体）
- [IBM CICS and the Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)（IBM CICS 和耦合设备：进阶知识）
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)（创建所需用户以安装 Db2 pureScale 功能）
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)（Db2icrt - 创建实例命令）
- [Db2 pureScale Clustered Database Solution](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)（Db2 pureScale 群集数据库解决方案）
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多迁移资源

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)（Azure 虚拟数据中心提升和迁移指南）
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)