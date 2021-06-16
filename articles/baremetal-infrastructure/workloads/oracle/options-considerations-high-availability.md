---
title: 适用于 Oracle BareMetal 基础结构服务器的选项
description: 了解适用于 Oracle BareMetal 基础结构服务器的选项和注意事项。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: eb77731ad018817cf6d868cefd6a4d84b8f6d330
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578647"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>适用于 Oracle BareMetal 基础结构服务器的选项

在本文中，我们将考虑相关选项和建议，以获取在 BareMetal 基础结构服务器上运行 Oracle 的最高保护和性能级别。 

## <a name="data-guard-protection-modes"></a>Data Guard 保护模式

Data Guard 提供仅通过 Oracle Real Applications Cluster (RAC)、逻辑复制（如 GoldenGate）和基于存储的复制而无法使用的保护。 

| 保护模式 | 说明 |
| --- | --- |
| 最大性能 | 默认保护模式。 它可提供最高级别的保护，而不会影响主数据库的性能。 数据一旦写入主数据库重做流，便会被视为已提交。 它随后会以异步方式复制到备用数据库。 通常，备用数据库会在几秒钟内收到它，但不保证实现该效果。 此模式通常满足业务要求（以及延隔监视），而无需主站点与备用站点之间建立低延迟网络连接。<br /><br />它可提供最佳操作持久性；但是，不保证零数据丢失。   |
| 最大可用性 | 提供最高级别的保护，而不会影响主数据库的可用性。 在数据也提交到至少一个备用数据库之前，从不会被视为已提交到主数据库。 如果主数据库无法将重做更改写入至少一个备用数据库，则它会回退到最大性能模式，而不会变为不可用。 <br /><br />它允许在备用站点不可用继续提供服务。 如果只有一个站点在工作，则在第二个站点处于联机状态并且重新建立同步之前，只会维护一个数据副本。 |
| 最大保护 | 提供与最大可用性相似的保护级别。 如果主数据库无法将重做更改写入至少一个备用数据库，则主数据库会随着添加的功能关闭。 这可确保不会发生数据丢失，但代价是可用性更加脆弱。 |

>[!IMPORTANT]
>如果需要恢复点目标 (RPO) 为零，则建议使用最大可用性配置。 这样即使发生多个故障，也可以保证 RPO。 例如，即使主数据库发生网络中断，随后主数据库有时丢失，同时网络中断仍然有效。

### <a name="data-guard-deployment-patterns"></a>Data Guard 部署模式

Oracle 允许为重做生成配置多个目标，从而允许使用多个备用数据库。 最常见的配置如下图所示，另一个区域中有单个备用数据库。

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="显示 Oracle 默认 Data Guard 部署的图。":::

对于默认部署，Data Guard 采用最大性能模式进行配置。 此配置通过异步重做传输提供近乎实时的数据复制。 备用数据库无需在 RAC 部署内运行，但建议它满足主站点的性能要求。

对于需要严格运行时间或 RPO 为零的环境，我们建议采用类似于下图所示的部署。 最大可用性配置包括在同步模式下应用重做的本地备用数据库以及在远程区域中运行的第二个备用数据库。

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="显示最大可用性 Data Guard 部署的图。":::

当在不同区域中运行数据库和应用程序服务器会影响应用程序性能时，可以创建本地备用数据库。 在此配置中，当主群集上需要计划内或计划外维护时，会使用本地备用数据库。 可以通过同步复制运行这些数据库，因为它们处于相同区域中，可确保它们之间不会丢失任何数据。

### <a name="data-guard-configuration-considerations"></a>Data Guard 配置注意事项

应实现 Data Guard 代理，因为它可简化 Data Guard 配置的实现，并确保遵循最佳做法。 它提供性能监视功能，并显著简化了切换、故障转移和重新实例化过程。

Data Guard 允许运行观察程序进程，该进程监视 Data Guard 配置中的所有数据库，以确定数据库可用性。 如果主数据库发生故障，Data Guard 观察程序可以自动启动故障转移到配置中的备用数据库。 可以基于物理站点数（最多三个），通过多个观察程序来实观 Data Guard 观察程序。 

