---
title: 适用于 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: 适用于 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure、Db2、SAP、IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 34de5fbfbccd84c716684d1f98a16c4d0a5e6344
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322137"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>适用于 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署

使用 Microsoft Azure，可以将运行于 IBM DB2 for Linux、UNIX 和 Windows (LUW) 的现有 SAP 应用程序迁移到 Azure 虚拟机。 借助 IBM DB2 for LUW 上的 SAP，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。
有关在 IBM DB2 for LUW 上运行 SAP Business Suite 的常规信息，请参阅 SAP 社区网络 (SCN)，网址为 <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>。

有关 Azure 中 DB2 for LUW 上的 SAP 的其他信息和更新，请参阅 SAP 说明 [2233094]。 

已发布有关 Azure 上 SAP 工作负荷的各种文章。  建议从 [Azure 上的 SAP 工作负荷 - 入门](./get-started.md)开始，然后选择感兴趣的领域

以下 SAP 说明与 Azure 上的 SAP 有关，涉及本文档中介绍的领域：

| 说明文档编号 |标题 |
| --- |--- |
| [1928533] |Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视度量值 |
| [1409604] |Windows 上的虚拟化：增强型监视 |
| [2191498] |Azure 的 Linux 上的 SAP：增强型监视 |
| [2233094] |DB6：Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 应用程序 - 附加信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |Red Hat Enterprise Linux 7.x：安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |

在阅读本文档之前，你应已阅读[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)文档，以及 [Azure 文档上的 SAP 工作负荷](./get-started.md)中的其他指南。 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 for Linux、UNIX 和 Windows 版本支持
自 DB2 版本 10.5 起，支持 Microsoft Azure 虚拟机服务中 IBM DB2 for LUW 上的 SAP。

有关支持的 SAP 产品和 Azure VM 类型的信息，请参阅 SAP 说明 [1928533]。

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 IBM DB2 for Linux、UNIX 和 Windows 配置准则
### <a name="storage-configuration"></a>存储配置
有关 SAP 工作负荷的 Azure 存储类型的概述，请参阅[适用于 SAP 工作负荷的 Azure 存储类型](./planning-guide-storage.md)一文。所有数据库文件都必须存储在 Azure 块存储的已装载磁盘上（Windows：NTFS，Linux：xfs 或 ext3）。 数据库文件不支持任何类型的网络驱动器或远程共享（例如以下 Azure 服务）： 

* [Microsoft Azure 文件服务](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)

