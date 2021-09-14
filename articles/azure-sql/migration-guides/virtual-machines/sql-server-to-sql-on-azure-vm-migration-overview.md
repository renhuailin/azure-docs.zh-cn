---
title: 将 SQL Server 迁移到 Azure VM 上的 SQL Server（迁移概述）
description: 了解将 SQL Server 迁移到 Azure VM 上的 SQL Server 时的不同迁移策略。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 09/07/2021
ms.openlocfilehash: afca22d3a0775e470becfbd31a2f67d99552938d
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541658"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>迁移概述：将 SQL Server 迁移到 Azure VM 上的 SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

了解将 SQL Server 迁移到 Azure 虚拟机 (VM) 上的 SQL Server 的不同迁移策略。 

你可以迁移在本地或以下位置运行的 SQL Server：

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS) 
- 计算引擎 (Google Cloud Platform - GCP)

有关其他迁移指南，请参阅[数据库迁移](/data-migration)。 

## <a name="overview"></a>概述

当想要使用熟悉的 SQL Server 环境以及 OS 控件，并想要利用云提供的功能（例如内置的 VM 高可用性、[自动备份](../../virtual-machines/windows/automated-backup.md)和[自动修补](../../virtual-machines/windows/automated-patching.md)）时，可迁移到 [Azure 虚拟机 (VM) 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)。 

通过使用 [Azure 混合权益许可模型](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)提供自己的许可证，或通过获取[免费安全更新程序](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)扩展 SQL Server 2008 和 SQL Server 2008 R2 的支持，从而节省成本。 


## <a name="choose-appropriate-target"></a>选择适当的目标

