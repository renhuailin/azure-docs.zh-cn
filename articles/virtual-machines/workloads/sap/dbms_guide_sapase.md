---
title: 适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: 适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/15/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aabb53a573ee8a3ccc5d98ab8316fee560dbf625
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603897"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署

本文档介绍在 Azure IaaS 中部署 SAP 时要考虑的多个不同领域。 在阅读本文档之前，应已经阅读了[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)文档以及 [Azure 文档上的 SAP 工作负荷](./get-started.md)中的其他指南。 本文档介绍 Linux 和 Windows 操作系统上运行的 SAP ASE。 Azure 上支持的最低版本为 SAP ASE 16.0.02 (Release 16 Support Pack 2)。 建议部署最新的 SAP 版本和最新的补丁级别。  建议的最低版本为 SAP ASE 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7)。  可以在[目标 ASE 16.0 发行计划和 CR 列表信息](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)中找到 SAP 的最新版本。

除了“SAP 产品可用性对照表”以外，还可在以下位置找到有关 SAP 应用程序的版本支持或安装媒体位置的更多信息：

- [SAP 支持说明 #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP 支持说明 #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP 支持说明 #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP 支持说明 #1973241](https://launchpad.support.sap.com/#/notes/1973241)

备注：在 SAP 内部和外部的所有文档中，产品名称写作 Sybase ASE 或 SAP ASE，在某些情况下还会混用这两种称法。 为保持一致，我们在本文档中使用了名称 SAP ASE。

## <a name="operating-system-support"></a>操作系统支持
“SAP 产品可用性对照表”包含每个 SAP 应用程序支持的操作系统和 SAP 内核组合。  完全支持 Linux 发行版 SUSE 12.x、SUSE 15.x 和 Red Hat 7.x。  不支持将 Oracle Linux 用作 SAP ASE 的操作系统。  建议使用最新可用的 Linux 版本。 Windows 客户应该使用 Windows Server 2016 或 Windows Server 2019 版本。  早期版本的 Windows（例如 Windows 2012）在技术上受支持，但我们始终建议使用最新的 Windows 版本。


## <a name="specifics-to-sap-ase-on-windows"></a>有关 Windows 上的 SAP ASE 的具体信息
自从有了 Microsoft Azure，可以将现有 SAP ASE 应用程序迁移到 Azure 虚拟机。 借助 Azure 虚拟机中的 SAP ASE，可以轻松地将这些应用程序迁移到 Microsoft Azure，从而减少部署、管理和维护企业级应用程序的总拥有成本。 借助 Azure 虚拟机中的 SAP ASE，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。

Microsoft Azure 提供了许多不同的虚拟机类型，可运行最小的 SAP 系统和布局，也可运行包含成千上万用户的大型 SAP 系统和布局。 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中提供了SAP 已认证的不同 VM SKU 的 SAP 大小调整 SAPS 数目。

可以在[适用于 Windows 的 SAP ASE 安装指南](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)中找到有关在 Windows 上安装 SAP ASE 的文档

“锁定内存中的页”是一种防止将 SAP ASE 数据库缓冲区内容移出的设置。此设置适用于具有大量内存的大型繁忙系统。 有关详细信息，请联系 BC-DB-SYB。 


## <a name="linux-operating-system-specific-settings"></a>Linux 操作系统特定的设置
在 Linux VM 上运行 `saptune`。默认应已启用配置文件“SAP-ASE Linux 巨页”，可使用以下命令对此进行验证  

`cat /proc/meminfo` 

页大小通常为 2048 KB。 有关详细信息，请参阅 [Linux 上的巨页](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html)一文 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>有关 SAP ASE 部署的 VM 和磁盘结构的建议

[SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中列出的任何 VM 类型都支持适用于 SAP NetWeaver 应用程序的 SAP ASE。用于中型 SAP ASE 数据库服务器的典型 VM 类型包括 Esv3。  有多个 TB 的大型数据库可以利用 M 系列的 VM 类型。 启用 M 系列写入加速器可以改善 SAP ASE 事务日志磁盘写入性能。 由于 SAP ASE 执行日志写入的方式，应认真测试写入加速器。  查看 [SAP 支持说明 #2816580](../../how-to-enable-write-accelerator.md) 并考虑运行性能测试。  
写入加速器仅适用于事务日志磁盘。 磁盘级缓存应设置为“无”。 如果 Azure 写入加速器的改善程度达不到其他 DBMS 的水平，请不要感到惊讶。 根据 SAP ASE 写入事务日志的方式，Azure 写入加速器的加速效果可能很小，甚至根本没有效果。
建议为数据设备和日志设备使用不同的磁盘。  系统数据库 sybsecurity 和 `saptools` 不需要专用磁盘，可将其放置在包含 SAP 数据库数据的磁盘上和日志设备上 

![SAP ASE 的存储配置](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>文件系统、条带大小和 IO 平衡 
除非另行配置，否则 SAP ASE 会按顺序将数据写入磁盘存储设备。 这意味着，具有四个设备的空 SAP ASE 数据库只会将数据写入第一个设备。  仅当第一个设备已满时，才会写入到其他磁盘设备。  对每个 SAP ASE 设备的读取和写入 IO 量可能不同。 若要平衡所有可用 Azure 磁盘中的磁盘 IO，需要使用 Windows 存储空间或 Linux LVM2。 在 Linux 上，建议使用 XFS 文件系统格式化磁盘。 应使用性能测试来测试 LVM 条带大小。 128 KB 条带大小是良好的起点。 在 Windows 上，应测试 NTFS 分配单元大小 (AUS)。 可使用 64 KB 作为起点值。 

建议根据[在 SAP Adaptive Server Enterprise 中配置自动数据库空间扩展](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)一文和 [SAP 支持说明 #1815695](https://launchpad.support.sap.com/#/notes/1815695) 中所述配置自动数据库扩展。 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure 虚拟机上的 SAP ASE 磁盘和文件系统示例配置 
以下模板显示了适用于 Linux 和 Windows 的示例配置。 在确认虚拟机和磁盘配置之前，请确保单个 VM 的网络和存储带宽配额足够，可以满足业务要求。 同时请记住，对于不同的 Azure VM 类型，可附加到 VM 的最大磁盘数量各不相同。 例如，E4s_v3 VM 的存储 IO 吞吐量限制为 48 MB/秒。 如果数据库备份活动所需的存储吞吐量超过 48 MB/秒，则不可避免地需要使用存储带宽吞吐量更高的更大 VM 类型。 配置 Azure 存储时，还需要记住，尤其是使用 [Azure 高级存储](../../premium-storage-performance.md)时，每 GB 容量的吞吐量和 IOPS 确实会有变化。 有关此主题的详细信息，请参阅 [Azure 中提供哪些磁盘类型？](../../disks-types.md)一文。 [内存优化的虚拟机大小](../../sizes-memory.md)一文及其中链接的文章阐述了具体 Azure VM 类型的配额。 

> [!NOTE]
>  如果将 DBMS 系统从本地转移到 Azure，我们建议在 VM 上执行监视，并评估 CPU、内存、IOPS 和存储吞吐量。 将观测到的峰值与上述文章中阐述的 VM 配额限制进行比较

下面的示例仅供演示，可以根据个人需要进行修改。 由于 SAP ASE 的设计，数据设备的数量不如其他数据库那样重要。 本文档中详述的数据设备数量仅用作指导。 

数据库大小介于 50 GB 与 250 GB 之间的小型 SAP ASE DB 服务器（例如 SAP Solution Manager）的配置示例如下所示

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高 | 16.0.03.07 或更高 | --- |
| 数据设备数量 | 4 | 4 | ---|
| 日志设备数量 | 1 | 1 | --- |
| 临时设备数量 | 1 | 1 | SAP BW 工作负载需要更多 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7.6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要进行工作负载测试 | 需要进行工作负载测试 | --- |
| 数据磁盘的数量和类型 | 高级存储：2 x P10 (RAID0) | 高级存储：2 x P10 (RAID0)| 缓存 = 只读 |
| 日志磁盘的数量和类型 | 高级存储：1 x P20  | 高级存储：1 x P20 | 缓存 = 无 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设使用单个实例 |
| 备份设备数量 | 4 | 4| --- |
| 备份磁盘的数量和类型 | 1 | 1 | --- |


数据库大小介于 250 GB 与 750 GB 之间的中型 SAP ASE DB 服务器（例如较小的 SAP Business Suite 系统）的配置示例如下所示

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高 | 16.0.03.07 或更高 | --- |
| 数据设备数量 | 8 | 8 | ---|
| 日志设备数量 | 1 | 1 | --- |
| 临时设备数量 | 1 | 1 | SAP BW 工作负载需要更多 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7.6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要进行工作负载测试 | 需要进行工作负载测试 | --- |
| 数据磁盘的数量和类型 | 高级存储：4 x P20 (RAID0) | 高级存储：4 x P20 (RAID0)| 缓存 = 只读 |
| 日志磁盘的数量和类型 | 高级存储：1 x P20  | 高级存储：1 x P20 | 缓存 = 无 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设使用单个实例 |
| 备份设备数量 | 4 | 4| --- |
| 备份磁盘的数量和类型 | 1 | 1 | --- |

数据库大小介于 750 GB 到 2000 GB 之间的小型 SAP ASE DB 服务器（例如较大的 SAP Business Suite 系统）配置示例如下所示：

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高 | 16.0.03.07 或更高 | --- |
| 数据设备数量 | 16 | 16 | ---|
| 日志设备数量 | 1 | 1 | --- |
| 临时设备数量 | 1 | 1 | SAP BW 工作负载需要更多 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7.6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要进行工作负载测试 | 需要进行工作负载测试 | --- |
| 数据磁盘的数量和类型 | 高级存储：4 x P30 (RAID0) | 高级存储：4 x P30 (RAID0)| 缓存 = 只读 |
| 日志磁盘的数量和类型 | 高级存储：1 x P20  | 高级存储：1 x P20 | 缓存 = 无 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设使用单个实例 |
| 备份设备数量 | 4 | 4| --- |
| 备份磁盘的数量和类型 | 1 | 1 | --- |


数据库大小超过 2 TB 的小型 SAP ASE DB 服务器（例如在全球范围内使用的较大 SAP Business Suite 系统）配置示例如下所示：

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | M 系列（1.0 到 4.0 TB RAM）  | M 系列（1.0 到 4.0 TB RAM） | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高 | 16.0.03.07 或更高 | --- |
| 数据设备数量 | 32 | 32 | ---|
| 日志设备数量 | 1 | 1 | --- |
| 临时设备数量 | 1 | 1 | SAP BW 工作负载需要更多 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7.6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要进行工作负载测试 | 需要进行工作负载测试 | --- |
| 数据磁盘的数量和类型 | 高级存储：4+ x P30 (RAID0) | 高级存储：4+ x P30 (RAID0)| 缓存 = 只读，考虑使用 Azure 超级磁盘 |
| 日志磁盘的数量和类型 | 高级存储：1 x P20  | 高级存储：1 x P20 | 缓存 = 无，考虑使用 Azure 超级磁盘 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设使用单个实例 |
| 备份设备数量 | 16 | 16 | --- |
| 备份磁盘的数量和类型 | 4 | 4 | 使用 LVM2/存储空间 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Azure 上的 SAP ASE 备份和还原注意事项
增加数据和备份设备的数量可提高备份和还原性能。 建议按照前面的表格中所示，将托管 SAP ASE 备份设备的 Azure 磁盘条带化。 小心平衡备份设备和磁盘的数量，确保备份吞吐量不超过 VM 吞吐量总配额的 40%-50%。 建议使用 SAP 备份压缩作为默认设置。 可在以下文章中找到更多详细信息：

- [SAP 支持说明 #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP 支持说明 #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP 支持说明 #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

不要使用驱动器 D:\ 或 /temp 空间作为数据库或日志转储目标。

### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，能够减少 IOPS 的度量值可能有助于分散可在 Azure 等 IaaS 方案中运行的工作负载。 因此，我们建议确保先使用 SAP ASE 压缩，然后再将现有 SAP 数据库上传到 Azure。

之所以建议先应用库压缩，再上传到 Azure，原因有以下几点：

* 减少要上传到 Azure 的数据量
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短
* 较小的数据库大小可以降低磁盘分配的成本

数据和 LOB 压缩可以在 Azure 虚拟机托管的 VM 中运行，如同在本地运行一样。 有关如何检查是否已在现有 SAP ASE 数据库中使用压缩的更多详细信息，请查看 [SAP 支持说明 1750510](https://launchpad.support.sap.com/#/notes/1750510)。 有关 SAP ASE 数据库压缩的更多详细信息，请查看 [SAP 支持说明 #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure 上的 SAP ASE 的高可用性 
HADR 用户指南详细介绍了双节点 SAP ASE“Always-on”解决方案的设置和配置。  此外，还支持第三个灾难恢复节点。 SAP ASE 支持许多高可用性[配置](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.4.1/en-US/9b40a3c038a34cbda1064312aa8d25a4.html)，包括共享磁盘和原生 OS 群集（例如 Pacemaker 和 Windows Server 故障转移群集）。 Azure 上的 SAP ASE 有两个受支持的高可用性配置：

- 故障管理器的 HA 感知 - SAP 内核是“HA 感知型”应用程序，能够识别主要和辅助 SAP ASE 服务器。 SAP ASE 的“HA 感知”解决方案与 Azure 之间未紧密集成，不会使用 Azure 内部负载均衡器。  [SAP ASE HADR 用户指南](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)中记录了此解决方案
- 故障管理器的浮动 IP - 此解决方案可用于 SAP 业务套件和非 SAP 业务套件应用程序。  此解决方案利用 Azure ILB，而 SAP ASE 数据库引擎提供探测端口。  故障管理器会调用 SAPHostAgent，以便在 ASE 主机上启动或停止辅助浮点 IP。  [SAP 说明 #3086679 - SYB：故障管理器：Microsoft Azure 上的浮动 IP 地址](https://launchpad.support.sap.com/#/notes/3086679)中记录了此解决方案


> [!NOTE]
> HA 感知解决方案或浮动 IP 解决方案的故障转移时间和其他特征是类似的。  在这两个解决方案之间做出决定时，客户应执行自己的测试和评估，其中包括计划内和计划外故障转移时间以及其他操作过程等因素。  

### <a name="third-node-for-disaster-recovery"></a>用于灾难恢复的第三个节点
除了使用 SAP ASE Always-On 实现本地高可用性之外，你可能还想将配置扩展到另一个 Azure 区域中异步复制的节点。 有关详细信息，请参阅 [Sybase 16.3 修补程序级别 3 Always-on + DR on Suse 12.3 的安装过程](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)。

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE 数据库加密和 SSL 
SAP Software Provisioning Manager (SWPM) 提供一个用于在安装期间加密数据库的选项。  如果你要使用加密，建议使用 SAP 完全数据库加密。  请参阅以下文章中的详细信息：

- [SAP 支持说明 #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP 支持说明 #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP 支持说明 #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP 支持说明 #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> 如果 SAP ASE 数据库已加密，则备份转储压缩将不起作用。 另请参阅 [SAP 支持说明 #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure 上的 SAP ASE 部署检查列表
 
- 部署 SAP ASE 16.0.03.07 或更高版本
- 更新到 FaultManager 和 SAPHostAgent 的最新版本与补丁
- 在最新可用的已认证 OS（例如 Windows 2019、SUSE 15.1 或 Redhat 7.6 或更高版本）上部署
- 使用 SAP 认证的 VM – 建议使用高内存 Azure VM SKU（例如 Es_v3）或超大系统 M 系列 VM SKU
- 使 VM 的磁盘 IOPS 及聚合吞吐量配额与磁盘设计相匹配。  部署足够数量的磁盘
- 使用 Windows 存储空间或 Linux LVM2 以及正确的条带大小和文件系统聚合磁盘
- 创建足够数量的设备用于保存数据、日志、临时文件和备份
- 考虑对超大系统使用超级磁盘 
- 在 Linux OS 上运行 `saptune` SAP-ASE 
- 使用数据库加密保护数据库 – 在 Azure 密钥保管库中手动存储密钥 
- 完成 [Azure 上的 SAP 检查列表](./sap-deployment-checklist.md) 
- 配置日志备份和完整备份 
- 测试 HA/DR、备份和还原，并执行压力和负载量测试 
- 确认自动数据库扩展是否正常工作 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>使用 DBACockpit 监视数据库实例
对于使用 SAP ASE 作为数据库平台的 SAP 系统，可以将 DBACockpit 作为事务 DBACockpit 中的嵌入式浏览器窗口或作为 Webdynpro 来访问。 但是，完整的数据库监视和管理功能仅在 DBACockpit 的 Webdynpro 实现中提供。

与本地系统一样，需执行数个步骤，才能启用 DBACockpit 的 Webdynpro 实现所使用的所有 SAP NetWeaver 功能。 遵循 [SAP 支持说明 #1245200](https://launchpad.support.sap.com/#/notes/1245200) 启用 Webdynpro 并生成必要的功能。 遵循上述说明中的指示操作时，还将配置 Internet Communication Manager (`ICM`) 以及用于 http 和 https 连接的端口。 http 的默认设置如下所示：

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

事务 DBACockpit 中生成的链接如下所示：

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

需要确保 ICM 使用完全限定的主机名，并且此名称可在打开 DBACockpit 的计算机上解析，具体取决于如何将托管 SAP 系统的 Azure 虚拟机连接到 AD 和 DNS。 请参阅 [SAP 支持说明 #773830](https://launchpad.support.sap.com/#/notes/773830)，了解 ICM 如何基于配置文件参数来确定完全限定的主机名，并在必要时显式设置 icm/host_name_full 参数。

如果在仅限云方案中部署了 VM，但未在本地与 Azure 之间建立跨界连接，则需要定义一个公共 IP 地址和一个 `domainlabel`。 VM 的公共 DNS 名称格式如下所示：

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 


将 SAP 配置文件参数 icm/host_name_full 设置为 Azure VM 的 DNS 名称，其链接可能如下所示：

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

在此情况下，需要确保：

* 在 Azure 门户中，针对用来与 ICM 通信的 TCP/IP 端口，向网络安全组添加入站规则
* 针对用来与 ICM 通信的 TCP/IP 端口，向 Windows 防火墙配置添加入站规则

针对自动导入的所有可用修正，建议定期应用适用于 SAP 版本的修正集合 SAP 说明：

* [SAP 支持说明 #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP 支持说明 #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP 支持说明 #1882376](https://launchpad.support.sap.com/#/notes/1882376)

可以在以下 SAP 说明中找到有关 SAP ASE 的 DBA Cockpit 的更多详细信息：

* [SAP 支持说明 #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP 支持说明 #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP 支持说明 #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP 支持说明 #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP 支持说明 #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP 支持说明 #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP 支持说明 #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP 支持说明 #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>SAP ASE 的有用链接、说明和白皮书
[SAP ASE 16.0.03.07 文档](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US)的起始页提供了有关以下内容的各篇文档的链接：

- SAP ASE 学习之旅 - 管理和监视
- SAP ASE 学习之旅 - 安装和升级

这些文档非常有用。 另一篇有用的文档是 [SAP Adaptive Server Enterprise 上的 SAP 应用程序的迁移和运行时最佳做法](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)。

其他有用的 SAP 支持说明包括：

- [SAP 支持说明 #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP 支持说明 #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP 支持说明 #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP 支持说明 #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP 支持说明 #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP 支持说明 #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP 支持说明 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP 支持说明 #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP 支持说明 #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP 支持说明 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支持说明 #1588316](https://launchpad.support.sap.com/#/notes/158831) 

以下资源中发布了其他信息 

- [SAP Adaptive Server Enterprise 上的 SAP 应用程序](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE Infocenter](http://infocenter.sybase.com/help/index.jsp) 
- [设置了第 3 个 DR 节点的 SAP ASE Always-on](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

每月新闻稿将通过 [SAP 支持说明 #2381575](https://launchpad.support.sap.com/#/notes/2381575) 发布 


## <a name="next-steps"></a>后续步骤
查看 [Azure 上的 SAP 工作负载：规划和部署检查列表](./sap-deployment-checklist.md)一文
