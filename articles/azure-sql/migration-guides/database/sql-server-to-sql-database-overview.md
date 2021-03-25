---
title: SQL Server 到 SQL 数据库：迁移概述
description: 了解可用于将 SQL Server 数据库迁移到 Azure SQL 数据库的各种工具和选项。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 733b2375a26b0157f88bc148b52932e2f3e3f2e2
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488267"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>迁移概述：将 SQL Server 到 SQL 数据库
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

了解有关将 SQL Server 迁移到 Azure SQL 数据库的不同迁移选项和注意事项。 

你可以迁移在本地或以下位置运行的 SQL Server： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS) 
- 计算引擎 (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform - GCP) 

有关其他方案，请参阅[数据库迁移指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概述

对于需要完全托管的平台即服务 (PaaS) 的 SQL Server 工作负载，[Azure SQL 数据库](../../database/sql-database-paas-overview.md)是推荐的目标选项。 SQL 数据库处理大多数数据库管理功能以及内置的高可用性、智能查询处理、可伸缩性和性能功能，以适应多种不同的应用程序类型。 

SQL 数据库为多种[部署模型](../../database/sql-database-paas-overview.md#deployment-models)和[服务层级](../../database/service-tiers-vcore.md#service-tiers)灵活性，以满足不同类型的应用程序或工作负载。

迁移到 SQL 数据库的一个主要好处是，你可以利用 PaaS 功能实现应用程序的现代化，并消除对实例级别范围内的技术组件（例如 SQL 代理作业）的任何依赖关系。

如果你选择[基于 vCore 的购买模型](../../database/service-tiers-vcore.md)还可以通过使用适用于 SQL Server 的 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/) 将 SQL Server 的本地许可证迁移到 Azure SQL 数据库来节省成本。

本指南旨在阐明在准备将 SQL Server 数据库迁移到 Azure SQL 数据库时的迁移选项和注意事项。  

## <a name="considerations"></a>注意事项 

评估迁移选项时要考虑的关键因素取决于： 

- 服务器和数据库的数量
- 数据库大小
- 迁移过程中可接受的业务停机时间 

本指南中列出的迁移选项考虑了这些因素。 对于到 Azure SQL 数据库的逻辑数据迁移，迁移时间可能取决于数据库中对象的数量和数据库的大小。 

不同的工具可用于不同的工作负载和用户首选项。 某些工具可用于执行使用基于 UI 的工具的单一数据库的快速迁移，而其他工具可迁移多个数据库，这些数据库可自动处理大规模的迁移。 

## <a name="choose-appropriate-target"></a>选择适当的目标

请考虑一般准则，以帮助你选择正确的 Azure SQL 数据库部署模型和服务层级。 可在部署期间选择计算和存储资源，然后在使用 [Azure 门户](../../database/scale-resources.md)之后对其进行更改，而不会导致应用程序停机。


**部署模型**：了解应用程序工作负载和使用模式，以在单一数据库或弹性池之间进行选择。 

- [单一数据库](../../database/single-database-overview.md)表示一个完全托管的数据库，适用于大多数新式云应用程序和微服务。
- [弹性池](../../database/elastic-pool-overview.md)是单一数据库的集合，其中包含一组共享资源（例如 CPU 或内存），适合将池中的数据库与可预测的使用模式结合使用，可有效地共享同一组资源。

**购买模型**：在 vCore、DTU 或无服务器购买模型之间进行选择。 

- 利用 [vCore 模型](../../database/service-tiers-vcore.md)，你可以选择 Azure SQL 数据库的 vCore 数量，使其成为从本地 SQL Server 转换时的最简单选择。 这是支持使用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)节省许可证成本的唯一选项。 
- [DTU 模型](../../database/service-tiers-dtu.md)提取基础计算、内存和 IO 资源，以便提供混合的 DTU。 
- [无服务器模型](../../database/serverless-tier-overview.md)适用于需要自动按需缩放的工作负载，计算资源按每秒使用情况计费。 无服务器计算层将在非活动期间（仅对存储计费）自动暂停数据库；当活动返回时，它将自动恢复数据库。 

**服务层**：在三个专为不同类型的应用程序设计的服务层级之间进行选择。