Azure 虚拟机在 Azure 的许多不同区域运行，还提供各种[计算机大小](../../../virtual-machines/sizes.md)和[存储选项](../../../virtual-machines/disks-types.md)。 在确定 SQL Server 工作负载 VM 和存储的适当大小时，请参阅 [Azure 虚拟机上 SQL Server 的性能准则](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#vm-size)。 确定工作负载的 VM 大小和存储要求。 建议通过基于性能的 [Azure Migrate 评估](../../../migrate/concepts-assessment-calculation.md#types-of-assessments)来调整它们的大小。 如果无法使用该选项，请参阅以下文章，了解如何创建自己的[性能基准](https://azure.microsoft.com/services/virtual-machines/sql-server/)。

另请注意 VM 上 SQL Server 的正确安装和配置方式。 建议使用 [Azure SQL 虚拟机映像库](../../virtual-machines/windows/create-sql-vm-portal.md)，因为通过它可以创建版本、编辑和操作系统都正确的 SQL Server VM。 此外，这样还会自动向 SQL Server [资源提供程序](../../virtual-machines/windows/create-sql-vm-portal.md)注册 Azure VM，从而启用自动备份和自动修补等功能。

## <a name="migration-strategies"></a>迁移策略

将用户数据库迁移到 Azure VM 上的 SQL Server 实例有两种迁移策略：迁移和直接迁移 。 

哪种方法适合你的业务通常取决于以下因素： 

- 迁移的大小和规模
- 迁移速度
- 应用程序对更改代码的支持情况
- 需要更改 SQL Server 版本、操作系统还是同时更改两者。
- 现有产品的可支持生命周期
- 迁移过程中应用程序的故障时间窗口

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="虚拟机迁移故障时间":::

下表描述了两种迁移策略的不同之处：
<br />

| **迁移策略** | **说明** | **使用时机** |
| --- | --- | --- |
| **直接迁移** | 使用“直接迁移”迁移策略，是将整个物理或虚拟 SQL Server 从其当前位置都移动到 Azure VM 上的 SQL Server 实例，而不对操作系统或 SQL Server 版本进行任何更改。 要使用直接迁移完成迁移，请参阅 [Azure Migrate](../../../migrate/migrate-services-overview.md)。 <br /><br /> 源服务器保持联机并保留服务请求，同时源服务器和目标服务器同步数据，从而实现几乎无缝的迁移。 | 适用于单个迁移乃至非常大规模的迁移，甚至适用于数据中心退出等方案。 <br /><br /> 只需对用户 SQL 数据库或应用程序代码进行少许更改，甚至不需要更改，从而更快实现整体迁移。 <br /><br />迁移商业智能服务（如 [SSIS](/sql/integration-services/sql-server-integration-services)、[SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) 和 [SSAS](/analysis-services/analysis-services-overview)）无需执行额外的步骤。 |
|**迁移** | 在想要升级目标 SQL Server 和/或操作系统版本时，可使用迁移策略。 <br /> <br /> 从 Azure 市场中选择一个 Azure VM，或从与源 SQL Server 版本匹配的已准备 SQL Server 映像中选择一个。 <br/> <br/> 使用[适用于 Azure Data Studio 的 Azure SQL 迁移扩展](../../../dms/migration-using-azure-data-studio.md)将 SQL Server 数据库迁移到 Azure 虚拟机上的 SQL Server，并最大程度缩短停机时间。 | 适用于需要或想要使用 SQL Server 较新版本中提供的功能时，或者需要升级不再支持的旧 SQL Server 和/或操作系统版本时。  <br /> <br /> 可能需要对应用程序或用户数据库进行一些更改，才能支持 SQL Server 升级。 <br /><br />如果在迁移范围内迁移[商业智能](#business-intelligence) 服务，可能需要考虑一些其他事项。 |


## <a name="lift-and-shift"></a>直接迁移  

下表详细介绍了“直接迁移”迁移策略中将 SQL Server 迁移到 Azure VM 上 SQL Server 的可用方法：
<br />

|**方法** | **最低源版本** | **最低目标版本** | **源备份大小约束** |  **说明** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM 存储限制](../../../index.yml) |  将现有 SQL Server 按原样移动到 Azure VM 上 SQL Server 的实例。 可大规模迁移多达 35000 个 VM 的工作负载。 <br /><br /> 源服务器在同步服务器数据期间将保持联机并处理请求，从而最大程度地缩短故障时间。 <br /><br /> 自动化和脚本：[Azure Site Recovery 脚本](../../../migrate/how-to-migrate-at-scale.md)和 [Azure 规模迁移和计划示例](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

> [!NOTE]
> 现在，可以使用 Azure Migrate 将[故障转移群集实例](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)和[可用性组](sql-server-availability-group-to-sql-on-azure-vm.md)解决方案直接迁移到 Azure VM 上的 SQL Server。 

## <a name="migrate"></a>Migrate  

由于设置简单，因此建议采用在本地执行本机 SQL Server [备份](/sql/t-sql/statements/backup-transact-sql)，然后再将该文件复制到 Azure 的迁移方法。 这种方法对于自 2008 年开始的所有 SQL Server 都支持更大的数据库 (>1 TB)，以及更大的数据库备份 (>1 TB)。 但是，对于在 SQL Server 2014 中启动的小于 1 TB 并能与 Azure 建立良好连接的数据库，最好方法是[将 SQL Server 备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。 

将 SQL Server 数据库迁移到 Azure VM 上的 SQL Server 实例时，必须选择一种适合需要切换到目标服务器时情况的方法，因为这会影响应用程序的故障时间窗口。

下表详细说明了将 SQL Server 数据库迁移到 Azure VM 上 SQL Server 的所有可用方法：
<br />

|**方法** | **最低源版本** | **最低目标版本** | **源备份大小约束** | **说明** |
| --- | --- | --- | --- | --- |
| **[适用于 Azure Data Studio 的 Azure SQL 迁移扩展](../../../dms/migration-using-azure-data-studio.md)** | SQL Server 2005 | SQL Server 2008 | [Azure VM 存储限制](../../../index.yml) |  这是适用于 Azure Data Studio 的 Azure SQL 迁移扩展中的一个易于使用的向导式扩展，用于将 SQL Server 数据库迁移到 Azure 虚拟机上的 SQL Server。 使用压缩来最大程度地减小传输的备份大小。 <br /><br /> 适用于 Azure Data Studio 的 Azure SQL 迁移扩展在一个简单的用户界面中提供了评估和迁移功能。  |
| **[备份到文件](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM 存储限制](../../../index.yml) |  这是一项简单且经过严格测试的技术，适用于跨计算机移动数据库。 使用压缩来最大程度地减小传输的备份大小。 <br /><br /> 自动化和脚本：[Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) 和[从 AzCopy 迁移到 Blob 存储](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[将备份迁移到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 对于 SQL Server 2016 为 12.8 TB；其他情况下为 1 TB | 另一种方法是使用 Azure 存储将备份文件移至 VM。 使用压缩来最大程度地减小传输的备份大小。 <br /><br /> 自动化和脚本：[T-SQL 或维护计划](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[数据迁移助手 (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM 存储限制](../../../index.yml) |  [DMA](/sql/dma/dma-overview) 会评估本地 SQL Server，然后再无缝升级到 SQL Server 的更高版本，或者迁移到 Azure VM 上的 SQL Server、Azure SQL 数据库或 Azure SQL 托管实例。 <br /><br /> 不应用于已启用文件流的用户数据库。<br /><br /> DMA 还包括迁移 [SQL 和 Windows 登录名](/sql/dma/dma-migrateserverlogins)以及评估 [SSIS 包](/sql/dma/dma-assess-ssis)的功能。 <br /><br /> 自动化和脚本：[命令行接口](/sql/dma/dma-commandline) |
| **[拆离和附加](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM 存储限制](../../../index.yml) | 这种方法适合计划[使用 Azure Blob 存储服务存储这些文件](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)，再将它们附加到 Azure VM 上 SQL Server 实例的情况，特别是对于非常大的数据库或备份和还原时间很长的情况非常有用。 <br /><br /> 自动化和脚本：[T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) 和[从 AzCopy 迁移到 Blob 存储](../../../storage/common/storage-use-azcopy-v10.md)|
|**[日志传送](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4（仅限 Windows） | SQL Server 2008 SP4（仅限 Windows） | [Azure VM 存储限制](../../../index.yml) | 日志传送会将事务日志文件从本地复制到 Azure VM 上的 SQL Server 实例中。 <br /><br /> 这样可在故障转移期间最大程度地缩短故障时间，并且配置开销低于设置 Always On 可用性组。 <br /><br /> 自动化和脚本：[T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[分布式可用性组](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM 存储限制](../../../index.yml) |  [分布式可用性组](/sql/database-engine/availability-groups/windows/distributed-availability-groups)是一种特殊类型的可用性组，横跨两个独立的可用性组。 加入分布式可用性组的可用性组无需位于同一位置和包括跨域支持。 <br /><br /> 这种方法可最大程度地缩短故障时间，适用于本地已配置可用性组的情况。 <br /><br /> 自动化和脚本：[T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> - 对于网络受限或无网络选项的大型数据传输，请参阅[连接性受限的大型数据传输](../../../storage/common/storage-solution-large-dataset-low-network.md)。
> - 现在，可以使用 Azure Migrate 将[故障转移群集实例](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)和[可用性组](sql-server-availability-group-to-sql-on-azure-vm.md)解决方案直接迁移到 Azure VM 上的 SQL Server。 

### <a name="considerations"></a>注意事项

下表列出了查看迁移方法时要考虑的要点：

- 为获得最佳数据传输性能，请使用压缩的备份文件将数据库和文件迁移到 Azure VM 上的 SQL Server 实例。 对于较大的数据库，除压缩之外，还可[将备份文件拆分为较小的文件](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server)，以便在备份和传输过程中提高性能。 
- 如果要从 SQL Server 2014 或更高版本迁移，请考虑在网络传输过程中[加密备份](/sql/relational-databases/backup-restore/backup-encryption)，以保护数据。
- 为在数据库迁移过程中最大程度地缩短故障时间，请使用 Always On 可用性组选项。 
- 为最大程度地缩短故障时间，并避免产生可用性组配置开销，请使用日志传送选项。 
- 对于网络受限或无网络选项，请使用 Azure 中提供的脱机迁移方法，例如备份和还原或[磁盘传输服务](../../../storage/common/storage-solution-large-dataset-low-network.md)。
- 此外，要在 Azure VM 上的 SQL Server 中更改 SQL Server 版本，请参阅[更改 SQL Server 版本](../../virtual-machines/windows/change-sql-server-edition.md)。

## <a name="business-intelligence"></a>商业智能 

在迁移用户数据库迁移范围之外的 SQL Server 商业智能服务时，可能会有其他注意事项。 

这些服务包括：

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>支持的版本

在准备将 SQL Server 数据库迁移到 Azure VM 上的 SQL Server 时，请务必考虑支持的 SQL Server 版本。 有关 Azure VM 上当前支持的 SQL Server 版本列表，请参阅 [Azure VM 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)。

## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的。

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[使用 Logman 自动执行 Perfmon 数据收集](https://www.microsoft.com/download/details.aspx?id=103114)|用于收集 Perfmon 数据以了解基线性能的工具，该工具可帮助推荐迁移目标。 该工具使用 logman.exe 创建命令，该命令可创建、启动、停止和删除远程 SQL Server 上设置的性能计数器。|
|[Multiple-SQL-VM-VNet-ILB](https://www.microsoft.com/download/details.aspx?id=103104)|本白皮书概述在 SQL Server Always On 可用性组配置中设置多个 Azure 虚拟机的步骤。|
|[每个区域支持超级 SSD 的 Azure 虚拟机](https://www.microsoft.com/download/details.aspx?id=103105)|这些 PowerShell 脚本提供一个编程选项，可用于检索支持超级 SSD 的 Azure 虚拟机的区域列表。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="next-steps"></a>后续步骤

要开始将 SQL Server 数据库迁移到 Azure VM 上的 SQL Server，请参阅[各个数据库迁移指南](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)。 

- 如需在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请查看[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)一文。

- 若要详细了解 Azure SQL，请参阅：
   - [部署选项](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 有关许可的信息，请参阅
   - [通过 Azure 混合权益自带许可](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [获得对 SQL Server 2008 和 SQL Server 2008 R2 的免费外延支持](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
