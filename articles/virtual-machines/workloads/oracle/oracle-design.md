---
title: 在 Azure 上设计和实现 Oracle 数据库 | Microsoft 文档
description: 在 Azure 环境中设计和实现 Oracle 数据库。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: f6f7312590b98474d5edab02ea8e73725aded229
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690075"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>在 Azure 中设计和实现 Oracle 数据库

**适用于：** :heavy_check_mark: Linux VM 

## <a name="assumptions"></a>假设

- 计划将 Oracle 数据库从本地迁移到 Azure。
- 已经为你要迁移的 Oracle Database 准备好了[诊断包](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm)或[自动工作负载存储库](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf)
- 你了解 Oracle 中的各项指标。
- 了解应用程序性能和平台利用率的基线。

## <a name="goals"></a>目标

- 了解如何在 Azure 中优化 Oracle 部署。
- 探索 Azure 环境中用于 Oracle 数据库的性能调整选项。
- 对通过体系结构进行物理优化的局限性、数据库代码 (SQL) 的优势或逻辑优化以及整体数据库设计具有明确的预期。

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>本地实现和 Azure 实现的区别 

以下是将本地应用程序迁移到 Azure 时需要注意的几个要点。 

其中的一个重要区别是，在 Azure 实现中与其他客户端共享虚拟机、磁盘和虚拟网络等资源。 此外，还可按需限制资源。 Azure 更注重从故障中恢复 (MTTR)，而不是关注避免故障 (MTBF)。

下表列出了 Oracle 数据库的本地实现和 Azure 实现之间的一些区别。