- [常规用途/标准服务层级](../../database/service-tier-general-purpose.md)提供了一个以预算导向的均衡选项，其计算和存储适用于交付中下层应用程序，存储层内置了冗余，可以从故障中恢复。 适用于大多数数据库工作负载。 
- [业务关键/高级服务层级](../../database/service-tier-business-critical.md)适用于需要高事务速率、低延迟 IO 和高级别复原能力的高层应用程序，具有可用于故障转移和卸载读取工作负载的辅助副本。
- [超大规模服务层级](../../database/service-tier-hyperscale.md)适用于具有不断增长的数据量并且需要自动纵向扩展到 100 TB 的数据库大小的数据库。 专为特大型数据库设计。 

> [!IMPORTANT]
> [管理 Azure SQL 数据库中的事务日志记录速率](../../database/resource-limits-logical-server.md#transaction-log-rate-governance)以限制过高的数据引入速率。 因此在迁移过程中可能需要扩展目标数据库资源 (vCore/DTU) 以减轻 CPU 或吞吐量的压力。 选择适当大小的目标数据库，但计划在必要时为迁移扩展资源。 


### <a name="sql-server-on-azure-vm-alternative"></a>Azure VM 上的 SQL Server 替代项

你的企业可能要求使 [Azure 虚拟机中的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 成为比 Azure SQL 数据库更合适的目标。 

如果以下内容适用于你的业务，请考虑改为移动到 Azure VM 上的 SQL Server： 

- 需要直接访问操作系统或文件系统（例如，为了在装有 SQL Server 的同一个虚拟机上安装第三方代理或自定义代理）。 
- 严重依赖于目前尚不支持的功能，如 FileStream/FileTable、PolyBase 和跨实例事务。 
- 绝对需要保持使用特定的 SQL Server 版本（如 2012）。 
- 计算要求比托管实例的要求低得多（如只需一个 vCore），且数据库整合不可接受。 


## <a name="migration-tools"></a>迁移工具 

推荐的迁移工具是数据迁移助手和 Azure 数据库迁移服务。 还有其他可替代的迁移选项。 

### <a name="recommended-tools"></a>建议的工具

下表列出了推荐的迁移工具： 

|技术 | 说明|
|---------|---------|
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | 通过 Azure Migrate for Azure SQL，可以在 VMware 上大规模发现和评估 SQL 数据资产，并获得 Azure SQL 部署建议、目标大小和每月估算。 | 
|[数据迁移助手 (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|数据迁移助手是一种桌面工具，可提供对 SQL Server 的无缝评估和到 Azure SQL 数据库（架构和数据）的迁移。 此工具可以安装在本地服务器上，也可以安装在可以连接到源数据库的本地计算机上。 迁移过程是在源数据库和目标数据库中的对象之间进行的逻辑数据移动。 </br> - 迁移单一数据库（架构和数据）|
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|第一方 Azure 服务，可使用 Azure 门户将 SQL Server 数据库迁移到 Azure SQL 数据库，或通过 PowerShell 自动迁移。 Azure DMS 要求在预配过程中选择首选的 Azure 虚拟网络 (VNet)，以确保与源 SQL Server 数据库建立连接。 </br> - 迁移单一数据库或大规模迁移。 |
| | |


### <a name="alternative-tools"></a>替代工具

下表列出了可替代的迁移工具： 

|技术 |说明  |
|---------|---------|
|[事务复制](../../database/replication-to-sql-database.md)|通过在保持事务的一致性的同时提供发布服务器-订阅服务器类型迁移选项，将数据从源 SQL Server 数据库表复制到 SQL 数据库。 在发布服务器上发生的增量数据更改将传播到订阅服务器。|
|[导入导出服务/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是扩展名为 `.bacpac` 的 Windows 文件，用于封装数据库的架构和数据。 BACPAC 既可以用于从源 SQL Server 导出数据，也可以将数据导入 Azure SQL 数据库。 可以使用 Azure 门户将 BACPAC 文件导入到新的 Azure SQL 数据库。 </br></br> 对于大型数据库或大量数据库，为提高缩放性和性能，应考虑使用 [SqlPackage](../../database/database-import.md#using-sqlpackage) 命令行实用工具来导出和导入数据库。|
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[大容量复制程序 (bcp) 实用工具](/sql/tools/bcp-utility)将数据从 SQL Server 实例复制到数据文件中。 使用 BCP 实用工具从源中导出数据，并将数据文件导入到目标 SQL 数据库中。 </br></br> 若要进行高速大容量复制操作以将数据移动到 Azure SQL 数据库，[智能大容量复制工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)可用于利用并行复制任务来最大化传输速度。|
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-database.md)|Azure 数据工厂中的[复制活动](../../../data-factory/copy-activity-overview.md)使用内置连接器和[集成运行时](../../../data-factory/concepts-integration-runtime.md)将数据从源 SQL Server 数据库迁移到 SQL 数据库。</br> </br> ADF 支持各种[连接器](../../../data-factory/connector-overview.md)，可将数据从 SQL Server 源移动到 SQL 数据库。|
|[SQL 数据同步](../../database/sql-data-sync-data-sql-server-sql-database.md)|使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨多个本地和云端数据库双向同步选定数据。</br>如果需要跨 Azure SQL 数据库或 SQL Server 中的多个数据库使数据保持最新，数据同步非常有用。|
| | |

## <a name="compare-migration-options"></a>比较迁移选项

比较迁移选项，选择适合你业务需求的路径。 

### <a name="recommended-options"></a>推荐选项

下表比较了推荐的迁移选项： 

|迁移选项  |何时使用  |注意事项  |
|---------|---------|---------|
|[数据迁移助手 (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | - 迁移单一数据库（架构和数据）。  </br> - 可在数据迁移过程中适应停机。 </br> </br> 受支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM | - 迁移活动执行数据库对象（从源到目标）之间的数据移动，因此建议在非高峰时间运行。 </br> - DMA 报告每个数据库对象的迁移状态，包括迁移的行数。  </br> - 对于大型迁移（数据库数量/数据库大小），请使用下面列出的 Azure 数据库迁移服务。|
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| - 迁移单一数据库或大规模迁移。 </br> - 可在迁移过程中适应停机。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM | - 可通过 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md) 自动执行大规模迁移。 </br> - 完成迁移的时间取决于数据库的大小和数据库中的对象数。 </br> - 需要源数据库设置为只读。 |
| | | |

### <a name="alternative-options"></a>替代选项

下表比较了替代迁移选项： 

|方法/技术 |何时使用    |注意事项  |
|---------|---------|---------|
|[事务复制](../../database/replication-to-sql-database.md)| - 通过不断地将源数据库表中的更改发布到目标 SQL 数据库表进行迁移。 </br> - 所选表（数据库的子集）的全部或部分数据库迁移。  </br> </br> 受支持的源： </br> - [SQL Server (2016 - 2019)，存在一些限制](/sql/relational-databases/replication/replication-backward-compatibility) </br> - AWS EC2  </br> - GCP 计算 SQL Server VM  | - 与其他迁移选项相比，设置相对复杂。   </br> - 提供连续复制选项以迁移数据（无需使数据库脱机）。  </br> - 在源 SQL Server 上设置发布服务器时，关于事务的复制，有一些限制需要考虑。 有关详细信息，请参阅[对发布对象的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)。 </br>- 可以[监视复制活动](/sql/relational-databases/replication/monitor/monitoring-replication)。    |
|[导入导出服务/BACPAC](../../database/database-import.md)| - 迁移单个业务线应用程序数据库。 </br>- 适用于较小的数据库。  </br>  - 不需要单独的迁移服务或工具。 </br> </br> 受支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM  |  - 需要停机，因为数据需要在源处导出并在目标处导入。   </br> - 导出/导入中使用的文件格式和数据类型需要与表架构一致，以避免“截断”/“数据类型不匹配”错误。  </br> - 导出包含大量对象的数据库所花费的时间可能会大大增加。       |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 迁移全部或部分数据迁移。 </br> - 可以适应停机。 </br> </br> 支持的源： </br> - SQL Server (2005 - 2019) 本地或 Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP 计算 SQL Server VM   | - 从源导出数据并导入目标时需要停机时间。 </br> - 导出/导入中使用的文件格式和数据类型需与表架构一致。 |
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-database.md)| - 从源 SQL Server 数据库迁移和/或转换数据。 </br> - 将数据从多个数据源合并到 Azure SQL 数据库，通常用于商业智能 (BI) 工作负载。  |  - 需要在 ADF 中创建数据移动管道，以将数据从源移动到目标。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)是一个重要的考虑因素，与管道触发器、活动运行、数据移动的持续时间等有关。 |
|[SQL 数据同步](../../database/sql-data-sync-data-sql-server-sql-database.md)| - 在源数据库和目标数据库之间同步数据。</br> - 适合在 Azure SQL 数据库和本地 SQL Server 之间以双向流运行连续同步。 | - Azure SQL 数据库必须是中心数据库，以与作为成员数据库的本地 SQL Server 数据库同步。</br> - 与事务复制相比，SQL 数据同步支持在本地和 Azure SQL 数据库之间进行双向数据同步。 </br> - 可能会对性能有更高的影响，具体取决于工作负载。|
| | | |

## <a name="feature-interoperability"></a>功能互操作性 

如果迁移的工作负载还依赖其他 SQL Server 功能，则还有其他注意事项。

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
通过将 SQL Server Integration Services (SSIS) 包重新部署到 [Azure 数据工厂](../../../data-factory/introduction.md)中的 Azure SSIS 运行时，将包迁移到 Azure。 Azure 数据工厂通过提供用于在 Azure 中执行 SSIS 包的运行时来[支持 SSIS 包的迁移](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination)。 此外，还可以使用[数据流](../../../data-factory/concepts-data-flow-overview.md)在 ADF 中以本机方式重写 SSIS ETL 逻辑。


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
将 SQL Server Reporting Services (SSRS) 报表迁移到 Power BI 中的分页报表。 使用  [RDL 迁移工具](https://github.com/microsoft/RdlMigration)来帮助准备和迁移报表。 此工具由 Microsoft 开发，可帮助客户将 RDL 报表从其 SSRS 服务器迁移到 Power BI。 它可在 GitHub 上使用，并记录迁移方案的端到端演练。 

#### <a name="high-availability"></a>高可用性
由于[常规用途（标准可用性模型）](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)和[业务关键（高级可用性模型）](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL 数据库中都已内置高可用性体系结构，因此在目标 Azure SQL 数据库上手动设置 SQL Server 高可用性功能（如 Always On 故障转移群集实例和 Always On 可用性组）已过时。 业务关键/高级服务层级还提供读取扩展，该扩展允许连接到其中一个辅助节点以便只读。 

除了 SQL 数据库中包含的高可用性体系结构之外，还有[自动故障转移组](../../database/auto-failover-group-overview.md)功能，使你可以管理托管实例中数据库到另一个区域的复制和故障转移。 

#### <a name="sql-agent-jobs"></a>SQL 代理作业
Azure SQL 数据库不会直接支持 SQL 代理作业，需要部署到[弹性数据库作业（预览版）](../../database/job-automation-overview.md)。

#### <a name="logins-and-groups"></a>登录名和组
在脱机模式下，使用数据库迁移服务 (DMS) 将 SQL 登录名从源 SQL Server 移动到 Azure SQL 数据库。  使用“迁移向导”中的“已选择登录名”边栏选项卡将登录名迁移到目标 SQL 数据库 。 

Windows 用户和组也可以通过在“DMS 配置”页中启用相应的切换按钮来使用 DMS 进行迁移。 

或者，还可以使用 Microsoft 数据迁移架构师专门设计的 [PowerShell 实用程序工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)。 该实用程序使用 PowerShell 创建 Transact-SQL (T-SQL) 脚本来重新创建登录名，并从源到目标选择数据库用户。 该工具自动将 Windows AD 帐户映射到 Azure AD 帐户，并且可以针对源 Active Directory 的每个登录名执行 UPN 查找。 该工具脚本自定义服务器和数据库角色，以及角色成员身份、数据库角色和用户权限。 目前不支持所包含的数据库，并且仅对一部分可能的 SQL Server 权限进行脚本编写。 


#### <a name="system-databases"></a>系统数据库
对于 Azure SQL 数据库，唯一适用的系统数据库为 [master](/sql/relational-databases/databases/master-database) 和 tempdb。 若要了解详细信息，请参阅 [Azure SQL 数据库中的 Tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。

## <a name="leverage-advanced-features"></a>利用高级功能 

请确保利用 SQL 数据库提供的基于云的高级功能。 例如，你不再需要担心管理备份的问题，因为服务会进行管理。 你可以还原到[保留期内的任何时间点](../../database/recovery-using-backups.md#point-in-time-restore)。 

要增强安全性，请考虑使用  [Azure Active Directory 身份验证](../../database/authentication-aad-overview.md)、[审核](../../database/auditing-overview.md)、 [威胁检测](../../database/azure-defender-for-sql.md)、 [行级别安全性](/sql/relational-databases/security/row-level-security)和 [动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)。

除了高级管理和安全功能以外，SQL 数据库还提供一组高级工具来帮助你[监视和优化工作负载](../../database/monitor-tune-overview.md)。 [Azure SQL Analytics（预览）](../../../azure-monitor/insights/azure-sql.md)是一种高级云监视解决方案，用于在单个视图中跨多个订阅大规模监视 Azure SQL 数据库中的所有数据库的性能。 Azure SQL Analytics 使用内置的智能收集和直观显示关键性能指标，以进行性能故障排除。

[自动优化](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) 会持续监视 SQL 执行计划统计信息的性能，并自动修复已识别的性能问题。 


## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的。

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[DBLoader 实用工具](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader 可用于将带分隔符的文本文件中的数据加载到 SQL Server 中。 此 Windows 控制台实用工具使用 SQL Server 本机客户端 bulkload 接口，该接口可用于所有版本的 SQL Server（包括 Azure SQL 数据库）。|
|[使用 PowerShell 批量创建数据库](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|这包括一组三个 PowerShell 脚本，可用于创建资源组 (create_rg.ps1)、[Azure 中的逻辑服务器](../../database/logical-servers.md) (create_sqlserver.ps1) 和 Azure SQL 数据库 (create_sqldb.ps1)。 这些脚本包括循环功能，因此你可以根据需要循环访问和创建任意数量的服务器和数据库。|
|[使用 MSSQL-Scripter 和 PowerShell 批量部署架构](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|此资产在 Azure 中创建一个资源组、一个或多个[逻辑服务器](../../database/logical-servers.md)来承载 Azure SQL 数据库，从一个本地 SQL Server（或多个 SQL Server (2005+)）导出每个架构，并将其导入 Azure SQL 数据库。|
|[将 SQL Server 代理作业转换为弹性数据库作业](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|此脚本将源 SQL Server 代理作业迁移到弹性数据库作业。|
|[从 Azure SQL 数据库发送邮件](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|这提供了一个解决方案，可作为本地 SQL Server 中可用的 SendMail 功能的替代方案。 该解决方案使用 Azure Functions 和 Azure SendGrid 服务从 Azure SQL 数据库发送电子邮件。|
|[用于将本地 SQL Server 登录名移动到 Azure SQL 数据库的实用工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|一个 PowerShell 脚本，可创建 T-SQL 命令脚本以重新创建登录名并从本地 SQL Server 到 Azure SQL 数据库选择数据库用户。 该工具允许将 Windows AD 帐户自动映射到 Azure AD 帐户，还可以选择性地迁移 SQL Server 本机登录名。|
|[使用 Logman 的 PerfMon 数据收集自动化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|一种工具，用于收集 PerMon 数据以了解基线性能，并帮助提供迁移目标建议。 该工具使用 logman.exe 创建将创建、启动、停止和删除在远程 SQL Server 上设置的性能计数器的命令|
|[白皮书 - 使用 BACPAC 将数据库迁移到 Azure SQL DB](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|此白皮书提供了一些指导和步骤，可帮助你使用 BACPAC 文件加快从 SQL Server 到 Azure SQL 数据库的迁移。|

这些资源是作为 Data SQL Ninja 计划的一部分开发的，该计划由 Azure 数据组工程团队提供赞助。 Data SQL Ninja 计划的核心宗旨是解锁和加速复杂的现代化进程，并争取数据平台向 Microsoft Azure 数据平台迁移的机会。 如果你认为贵组织有意参与 Data SQL Ninja 计划，请联系帐户团队并让他们提交提名。


## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 迁移到 Azure SQL 数据库，请参阅 [SQL Server 到 SQL 托管实例迁移指南](sql-server-to-sql-database-guide.md)。

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要了解有关 SQL 数据库的详细信息，请参阅：
   - [Azure SQL 数据库概述](../../database/sql-database-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 

- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。