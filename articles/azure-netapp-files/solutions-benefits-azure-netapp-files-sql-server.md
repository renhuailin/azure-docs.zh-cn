---
title: 使用 Azure NetApp 文件进行 SQL Server 部署的好处 | Microsoft Docs
description: 介绍有关使用 Azure NetApp 文件进行 SQL Server 部署的详细的成本分析和性能优势。
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863898"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>使用 Azure NetApp 文件进行 SQL Server 部署的好处

相比于块存储解决方案，Azure NetApp 文件可降低 SQL Server 总拥有成本 (TCO)。  使用块存储时，虚拟机对磁盘操作的 I/O 和带宽施加限制，而使用 Azure NetApp 文件时仅应用网络带宽限制。  换句话说，没有对 Azure NetApp 文件应用任何 VM 级别的 I/O 限制。 如果没有 I/O 限制，则已连接到 Azure NetApp 文件且在较小虚拟机上运行的 SQL Server 与在较大虚拟机上运行的 SQL Server 在性能上并无二致。 正因如此，实例大小的减少可将计算成本降低到之前价格标签的 25%。  借助 Azure NetApp 文件降低计算成本。  

但相比于 SQL Server 许可证成本，计算成本较低。  Microsoft SQL Server [许可](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf)与物理核心计数相关联。 因此，减少实例大小节省了更多软件许可成本。 借助 Azure NetApp 文件降低软件许可证成本。

存储本身的成本因数据库的实际大小而异。 无论选择哪种存储，托管磁盘还是文件共享，容量都会产生成本。  随着数据库大小的增加和存储成本的增加，存储会导致 TCO 的增加，从而影响总体成本。  因此，改善这种状况的方法就是：借助 Azure NetApp 文件降低 SQL Server 部署成本。 

本文介绍有关使用 Azure NetApp 文件进行 SQL Server 部署的详细成本分析和性能优势。 有了 Azure NetApp 文件，较小的实例不仅有足够的 CPU 来完成只有较大实例上的块才有可能完成的数据库工作，而且在许多情况下，较小实例的性能甚至更优于基于磁盘的较大实例。 

## <a name="detailed-cost-analysis"></a>详细成本分析 

本部分中的两组图形介绍了 TCO 示例。  已为每个方案选择了托管磁盘的数量和类型、Azure NetApp 文件服务级别以及容量，以实现最优的价格和容量性能。  每个图形中显示分组的计算机（例如将使用 Azure NetApp 文件的 D16 与使用托管磁盘的 D64 进行对比），且为每种计算机类型细分价格。  

第一组图形通过 D16s_v3 与 D64、D8 与 D32 以及 D4 与 D16 的对比，显示了使用 1 TiB 数据库大小的解决方案的总体成本。 每种配置的预计 IOPS 通过绿色或黄色线条指示，并与右侧 Y 轴相对应。

