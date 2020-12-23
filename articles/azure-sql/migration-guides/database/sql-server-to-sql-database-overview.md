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
ms.openlocfilehash: 2d668775e8d11faa0b2913cebc41e5217b49c278
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357310"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>迁移概述： SQL 数据库 SQL Server
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

了解将 SQL Server 迁移到 Azure SQL 数据库的不同迁移选项和注意事项。 

你可以迁移 SQL Server 本地或上运行的： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS)  
- 计算引擎 (Google Cloud Platform GCP)   
- 适用于 SQL Server (Google Cloud Platform 的云 SQL – GCP)  

对于其他方案，请参阅 [数据库迁移指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概述

对于需要完全托管的平台即服务 (PaaS) 的 SQL Server 工作负荷， [AZURE SQL 数据库](../../database/sql-database-paas-overview.md)是推荐的目标选项。 SQL 数据库将处理大多数数据库管理功能，并提供高可用性、智能查询处理、可伸缩性和性能功能，以适合多种不同的应用程序类型。 

SQL 数据库提供了多个 [部署模型](../../database/sql-database-paas-overview.md#deployment-models) 和可满足不同类型的应用程序或工作负荷的 [服务层](../../database/service-tiers-vcore.md#service-tiers) 的灵活性。

迁移到 SQL 数据库的主要好处之一是，你可以利用 PaaS 功能实现应用程序的现代化，并消除在实例级别（例如 SQL 代理作业）范围内的技术组件的任何依赖关系。

你还可以将 SQL Server 的本地许可证迁移到 Azure SQL 数据库，方法是使用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/) 以便 SQL Server 应选择 [基于 vCore 的购买模型](../../database/service-tiers-vcore.md)。

本指南旨在阐明在准备将 SQL Server 数据库迁移到 Azure SQL 数据库时的迁移选项和注意事项。  

## <a name="considerations"></a>注意事项 

评估迁移选项时要考虑的关键因素取决于： 

- 服务器和数据库的数量
- 数据库大小
- 迁移过程中可接受的业务停机时间 

本指南中列出的迁移选项考虑到这些因素。 对于到 Azure SQL 数据库的逻辑数据迁移，要迁移的时间可能取决于数据库中对象的数量和数据库的大小。 

不同的工具可用于不同的工作负荷和用户首选项。 某些工具可用于使用基于 UI 的工具快速迁移单个数据库，而其他工具可迁移多个数据库，这些数据库可自动处理大规模的迁移。 

## <a name="choose-appropriate-target"></a>选择适当的目标

请考虑一般准则，以帮助你选择正确的部署模型和 Azure SQL 数据库的服务层。 你可以在部署过程中选择计算和存储资源，然后使用  [Azure 门户](../../database/scale-resources.md)  以后再对其进行更改，而不会导致应用程序停机。


**部署模型**：了解应用程序工作负载和使用模式，以决定单个数据库或弹性池。 

- [单个数据库](../../database/single-database-overview.md)代表适用于大多数现代云应用程序和微服务的完全托管的数据库。
- [弹性池](../../database/elastic-pool-overview.md)是单个数据库的集合，其中包含一组共享资源（例如 CPU 或内存），适用于将池中的数据库与可预测的使用模式组合在一起，从而可以有效地共享同一组资源。

**采购模型**：在 VCORE、DTU 或无服务器购买模型之间进行选择。 

- [VCore 模型](../../database/service-tiers-vcore.md)允许选择 Azure SQL 数据库的 vcore 数量，使其成为从本地 SQL Server 翻译时最简单的选择。 这是支持 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)的许可成本节省的唯一选项。 
- [DTU 模型](../../database/service-tiers-dtu.md)将对基础计算、内存和 IO 资源进行抽象，以提供混合的 DTU。 
- [无服务器模型](../../database/serverless-tier-overview.md)适用于需要自动按需缩放的工作负荷，以及按使用情况计费的计算资源。 无服务器计算层会在非活动期间自动暂停数据库 (仅对存储计费) ，并在活动返回时自动恢复数据库。 

**服务层**：在为不同类型的应用程序设计的三个服务层之间进行选择。

