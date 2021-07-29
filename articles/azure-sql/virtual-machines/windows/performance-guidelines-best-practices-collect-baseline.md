---
title: 存储基线：性能最佳做法和准则
description: 提供收集性能基线的步骤，作为实现 Azure 虚拟机 (VM) 上 SQL Server 性能优化的指导原则。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 46dff70c8d1064f05d89af9340b5b07e7adc747e
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112079888"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>收集基线：Azure VM 上 SQL Server 的性能最佳做法
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供的信息为一系列收集性能基线的最佳做法和指导原则，以优化 Azure 虚拟机 (VM) 上 SQL Server 的性能。

通常需要在针对成本优化和针对性能优化之间进行权衡。 这一系列性能最佳做法侧重于实现 Azure 虚拟机上 SQL Server 的最佳性能。 如果工作负荷要求较低，可能不需要每项建议的优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

## <a name="overview"></a>概述

要使用更规范的方法，请使用 PerfMon/LogMan 收集性能计数器并捕获 SQL Server 等待统计信息，以更好地理解源环境的一般压力和潜在问题。 

首先按照[应用程序性能检查表](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)收集源工作负载在峰值时间的 CPU、内存、[IOPS](../../../virtual-machines/premium-storage-performance.md#iops)、[吞吐量](../../../virtual-machines/premium-storage-performance.md#throughput)和[延迟](../../../virtual-machines/premium-storage-performance.md#latency)。 

在高峰时段收集数据，例如典型工作日期间的工作负载，以及其他高负载过程（如日结束处理和周末 ETL 工作负载）。 请考虑为非典型的繁重工作负载（如季度末处理）纵向扩展资源，然后在工作负载完成后纵向缩减。 

使用性能分析选择可根据工作负载的性能要求缩放的 [VM 大小](../../../virtual-machines/sizes-memory.md)。


## <a name="storage"></a>存储

SQL Server 性能很大程度上取决于 I/O 子系统，存储性能根据 IOPS 和吞吐量度量。 除非数据库适合物理内存，否则 SQL Server 会不断将数据库页引入或引出缓冲池。 应以不同的方式处理 SQL Server 的数据文件。 对日志文件的访问是按顺序进行的，除非需要回滚事务，此时将随机访问包括 TempDB 在内的数据文件，。 如果 I/O 子系统速度较慢，则用户可能会遇到性能问题，例如响应时间长和任务因超时而无法完成。 

默认情况下，Azure 市场虚拟机在物理磁盘上具有与数据文件不同的日志文件。 TempDB 数据文件计数和大小符合最佳做法，并以临时 `D:\` 驱动为目标。 

以下 PerfMon 计数器可帮助验证 SQL Server 所需的 IO 吞吐量： 
* **\LogicalDisk\Disk Reads/Sec**（读取 IOPS）
* **\LogicalDisk\Disk Writes/Sec**（写入 IOPS） 
* **\LogicalDisk\Disk Read Bytes/Sec**（读取数据、日志和 tempdb 文件的吞吐量要求）
* **\LogicalDisk\Disk Write Bytes/Sec**（写入数据、日志和 tempdb 文件的吞吐量要求）

使用峰值级别的 IOPS 和吞吐量要求，评估与测量的容量相匹配的 VM 大小。 

如果你的工作负载要求读取 20K IOPS、写入 10K IOPS，你可以选择带 2 个使用存储空间进行条带化的 P30 磁盘的 E16s_v3（有最多 32K 已缓存和 25600 未缓存的 IOPS）或 M16_s（有最多 20K 已缓存和 10K 未缓存的 IOPS）。 

请确保了解工作负载的吞吐量和 IOPS 要求，因为 VM 具有不同的 IOPS 和吞吐量缩放限制。

## <a name="memory"></a>内存

跟踪 OS 使用的外部内存以及 SQL Server 内部使用的内存。 识别任一组件的压力将有助于调整虚拟机的大小并识别调整的机会。 

以下 PerfMon 计数器可帮助验证 SQL Server 虚拟机的内存运行状况： 
* \Memory\Available MBytes
* [\SQLServer:Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page life expectancy](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>计算

Azure 中的计算管理方式与本地计算管理方式不同。 由于管理开销和获取新硬件的成本，本地服务器设计为持续数年无需升级。 虚拟化可缓解其中一些问题，并且应用程序经过优化，可充分利用基础硬件，这意味着任何对资源消耗的重大更改都需要重新平衡整个物理环境。 

在 Azure 中，这并不困难，因为在不同系列的硬件上，甚至在不同的区域中，很容易获得新虚拟机。 

在 Azure 中，你希望利用尽可能多的虚拟机资源，因此，应将 Azure 虚拟机的平均 CPU 配置得尽可能高，而不影响工作负载。 

以下 PerfMon 计数器可帮助验证 SQL Server 虚拟机的计算运行状况：
* \Processor Information(_Total)\% Processor Time
* \Process(sqlservr)\% Processor Time

> [!NOTE] 
> 理想情况下，尝试使用 80% 的计算能力，峰值时超过 90%，但在任何持续的时间内均不会达到 100%。 从根本上说，你只需预配应用程序所需的计算能力，然后计划根据业务需求进行纵向扩展或纵向缩减。 


## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅本系列中的其他文章：
- [快速核对清单](performance-guidelines-best-practices-checklist.md)
- [VM 大小](performance-guidelines-best-practices-vm-size.md)
- [存储](performance-guidelines-best-practices-storage.md)
- [安全性](security-considerations-best-practices.md)
- [HADR 设置](hadr-cluster-best-practices.md)


有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.yml)。
