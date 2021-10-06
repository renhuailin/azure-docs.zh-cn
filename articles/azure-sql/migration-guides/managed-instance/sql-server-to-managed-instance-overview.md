---
title: 将 SQL Server 迁移到 SQL 托管实例：迁移概述
description: 了解可用于将 SQL Server 数据库迁移到 Azure SQL 托管实例的工具和选项。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 09/07/2021
ms.openlocfilehash: 62f895fdea75ff97154910c177cda04413663d68
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352038"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>迁移概述：将 SQL Server 到 Azure SQL 托管实例
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

了解用于将 SQL Server 数据库迁移到 Azure SQL 托管实例的选项和注意事项。 

你可以迁移在本地或以下位置运行的 SQL Server 数据库： 

- Azure 虚拟机上的 SQL Server。  
- Amazon Web Services (AWS) 弹性计算云 (EC2)。 
- AWS 关系数据库服务 (RDS)。 
- Google Cloud Platform (GCP) 中的计算引擎。  
- GCP 中 SQL Server 的云 SQL。 

有关其他迁移指南，请参阅[数据库迁移](/data-migration)。 

## <a name="overview"></a>概述

对于需要完全托管服务而无需管理虚拟机或其操作系统的 SQL Server 工作负载，建议将 [Azure SQL 托管实例](../../managed-instance/sql-managed-instance-paas-overview.md) 作为目标选项。 使用 SQL 托管实例，只需最小程度的应用程序或数据库更改，便可将本地应用程序迁移到 Azure。 它通过本机虚拟网络支持实现实例的完全隔离。 

请务必查看 [Azure SQL 托管实例中提供的](../../database/features-comparison.md) SQL Server 数据库引擎功能，以验证迁移目标的可支持性。  

## <a name="considerations"></a>注意事项 

评估迁移选项时要考虑的关键因素如下： 
- 服务器和数据库的数量
- 数据库大小
- 迁移过程中可接受的业务停机时间 

将 SQL Server 数据库迁移到 SQL 托管实例的主要优点之一是，可以选择迁移整个实例，也可以只迁移单个数据库的子集。 请仔细计划在迁移过程中包括以下各项： 
- 需要共存于同一实例的所有数据库 
- 应用程序所需的实例级对象，包括登录名、凭据、SQL 代理作业和操作员，以及服务器级触发器 

> [!NOTE]
> Azure SQL 托管实例保证 99.99% 的可用性，即使在关键场景中也是如此。 SQL 托管实例中某些功能产生的开销无法禁用。 有关详细信息，请参阅 [SQL 托管实例与 SQL Server 性能存在差异的关键原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)博客文章。 


## <a name="choose-an-appropriate-target"></a>选择适当的目标

可借助以下通用准则来选择正确的服务层和 SQL 托管实例的特征，以帮助匹配你的[性能基线](sql-server-to-managed-instance-performance-baseline.md)： 