[ ![图形显示使用 1 TiB 数据库大小的解决方案的总体成本。](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png) ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


第二组图形显示使用 50 TiB 数据库的总体成本。 但比较方式是相同的，例如将使用 Azure NetApp 文件的 D16 与使用块的 D64 进行比较。 

[ ![图形显示使用 50 TiB 数据库大小的总体成本。](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png) ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>性能，强有力的性能支持  

若要显著降低成本，需要性能的强力支持，例如，常规 Azure 清单中的较大实例支持 80000 次磁盘 IOPS。 单个 Azure NetApp 文件卷可以实现 80000 次数据库 IOPS，D16 这类实例能够使用此卷。 D16 通常能实现 25600 次磁盘 IOPS，其大小是 D64 的 25%。  D64s_v3 可以实现 80000 次磁盘 IOPS，因此性能表现优异，位于比较点的上层水平。

D16s_v3 可以驱动 Azure NetApp 文件卷实现 80000 次数据库 IOPS。 SQL 存储基准 (SSB) 的基准测试工具证明，D16 实例实现的工作负荷是 D64 实例磁盘可实现的工作负荷的 125%。  有关此工具的详细信息，请参阅 [SSB 测试工具](#ssb-testing-tool)部分。

使用 1 TiB 工作集大小、80% 读取和 20% 更新的 SQL Server 工作负荷，度量了 D 实例类中大多数实例的性能；但并非全部，因为 D2 和 D64 实例本身未经过测试。 前者不支持加速网络而不予考虑，后者则是比较点。 请参阅下图，了解 D4s_v3、D8s_v3、D16s_v3 和 D32s_v3 各自的限制。  图形中未显示托管磁盘存储测试。 绘出的比较值直接取自 [Azure 虚拟机限制表](../virtual-machines/dv3-dsv3-series.md)中 D 类实例类型的数据。

借助 Azure NetApp 文件，D 类中的每个实例都可以达到或超过实例磁盘性能的两倍。  借助 Azure NetApp 文件显著降低软件许可证成本。  

* D4 CPU 利用率为 75% 时与 D16 磁盘功能相当。  
    * D16 的速率限制为 25600 次磁盘 IOPS。  
* D8 CPU 利用率为 75% 时与 D32 磁盘功能相当。  
    * D32 的速率限制为 51200 次磁盘 IOPS。  
* D16 CPU 利用率为 55% 时与 D64 磁盘功能相当。  
    * D64 的速率限制为 80000 次磁盘 IOPS。  
* D32 CPU 利用率为 15% 时也与 D64 磁盘功能相当。  
    * 如上所述，D64 的速率限制为 80000 次磁盘 IOPS。  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU 限制测试 - 性能与处理能力

下图汇总了 S3B CPU 限制测试：

![关系图显示使用 Azure NetApp 文件的单实例 SQL Server 的平均 CPU 百分比。](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

可伸缩性只是冰山一角。 延迟更加举足轻重。  较小的虚拟机驱动较高的 I/O 速率并不难，难的是与此同时实现个位数的低延迟，如下所示。  

* D4 使用 Azure NetApp 文件驱动 26000 次 IOPS，延迟为 2.3 毫秒。  
* D8 使用 Azure NetApp 文件驱动 51000 次 IOPS，延迟为 2.0 毫秒。  
* D16 使用 Azure NetApp 文件驱动 88000 次 IOPS，延迟为 2.8 毫秒。
* D32 使用 Azure NetApp 文件驱动 80000 次 IOPS，延迟为 2.4 毫秒。  

### <a name="s3b-per-instance-type-latency-results"></a>S3B 每个实例类型的延迟结果

下图显示了使用 Azure NetApp 文件的单实例 SQL Server 的延迟：

![关系图显示使用 Azure NetApp 文件的单实例 SQL Server 的延迟。](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB 测试工具 
 
[TPC-E](http://www.tpc.org/tpce/) 基准测试工具在设计上强调计算，而非存储。 本部分中所示的测试结果基于名为“SQL 存储基准”(SSB) 的压力测试工具。  SQL Server 存储基准可以驱动对 SQL Server 数据库的大规模 SQL 执行，以模拟 OLTP 工作负荷，这与 [SLOB2 Oracle 基准测试工具](https://kevinclosson.net/slob/)类似。 

SSB 工具生成 SELECT 和 UPDATE 驱动的工作负荷，该工作负荷将所述语句直接发出到 Azure 虚拟机中运行的 SQL Server 数据库。  对于此项目，SSB 工作负荷从 1 到 100 SQL Server 用户逐渐增加，每个用户 15 分钟 10 或 12 个中间点。  这些运行的所有性能指标都是从 perfmon 的角度来看，对于重复性而言，SSB 针对每个方案运行三次。 

测试本身配置为 80% SELECT 语句和 20% UPDATE 语句，因此执行 90% 随机读取操作。  数据库本身由 SSB 创建，大小为 1000 GB。 它由 15 个用户表构成，每个用户表 9,000,000 行，每行 8192 个字节。 

SSB 基准是一种开源工具。  可在 [SQL 存储基准 GitHub 页](https://github.com/NetApp/SQL_Storage_Benchmark.git)中免费获得。  


## <a name="in-summary"></a>总之，  

借助 Azure NetApp 文件，可以提高 SQL server 性能，同时显著降低总拥有成本。 

## <a name="next-steps"></a>后续步骤

* [创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md) 
* [使用 Azure NetApp 文件的解决方案体系结构 - SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