使用基于 Azure 页 BLOB 存储或托管磁盘的磁盘时，[适用于 SAP 工作负载的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的表述也适用于利用 DB2 DBMS 进行的部署。

如先前在文档通用部分中所述，Azure 磁盘的 IOPS 吞吐量存在配额。 确切的配额因所用 VM 类型而异。 你可在[此处 (Linux)][virtual-machines-sizes-linux] 和[此处 (Windows)][virtual-machines-sizes-windows] 找到 VM 类型及其配额的列表。

只要每个磁盘当前的 IOPS 配额够用，就可以将所有数据库文件存储在装载的单个磁盘上。 但应该始终在不同的磁盘/VHD 上分隔数据文件和事务日志文件。

有关性能注意事项，另请参阅 SAP 安装指南中的“数据库目录的数据安全和性能注意事项”一章。

也可以按[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中所述，使用 Windows 存储池（只有 Windows Server 2012 及更高版本中提供），或者使用 Linux 上的 LVM 或 mdadm，在多个磁盘上创建一个大型逻辑设备。

<!-- log_dir, sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

对于 Azure M 系列 VM，使用 Azure 写入加速器时，与 Azure 高级存储性能相比，可通过多种因素减少写入事务日志的延迟。 因此，应该为形成 DB2 事务日志卷的 VHD 部署 Azure 写入加速器。 有关详细信息，请阅读文档[写入加速器](../../how-to-enable-write-accelerator.md)。

IBM Db2 LUW 11.5 已发布对 4 KB 扇区大小的支持。 在旧版 Db2 中，必须使用 512 个字节的扇区大小。 高级 SSD 盘为原生 4 KB，并具备 512 字节的仿真。 默认情况下，超级磁盘使用 4 KB 的扇区大小。 你可以在创建超级磁盘期间启用 512 个字节的扇区大小。 有关详细信息，请参阅[使用 Azure 超级磁盘](../../disks-enable-ultra-ssd.md#deploy-an-ultra-disk---512-byte-sector-size)。 低于 11.5 的 IBM Db2 LUW 版本只支持 512 个字节的扇区大小。

在 Windows 上，将存储池用于 `log_dir`、`sapdata` 和 `saptmp` 目录的 Db2 存储路径时，必须将物理磁盘扇区大小指定为 512 个字节。 使用 Windows 存储池时，必须通过命令行界面使用参数 `-LogicalSectorSizeDefault`，以手动方式创建存储池。 有关详细信息，请参阅 <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>。


## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>针对 IBM Db2 部署的 VM 和磁盘结构的建议

SAP 支持说明 [1928533] 中列出的任何 VM 类型都支持 IBM Db2 for SAP NetWeaver 应用程序。  建议用于运行 IBM Db2 数据库的 VM 系列是用于大型多 TB 数据库的 Esd_v4/Eas_v4/Es_v3 和 M/M_v2 系列。 通过启用 M 系列写入加速器，可以提升 IBM Db2 事务日志磁盘写入性能。 

下面是 Db2 部署上 SAP 的各种大小（从小到大）和用途的基准配置。 此列表基于 Azure 高级存储。 不过，Azure 超级磁盘也完全支持 Db2，因此也可以使用 Azure 超级磁盘。 请使用容量、突发吞吐量和突发 IOPS 的值来定义超级磁盘配置。 可以将/db2/```<SID>```/log_dir 的 IOPS 限制为大约 5000 IOPS。 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>超小型 SAP 系统：数据库大小 50 - 200 GB：示例解决方案管理器
| VM 名称/大小 |Db2 装入点 |Azure 高级磁盘 |磁盘的 NR |IOPS |吞吐量 [MB/秒] |大小 [GB] |突发 IOPS |突发吞吐量 [GB] | 条带大小 | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  ||  |
|vCPU：4 |/db2/```<SID>```/sapdata |P10 |2 |1,000  |200  |256  |7,000  |340  |256 KB |ReadOnly |
|RAM：32 GiB |/db2/```<SID>```/saptmp |P6 |1 |240  |50  |128  |3,500  |170  | ||
| |/db2/```<SID>```/log_dir |P6 |2 |480  |100  |128  |7,000  |340  |64 KB ||
| |/db2/```<SID>```/offline_log_dir |P10 |1 |500  |100  |128  |3,500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>小型 SAP 系统：数据库大小为 200 - 750 GB：小型企业套件
| VM 名称/大小 |Db2 装入点 |Azure 高级磁盘 |磁盘的 NR |IOPS |吞吐量 [MB/秒] |大小 [GB] |突发 IOPS |突发吞吐量 [GB] | 条带大小 | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU：16 |/db2/```<SID>```/sapdata |P15 |4 |4,400  |500  |1.024  |14,000  |680  |256 KB |ReadOnly |
|RAM：128 GiB |/db2/```<SID>```/saptmp |P6 |2 |480  |100  |128  |7,000  |340  |128 KB ||
| |/db2/```<SID>```/log_dir |P15 |2 |2,200  |250  |512  |7,000  |340  |64 KB ||
| |/db2/```<SID>```/offline_log_dir |P10 |1 |500  |100  |128  |3,500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>中型 SAP 系统：数据库大小为 500 - 1000 GB：小型企业套件
| VM 名称/大小 |Db2 装入点 |Azure 高级磁盘 |磁盘的 NR |IOPS |吞吐量 [MB/秒] |大小 [GB] |突发 IOPS |突发吞吐量 [GB] | 条带大小 | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU：32 |/db2/```<SID>```/sapdata |P30 |2 |10,000  |400  |2.048  |10,000  |400  |256 KB |ReadOnly |
|RAM：256 GiB |/db2/```<SID>```/saptmp |P10 |2 |1,000  |200  |256  |7,000  |340  |128 KB ||
| |/db2/```<SID>```/log_dir |P20 |2 |4,600  |300  |1.024  |7,000  |340  |64 KB ||
| |/db2/```<SID>```/offline_log_dir |P15 |1 |1,100  |125  |256  |3,500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>大型 SAP 系统：数据库大小为 750 - 2000 GB：企业套件
| VM 名称/大小 |Db2 装入点 |Azure 高级磁盘 |磁盘的 NR |IOPS |吞吐量 [MB/秒] |大小 [GB] |突发 IOPS |突发吞吐量 [GB] | 条带大小 | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU：64 |/db2/```<SID>```/sapdata |P30 |4 |20,000  |800  |4.096  |20,000  |800  |256 KB |ReadOnly |
|RAM：504 GiB |/db2/```<SID>```/saptmp |P15 |2 |2,200  |250  |512  |7,000  |340  |128 KB ||
| |/db2/```<SID>```/log_dir |P20 |4 |9,200  |600  |2.048  |14,000  |680  |64 KB ||
| |/db2/```<SID>```/offline_log_dir |P20 |1 |2,300  |150  |512  |3,500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>大型多 TB SAP 系统：数据库大小为 2 TB 起：全局业务套件系统
| VM 名称/大小 |Db2 装入点 |Azure 高级磁盘 |磁盘的 NR |IOPS |吞吐量 [MB/秒] |大小 [GB] |突发 IOPS |突发吞吐量 [GB] | 条带大小 | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3,500  |170  || |
|vCPU：128 |/db2/```<SID>```/sapdata |P40 |4 |30,000  |1.000  |8.192  |30,000  |1.000  |256 KB |ReadOnly |
|RAM：2048 GiB |/db2/```<SID>```/saptmp |P20 |2 |4,600  |300  |1.024  |7,000  |340  |128 KB ||
| |/db2/```<SID>```/log_dir |P30 |4 |20,000  |800  |4.096  |20,000  |800  |64 KB |WriteAccelerator |
| |/db2/```<SID>```/offline_log_dir |P30 |1 |5,000  |200  |1.024  |5,000  |200  || |


### <a name="using-azure-netapp-files"></a>使用 Azure NetApp 文件
托管在 Suse 或 Red Hat Linux 来宾 OS 中的 IBM Db2 支持使用基于 Azure NetApp 文件 (ANF) 的 NFS v4.1 卷。 应创建至少四个不同的卷，如下所示：

- 用于 saptmp1、sapmnt、usr_sap、```<sid>```_home、db2```<sid>```_home、db2_software 的共享卷
- 用于 sapdata1 到 sapdatan 的一个数据卷
- 用于重做日志目录的一个日志卷
- 用于日志存档和备份的一个卷

第五个可能的卷可以是用于实现更长期备份的 ANF 卷，可用于拍摄快照并将快照存储在 Azure Blob 存储中。

配置可能如下所示

![使用 ANF 的 Db2 配置示例](./media/dbms_guide_ibm/anf-configuration-example.png)


必须基于性能要求选择 ANF 托管的卷的性能层和大小。 但是，建议对数据和日志卷采用超高性能级别。 不支持将块存储和共享存储类型混合用于数据和日志卷。

对于装载选项，装载这些卷可能如下所示（需要将 ```<SID>``` 和 ```<sid>``` 替换为 SAP 系统的 SID）：

```
vi /etc/idmapd.conf   
 # Example
 [General]
 Domain = defaultv4iddomain.com
 [Mapping]
 Nobody-User = nobody
 Nobody-Group = nobody

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2shared /mnt 
mkdir -p /db2/Software /db2/AN1/saptmp /usr/sap/<SID> /sapmnt/<SID> /home/<sid>adm /db2/db2<sid> /db2/<SID>/db2_software
mkdir -p /mnt/Software /mnt/saptmp  /mnt/usr_sap /mnt/sapmnt /mnt/<sid>_home /mnt/db2_software /mnt/db2<sid>
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2data /mnt
mkdir -p /db2/AN1/sapdata/sapdata1 /db2/AN1/sapdata/sapdata2 /db2/AN1/sapdata/sapdata3 /db2/AN1/sapdata/sapdata4
mkdir -p /mnt/sapdata1 /mnt/sapdata2 /mnt/sapdata3 /mnt/sapdata4
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2log /mnt 
mkdir /db2/AN1/log_dir
mkdir /mnt/log_dir
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2backup /mnt
mkdir /db2/AN1/backup
mkdir /mnt/backup
mkdir /db2/AN1/offline_log_dir /db2/AN1/db2dump
mkdir /mnt/offline_log_dir /mnt/db2dump
umount /mnt
```

>[!NOTE]
> 需要硬装载选项提交和同步


### <a name="backuprestore"></a>备份/还原
支持通过 IBM DB2 for LUW 提供备份/还原功能，其方式与在标准 Windows Server 操作系统和 Hyper-V 上一样。

请确保已经设置了有效的数据库备份策略。 

与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 使用多个目标目录来写入备份

>[!NOTE]
>Windows 上的 DB2 不支持 Windows VSS 技术。 因此，Azure 备份服务的应用程序一致 VM 备份不适用于 DB2 DBMS 所部署到的 VM。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复

#### <a name="linux-pacemaker"></a>Linux Pacemaker

支持使用 Pacemaker 的 Db2 高可用性灾难恢复 (HADR)。 支持 SLES 和 RHEL 操作系统。 此配置可实现 IBM Db2 for SAP 的高可用性。 部署指南：
* SLES：[具有 Pacemaker 的 SUSE Linux Enterprise Server 上 Azure VM 中的 IBM Db2 LUW 的高可用性](dbms-guide-ha-ibm.md) 
* RHEL：[Red Hat Enterprise Linux Server 上 Azure VM 中 IBM Db2 LUW 的高可用性](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Windows 群集服务器

不支持 Microsoft 群集服务器 (MSCS)。

支持 DB2 高可用性灾难恢复 (HADR)。 如果 HA 配置的虚拟机具有有效的名称解析，Azure 中的设置将与任何本地设置无任何差别。 建议不要完全依赖于 IP 解析。

请勿将异地复制用于存储数据库磁盘的存储帐户。 有关详细信息，请参阅文档：[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)。 

### <a name="accelerated-networking"></a>加速网络
对于 Window 上的 DB2 部署，强烈建议使用加速网络的 Azure 功能，如 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)所述。 也可考虑[适用于 SAP 工作负载的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的建议。 


### <a name="specifics-for-linux-deployments"></a>Linux 部署的详细信息
只要每个磁盘当前的 IOPS 配额够用，就可以将所有数据库文件存储在单个磁盘上。 但应该始终在不同的磁盘上分隔数据文件和事务日志文件。

如果单个 Azure VHD 的 IOPS 或 I/O 吞吐量不足，可使用 LVM（逻辑卷管理器）或 MDADM（如[适用于 SAP 工作负载的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中所述），在多个磁盘上创建一个大型逻辑设备。
如果磁盘包含 sapdata 和 saptmp 目录的 DB2 存储路径，则必须将物理磁盘扇区的大小指定为 512 KB。

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>其他
如[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)所述，Azure 可用性集或 SAP 监视等所有其他常规领域也适用于使用 IBM Database 进行的 VM 部署。

## <a name="next-steps"></a>后续步骤
阅读文章 

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](dbms_guide_general.md)


[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