- 使用 CPU 使用情况基线来配置与 SQL Server 实例使用的核心数量相匹配的托管实例。 可能有必要横向扩展资源以匹配[硬件生成特征](../../managed-instance/resource-limits.md#hardware-generation-characteristics)。 
- 使用内存使用情况基线来选择 [vCore 选项](../../managed-instance/resource-limits.md#service-tier-characteristics)，使其正确匹配你的内存分配。 
- 使用文件子系统的基线 I/O 延迟，在“常规用途”（延迟大于 5 毫秒）与“业务关键”（延迟小于 3 毫秒）服务层级之间进行选择。 
- 使用基线吞吐量来预分配数据和日志文件的大小，以实现预期的 I/O 性能。 

可在部署期间选择计算和存储资源，然后[在使用 Azure 门户之后对其进行更改](../../database/scale-resources.md)，而不会导致应用程序停机。 

> [!IMPORTANT]
> 任何不符合[托管实例虚拟网络要求](../../managed-instance/connectivity-architecture-overview.md#network-requirements)的情况都可能导致无法创建新实例或使用现有实例。 详细了解如何 [创建新的](../../managed-instance/virtual-network-subnet-create-arm-template.md) 和  [配置现有的](../../managed-instance/vnet-existing-add-subnet.md) 网络。 

在 Azure SQL 托管实例中选择目标服务层（常规用途与业务关键）的另一项主要考虑因素是某些功能的可用性，例如仅在业务关键层可用的内存中 OLTP。 

### <a name="sql-server-vm-alternative"></a>SQL Server VM 替代项

你的企业可能要求使 [Azure 虚拟机中的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 成为比 Azure SQL 托管实例更合适的目标。 

如果你的业务符合以下情况之一，请考虑改用 SQL Server 虚拟机 (VM)： 

- 需要直接访问操作系统或文件系统（例如，为了在装有 SQL Server 的同一个虚拟机上安装第三方代理或自定义代理）。 
- 严重依赖于目前尚不支持的功能，例如 FileStream/FileTable、PolyBase 和跨实例事务。 
- 需要保持使用特定的 SQL Server 版本（例如 2012）。 
- 计算要求比托管实例的要求低得多（如只需一个 vCore），且数据库整合不可接受。 

## <a name="migration-tools"></a>迁移工具

建议使用以下迁移工具： 

|技术 | 说明|
|---------|---------|
|[适用于 Azure Data Studio 的 Azure SQL 迁移扩展](../../../dms/migration-using-azure-data-studio.md)  | 适用于 Azure Data Studio 的 Azure SQL 迁移扩展提供 Azure Data Studio 中的 SQL Server 评估和迁移功能。 它支持联机（适用于需要最短停机时间的迁移）或脱机（适用于迁移期间持续停机的迁移）模式下的迁移。 |
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | 此 Azure 服务可帮助你在 VMware 上大规模发现和评估 SQL 数据。 它提供 Azure SQL 部署建议、目标大小调整和每月费用估计。 | 
|[Azure 数据库迁移服务](../../../dms/tutorial-sql-server-to-managed-instance.md)  | 此 Azure 服务支持在脱机模式下迁移，适合能够在迁移过程中适应停机的应用程序。 与联机模式下的连续迁移不同，脱机模式下的迁移将一次性完成从源到目标的完整数据库备份的还原。 | 
|[本地备份和还原](../../managed-instance/restore-sample-database-quickstart.md) | SQL 托管实例支持还原本机 SQL Server 数据库备份（.bak 文件）。 对于能够向 Azure 存储提供完整数据库备份的客户来说，这是最便捷的迁移选项。 还支持完整备份和差异备份，本文后面的[迁移资产部分](#migration-assets)中进行了介绍。| 
|[日志重播服务](../../managed-instance/log-replay-service-migrate.md) | 这种云服务为 SQL 托管实例启用，并且基于 SQL Server 日志传送技术。 此迁移选项适合可以向 Azure 存储提供完整、差异和日志数据库备份的客户。 日志重播服务用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。| 
| | |

下表列出了可替代的迁移工具： 

|**技术** |**说明**  |
|---------|---------|
|[事务复制](../../managed-instance/replication-transactional-overview.md) | 通过提供“发布者 - 订阅者”类型迁移选项，同时保持事务一致性，将数据从源 SQL Server 数据库表复制到 SQL 托管实例。 | 
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [大容量复制程序 (bcp) 工具](/sql/tools/bcp-utility)将数据从 SQL Server 实例复制到数据文件中。 使用该工具从源中导出数据，并将数据文件导入到目标 SQL 托管实例中。 </br></br> 若要执行高速大容量复制操作将数据移动到 Azure SQL 托管实例，可使用[智能大容量复制工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)，利用并行复制任务来最大化传输速度。 | 
|[导入导出向导/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是扩展名为 .bacpac 的 Windows 文件，用于封装数据库的架构和数据。 既可以使用 BACPAC 从 SQL Server 源导出数据，也可以将数据导回 Azure SQL 托管实例。 |  
|[Azure 数据工厂](../../../data-factory/connector-azure-sql-managed-instance.md)|  Azure 数据工厂中的[复制活动](../../../data-factory/copy-activity-overview.md)使用内置连接器和[集成运行时](../../../data-factory/concepts-integration-runtime.md)将数据从源 SQL Server 数据库迁移到 SQL 托管实例。</br> </br> 数据工厂支持各种[连接器](../../../data-factory/connector-overview.md)，可将数据从 SQL Server 源迁移到 SQL 托管实例。 |

## <a name="compare-migration-options"></a>比较迁移选项

比较迁移选项，选择适合你业务需求的路径。 

下表比较了我们推荐的迁移选项： 

|迁移选项  |何时使用  |注意事项  |
|---------|---------|---------|
|[适用于 Azure Data Studio 的 Azure SQL 迁移扩展](../../../dms/migration-using-azure-data-studio.md) | - 大规模迁移单个数据库或多个数据库。 </br> - 可以在联机（最短停机时间）和脱机（可接受停机时间）模式下运行。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM |  - 易于设置和入门。 </br> - 要求安装自承载集成运行时以访问本地 SQL Server 和备份。 </br> - 包括评估和迁移功能。 |
|[Azure 数据库迁移服务](../../../dms/tutorial-sql-server-to-managed-instance.md) | - 大规模迁移单个数据库或多个数据库。 </br> - 可在迁移过程中适应停机。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM |  - 可通过 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md) 自动执行大规模迁移。 </br> - 完成迁移的时间取决于数据库的大小，并受备份和还原时间的影响。 </br> - 可能需要足够的停机时间。 |
|[本机备份和还原](../../managed-instance/restore-sample-database-quickstart.md) | - 迁移单个业务线应用程序数据库。  </br> - 无需单独的迁移服务或工具即可快速轻松地进行迁移。  </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM | - 数据库备份使用多个线程来优化指向 Azure Blob 存储的数据传输，但是合作伙伴带宽和数据库大小会影响传输速率。 </br> - 停机时间应包含执行完整备份和还原所需的时间（这是数据操作的大小）。| 
|[日志重播服务](../../managed-instance/log-replay-service-migrate.md) | - 迁移单个业务线应用程序数据库。  </br> - 需要对数据库迁移进行更多的控制。  </br> </br> 支持的源： </br> - SQL Server (2008 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM | - 迁移需要在 SQL Server 上进行完整的数据库备份，并将备份文件复制到 Azure Blob 存储。 日志重播服务用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。 </br> - 在迁移过程中还原的数据库将处于还原模式，并且在该过程完成之前不能用于读取或写入。| 
| | | |

下表比较了替代迁移选项： 

|方法或技术 |何时使用 |注意事项  |
|---------|---------|---------|
|[事务复制](../../managed-instance/replication-transactional-overview.md) | - 通过持续将更改内容从源数据库表发布到目标 SQL 托管实例数据库表来进行迁移。 </br> - 对所选表（数据库的子集）执行全部或部分数据库迁移。  </br> </br> 支持的源： </br> - SQL Server (2012 - 2019)，存在一些限制 </br> - AWS EC2  </br> - GCP 计算 SQL Server VM | </br> - 与其他迁移选项相比，设置相对复杂。   </br> - 提供连续复制选项以迁移数据（无需使数据库脱机）。</br> - 在源 SQL Server 实例上设置发布服务器时，关于事务的复制，有一些限制需要考虑。 有关详细信息，请参阅[对发布对象的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)。  </br> - 具有[监控复制活动](/sql/relational-databases/replication/monitor/monitoring-replication)的功能。    |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 执行全部或部分数据迁移。 </br> - 可以适应停机。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM   | - 从源导出数据并导入目标时需要停机时间。 </br> - 导出或导入中使用的文件格式和数据类型需与表架构一致。 |
|[导入导出向导/BACPAC](../../database/database-import.md)| - 迁移单个业务线应用程序数据库。 </br>- 适用于较小的数据库。  </br>  不需要单独的迁移服务或工具。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM  |   </br> - 需要停机，因为数据需要在源处导出并在目标处导入。   </br> - 导出或导入中使用的文件格式和数据类型需要与表架构一致，以避免“截断”或“数据类型不匹配”错误。 </br> - 导出包含大量对象的数据库所花费的时间可能会大大增加。 |
|[Azure 数据工厂](../../../data-factory/connector-azure-sql-managed-instance.md)| - 从源 SQL Server 数据库迁移和/或转换数据。</br> - 通常要为商业智能 (BI) 工作负载将多个数据源的数据合并到 Azure SQL 托管实例。   </br> - 需要在数据工厂中创建数据移动管道，以将数据从源移动到目标。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)是一个重要考虑事项，基于管道触发器、活动运行、数据移动的持续时间等因素。 |
| | | |

## <a name="feature-interoperability"></a>功能互操作性 

如果迁移的工作负载还依赖其他 SQL Server 功能，则还有其他注意事项。 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

使用 [Azure 数据库迁移服务](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)将 SSISDB 中的 SQL Server Integration Services (SSIS) 包和项目迁移到 Azure SQL 托管实例。 

迁移中仅支持 SSISDB 中以 SQL Server 2012 开头的 SSIS 包。 在迁移前转换旧 SSIS 包。 有关详细信息，请参阅[项目转换教程](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)。 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

可将 SQL Server Reporting Services (SSRS) 报表迁移到 Power BI 中的分页报表。 使用  [RDL 迁移工具](https://github.com/microsoft/RdlMigration)来帮助准备和迁移报表。 Microsoft 开发了此工具来帮助客户将报表定义语言 (RDL) 报表从 SSRS 服务器迁移到 Power BI。 它在 GitHub 上提供，并记录了迁移方案的端到端演练。 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

可以将 SQL Server 2012 及更高版本中的 SQL Server Analysis Services 表格模型迁移到 Azure Analysis Services，这是 Azure 中 Analysis Services 表格模型的平台即服务 (PaaS) 部署模型。 可以在此[视频教程](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)中了解有关将本地模型迁移到 Azure Analysis Services 的详细信息。

另外，可以考虑[使用新的 XMLA 读/写终结点将本地 Analysis Services 表格模型迁移到 Power BI Premium](/power-bi/admin/service-premium-connect-tools)。 

### <a name="high-availability"></a>高可用性

SQL Server 高可用性功能 Always On 故障转移群集实例和 Always On 可用性组在目标 SQL 托管实例上已过时。 SQL 托管实例的[常规用途（标准可用性模型）](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)和[业务关键（高级可用性模型）](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)服务层中均已内置高可用性体系结构。 高级可用性模型还提供读取扩展，该扩展允许出于只读目的连接到某个辅助节点。     

除了 SQL 托管实例中包含的高可用性体系结构之外，借助[自动故障转移组](../../database/auto-failover-group-overview.md)功能还可以管理将托管实例中的数据库复制和故障转移到另一个区域的操作。 

### <a name="sql-agent-jobs"></a>SQL 代理作业

使用脱机 Azure 数据库迁移服务选项来迁移 [SQL Agent 作业](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)。 其他情况下，使用 SQL Server Management Studio 在 Transact-SQL (T-SQL) 中编写脚本，然后在目标 SQL 托管实例上手动重新创建它们。 

> [!IMPORTANT]
> 目前，Azure 数据库迁移服务仅支持涉及 T-SQL 子系统步骤的作业。 涉及 SSIS 打包步骤的作业需要手动迁移。 

### <a name="logins-and-groups"></a>登录名和组

在脱机模式下，使用数据库迁移服务将 SQL 登录名从 SQL Server 源移动到 Azure SQL 托管实例。  使用“迁移向导”中的[选择登录名](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)窗格将登录名迁移到目标 SQL 托管实例。 

默认情况下，Azure 数据库迁移服务仅支持迁移 SQL 登录名。 但是，可通过以下方式启用 Windows 登录名迁移：

- 确保目标 SQL 托管实例具有 Azure Active Directory (Azure AD) 读取访问权限。 具有全局管理员角色的用户可以通过 Azure 门户来配置该访问权限。
- 将 Azure 数据库迁移服务配置为启用 Windows 用户或组登录迁移。 可以通过 Azure 门户在“配置”页上进行设置。 启用此设置后，请重启该服务，以使更改生效。

重启服务后，Windows 用户或组登录名将出现在可用于迁移的登录名列表中。 对于迁移的所有 Windows 用户或组登录名，系统都会提示提供关联的域名。 不支持服务用户帐户（域名为 NT AUTHORITY 的帐户）和虚拟用户帐户（域名为 NT SERVICE 的帐户）。 若要了解详细信息，请参阅[如何使用 T-SQL 将 SQL Server 实例中的 Windows 用户和组迁移到 Azure SQL 托管实例](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)。

或者，还可以使用 Microsoft 数据迁移架构师专门设计的 [PowerShell 实用程序](https://www.microsoft.com/download/details.aspx?id=103111)。 该实用程序使用 PowerShell 创建一个 T-SQL 脚本来重新创建登录名，并从源中选择数据库用户到目标。 

PowerShell 实用程序自动将 Windows Server Active Directory 帐户映射到 Azure AD 帐户，并且可以针对源 Active Directory 实例的每个登录名执行 UPN 查找。 该实用程序脚本自定义服务器和数据库角色，以及角色成员身份和用户权限。 目前不支持所包含的数据库，并且仅对一部分可能的 SQL Server 权限进行脚本编写。 

### <a name="encryption"></a>加密

使用本机还原选项将 [透明数据加密](../../database/transparent-data-encryption-tde-overview.md)保护的数据库迁移到托管实例时，需要先[将源 SQL Server 实例中的相应证书迁移](../../managed-instance/tde-certificate-migrate.md)到目标 SQL 托管实例，再还原数据库 。 

### <a name="system-databases"></a>系统数据库

不支持还原系统数据库。 若要迁移实例级对象（存储在 master 和 msdb 数据库中），请使用 T-SQL 编写其脚本，然后在目标托管实例上重新创建它们。 

### <a name="in-memory-oltp-memory-optimized-tables"></a>内存中 OLTP（内存优化表）

SQL Server 提供内存中 OLTP 功能。 它允许使用内存优化表、内存优化表类型和本地编译的 SQL 模块来运行具有高吞吐量和低延迟事务处理要求的工作负载。 

> [!IMPORTANT]
> 内存中 OLTP 仅在 Azure SQL 托管实例的业务关键层中受支持。 在常规用途层中不受支持。

如果本地 SQL Server 中存在内存优化表或内存优化表类型，并且你想要迁移到 Azure SQL 托管实例，则你应该：

- 为支持内存中 OLTP 的目标 SQL 托管实例选择业务关键层。
- 如果你想迁移到 Azure SQL 托管实例中的常规用途层，请删除内存优化表、内存优化表类型和本地编译的 SQL 模块，它们在迁移数据库之前与内存优化对象交互。 可使用以下 T-SQL 查询识别迁移到常规用途层之前需要删除的所有对象：

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

若要详细了解内存中技术，请参阅[通过使用 Azure SQL 数据库和 Azure SQL 托管实例的内存中技术来优化性能](../../in-memory-oltp-overview.md)。

## <a name="advanced-features"></a>高级功能 

请确保利用 SQL 托管实例中的基于云的高级功能。 例如，你不需要担心管理备份的问题，因为服务会进行管理。 你可以还原到[保留期内的任何时间点](../../database/recovery-using-backups.md#point-in-time-restore)。 此外，也无需考虑设置高可用性，因为 [系统中内置了高可用性](../../database/high-availability-sla.md)。 

要增强安全性，请考虑使用  [Azure AD 身份验证](../../database/authentication-aad-overview.md)、[审核](../../managed-instance/auditing-configure.md)、 [威胁检测](../../database/azure-defender-for-sql.md)、 [行级别安全性](/sql/relational-databases/security/row-level-security)和 [动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)。

除了高级管理和安全功能以外，SQL 托管实例还提供高级工具来帮助你[监视和优化工作负载](../../database/monitor-tune-overview.md)。 借助 [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md)，你可以集中监视大量托管实例。托管实例中的 [自动优化](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) 连续监视 SQL 计划执行的性能，并自动修复已识别的性能问题。 

只有将[数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)更新到最新的兼容性级别 (150) 后，某些功能才可用。 

## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的。

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130)| 此工具为工作负载提供建议的“最适合”目标平台、云就绪性和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[将本地 SQL Server 登录名迁移到 Azure SQL 托管实例的实用程序](https://www.microsoft.com/download/details.aspx?id=103111)|PowerShell 脚本可创建一个 T-SQL 命令脚本来重新创建登录名并从本地 SQL Server 选择数据库用户到 Azure SQL 托管实例。 该工具允许将 Windows Server Active Directory 帐户自动映射到 Azure AD 帐户，还可以选择性地迁移 SQL Server 本机登录名。|
|[使用 Logman 自动执行 Perfmon 数据收集](https://www.microsoft.com/download/details.aspx?id=103114)|可以使用 Logman 工具收集 Perfmon 数据（以帮助你了解基准性能）和获取迁移目标建议。 该工具使用 logman.exe 创建命令，该命令将创建、启动、停止和删除在远程 SQL Server 实例上设置的性能计数器。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。


## <a name="next-steps"></a>后续步骤

- 若要开始将 SQL Server 数据库迁移到 Azure SQL 托管实例，请参阅 [SQL Server 到 Azure SQL 托管实例的迁移指南](sql-server-to-managed-instance-guide.md)。

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL 托管实例，请参阅：
   - [Azure SQL 托管实例中的服务层级](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server 与 Azure SQL 托管实例之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 

- 有关云迁移的框架和采用周期的详细信息，请参阅：
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负荷计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)。

- 若要详细了解如何在数据访问层执行 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
