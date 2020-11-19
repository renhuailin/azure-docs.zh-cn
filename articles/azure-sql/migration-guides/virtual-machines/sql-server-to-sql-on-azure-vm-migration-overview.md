---
title: '在 Azure VM 上 SQL Server 的 SQL Server (迁移概述) '
description: 若要将 SQL Server 迁移到 Azure Vm SQL Server，请参阅不同的迁移策略。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: d47abaade13958b4e28d3ad5f62b88e8a53e89a9
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917829"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>迁移概述：在 Azure Vm 上 SQL Server SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

了解将 SQL Server 迁移到 Azure 虚拟机 (Vm) SQL Server 的不同迁移策略。 

你可以迁移 SQL Server 本地或上运行的：

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS)  
- 计算引擎 (Google Cloud Platform GCP) 

对于其他方案，请参阅 [数据库迁移指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概述

当你想要将熟悉的 SQL Server 环境与 OS 控制一起使用，并想要利用云提供的功能，例如内置的 VM 高可用性、[自动备份](../../virtual-machines/windows/automated-backup.md)和[自动修补](../../virtual-machines/windows/automated-patching.md)功能时，可[在 Azure 虚拟机 (vm) ](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview)迁移到 SQL Server。 

通过使用 [Azure 混合权益许可模式](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) 提供自己的许可证，或通过获取 [免费安全更新](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)扩展 SQL Server 2008 和 SQL Server 2008 R2 的支持，从而节省成本。 


## <a name="choosing-appropriate-target"></a>选择适当的目标

Azure 虚拟机在 Azure 的许多不同区域运行，还提供各种 [计算机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 和 [存储选项](https://docs.microsoft.com/azure/virtual-machines/disks-types)。 确定 SQL Server 工作负荷的 VM 和存储的正确大小时，请参阅 [Azure 虚拟机上 SQL Server 的性能准则](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices#vm-size-guidance)。 确定工作负荷的 VM 大小和存储要求。 建议通过 Performance-Based [Azure Migrate 评估](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#types-of-assessments)调整这些大小。 如果这不是可用选项，请参阅以下文章，了解如何创建自己 [的性能基准](https://azure.microsoft.com/services/virtual-machines/sql-server/)。

还应考虑在 VM 上正确安装和配置 SQL Server。 建议使用 [AZURE SQL 虚拟机映像库](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) ，因为这样可以创建一个具有适当版本、版本和操作系统的 SQL Server VM。 这也会自动向 SQL Server [资源提供程序](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) 注册 Azure VM，从而启用自动备份和自动修补等功能。

## <a name="migration-strategies"></a>迁移策略

有两种迁移策略可将用户数据库迁移到 Azure Vm 上的 SQL Server 实例：**迁移和升级**。 **lift and shift** 

适用于你的业务的适当方法通常取决于以下因素： 

- 迁移的大小和规模
- 迁移速度
- 更改代码的应用程序支持
- 需要更改 SQL Server 版本、操作系统或同时更改两者。
- 现有产品的可支持生命周期
- 迁移过程中应用程序停机时间的窗口

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="虚拟机迁移停机时间":::

下表描述了这两种迁移策略的不同之处：
<br />

| **迁移策略** | **描述** | **使用时机** |
| --- | --- | --- |
| **提升 & 班次** | 使用 "提升" 和 "迁移" 迁移策略，将整个物理或虚拟 SQL Server 从其当前位置移动到 Azure VM 上的 SQL Server 实例中，无需对操作系统或 SQL Server 版本进行任何更改。 若要完成提升和转移迁移，请参阅 [Azure Migrate](../../../migrate/migrate-services-overview.md)。 <br /><br /> 源服务器保持联机和服务请求，同时源服务器和目标服务器同步数据，从而实现几乎无缝的迁移。 | 用于单个到非常大规模的迁移，甚至适用于数据中心出口之类的方案。 <br /><br /> 用户 SQL 数据库或应用程序不需要进行任何代码更改，从而提高整体迁移速度。 <br /><br />不需要执行其他步骤来迁移商业智能服务（如  [SSIS](/sql/integration-services/sql-server-integration-services)、 [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)和 [SSAS](/analysis-services/analysis-services-overview)）。 |
|**迁移** | 如果要升级目标 SQL Server 和/或操作系统版本，请使用迁移策略。 <br /> <br /> 从 Azure Marketplace 中选择一个 Azure VM，或从与源 SQL Server 版本匹配的已准备 SQL Server 映像中选择一个。 | 如果需要使用在 SQL Server 的较新版本中提供的功能，或者如果要求升级不再支持的旧 SQL Server 和/或操作系统版本，则使用。  <br /> <br /> 可能需要对某些应用程序或用户数据库进行更改才能支持 SQL Server 升级。 <br /><br />如果在迁移范围内迁移 [商业智能](#business-intelligence) 服务，可能需要考虑其他一些事项。 |


## <a name="lift-and-shift"></a>直接迁移  

下表详细介绍了 " **提升" 和** "迁移" 迁移策略的可用方法，以将 SQL Server 数据库迁移到 Azure vm SQL Server：
<br />

|**方法** | **最小源版本** | **最低目标版本** | **源备份大小约束** |  **说明** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM 存储限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  要在 Azure VM 上按原样移动到 SQL Server 实例的现有 SQL Server。 可扩展多达 35000 Vm 的迁移工作负荷。 <br /><br /> 源服务器 () 在同步服务器数据期间保持联机和处理请求，从而最大程度地减少停机时间。 <br /><br /> **自动化 & 脚本**： [Azure Site Recovery 脚本](../../../migrate/how-to-migrate-at-scale.md) 和 [Azure 的缩放迁移和计划示例](/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

由于设置很简单，因此建议的迁移方法是在本地执行本机 SQL Server [备份](/sql/t-sql/statements/backup-transact-sql) ，然后将该文件复制到 Azure。 此方法支持从2008和更大的数据库 ( 备份开始的所有版本 SQL Server ( # B0 1) TB 的数据库，) # B1 1 TB。 但是，对于从 SQL Server 2014 开始、小于 1 TB 的数据库，以及与 Azure 建立良好连接的数据库， [SQL Server 备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 是更好的方法。 

将 SQL Server 数据库迁移到 Azure Vm 上的 SQL Server 的实例时，必须选择一种适合在需要切换到目标服务器的方法，因为这会影响应用程序停机时间范围。

下表详细说明了在 Azure Vm 上将 SQL Server 数据库迁移到 SQL Server 的所有可用方法：
<br />

|**方法** | **最小源版本** | **最低目标版本** | **源备份大小约束** | **说明** |
| --- | --- | --- | --- | --- |
| **[备份到文件](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM 存储限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  这是一种简单且经过测试的技术，用于跨计算机移动数据库。 使用压缩来最大程度地减少传输的备份大小。 <br /><br /> **自动化 & 脚本**： [transact-sql (T-sql)](/sql/t-sql/statements/backup-transact-sql) 和 [AzCopy 到 Blob 存储](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| SQL Server 2016 为 12.8 TB，否则为 1 TB | 使用 Azure 存储将备份文件移动到 VM 的替代方法。 使用压缩来最大程度地减少传输的备份大小。 <br /><br /> **自动化 & 脚本**：  [t-sql 或维护计划](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[数据迁移助手 (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM 存储限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [DMA](/sql/dma/dma-overview)会评估本地 SQL Server，然后无缝升级到 SQL Server 的更高版本，或者迁移到 azure Vm、Azure sql 数据库或 azure sql 托管实例中的 SQL Server。 <br /><br /> 不应在启用 Filestream 的用户数据库上使用。<br /><br /> DMA 还包括迁移 [SQL 和 Windows 登录名](/sql/dma/dma-migrateserverlogins) 以及评估 [SSIS 包](/sql/dma/dma-assess-ssis)的功能。 <br /><br /> **自动化 & 脚本**： [命令行界面](/sql/dma/dma-commandline) |
| **[分离和附加](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM 存储限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | 当你计划 [使用 Azure Blob 存储服务存储这些文件](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) 并将它们附加到 azure VM 上的 SQL Server 实例时，请使用此方法，特别是对于非常大的数据库或备份和还原时间太长的情况下。 <br /><br /> **自动化 & 脚本**：  [t-sql](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) 和 [AzCopy 到 Blob 存储](../../../storage/common/storage-use-azcopy-v10.md)|
|**[日志传送](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 仅 (Windows)  | SQL Server 2008 SP4 仅 (Windows)  | [Azure VM 存储限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | 日志传送将事务日志文件从本地复制到 Azure VM 上的 SQL Server 实例中。 <br /><br /> 这在故障转移期间提供最少的停机时间，并且配置开销低于设置 Always On 可用性组。 <br /><br /> **自动化 & 脚本编写**： [t-sql](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[分布式可用性组](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM 存储限制](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [分布式可用性组](/sql/database-engine/availability-groups/windows/distributed-availability-groups)是一种特殊类型的可用性组，它跨两个单独的可用性组。 参与分布式可用性组的可用性组不需要位于同一位置，并且包括跨域支持。 <br /><br /> 此方法最大程度地减少停机时间，在本地配置可用性组时使用。 <br /><br /> **自动化 & 脚本编写**： [t-sql](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> 对于不允许网络选项的大数据传输，请参阅 [具有有限连接性的大型数据传输](../../../storage/common/storage-solution-large-dataset-low-network.md)。
> 

### <a name="considerations"></a>注意事项

下面列出了查看迁移方法时要考虑的要点：

- 为了获得最佳的数据传输性能，请使用压缩的备份文件将数据库和文件迁移到 Azure VM 上的 SQL Server 实例中。 对于较大的数据库，除了压缩外，还 [将备份文件拆分为较小的文件](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) ，以便在备份和传输过程中提高性能。 
- 如果从 SQL Server 2014 或更高版本迁移，请考虑在网络传输过程中对 [备份进行加密](/sql/relational-databases/backup-restore/backup-encryption) 以保护数据。
- 若要在数据库迁移过程中最大程度地减少停机时间，请使用 Always On 可用性组选项。 
- 若要最大程度地减少停机时间，而无需配置可用性组的开销，请使用日志传送选项。 
- 对于 "仅限不允许网络" 选项，请使用 Azure 中可用的脱机迁移方法，例如备份和还原或 [磁盘传输服务](../../../storage/common/storage-solution-large-dataset-low-network.md) 。
- 若要在 Azure VM 的 SQL Server 上更改 SQL Server 版本，请参阅 [更改 SQL Server 版本](../../virtual-machines/windows/change-sql-server-edition.md)。

## <a name="business-intelligence"></a>商业智能 

在迁移用户数据库迁移范围外 SQL Server 商业智能服务时，可能会有其他注意事项。 

这些服务包括：

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>支持的版本

准备将 SQL Server 数据库迁移到 Azure Vm 上的 SQL Server 时，请确保考虑支持的 SQL Server 版本。 有关 Azure Vm 上受支持的当前受支持 SQL Server 版本的列表，请参阅 [Azure vm](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)上的 SQL Server。


## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 数据库迁移到 Azure Vm 上的 SQL Server，请参阅 [各个数据库迁移指南](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)。 

- 有关可用于帮助你完成各种数据库和数据迁移方案以及专业任务的 Microsoft 和第三方服务和工具的矩阵，请参阅文章 [用于数据迁移的服务和工具。](../../../dms/dms-tools-matrix.md)

- 若要了解有关 Azure SQL 的详细信息，请参阅：
   - [部署选项](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要了解有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [成本调整和大小调整工作负荷迁移到 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 有关许可的信息，请参阅
   - [通过 Azure 混合权益自带许可证](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [获取 SQL Server 2008 和 SQL Server 2008 R2 的免费扩展支持](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- 若要评估应用程序访问层，请参阅 [数据访问迁移工具包 (预览) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 有关如何执行数据访问层 A/B 测试的详细信息，请参阅 [数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