- [常规用途/标准服务层](../../database/service-tier-general-purpose.md) 提供了一个面向预算的平衡选项，该选项适用于交付低层应用程序的计算和存储，并在存储层内置了冗余以从故障中恢复。 适用于大多数数据库工作负荷。 
- [业务关键/高级服务层](../../database/service-tier-business-critical.md) 适用于需要高事务速率、低延迟 IO 和高级别复原功能的高层应用程序，这些应用程序可用于故障转移并用于卸载读取工作负荷。
- [超大规模服务层](../../database/service-tier-hyperscale.md) 适用于已增大数据量并且需要自动扩展到 100 TB 数据库大小的数据库。 专为超大型数据库设计。 

> [!IMPORTANT]
> [事务日志速率受](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) Azure SQL 数据库限制，以限制高引入率。 因此，在迁移过程中，可能需要扩展目标数据库资源 (Vcore/Dtu) ，以减轻 CPU 或吞吐量的压力。 选择适当大小的目标数据库，但计划在必要时为迁移扩展资源。 


### <a name="sql-server-on-azure-vm-alternative"></a>SQL Server Azure VM 替代项

你的企业可能要求将 [Azure 虚拟机上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 设置为比 Azure SQL 数据库更适合的目标。 

如果以下情况适用于你的业务，请考虑改为转到 Azure VM 上的 SQL Server： 

- 如果需要对操作系统或文件系统的直接访问权限，如使用 SQL Server 在同一虚拟机上安装第三方或自定义代理。 
- 严重依赖于目前尚不支持的功能，如 FileStream/FileTable、PolyBase 和跨实例事务。 
- 绝对需要保持使用特定的 SQL Server 版本（如 2012）。 
- 计算要求比托管实例的要求低得多（如只需一个 vCore），且数据库整合不可接受。 


## <a name="migration-tools"></a>迁移工具 

建议用于迁移的工具是数据迁移助手和 Azure 数据库迁移服务。 还可以使用其他其他迁移选项。 

### <a name="recommended-tools"></a>建议的工具

下表列出了建议的迁移工具： 

|技术 | 描述|
|---------|---------|
|[数据迁移助手 (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|数据迁移助手是一种桌面工具，提供 (架构和数据) 的 SQL Server 和迁移到 Azure SQL 数据库的无缝评估。 此工具可以安装在本地服务器上，也可以安装在可以连接到源数据库的本地计算机上。 迁移过程是在源数据库和目标数据库中的对象之间进行的逻辑数据移动。 </br> - (架构和数据迁移单个数据库) |
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|第一方 Azure 服务，可使用 Azure 门户将 SQL Server 数据库迁移到 Azure SQL 数据库，或通过 PowerShell 自动迁移。 Azure DMS 要求在预配过程中选择首选的 Azure 虚拟网络 (VNet) ，以确保与源 SQL Server 数据库建立连接。 </br> -迁移单一数据库或大规模迁移。 |
| | |


### <a name="alternative-tools"></a>替代工具

下表列出了其他迁移工具： 

|技术 |描述  |
|---------|---------|
|[事务复制](../../database/replication-to-sql-database.md)|在保持事务一致性的同时，通过提供发布服务器类型迁移选项，将数据从源 SQL Server 数据库表 (s) 复制到 SQL 数据库。 在发布服务器上发生的增量数据更改将传播到订阅服务器。|
|[导入导出服务/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是一个 Windows 文件，其 `.bacpac` 扩展名封装了数据库的架构和数据。 BACPAC 可用于从源 SQL Server 导出数据，并将数据导入 Azure SQL 数据库。 可以使用 Azure 门户将 BACPAC 文件导入到新的 Azure SQL 数据库。 </br></br> 对于包含大型数据库大小或大量数据库的规模和性能，应考虑使用 [SqlPackage](../../database/database-import.md#using-sqlpackage) 命令行实用工具导出和导入数据库。|
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[大容量复制程序 (bcp) 实用工具](/sql/tools/bcp-utility)将数据从 SQL Server 实例复制到数据文件中。 使用 BCP 实用工具从源导出数据，并将数据文件导入到目标 SQL 数据库。 </br></br> 对于将数据移至 Azure SQL 数据库的高速大容量复制操作， [智能大容量复制工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) 可用于通过利用并行复制任务最大限度地提高传输速度。|
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-database.md)|Azure 数据工厂中的 [复制活动](../../../data-factory/copy-activity-overview.md) 将数据从源 SQL Server 数据库 (s) 迁移到使用内置连接器和 [Integration Runtime](../../../data-factory/concepts-integration-runtime.md)的 SQL 数据库。</br> </br> ADF 支持多种 [连接器](../../../data-factory/connector-overview.md) ，可将数据从 SQL Server 源移到 SQL 数据库。|
|[SQL 数据同步](../../database/sql-data-sync-data-sql-server-sql-database.md)|使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨多个本地和云端数据库双向同步选定数据。</br>如果需要跨 Azure SQL 数据库或 SQL Server 中的多个数据库使数据保持最新，数据同步非常有用。|
| | |

## <a name="compare-migration-options"></a>比较迁移选项

比较迁移选项，选择适合你的业务需求的路径。 

### <a name="recommended-options"></a>推荐选项

下表比较了建议的迁移选项： 

|迁移选项  |何时使用  |注意事项  |
|---------|---------|---------|
|[数据迁移助手 (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | -迁移 (架构和数据) 的单一数据库。  </br> -可以在数据迁移过程中适应停机时间。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM | -迁移活动在 (从源到目标) 的数据库对象之间执行数据移动，因此建议在非高峰时间运行。 </br> -DMA 报告每个数据库对象的迁移状态，包括迁移的行数。  </br> -对于大型迁移 (数据库) 数量/数据库的大小，请使用下面列出的 Azure 数据库迁移服务。|
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| -迁移单一数据库或大规模迁移。 </br> -可在迁移过程中适应停机时间。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM | -可通过 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)自动完成大规模迁移。 </br> 完成迁移的时间取决于数据库大小和数据库中的对象数。 </br> -要求源数据库设置为只读。 |
| | | |

### <a name="alternative-options"></a>替代选项

下表比较了其他迁移选项： 

|方法/技术 |何时使用    |注意事项  |
|---------|---------|---------|
|[事务复制](../../database/replication-to-sql-database.md)| -通过将源数据库表中的更改连续发布到目标 SQL 数据库表来进行迁移。 </br> - (数据库) 子集的选定表的完全或部分数据库迁移。  </br> </br> 支持的源： </br> - [SQL Server (2016-2019) ，但有一些限制](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP 计算 SQL Server VM  | 与其他迁移选项相比，安装相对比较复杂。   </br> -提供一个连续复制选项，用于在不使数据库脱机) 的情况下迁移数据 (。  </br> -在源 SQL Server 上设置发布服务器时，事务复制有许多需要考虑的限制。 有关详细信息，请参阅 [发布对象的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) 。 </br>-可以 [监视复制活动](/sql/relational-databases/replication/monitor/monitoring-replication)。    |
|[导入导出服务/BACPAC](../../database/database-import.md)| -迁移单独的业务线应用程序数据库。 </br>-适用于较小的数据库。  </br>  -不需要单独的迁移服务或工具。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM  |  -需要停机，因为需要在源位置导出数据，并在目标上导入数据。   </br> -导出/导入中使用的文件格式和数据类型需要与表架构一致，以避免截断/数据类型不匹配错误。  </br> -导出具有大量对象的数据库所花费的时间可能会显著提高。       |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -迁移完全或部分数据迁移。 </br> -可适应停机时间。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM   | -需要停机，以便从源导出数据并导入到目标。 </br> -导出/导入中使用的文件格式和数据类型需要与表架构一致。 |
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-database.md)| -迁移和/或转换源 SQL Server 数据库中的数据。 </br> -将数据从多个数据源合并到 Azure SQL Database 通常用于商业智能 (BI) 工作负荷。  |  -要求在 ADF 中创建数据移动管道，以将数据从源移动到目标。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 是一个重要的考虑因素，基于管道触发器、活动运行、数据移动持续时间等。 |
|[SQL 数据同步](../../database/sql-data-sync-data-sql-server-sql-database.md)| -在源数据库和目标数据库之间同步数据。</br> -适用于在双向流中的 Azure SQL 数据库和本地 SQL Server 之间运行连续同步。 | -Azure SQL 数据库必须是用于与本地 SQL Server 数据库作为成员数据库进行同步的中心数据库。</br> -与事务复制相比，SQL 数据同步支持在本地和 Azure SQL 数据库之间进行双向数据同步。 </br> -对于性能的影响可能会更高，具体取决于工作负荷。|
| | | |

## <a name="feature-interoperability"></a>功能互操作性 

迁移依赖于其他 SQL Server 功能的工作负荷时，还有其他注意事项。

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
通过在 [Azure 数据工厂](../../../data-factory/introduction.md)中将包重新部署到 AZURE-ssis 运行时，将 SQL SERVER INTEGRATION SERVICES (SSIS) 包迁移到 azure。 Azure 数据工厂提供了一个生成用于在 Azure 中执行 SSIS 包的运行时， [支持迁移 SSIS 包](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) 。 此外，还可以使用 [数据流](../../../data-factory/concepts-data-flow-overview.md)在 ADF 中以本机方式重写 SSIS ETL 逻辑。


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
将 SQL Server Reporting Services (SSRS) 报表迁移到 Power BI 中的分页报表。 使用 [RDL 迁移工具](https://github.com/microsoft/RdlMigration) 帮助准备和迁移报表。 此工具由 Microsoft 开发，可帮助客户将 RDL 报表从其 SSRS 服务器迁移到 Power BI。 它在 GitHub 上可用，并记录了迁移方案的端到端演练。 

#### <a name="high-availability"></a>高可用性
手动设置 SQL Server 高可用性功能，如 Always On 故障转移群集实例和 Always On 可用性组在目标 Azure SQL 数据库上过时，因为高可用性体系结构已内置 [常规用途 (标准可用性模型) ](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 和业务关键 ([高级可用性模型 ](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)) SQL 数据库。 "业务关键"/"高级" 服务层还提供读取横向扩展，允许连接到某个辅助节点进行只读。 

除了 SQL 数据库中包含的高可用性体系结构外，还提供了 [自动故障转移组](../../database/auto-failover-group-overview.md) 功能，可用于管理托管实例中数据库到另一个区域的复制和故障转移。 

#### <a name="sql-agent-jobs"></a>SQL 代理作业
SQL 代理作业不会直接在 Azure SQL 数据库中受支持，需要将其部署到 [弹性数据库作业 (预览) ](../../database/job-automation-overview.md#elastic-database-jobs-preview)。

#### <a name="logins-and-groups"></a>登录名和组
使用数据库迁移服务将 SQL 登录名从源 SQL Server 移到 Azure SQL 数据库， (DMS) 处于脱机模式。  使用 **迁移向导** 中的 "**所选登录名**" 边栏选项卡将登录名迁移到目标 SQL 数据库。 

Windows 用户和组也可以通过在 "DMS 配置" 页中启用相应的切换按钮来使用 DMS 进行迁移。 

或者，你可以使用 Microsoft 数据迁移架构师专门设计的 [PowerShell 实用程序工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) 。 实用工具使用 PowerShell 创建 Transact-sql (T-sql) 脚本，以重新创建登录名，并从源到目标选择数据库用户。 该工具会自动将 Windows AD 帐户映射到 Azure AD 帐户，并可针对源 Active Directory 针对每个登录执行 UPN 查找。 该工具为自定义服务器和数据库角色以及角色成员身份、数据库角色和用户权限编写脚本。 目前尚不支持包含的数据库，并且仅编写了一小部分可能的 SQL Server 权限。 


#### <a name="system-databases"></a>系统数据库
对于 Azure SQL 数据库，唯一适用的系统数据库为 [master](/sql/relational-databases/databases/master-database) 和 tempdb。 若要了解详细信息，请参阅 [AZURE SQL 数据库中的 Tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。

## <a name="leverage-advanced-features"></a>利用高级功能 

请确保利用 SQL 数据库提供的基于云的高级功能。 例如，你不再需要担心如何管理备份，因为服务会为你执行此功能。 你可以还原到 [保留期内的任意时间点](../../database/recovery-using-backups.md#point-in-time-restore)。 

若要增强安全性，请考虑使用 [Azure Active Directory 身份验证](../../database/authentication-aad-overview.md)、 [审核](../../database/auditing-overview.md)、 [威胁检测](../../database/azure-defender-for-sql.md)、 [行级别安全性](/sql/relational-databases/security/row-level-security)和 [动态数据屏蔽](/sql/relational-databases/security/dynamic-data-masking)。

除了高级管理和安全功能，SQL 数据库还提供了一组可帮助你 [监视和优化工作负荷](../../database/monitor-tune-overview.md)的高级工具。 [Azure SQL Analytics (预览版) ](../../../azure-monitor/insights/azure-sql.md) 是一种高级云监视解决方案，用于在单个视图中大规模和跨多个订阅监视 Azure SQL 数据库中的所有数据库的性能。 Azure SQL Analytics 收集和直观显示关键性能指标，并提供内置的智能性能故障排除。

[自动优化](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)  持续监视 SQL 执行计划统计信息的性能，并自动修复已识别的性能问题。 


## <a name="migration-assets"></a>迁移资产 

有关更多帮助，请参阅为实际迁移项目开发的以下资源。

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报告生成，有助于通过提供自动化和统一的目标平台决策过程来加快大型房地产评估。|
|[DBLoader 实用程序](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|可以使用 DBLoader 将分隔文本文件中的数据加载到 SQL Server 中。 此 Windows 控制台实用工具使用 SQL Server native client bulkload 接口，该接口适用于所有版本的 SQL Server，包括 Azure SQL 数据库。|
|[通过 PowerShell 创建大容量数据库](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|这包括一组三个 PowerShell 脚本，可用于创建资源组 ( # A0) 、Azure ( # A1) [中的逻辑服务器](../../database/logical-servers.md) ，以及 Azure SQL 数据库 ( # A2) 。 脚本包括循环功能，以便可以根据需要循环访问和创建任意数量的服务器和数据库。|
|[利用 MSSQL-Scripter & PowerShell 进行大容量架构部署](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|此资产在 Azure 中创建一个资源组、一个或多个 [逻辑服务器](../../database/logical-servers.md) 来托管 Azure SQL 数据库，从本地 SQL Server (或多个 SQL server (2005 +) 导出每个架构，并将其导入 Azure SQL 数据库。|
|[将 SQL Server 代理作业转换为弹性数据库作业](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|此脚本将源 SQL Server 代理作业迁移到弹性数据库作业。|
|[从 Azure SQL 数据库发送邮件](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|这提供了一种解决方案作为本地 SQL Server 中提供的 SendMail 功能的替代方案。 解决方案使用 Azure Functions 和 Azure SendGrid 服务从 Azure SQL Database 发送电子邮件。|
|[用于将本地 SQL Server 登录移到 Azure SQL 数据库的实用工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|一个 PowerShell 脚本，它创建一个 T-sql 命令脚本来重新创建登录名，并选择 "数据库用户" 从本地 SQL Server 到 Azure SQL 数据库。 该工具允许将 Windows AD 帐户自动映射到 Azure AD 帐户，还可以选择迁移 SQL Server 本机登录名。|
|[使用 Logman 的 PerfMon 数据收集自动化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|一种工具，该工具收集 PerMon 数据以了解基线性能，并帮助迁移目标建议。 此工具使用 logman.exe 创建命令，该命令将创建、启动、停止和删除在远程 SQL Server 上设置的性能计数器|
|[白皮书-使用 BACPAC 将数据库迁移到 Azure SQL DB](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|本白皮书提供了一些指导和步骤，可帮助使用 BACPAC 文件加快从 SQL Server 到 Azure SQL 数据库的迁移。|

这些资源是作为 Data SQL Ninja 计划的一部分开发的，该计划由 Azure 数据组工程团队提供赞助。 Data SQL Ninja 计划的核心宗旨是解锁和加速复杂的现代化进程，并争取数据平台向 Microsoft Azure 数据平台迁移的机会。 如果你认为贵组织有意参与 Data SQL Ninja 计划，请联系帐户团队并让他们提交提名。


## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 迁移到 Azure SQL Database，请参阅 [SQL 数据库迁移指南 SQL Server](sql-server-to-sql-database-guide.md)。

- 有关可用于帮助你完成各种数据库和数据迁移方案以及专业任务的 Microsoft 和第三方服务和工具的矩阵，请参阅 [数据迁移的服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要了解有关 SQL 数据库的详细信息，请参阅：
   - [Azure SQL Database 概述](../../database/sql-database-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 

- 若要了解有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [成本调整和大小调整工作负荷迁移到 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要评估应用程序访问层，请参阅 [数据访问迁移工具包 (预览) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 有关如何执行数据访问层 A/B 测试的详细信息，请参阅 [数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。