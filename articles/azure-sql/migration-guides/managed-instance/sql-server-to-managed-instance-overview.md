---
title: 将 SQL Server 迁移到 SQL 托管实例：迁移概述
description: 了解可用于将 SQL Server 数据库迁移到 Azure SQL 托管实例的各种工具和选项。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: ac2b535b2e6b7a6b4169d08dd1768d69e685a216
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561987"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>迁移概述：将 SQL Server 到 SQL 托管实例
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

了解有关将 SQL Server 迁移到 Azure SQL 托管实例的不同迁移选项和注意事项。 

你可以迁移在本地或以下位置运行的 SQL Server： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS) 
- 计算引擎 (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform - GCP) 

有关其他方案，请参阅[数据库迁移指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概述

对于需要完全托管服务而无需管理虚拟机或其操作系统的 SQL Server 工作负载，建议将 [Azure SQL 托管实例](../../managed-instance/sql-managed-instance-paas-overview.md) 作为目标选项。 使用 SQL 托管实例，只需最小程度的应用程序或数据库更改，便可将本地应用程序直接迁移到 Azure，同时可通过本机虚拟网络 (VNet) 支持实现实例的完全隔离。 

## <a name="considerations"></a>注意事项 

评估迁移选项时要考虑的关键因素取决于： 
- 服务器和数据库的数量
- 数据库大小
- 迁移过程中可接受的业务停机时间 

将 SQL Server 迁移到 SQL 托管实例的主要优点之一是，可以选择迁移整个实例，也可以只迁移单个数据库的子集。 请仔细计划在迁移过程中包括以下各项： 
- 需要共存于同一实例的所有数据库 
- 应用程序所需的实例级对象，包括登录名、凭据、SQL 代理作业和操作员，以及服务器级触发器。 

> [!NOTE]
> 即使发生严重的情况，Azure SQL 托管实例也能保证 99.99% 的可用性，因此，SQL MI 无法禁用这些功能造成的开销。 有关详细信息，请参阅[可能导致 SQL Server 和 Azure SQL 托管实例上出现不同性能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)博客。 


## <a name="choose-appropriate-target"></a>选择适当的目标

可借助一些通用准则来选择正确的服务层和 SQL 托管实例的特征，以帮助匹配你的[性能基线](sql-server-to-managed-instance-performance-baseline.md)： 

- 使用 CPU 使用情况基线来配置与 SQL Server 实例使用的核心数量相匹配的托管实例。 可能有必要横向扩展资源以匹配[硬件生成特征](../../managed-instance/resource-limits.md#hardware-generation-characteristics)。 
- 使用内存使用情况基线来选择 [vCore 选项](../../managed-instance/resource-limits.md#service-tier-characteristics)，使其正确匹配你的内存分配。 
- 使用文件子系统的基线 IO 延迟，在“常规用途”（延迟大于 5 毫秒）与“业务关键”（延迟小于 3 毫秒）服务层级之间进行选择。 
- 使用基线吞吐量来预分配数据和日志文件的大小，以实现预期的 IO 性能。 

可在部署期间选择计算和存储资源，然后在使用 [Azure 门户](../../database/scale-resources.md)之后对其进行更改，而不会导致应用程序停机。 

> [!IMPORTANT]
> 任何不符合[托管实例虚拟网络要求](../../managed-instance/connectivity-architecture-overview.md#network-requirements)的情况都可能导致无法创建新实例或使用现有实例。 详细了解如何 [创建新的](../../managed-instance/virtual-network-subnet-create-arm-template.md) 和  [配置现有的](../../managed-instance/vnet-existing-add-subnet.md) 网络。 

在 Azure SQL 托管实例中选择目标服务层（常规用途与业务关键）的另一项主要考虑因素是某些功能的可用性，例如仅在业务关键层可用的内存中 OLTP。 

### <a name="sql-server-vm-alternative"></a>SQL Server VM 替代项

也有可能 Azure VM 上的 SQL Server 比 Azure SQL 托管实例更符合你的业务要求。 

如果你的业务符合以下情况，请考虑改用 SQL Server VM： 

- 需要直接访问操作系统或文件系统（例如，为了在装有 SQL Server 的同一个虚拟机上安装第三方代理或自定义代理）。 
- 严重依赖于目前尚不支持的功能，如 FileStream/FileTable、PolyBase 和跨实例事务。 
- 绝对需要保持使用特定的 SQL Server 版本（如 2012）。 
- 计算要求比托管实例的要求低得多（如只需一个 vCore），且数据库整合不可接受。 


## <a name="migration-tools"></a>迁移工具


推荐的迁移工具是数据迁移助手和 Azure 数据库迁移服务。 还有其他可替代的迁移选项。 

### <a name="recommended-tools"></a>建议的工具

下表列出了推荐的迁移工具： 

|技术 | 描述|
|---------|---------|
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | 通过 Azure Migrate for Azure SQL，可以在 VMware 上大规模发现和评估 SQL 数据资产，并获得 Azure SQL 部署建议、目标大小和每月估算。 | 
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | 支持在脱机模式下迁移的第一方 Azure 服务，适合能够在迁移过程中适应停机的应用程序。 与联机模式下的连续迁移不同，脱机模式下的迁移将一次性完成从源到目标的完整数据库备份的还原。 | 
|[本地备份和还原](../../managed-instance/restore-sample-database-quickstart.md) | SQL 托管实例支持本机 SQL Server 数据库备份（.bak 文件）的还原 (RESTORE)，对于能够提供到 Azure 存储的完整数据库备份的客户而言，它是最简单的迁移选项。 还支持完整备份和差异备份，本文后面的[迁移资产部分](#migration-assets)中进行了介绍。| 
|[日志重播服务 (LRS)](../../managed-instance/log-replay-service-migrate.md) | 这是一项基于 SQL Server 日志传送技术为托管实例启用的云服务，它是适用于可向 Azure 存储提供完整、差异和日志数据库备份的客户的迁移选项。 LRS 用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。| 
| | |

### <a name="alternative-tools"></a>替代工具

下表列出了可替代的迁移工具： 

|技术 |说明  |
|---------|---------|
|[事务复制](../../managed-instance/replication-transactional-overview.md) | 通过提供“发布者 - 订阅者”类型迁移选项，同时保持事务一致性，将数据从源 SQL Server 数据库表复制到 SQL 托管实例。 |  |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [批量复制程序 (bcp) 实用程序](/sql/tools/bcp-utility)将数据从 SQL Server 实例复制到数据文件中。 使用 BCP 实用程序从源中导出数据，并将数据文件导入到目标 SQL 托管实例中。</br></br> 若要执行高速批量复制操作以将数据迁移到 Azure SQL 数据库，可使用[智能批量复制工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)，利用并行复制任务最大程度加快传输速度。 | 
|[导入导出 Wizard / BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是扩展名为 `.bacpac` 的 Windows 文件，用于封装数据库的架构和数据。 BACPAC 既可以用于从源 SQL Server 导出数据，也可以将文件导回 Azure SQL 托管实例。  |  
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| Azure 数据工厂中的[复制活动](../../../data-factory/copy-activity-overview.md)使用内置连接器和[集成运行时](../../../data-factory/concepts-integration-runtime.md)将数据从源 SQL Server 数据库迁移到 SQL 托管实例。</br> </br> ADF 支持各种[连接器](../../../data-factory/connector-overview.md)，可将数据从 SQL Server 源迁移到 SQL 托管实例。 |
| | |

## <a name="compare-migration-options"></a>比较迁移选项

比较迁移选项，选择适合你业务需求的路径。 

### <a name="recommended-options"></a>推荐选项

下表比较了推荐的迁移选项： 

|迁移选项  |何时使用  |注意事项  |
|---------|---------|---------|
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | - 大规模迁移单个数据库或多个数据库。 </br> - 可在迁移过程中适应停机时间。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM |  - 可以通过 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md) 自动进行大规模迁移。 </br> - 完成迁移的时间取决于数据库的大小，并受备份和还原时间的影响。 </br> - 可能需要足够的停机时间。 |
|[本机备份和还原](../../managed-instance/restore-sample-database-quickstart.md) | - 迁移单个业务线应用程序数据库。  </br> - 无需单独的迁移服务或工具即可快速轻松地进行迁移。  </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM | - 数据库备份使用多个线程来优化指向 Azure Blob 存储的数据传输，但是 ISV 带宽和数据库大小会影响传输速率。 </br> - 停机时间应包含执行完整备份和还原所需的时间（这是数据操作的大小）。| 
|[日志重播服务 (LRS)](../../managed-instance/log-replay-service-migrate.md) | - 迁移单个业务线应用程序数据库。  </br> - 需要对数据库迁移进行更多的控制。  </br> </br> 支持的源： </br> - SQL Server (2008 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM | - 迁移需要在 SQL Server 上进行完整的数据库备份，并将备份文件复制到 Azure Blob 存储。 LRS 用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。 </br> - 在迁移过程中还原的数据库将处于还原模式，并且在该过程完成之前不能用于读取或写入。| 
| | | |

### <a name="alternative-options"></a>替代选项

下表比较了替代迁移选项： 

|方法/技术 |何时使用 |注意事项  |
|---------|---------|---------|
|[事务复制](../../managed-instance/replication-transactional-overview.md) | - 通过持续将更改内容从源数据库表发布到目标 SQL 托管实例数据库表来进行迁移。 </br> - 所选表的全部或部分数据库迁移（数据库子集）。  </br> </br> 支持的源： </br> - SQL Server (2012 - 2019)，存在一些限制 </br> - AWS EC2  </br> - GCP 计算 SQL Server VM | </br> - 与其他迁移选项相比，设置相对复杂。   </br> - 提供连续复制选项以迁移数据（无需使数据库脱机）。</br> - 在源 SQL Server 上设置发布服务器时，关于事务的复制，有一些限制需要考虑。 请参阅[对发布对象的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)了解详细信息。  </br> - 具有[监控复制活动](/sql/relational-databases/replication/monitor/monitoring-replication)的功能。    |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 迁移全部或部分数据迁移。 </br> - 可以适应停机时间。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM   | - 从源导出数据并导入目标时需要停机时间。 </br> - 导出/导入过程中使用的文件格式和数据类型需要与表架构一致。 |
|[导入导出 Wizard / BACPAC](../../database/database-import.md)| - 迁移单个业务线应用程序数据库。 </br>- 适用于较小的数据库。  </br>  不需要单独的迁移服务或工具。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM  |   </br> - 需要停机，因为数据需要在源处导出并在目标处导入。   </br> - 导出/导入中使用的文件格式和数据类型需要与表架构一致，以避免“截断”/“数据类型不匹配”错误。 </br> - 导出包含大量对象的数据库所花费的时间可能会非常长。 |
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| - 从源 SQL Server 数据库迁移和/或转换数据。</br> - 通常要为商业智能 (BI) 工作负载将多个数据源的数据合并到 Azure SQL 托管实例。   </br> - 需要在 ADF 中创建数据移动管道，以便将数据从源移动到目标。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)是一个重要的考虑因素，它取决于管道触发器、活动运行数、数据移动的持续时间等。 |
| | | |

## <a name="feature-interoperability"></a>具有互操作性 

如果迁移的工作负载还依赖其他 SQL Server 功能，则还有其他注意事项。 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

使用 [Azure 数据库迁移服务 (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md) 将 SSISDB 中的 SQL Server Integration Services (SSIS) 包和项目迁移到 Azure SQL 托管实例。 

迁移中仅支持 SSISDB 中以 SQL Server 2012 开头的 SSIS 包。 在迁移前转换旧 SSIS 包。 有关详细信息，请参阅[项目转换教程](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model)。 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) 报表可迁移到 Power BI 中的分页报表。 使用  [RDL 迁移工具](https://github.com/microsoft/RdlMigration)帮助准备和迁移报告。 此工具由 Microsoft 开发，可帮助客户将 RDL 报表从其 SSRS 服务器迁移到 Power BI。 可在 GitHub 上使用该工具，它记录有迁移方案的端到端演练过程。 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

可以将 SQL Server 2012 及更高版本中的 SQL Server Analysis Services 表格模型迁移到 Azure Analysis Services，这是 Azure 中 Analysis Services 表格模型的 PaaS 部署模型。 可以在此[视频教程](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)中了解有关将本地模型迁移到 Azure Analysis Services 的详细信息。

另外，还可以考虑[使用新的 XMLA 读/写终结点将本地 Analysis Services 表格模型迁移到 Power BI Premium](/power-bi/admin/service-premium-connect-tools)。 
> [!NOTE]
> Power BI XMLA 读/写终结点功能当前为公共预览版，在正式发布之前，不应将其用于生产工作负载。

#### <a name="high-availability"></a>高可用性

由于[常规用途（标准可用性模型）](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)和[业务关键（高级可用性模型）](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL 托管实例中都已内置有高可用性体系结构，因此 SQL Server 高可用性功能 Always On 故障转移群集实例和 Always On 可用性组在目标 Azure SQL 托管实例上已过时。 高级可用性模型还提供读取扩展，该扩展允许出于只读目的连接到某个辅助节点。     

除了 SQL 托管实例中包含的高可用性体系结构之外，还有[自动故障转移组](../../database/auto-failover-group-overview.md)功能，可借助该功能管理将托管实例中的数据库复制和故障转移到另一个区域的操作。 

#### <a name="sql-agent-jobs"></a>SQL 代理作业

使用脱机 Azure 数据库迁移服务 (DMS) 选项来迁移 [SQL Agent 作业](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)。 其他情况下，使用 SQL Server Management Studio 在 Transact-SQL (T-SQL) 中编写脚本，然后在目标 SQL 托管实例上手动重新创建它们。 

> [!IMPORTANT]
> 目前，Azure DMS 仅支持涉及 T-SQL 子系统步骤的作业。 涉及 SSIS 打包步骤的作业需要手动迁移。 

#### <a name="logins-and-groups"></a>登录名和组

在脱机模式下可以使用数据库迁移服务 (DMS) 将来自源 SQL Server 的 SQL 登录名移动到 Azure SQL 托管实例。  使用“迁移向导”中的“[选择登录名](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)”边栏选项卡将登录名迁移到目标 SQL 托管实例 。 

默认情况下，Azure 数据库迁移服务仅支持迁移 SQL 登录名。 但是，可通过以下方式启用迁移 Windows 登录名的功能：

确保目标 SQL 托管实例具有 Azure AD 读取访问权限，该权限可由具有“全局管理员”角色的用户通过 Azure 门户进行配置。
配置 Azure 数据库迁移服务实例以启用 Windows 用户/组登录名迁移，这通过 Azure 门户在“配置”页上进行设置。 启用此设置后，重启服务以使更改生效。

重启服务后，Windows 用户/组登录名将出现在可用于迁移的登录名列表中。 对于迁移的所有 Windows 用户/组登录名，系统都会提示提供关联的域名。 不支持服务用户帐户（域名为 NT AUTHORITY 的帐户）和虚拟用户帐户（域名为 NT SERVICE 的帐户）。

若要了解详细信息，请参阅[如何使用 T-SQL 将 SQL Server 实例中的 Windows 用户和组迁移到 Azure SQL 托管实例](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)。

或者，还可以使用由 Microsoft 数据迁移架构师专门设计的 [PowerShell 实用程序工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)。 该实用程序使用 PowerShell 创建一个 T-SQL 脚本来重新创建登录名，并从源中选择数据库用户到目标。 该工具自动将 Windows AD 帐户映射到 Azure AD 帐户，并且可以对照源 Active Directory 对每个登录名进行 UPN 查找。 该工具脚本自定义服务器和数据库角色，以及角色成员资格、数据库角色和用户权限。 当前不支持所包含的数据库，并且仅对可能的 SQL Server 权限的一部分进行了脚本编写。 

#### <a name="encryption"></a>加密

使用本机还原选项将 [透明数据加密](../../database/transparent-data-encryption-tde-overview.md)保护的数据库迁移到托管实例时，需要先[将源 SQL Server 中的相应证书迁移](../../managed-instance/tde-certificate-migrate.md)到目标 SQL 托管实例，再还原数据库 。 

#### <a name="system-databases"></a>系统数据库

不支持还原系统数据库。 若要迁移实例级对象（存储在 master 或 msdb 数据库中），请使用 Transact-SQL (T-SQL) 对它们进行脚本编写，然后在目标托管实例上重新创建它们。 

#### <a name="in-memory-oltp-memory-optimized-tables"></a>内存中 OLTP（内存优化表）

SQL Server 提供内存中 OLTP 功能，允许使用内存优化表、内存优化表类型和本地编译的 SQL 模块来运行具有高吞吐量和低延迟事务处理要求的工作负载。 

> [!IMPORTANT]
> 内存中 OLTP 仅在 Azure SQL 托管实例中的业务关键层中受支持（在常规用途层中不受支持）。

如果本地 SQL Server 中存在内存优化表或内存优化表类型，并且你想要迁移到 Azure SQL 托管实例，则你应该：

- 为支持内存中 OLTP 的目标 Azure SQL 托管实例选择业务关键层，或
- 如果你想迁移到 Azure SQL 托管实例中的常规用途层，请删除内存优化表、内存优化表类型和本地编译的 SQL 模块，它们在迁移数据库之前与内存优化对象交互。 以下 T-SQL 查询可用于识别迁移到常规用途层之前需要删除的所有对象：

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

若要详细了解内存中技术，请参阅[通过使用 Azure SQL 数据库和 Azure SQL 托管实例的内存中技术来优化性能](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview)

## <a name="leverage-advanced-features"></a>利用高级功能 

请确保利用 SQL 托管实例提供的基于云的高级功能。 例如，你不用再操心备份管理，服务会为你管理。 可将数据库还原到某个[保留期内的时间点](../../database/recovery-using-backups.md#point-in-time-restore)。 此外，无需考虑设置高可用性，因为系统中内置了 [高可用性](../../database/high-availability-sla.md)。 

若要增强安全性，请考虑使用  [Azure Active Directory 身份验证](../../database/authentication-aad-overview.md)、[审核](../../managed-instance/auditing-configure.md)、 [威胁检测](../../database/azure-defender-for-sql.md)、 [行级别安全性](/sql/relational-databases/security/row-level-security)和 [动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)。

除了高级管理和安全功能以外，SQL 托管实例还提供一组高级工具来帮助你[监视和优化工作负载](../../database/monitor-tune-overview.md)。 借助 [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md)，你可以集中监视大量托管实例。托管实例中的 [自动优化](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) 连续监视 SQL 计划执行统计信息的性能，并自动修复已识别的性能问题。 

只有将[数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)更新到最新的兼容性级别 (150) 后，某些功能才可用。 

## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的。

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[DBLoader 实用程序](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader 可用于将带分隔符的文本文件中的数据加载到 SQL Server 中。 此 Windows 控制台实用程序使用 SQL Server 本机客户端大容量加载接口，该接口可在所有版本的 SQL Server（包括 Azure SQL MI）上运行。|
|[将本地 SQL Server 登录名迁移到 Azure SQL 托管实例的实用程序](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|PowerShell 脚本可创建一个 T-SQL 命令脚本来重新创建登录名并从本地 SQL Server 选择数据库用户到 Azure SQL 托管实例。 使用该工具可将 Windows AD 帐户自动映射到 Azure AD 帐户，还可以选择性地迁移 SQL Server 本机登录名。|
|[使用 Logman 自动执行 Perfmon 数据收集](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|用于收集 Perfmon 数据以了解基线性能的工具，该工具可帮助推荐迁移目标。 该工具使用 logman.exe 创建命令，该命令可创建、启动、停止和删除远程 SQL Server 上设置的性能计数器。|
|[白皮书 - 通过还原完整备份和差异备份将数据库迁移到 Azure SQL 托管实例](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|如果只有完整备份和差异备份（且没有日志备份功能），可借助此白皮书获取相关指导和了解相关步骤，加速完成从 SQL Server 到 Azure SQL 托管实例的迁移。|

这些资源是作为 Data SQL Ninja 计划的一部分开发的，该计划由 Azure 数据组工程团队提供赞助。 Data SQL Ninja 计划的核心宗旨是解锁和加速复杂的现代化进程，并争取数据平台向 Microsoft Azure 数据平台迁移的机会。 如果你认为贵组织有意参与 Data SQL Ninja 计划，请联系帐户团队并让他们提交提名。


## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 迁移到 Azure SQL 托管实例，请参阅 [SQL Server 到 SQL 托管实例的迁移指南](sql-server-to-managed-instance-guide.md)。

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL 托管实例，请参阅：
   - [Azure SQL 托管实例中的服务层级](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server 与 Azure SQL 托管实例之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
