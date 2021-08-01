---
title: Azure NetApp 文件单一卷上的 Oracle 数据库性能 | Microsoft Docs
description: 介绍 Oracle 数据库上 Azure NetApp 文件单一卷的性能测试结果。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571283"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Azure NetApp 文件单卷上的 Oracle 数据库性能

本文讨论有关云中 Oracle 的以下主题。 数据库管理员、云架构师或存储架构师可能对以下主题特别感兴趣：   

* 驱动联机事务处理 (OLTP) 工作负载（大多数是随机 I/O）或联机分析处理 (OLAP) 工作负载（大多数是顺序 I/O）时，性能如何？   
* 普通 Linux 内核 NFS (kNFS) 客户端和 Oracle 自身的 Direct NFS 客户端的性能有何不同？
* 就带宽而言，单个 Azure NetApp 文件卷的性能是否足够？

## <a name="testing-environment-and-components"></a>测试环境和组件

下图演示了用于测试的环境。 为了保持一致性和简便性，已使用 Ansible playbook 来部署测试台的所有组件。

![Oracle 测试环境](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>虚拟机配置

测试中对虚拟机使用了以下设置：
* 操作系统：   
    RedHat Enterprise Linux 7.8 (wle-ora01)
* 实例类型：   
    测试中使用了两个型号 – D32s_v3 和 D64s_v3
* 网络接口计数：   
    一 (1) 个，置于子网 3 中  
* 磁盘：   
    在单个高级磁盘中放置了 Oracle 二进制文件和 OS

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp 文件配置
测试中使用了以下 Azure NetApp 文件配置：   

* 容量池大小：  
    配置了多种池大小：4 TiB、8 TiB、16 TiB、32 TiB 
* 服务级别：  
    超级（每 1 TiB 的已分配卷容量的带宽为 128 MiB/秒）
* 卷:  
    已评估一个和两个卷的测试

### <a name="workload-generator"></a>工作负载生成器 

测试中使用了生成 SLOB 2.5.4.2 的工作负载。 SLOB (Silly Little Oracle Benchmark) 是 Oracle 领域中众所周知的工作负载生成器，旨在使用 SGA 缓冲的物理 I/O 工作负载对 I/O 子系统施加压力和进行测试。  

SLOB 2.5.4.2 不支持可插式数据库 (PDB)。 因此，已在 `setup.sh` 和 `runit.sh` 脚本中添加一项更改，使该生成器能够支持 PDB。  

以下部分将介绍测试中使用的 SLOB 变量。

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>工作负载：80% SELECT，20% UPDATE | 随机 I/O – `slob.conf` 变量   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>工作负载：100% SELECT | 顺序 I/O – `slob.conf` 变量

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>数据库

用于测试的 Oracle 版本为 Oracle Database Enterprise Edition 19.3.0.0。

Oracle 参数如下：  
* `sga_max_size`：4096M
* `sga_target`：4096
* `db_writer_processes`：12
* `awr_pdb_autoflush_enabled`：true
* `filesystemio_options`：SETALL
* `log_buffer`：134217728

为 SLOB 数据库创建了 PDB。

下图显示创建了名为 PERFIO、大小为 600 GB 的表空间（包含 20 个数据文件，每个数据文件大小为 30 GB）用于承载四个 SLOB 用户架构。 每个用户架构的大小为 125 GB。

![Oracle 数据库](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>性能指标

目标是报告应用程序体验的 IO 性能。 因此，本文中的所有示意图都使用 Oracle 数据库通过其自动工作负载存储库 (AWR) 报告提供的指标。 示意图中使用的指标如下：   

* 每秒平均 IO 请求数   
    对应于负载配置文件部分中平均“每秒读取 IO 请求数”和平均“每秒写入 IO 请求数”的总和
* 平均 IO MB/秒   
    对应于负载配置文件部分中平均“每秒读取 IO MB”和平均“每秒写入 IO MB”的总和
* 平均读取延迟   
    对应于 Oracle 等待事件“db file sequential read”的平均延迟，以微秒为单位
* 每个架构的线程数   
    对应于每个用户架构的 SLOB 线程数

## <a name="performance-measurement-results"></a>性能测量结果  

本部分介绍性能测量结果。

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS 客户端与 Oracle Direct NFS

此方案在 Azure VM Standard_D32s_v3 (Intel E5-2673 v4 @ 2.30 GHz) 上运行。 75% 的工作负载为 SELECT，25% 为 UPDATE，大多为随机 I/O，数据库缓冲区命中率约为 7.5%。 

如下图所示，Oracle DNFS 客户端的吞吐量是普通 Linux kNFS 客户端的 2.8 倍：  

![Linux kNFS 客户端与 Oracle Direct NFS 的比较](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

下图显示了读取操作的延迟曲线。 在此上下文中，kNFS 客户端的瓶颈在于客户端与 NFS 服务器（Azure NetApp 文件卷）之间建立的单个 NFS TCP 套接字连接。  

![Linux kNFS 客户端与 Oracle Direct NFS 的延迟曲线比较](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS 客户端之所以每秒能够推送更多的 IO 请求，是因为它可以创建数百个 TCP 套接字连接，因而可以利用并行度。 如 [Azure NetApp 文件配置](#anf_config)中所述，每额外分配 1 TiB 容量，就能使带宽增大 128 MiB/秒。 DNFS 的吞吐量上限为 1 GiB/秒，这也是 8 TiB 容量选项施加的限制。 容量越高，促成的吞吐量也越高。

吞吐量只是其中的一个考虑因素。 另一个考虑因素是延迟，它会对用户体验产生重大影响。 如下图所示，与 DNFS 相比，kNFS 的延迟预期会急剧增大。 

![Linux kNFS 客户端与 Oracle Direct NFS 的读取延迟比较](../media/azure-netapp-files/performance-oracle-read-latency.png)  

直方图提供了对数据库延迟的极好见解。 下图提供了在最高并发数据点（每个架构 32 个线程）处使用 DNFS 时，从记录的“db file sequential read”（按顺序读取 db 文件）角度看的完整视图。 如下图所示，在所有的读取操作中，为 47% 的操作提供服务的延迟为 512 微秒至 1000 微秒，为 90% 的读取操作提供服务的延迟小于 2 毫秒。

![Linux kNFS 客户端与 Oracle Direct NFS 的比较直方图](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

总之，很明显，在 NFS 上必须使用 DNFS 来改善 Oracle 数据库实例的性能。

### <a name="single-volume-performance-limits"></a>单一卷的性能限制

本部分介绍具有随机 I/O 和顺序 I/O 的单一卷的性能限制。 

#### <a name="random-io"></a>随机 I/O

DNFS 可消耗的带宽要比 8 TB Azure NetApp 文件性能配额提供的带宽多得多。 通过将 Azure NetApp 文件卷容量提高至 16 TiB（这会即时发生变化），卷带宽量已在 1024 MiB/秒的基础上增大了一倍，达到了 2048 MiB/秒。 

下图显示了 80% SELECT 和 20% UPDATE 工作负载的数据库缓冲区命中率为 8% 的配置。 SLOB 能够促成单个卷每秒处理 200,000 个 NFS I/O 请求。 考虑到每个操作的大小为 8 KiB，测试的系统每秒可传送大约 200,000 个 IO 请求或 1600 MiB 数据。
 
![Oracle DNFS 吞吐量](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

以下读取延迟曲线图显示，随着读取吞吐量的提高，延迟在不到 1 毫秒时平稳增大，当每秒平均读取 IO 请求数约为 165,000、平均读取延迟约为 1.3 毫秒时，曲线向上弯折。  此延迟值令人难以置信，它实现了 Azure 云中几乎所有其他技术都无法媲美的 I/O 速率。 

![Oracle DNFS 延迟曲线](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>顺序 I/O  

如下图所示，并非所有 I/O 都是随机性的，例如，将 RMAN 备份或完整表扫描视为需要尽可能多的带宽的工作负载时。  下图显示了使用前面所述的相同配置但将卷大小调整为 32 TiB 时，单个 Oracle DB 实例的吞吐量可以提升至 3900 MB/秒，这非常接近 Azure NetApp 文件卷的 32 TB 性能配额（128 MB/秒 * 32 = 4096 MB/秒）。

![Oracle DNFS I/O](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

总而言之，Azure NetApp 文件可帮助你将 Oracle 数据库部署到云中。 它能够提供数据库所需的性能。 可以随时动态调整卷配额，而不会造成中断。

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的性能基准测试建议](azure-netapp-files-performance-metrics-volumes.md)
- [Linux 性能基准](performance-benchmarks-linux.md)