此观察程序应位于将支持应用层的基础结构上。 主观察程序应始终存在于主数据库不在的物理站点上。 建议在自动执行 Data Guard 观察程序触发的故障转移操作时务必小心。 首先，确保应用程序经过设计和测试，可在数据库在单独位置运行时提供可接受的服务。

如果应用程序只能在本地运行，则必须手动故障转移到辅助站点。 需要高可用性级别（99.99% 或 99.999% 运行时间）的环境应同时使用本地和远程备用数据库，如上图所示。 在这些情况下，参数 FastStartFailoverTarget 只会设置为本地备用数据库。

对于支持跨站点应用程序/数据库访问的所有应用程序，FastStartFailoverTarget 会设置为 Data Guard 配置中的所有备用数据库。

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) 是 Oracle Database Enterprise Edition 包含的基本 Data Guard 功能的超集。 它提供添加的以下功能，这些功能可在整个 Oracle Exadata 部署中使用：

- 独有的损坏检测和自动修复。
- 快速故障转移到生产的同步副本 – 手动或自动。
- 将生产工作负载卸载到同步备用开放只读数据库。
- 数据库滚动升级和备用。 使用物理备用进行首次修补。
- 将增量备份卸载到备用数据库。
- 跨任何距离的零数据丢失数据恢复保护，不会影响性能。

[https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) 处的白皮书很好地概述了以上功能，如下图所示。

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="显示 Oracle Active Data Guard 功能概述的图。":::

## <a name="backup-recommendations"></a>备份建议

务必备份数据库。 使用还原和恢复功能可将数据库还原到相同或其他系统，或是恢复数据库文件。

需创建备份恢复策略来保护 Oracle Database 设备数据库不会遭受数据丢失，这十分重要。 造成此类丢失的原因可能是磁盘的物理问题导致读取或写入运行数据库所需的磁盘文件失败。 用户错误也可能会导致数据丢失。 通过备份功能能够进行数据库时间点还原 (PITR)、系统更改编号 (SCN) 恢复和最新恢复。 可以在浏览器用户界面中或是从命令行界面创建备份策略。

有以下备份选项可用：

- 备份到 NFS 存储卷（快速恢复区域 (FRA) - /u98）。
- 使用 Azure NetApp 文件 SnapCenter – 快照。

要考虑的过程：

- 手动或自动备份。
- 自动备份会写入 NFS 存储卷（例如 /u98）。
- 备份在数据库系统时区的午夜 12:00 – 早上 6:00 之间运行。
- 当前保持期：7、15、30、45 和 60 天。

- 从存储在对象存储中的备份恢复数据库：
  - 恢复到数据丢失可能最少的上一个已知良好状态。
  - 使用指定时间戳。
  - 使用指定 SCN。
  - BackupReport – 使用备份报告中的 SCN 而不是指定 SCN。

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="显示客户数据库备份到 FRA (/u98) 和非 FRA (/u95) 的图。":::

### <a name="backup-policy"></a>备份策略

备份策略定义备份详细信息。 创建备份策略时，会为数据库备份 FRA（NFS 位置）定义目标并定义恢复窗口。

默认情况下，使用 BASIC 压缩算法。 对磁盘或 NFS 备份策略使用 LOW、MEDIUM 或 HIGH 压缩算法时，有一些许可证注意事项。

### <a name="backup-levels"></a>备份级别

执行备份时指定备份级别。

- 级别 0 - 完整
- 级别 1 – 增量
- LongTerm/Archievelog - 备份保留策略除外，使用非 FRA 位置（例如 /u95）。

## <a name="next-steps"></a>后续步骤

了解如何在发生故障时恢复 Oracle 数据库：

> [!div class="nextstepaction"]
> [在 Azure BareMetal 基础结构上恢复 Oracle 数据库](oracle-high-availability-recovery.md)
