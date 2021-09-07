---
title: 适用于 Azure VM 中 SQL Server 备份的 Azure 备份支持矩阵
description: 提供有关在使用 Azure 备份服务备份 Azure VM 中的 SQL Server 时的支持设置和限制摘要。
ms.topic: conceptual
ms.date: 08/20/2021
ms.custom: references_regions
ms.openlocfilehash: 78dace2a60ff566af3485e6be0b7d9efc42d8654
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103873"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>适用于 Azure VM 中 SQL Server 备份的支持矩阵

可以使用 Azure 备份服务备份 Microsoft Azure 云平台上托管的 Azure VM 中的 SQL Server 数据库。 本文汇总了 Azure VM 中的 SQL Server 备份方案和部署的一般支持设置和限制。

## <a name="scenario-support"></a>方案支持

**支持** | **详细信息**
--- | ---
**支持的部署** | 支持 SQL 市场 Azure VM 和非市场（手动安装的 SQL Server）VM。
**支持的区域** | 适用于 SQL Server 数据库的 Azure 备份已在所有区域提供，但以下区域除外：法国南部 (FRS)、英国北部 (UKN)、英国南部 2 (UKS2)、UG IOWA (UGI) 和德国（黑森林）
**受支持的操作系统** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支持 Linux。
**支持的 SQL Server 版本** | SQL Server 2019、SQL Server 2017（详见[“搜索产品生命周期”页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)）、SQL Server 2016 和 SP（详见[“搜索产品生命周期”页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)）、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。<br><br>不支持 Express Local DB 版本。
**支持的 .NET 版本** | 安装在 VM 上的 .NET Framework 4.5.2 或更高版本
**支持的部署** | 支持 SQL 市场 Azure VM 和非市场（手动安装的 SQL Server）VM。 [可用性组](backup-sql-server-on-availability-groups.md)始终支持独立实例。

## <a name="feature-considerations-and-limitations"></a>功能注意事项和限制

|设置  |最大限制 |
|---------|---------|
|服务器（和保管库）中可以保护的数据库数    |   2000      |
|支持的数据库大小（超出此值，可能会出现性能问题）   |   6 TB*      |
|数据库中支持的文件数    |   1000      |

*数据库大小限制取决于我们支持的数据传输速率以及备份时间限制配置。这并非硬性限制。[详细了解](#backup-throughput-performance)备份吞吐量性能。

* SQL Server 备份可配置在 Azure 门户或 PowerShell 中。 不支持 CLI。
* 此解决方案在 Azure 资源管理器 VM 和经典 VM 这两种[部署](../azure-resource-manager/management/deployment-models.md)上均受支持。
* 支持所有备份类型（完整/差异/日志）和恢复模式（简单/完整/批量记录）。
* 对于只读数据库：只有完整和仅复制完整备份是受支持的备份类型。
* 如果用户在备份策略中显式启用了 SQL 本机压缩，则支持该压缩。 Azure 备份会根据用户设置的此控件的值，用 COMPRESSION / NO_COMPRESSION 子句替代实例级别的默认值。
* 支持启用了 TDE 的数据库备份。 若要将 TDE 加密的数据库还原到另一个 SQL Server，需先[将证书还原到目标服务器](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)。 在 SQL Server 2016 及更高版本中，启用了 TDE 的数据库可以使用备份压缩功能，但传输大小较小（如[此处](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)所述）。
* 不支持对镜像数据库和数据库快照执行备份和还原操作。
* 不支持 SQL Server 故障转移群集实例 (FCI)。

## <a name="backup-throughput-performance"></a>备份吞吐量性能

对大型 SQL 数据库 (500 GB) 进行完整和差异备份时，Azure 备份可提供 200 Mbps 的稳定数据传输速率。 若要利用最佳性能，请确保：

- 为基础 VM（包含托管了数据库的 SQL Server 实例）配置了所需的网络吞吐量。 如果 VM 的最大吞吐量小于 200 Mbps，则 Azure 备份无法以最佳速度传输数据。<br>此外，必须为包含数据库文件的磁盘预配足够的吞吐量。 [详细了解](../virtual-machines/disks-performance.md) Azure VM 中的磁盘吞吐量和性能。 
- 在 VM 中运行的进程不消耗 VM 带宽。 
- 备份计划分散在一部分数据库之间。 在 VM 上并发运行的多个备份彼此分担网络消耗量。 [详细了解](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-)如何控制并发备份数。

>[!NOTE]
> [下载详细资源规划器](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)，以根据 VM 资源、带宽和备份策略，计算建议每个服务器保护的数据库的大概数量。

## <a name="next-steps"></a>后续步骤

了解如何[备份 Azure VM 上运行的 SQL Server 数据库](backup-azure-sql-database.md)。