|  | 本地实现 | Azure 实现 |
| --- | --- | --- |
| **联网** |LAN/WAN  |SDN（软件定义的网络）|
| **安全组** |IP/端口限制工具 |[网络安全组 (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **复原能力** |MTBF（平均无故障时间） |MTTR（平均恢复时间）|
| **计划内维护** |修补/升级|[可用性集](/previous-versions/azure/virtual-machines/windows/infrastructure-example)（由 Azure 管理的修补/升级） |
| **资源** |专用  |与其他客户端共享|
| **区域** |数据中心 |[区域对](../../regions.md#region-pairs)|
| **存储** |SAN/物理磁盘 |[Azure 托管的存储](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **可伸缩** |垂直缩放 |横向缩放|


### <a name="requirements"></a>要求

- 确定实际的 CPU 使用率，由于 Oracle 是按核心授权的，因此调整 vCPU 需求可能是节省成本的关键做法。 
- 确定数据库大小、备份存储和增长率。
- 确定 IO 要求，这可以根据 Oracle Statspack 和 AWR 报告或者通过 OS 级存储监视工具来估算。

## <a name="configuration-options"></a>配置选项

在 Azure 环境中，可以优化四个潜在区域来提高性能：

- 虚拟机大小
- 网络吞吐量
- 磁盘类型和配置
- 磁盘缓存设置

### <a name="generate-an-awr-report"></a>生成 AWR 报表

如果你已有一个 Oracle Enterprise Edition 数据库并打算将其迁移到 Azure，可以使用多个选项。 如果你有适用于 Oracle 实例的[诊断包](https://www.oracle.com/technetwork/oem/pdf/511880.pdf)，则可以运行 Oracle AWR 报告来获取指标（IOPS、Mbps、GiB 等）。 对于没有诊断包许可证的数据库或者对于 Standard Edition 数据库，可以在收集手动快照后，使用 Statspack 报告收集相同的重要指标。  这两种报告方法的主要差别是：AWR 是自动收集的，与以前的 Statspack 报告选项相比，它提供的数据库相关信息更多。

可考虑分别在正常工作负荷与峰值工作负荷期间运行 AWR 报表，以便进行比较。 若要收集更准确的工作负载，请考虑将报告周期延长为一周而不是 24 小时，并认识到 AWR 确实会在其报告中提供平均值作为计算结果的一部分。  对于数据中心迁移，我们建议收集用于在生产系统中进行大小调整的报告，并估算用于用户测试、测试、开发等任务的剩余数据库副本数百分比（UAT 与生产环境相同，而测试和开发占生产规模的 50%，等等）

默认情况下，AWR 存储库保留 8 天的数据，并每隔一小时创建快照。  若要从命令行运行 AWR 报告，可以在终端中执行以下命令：

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>关键指标

报告将提示输入以下信息：
- 报告类型：HTML 或 TEXT（HTML 版本为 12.1，提供的信息比 TEXT 格式更多。）
- 要显示多少天的快照（如果间隔为一小时，一周的报告将使用 168 个不同的快照 ID）
- 报告周期的起始 SnapshotID。
- 报告周期的结束 SnapshotId。
- AWR 脚本要创建的报告的名称。

如果在真实应用程序群集 (RAC) 上运行 AWR，则命令行报告是 awrgrpt.sql 而不是 awrrpt.sql。  “g”报告将以单个报告的形式为 RAC 数据库中的所有节点创建一个报告，而无需你在每个 RAC 节点上运行一次报告。

以下是可以从 AWR 报表获得的指标：

- 数据库名、实例名和主机名
- 数据库版本（Oracle 的可支持性）
- CPU/核心
- SGA/PGA（以及大小不足时告知这一情况的顾问消息）
- 总内存 (GB)
- 繁忙 CPU 百分比
- 数据库 CPU
- IOPs（读/写）
- MBPs（读/写）
- 网络吞吐量
- 网络延迟率（低/高）
- 排名靠前的等待事件 
- 数据库的参数设置
- 数据库 RAC 和 Exadata 是否使用高级功能或配置

### <a name="virtual-machine-size"></a>虚拟机大小

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. 根据 AWR 报表提供的 CPU、内存和 I/O 使用情况估计虚拟机的大小

用户可能想要了解超时最严重的五大前景事件，这些事件指明了系统的瓶颈所在。

例如，在下图中，日志文件同步在最顶部。 它指示 LGWR 将日志缓冲区写入恢复日志文件前需要等待的数量。 这些结果指示存储区或磁盘的性能需要提高。 此外，此图还显示了 CPU（内核）数和内存大小。

![显示表顶部的日志文件同步的屏幕截图。](./media/oracle-design/cpu_memory_info.png)

下图显示了读写的 I/O 总数。 在报表生成期间，读取了 59 GB 的数据，写入了 247.3 GB 的数据。

![显示读写操作的 I/O 统计总量的屏幕截图。](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. 选择虚拟机

从 AWR 报表收集信息后，应根据该信息选择一个虚拟机，其大小应大致可满足需求。 可在[内存优化](../../sizes-memory.md)一文中查看可用的虚拟机列表。

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. 根据 ACU 微调相似虚拟机系列的虚拟机大小

选择虚拟机后，请注意虚拟机的 ACU。 可根据 ACU 值选择不同的虚拟机，以便更好地满足需求。 有关详细信息，请参阅 [Azure 计算单元](../../acu.md)。

![“ACU 单元”页的屏幕截图](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>网络吞吐量

下图显示了吞吐量与 IOPS 之间的关系：

![吞吐量的屏幕截图](./media/oracle-design/throughput.png)

总网络吞吐量的估算以下列信息为基础：
- SQL*Net 流量
- MBps x 服务器数量（出站流，如 Oracle Data Guard）
- 其他因素，如应用程序复制

![SQL*Net 吞吐量的屏幕截图](./media/oracle-design/sqlnet_info.png)

基于网络带宽要求，有多种网关类型可供选择。 例如，Basic、VpnGw 和 Azure ExpressRoute。 有关详细信息，请参阅 [VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)页。

**建议**

- 与本地部署相比，网络延迟更高。 减少网络往返次数可显著提高性能。
- 若要减少网络往返，可在同一虚拟机上合并事务繁多的应用或“聊天式”应用。
- 使用具有[加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md)的虚拟机实现更好的网络性能。
- 对于某些 Linux 发行版，请考虑启用 [TRIM/UNMAP 支持](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)。
- 在单独的虚拟机上安装 [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html)。
- 默认情况下不会在 Linux 上启用巨页。 请考虑启用巨页并在 Oracle DB 中设置 `use_large_pages = ONLY`。 这可能有助于提高性能。 有关详细信息，请参阅 [USE_LARGE_PAGES](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)。

### <a name="disk-types-and-configurations"></a>磁盘类型和配置

- 默认 OS 磁盘：这些磁盘类型提供永久性数据和缓存。 它们针对启动时的 OS 访问进行了优化，不适用于事务性或数据仓库（分析）工作负荷。

- 托管磁盘：由 Azure 管理用于 VM 磁盘的存储帐户。 指定所需的磁盘类型（对于 Oracle 工作负载，最常用的类型是高级 SSD）和磁盘大小。 Azure 将创建和管理该磁盘。  高级 SSD 托管磁盘仅适用于已经过内存优化和专门设计的 VM 系列。 选择特定虚拟机大小后，菜单将只显示以此虚拟机大小为基础的可用高级存储 SKU。

![“托管磁盘”页的屏幕截图](./media/oracle-design/premium_disk01.png)

在虚拟机上配置存储后，可能需要在创建数据库前先对磁盘进行负载测试。 了解延迟和吞吐量的 I/O 速率有助于判断 VM 是否支持预计的吞吐量，并实现预期延迟目标。

有多种工具可用于应用程序负载测试，包括 Oracle Orion、Sysbench、SLOB 和 Fio。

部署 Oracle 数据库后重新运行负载测试。 启动正常工作负荷与峰值工作负荷，结果将显示所在环境的基线。  工作负载测试应与现实贴合 - 运行一个你在现实中完全不会在 VM 上运行的工作负载没有任何意义。

由于 Oracle 在很多情况下是 IO 密集型数据库，因此根据 IOPS 速率（而不是存储大小）来调整存储大小相当重要。 例如，如果要求的 IOPS 是 5,000，但是你只需 200 GB，则即使 P30 级别高级磁盘的存储空间大于 200 GB，你可能仍会使用它。

可从 AWR 报表获取 IOPS 速率。 它由恢复日志、物理读取和写入速率确定。  还必须验证所选 VM 系列是否能够处理工作负载的 IO 需求。  如果 VM 的 IO 限制低于存储，则最大限制将由 VM 设置。

![“AWR 报表”页的屏幕截图](./media/oracle-design/awr_report.png)

例如，恢复大小是每秒 12,200,000 字节，相当于 11.63 MBP。
则 IOPS 为 12,200,000 / 2,358 = 5,174。

在明确 I/O 要求后，可选择最适合的驱动器组合以满足这些要求。

**建议**

- 对于 Data 表空间，使用托管存储或 Oracle ASM 在多个磁盘间分散 I/O 工作负荷。
- 使用 Oracle 高级压缩来降低 I/O（针对数据和索引）。
- 将重做日志、Temp 和 Undo 表空间分隔在不同的数据磁盘上。
- 不要将任何应用程序文件放在默认 OS 磁盘 (/dev/sda) 中。 这些磁盘未针对快速 VM 启动时间进行优化，可能无法为应用程序提供良好的性能。
- 在高级存储上使用 M 系列 VM 时，请在重做日志磁盘上启用[写入加速器](../../how-to-enable-write-accelerator.md)。
- 考虑将延迟较高的重做日志移到超级磁盘。

### <a name="disk-cache-settings"></a>磁盘缓存设置

有三个选项用于主机缓存，但对于 Oracle 数据库，建议仅将“只读”缓存用于数据库工作负载。  “读/写”可能会在数据文件中引入严重的漏洞，在这种情况下，数据库写入的目的是将信息记录到数据文件，而不是缓存信息。

与文件系统或应用程序不同，对于数据库，建议将“只读”用于主机缓存：缓存所有请求供将来读取。 所有写入内容将继续写入到磁盘。

**建议**

为了最大限度地提高吞吐量，我们建议最初尽可能地将“只读”用于主机缓存。 请注意，对于高级存储，在使用“只读”选项来装载文件系统时，必须禁用“屏障”。 通过 UUID 将 /etc/fstab 文件更新到磁盘。

![显示“只读”和“无”选项的托管磁盘页的屏幕截图。](./media/oracle-design/premium_disk02.png)

- 对于 OS 磁盘，请使用默认的“读/写”缓存，并为 Oracle 工作负载 VM 使用高级 SSD。  此外，请确保用于交换的卷也在高级 SSD 上。
- 对于所有数据文件，请使用“只读”进行缓存。 “只读”缓存仅适用于高级托管磁盘、P30 和更高级别。  可用于“只读”缓存的卷大小不能超过 4095GiB。  分配任何更大的容量默认会禁用主机缓存。

如果工作负载在白天和夜晚的差异很大，并且 IO 工作负载能够支持具有突发功能的 P1-P20 高级 SSD，则此类 SSD 可以在夜间运行批处理负载或者在 IO 需求有限期间提供所需的性能。  

## <a name="security"></a>安全性

设置并配置 Azure 环境后，下一步是保护网络的安全。 以下是一些建议：

- NSG 策略：可使用子网或 NIC 定义 NSG。 可以在子网级别更轻松地控制访问，以确保安全性并为应用程序防火墙等组件实现强制路由。

- Jumpbox：为了提高访问的安全性，管理员不应直接连接到应用程序服务或数据库。 使用 Jumpbox 作为管理员计算机与 Azure 资源之间的媒介。
![“Jumpbox 拓扑”页屏幕截图](./media/oracle-design/jumpbox.png)

    应将管理员计算机的 IP 限制为仅允许访问 jumpbox。 Jumpbox 应有权访问应用程序和数据库。

- 专用网络（子网）：建议将应用程序服务和数据库置于单独的子网上，以便通过 NSG 策略可以更好地进行控制。


## <a name="additional-reading"></a>附加阅读材料

- [配置 Oracle ASM](configure-oracle-asm.md)
- [配置 Oracle Data Guard](configure-oracle-dataguard.md)
- [配置 Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle 备份和恢复](./oracle-overview.md)

## <a name="next-steps"></a>后续步骤

- [教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)
- [浏览 VM 部署 Azure CLI 示例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
