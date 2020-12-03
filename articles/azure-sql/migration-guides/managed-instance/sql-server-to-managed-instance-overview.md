---
title: SQL Server SQL 托管实例-迁移概述
description: 了解可用于将 SQL Server 数据库迁移到 Azure SQL 托管实例的各种工具和选项。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 878d59445dde839ca9e702ac0c49af676e48a42f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531184"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>迁移概述： SQL 托管实例 SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

了解将 SQL Server 迁移到 Azure SQL 托管实例的不同迁移选项和注意事项。 

你可以迁移 SQL Server 本地或上运行的： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS)  
- 计算引擎 (Google Cloud Platform GCP)   
- 适用于 SQL Server (Google Cloud Platform 的云 SQL – GCP)  

对于其他方案，请参阅 [数据库迁移指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概述

对于需要完全托管的服务而无需管理虚拟机或其操作系统的 SQL Server 工作负荷， [AZURE SQL 托管实例](../../managed-instance/sql-managed-instance-paas-overview.md)是推荐的目标选项。 SQL 托管实例使你能够将你的本地应用程序直接迁移到 Azure，只需进行最少的应用程序或数据库更改，同时可以通过本机虚拟网络 (VNet) 支持来实现实例的完全隔离。 

## <a name="considerations"></a>注意事项 

评估迁移选项时要考虑的关键因素取决于： 
- 服务器和数据库的数量
- 数据库大小
- 迁移过程中可接受的业务停机时间 

将 SQL Server 迁移到 SQL 托管实例的主要好处之一是，可以选择迁移整个实例，也可以只迁移单个数据库的子集。 请仔细计划在迁移过程中包括以下各项： 
- 需要在同一实例上定位的所有数据库 
- 应用程序所需的实例级对象，包括登录名、凭据、SQL 代理作业和运算符以及服务器级触发器。 

> [!NOTE]
> 即使在关键情况下，Azure SQL 托管实例仍可保证99.99% 的可用性，因此无法禁用 SQL MI 中某些功能导致的开销。 有关详细信息，请参阅 [可能导致 SQL Server 和 AZURE SQL 托管实例博客上的性能不同的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 。 


## <a name="choose-appropriate-target"></a>选择适当的目标

一些常规指导原则可帮助你选择适当的服务层和 SQL 托管实例的特性，以帮助匹配 [性能基线](sql-server-to-managed-instance-performance-baseline.md)： 

- 使用 "CPU 使用率" 基线来预配与 SQL Server 实例使用的内核数相匹配的托管实例。 可能需要对资源进行缩放以匹配 [硬件生成特征](../../managed-instance/resource-limits.md#hardware-generation-characteristics)。 
- 使用 "内存使用率" 基线选择与内存分配适当匹配的 [vCore 选项](../../managed-instance/resource-limits.md#service-tier-characteristics) 。 
- 使用文件子系统的基线 IO 延迟，以选择大于 5 ms) 常规用途 (滞后时间，并业务关键低于 3 ms (服务层的) 滞后时间。 
- 使用基线吞吐量来预分配数据和日志文件的大小，以实现预期的 IO 性能。 

可以在部署期间选择计算和存储资源，然后在使用 [Azure 门户](../../database/scale-resources.md) 后更改它们，而不会导致应用程序停机。 

> [!IMPORTANT]
> 托管实例中的任何差异 [虚拟网络要求](../../managed-instance/connectivity-architecture-overview.md#network-requirements) 会阻止你创建新的实例或使用现有的实例。 了解有关 [创建新](../../managed-instance/virtual-network-subnet-create-arm-template.md)   网络和 [配置现有](../../managed-instance/vnet-existing-add-subnet.md?branch=release-ignite-arc-data)   网络的详细信息。 

### <a name="sql-server-vm-alternative"></a>SQL Server VM 替代项

你的企业可能要求在 Azure Vm 上使 SQL Server 比 Azure SQL 托管实例更适合。 

如果以下情况适用于你的业务，请考虑改为转到 SQL Server VM： 

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
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | 第一方 Azure 服务支持在脱机模式下进行迁移，适用于在迁移过程中可能会承受停机时间的应用程序。 与在联机模式下的连续迁移不同的是，脱机模式迁移对源和目标运行一次完整数据库备份。 | 
|[本机备份和还原](../../managed-instance/restore-sample-database-quickstart.md) | SQL 托管实例支持将本机 SQL Server 数据库备份还原 ( .bak 文件) ，使其成为可向 Azure 存储提供完整数据库备份的客户的最简单迁移选项。 本文后面的 " [迁移资产" 部分](#migration-assets) 还支持完整备份和差异备份。| 
| | |

### <a name="alternative-tools"></a>替代工具

下表列出了其他迁移工具： 

|技术 |描述  |
|---------|---------|
|[事务复制](../../managed-instance/replication-transactional-overview.md) | 在保持事务一致性的同时，通过提供发布服务器类型迁移选项，将数据从源 SQL Server 数据库表 (s) 复制到 SQL 托管实例。 |  |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [大容量复制程序 (bcp) 实用工具](/sql/tools/bcp-utility)将数据从 SQL Server 实例复制到数据文件中。 使用 BCP 实用工具从源导出数据，并将数据文件导入到目标 SQL 托管实例中。</br></br> 对于将数据移至 Azure SQL 数据库的高速大容量复制操作， [智能大容量复制工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) 可用于通过利用并行复制任务最大限度地提高传输速度。 | 
|[导入导出向导/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是一个 Windows 文件，其 `.bacpac` 扩展名封装了数据库的架构和数据。 BACPAC 可用于从源 SQL Server 导出数据，并将文件导入回 Azure SQL 托管实例。  |  
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| Azure 数据工厂中的 [复制活动](../../../data-factory/copy-activity-overview.md) 将数据从源 SQL Server 数据库 (s) 迁移到使用内置连接器和 [Integration Runtime](../../../data-factory/concepts-integration-runtime.md)的 SQL 托管实例。</br> </br> ADF 支持多种 [连接器](../../../data-factory/connector-overview.md) ，可将数据从 SQL Server 源移到 SQL 托管实例。 |
| | |

## <a name="compare-migration-options"></a>比较迁移选项

比较迁移选项，选择适合你的业务需求的路径。 

### <a name="recommended-options"></a>推荐选项

下表比较了建议的迁移选项： 

|迁移选项  |何时使用  |注意事项  |
|---------|---------|---------|
|[Azure 数据库迁移服务 (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | -大规模迁移单个数据库或多个数据库。 </br> -可在迁移过程中适应停机时间。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM |  -可通过 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md)自动完成大规模迁移。 </br> -完成迁移的时间取决于数据库大小，并受备份和还原时间的影响。 </br> -可能需要足够的停机时间。 |
|[本机备份和还原](../../managed-instance/restore-sample-database-quickstart.md) | -)  (迁移单个业务线应用程序数据库。  </br> -无需单独的迁移服务或工具就能快速轻松地进行迁移。  </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM | -数据库备份使用多个线程来优化到 Azure Blob 存储的数据传输，但 ISV 带宽和数据库大小可能影响传输速率。 </br> -停机时间应能容纳执行完整备份和还原所需的时间 (这是) 的数据操作的大小。| 
| | | |

### <a name="alternative-options"></a>替代选项

下表比较了其他迁移选项： 

|方法/技术 |何时使用 |注意事项  |
|---------|---------|---------|
|[事务复制](../../managed-instance/replication-transactional-overview.md) | -通过将源数据库表中的更改连续发布到目标 SQL 托管实例数据库表来进行迁移。 </br> - (数据库) 子集的选定表的完全或部分数据库迁移。  </br> </br> 支持的源： </br> -SQL Server (2012-2019) ，但有一些限制 </br> -AWS EC2  </br> -GCP 计算 SQL Server VM | </br> 与其他迁移选项相比，安装相对比较复杂。   </br> -提供一个连续复制选项，用于在不使数据库脱机) 的情况下迁移数据 (。</br> -在源 SQL Server 上设置发布服务器时，事务复制有许多需要考虑的限制。 有关详细信息，请参阅 [发布对象的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) 。  </br> -可使用 [监视复制活动](/sql/relational-databases/replication/monitor/monitoring-replication) 的功能。    |
|[大容量复制](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -迁移完全或部分数据迁移。 </br> -可适应停机时间。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM   | -需要停机，以便从源导出数据并导入到目标。 </br> -导出/导入中使用的文件格式和数据类型需要与表架构一致。 |
|[导入导出向导/BACPAC](../../database/database-import.md)| -)  (迁移单个业务线应用程序数据库。 </br>-适用于较小的数据库。  </br>  不需要单独的迁移服务或工具。 </br> </br> 支持的源： </br> -SQL Server (2005-2019) 本地或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 计算 SQL Server VM  |   </br> -需要停机，因为需要在源位置导出数据，并在目标上导入数据。   </br> -导出/导入中使用的文件格式和数据类型需要与表架构一致，以避免截断/数据类型不匹配错误。 </br> -导出具有大量对象的数据库所花费的时间可能会显著提高。 |
|[Azure 数据工厂 (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| -从源 SQL Server 数据库 (s) 迁移和/或转换数据。</br> -将数据从多个数据源合并到 Azure SQL 托管实例通常用于商业智能 (BI) 工作负荷。   </br> -要求在 ADF 中创建数据移动管道，以将数据从源移动到目标。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 是一个重要的考虑因素，基于管道触发器、活动运行、数据移动持续时间等。 |
| | | |

## <a name="feature-interoperability"></a>功能互操作性 

迁移依赖于其他 SQL Server 功能的工作负荷时，还有其他注意事项。 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

使用 [Azure 数据库迁移服务 (DMS) ](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)，将 SSISDB 中 SQL SERVER INTEGRATION SERVICES (SSIS) 包和项目迁移到 azure SQL 托管实例。 

对于迁移，仅支持 SSISDB 中从 SQL Server 2012 开始的 SSIS 包。 在迁移前转换旧 SSIS 包。 有关详细信息，请参阅 [项目转换教程](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) 。 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) 报表可以迁移到 Power BI 中的分页报表。 使用 [RDL 迁移工具](https://github.com/microsoft/RdlMigration) 来帮助准备和迁移报表。 此工具由 Microsoft 开发，可帮助客户将 RDL 报表从其 SSRS 服务器迁移到 Power BI。 它在 GitHub 上可用，并记录了迁移方案的端到端演练。 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server 2012 及更高版本中 SQL Server Analysis Services 表格模型可迁移到 Azure Analysis Services，这是 Azure 中 Analysis Services 表格模型的 PaaS 部署模型。 可以在本 [视频教程](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)中了解有关将本地模型迁移到 Azure Analysis Services 的详细信息。

此外，还可以考虑将本地 Analysis Services 表格模型迁移到 [使用新的 XMLA 读取/写入终结点 Power BI Premium](/power-bi/admin/service-premium-connect-tools)。 
> [!NOTE]
> Power BI XMLA 读取/写入终结点功能目前处于公共预览状态，在功能公开上市之前，不应考虑将其用于生产工作负荷。

#### <a name="high-availability"></a>高可用性

SQL Server 高可用性功能 Always On 故障转移群集实例和 Always On 可用性组在目标 Azure SQL 托管实例上过时，因为高可用性体系结构已内置常规用途 ([标准可用性模型) ](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 和业务关键 ([高级可用性模型 ](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)) SQL 托管实例。 高级可用性模型还提供读取横向扩展，允许连接到某个辅助节点以实现只读目的。     

除了 SQL 托管实例中包含的高可用性体系结构外，还提供了 [自动故障转移组](../../database/auto-failover-group-overview.md) 功能，可用于管理托管实例中数据库到另一个区域的复制和故障转移。 

#### <a name="sql-agent-jobs"></a>SQL 代理作业

使用脱机 Azure 数据库迁移服务 (DMS) 选项来迁移 [SQL 代理作业](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md#offline-migrations)。 否则，请使用 SQL Server Management Studio 在 Transact-sql (T-sql) 中编写作业脚本，然后在目标 SQL 托管实例上手动重新创建这些作业。 

> [!IMPORTANT]
> 目前，Azure DMS 仅支持具有 T-sql 子系统步骤的作业。 包含 SSIS 包步骤的作业必须手动迁移。 

#### <a name="logins-and-groups"></a>登录名和组

可以使用数据库迁移服务将源 SQL Server 中的 SQL 登录名移动到 Azure SQL 托管实例， (DMS) 处于脱机模式。  使用 **迁移向导** 中的 "**[选择登录名](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)**" 边栏选项卡将登录名迁移到目标 SQL 托管实例。 

默认情况下，Azure 数据库迁移服务仅支持迁移 SQL 登录名。 但是，可通过以下方式启用迁移 Windows 登录名的功能：

确保目标 SQL 托管实例具有 Azure AD 读取访问权限，该访问权限可通过具有 " **公司管理员** " 或 " **全局管理员**" 角色的用户 Azure 门户进行配置。
配置 Azure 数据库迁移服务实例以启用 Windows 用户/组登录名迁移，这通过 Azure 门户在“配置”页上进行设置。 启用此设置后，重启服务以使更改生效。

重启服务后，Windows 用户/组登录名将出现在可用于迁移的登录名列表中。 对于迁移的所有 Windows 用户/组登录名，系统都会提示提供关联的域名。 不支持服务用户帐户（域名为 NT AUTHORITY 的帐户）和虚拟用户帐户（域名为 NT SERVICE 的帐户）。

若要了解详细信息，请参阅 [如何使用 t-sql 将 SQL Server 实例中的 windows 用户和组迁移到 AZURE SQL 托管实例](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)。

或者，你可以使用 Microsoft 数据迁移架构师专门设计的 [PowerShell 实用程序工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) 。 实用工具使用 PowerShell 创建 T-sql 脚本，以便重新创建登录名，并从源数据库中选择数据库用户。 该工具会自动将 Windows AD 帐户映射到 Azure AD 帐户，并可针对源 Active Directory 针对每个登录执行 UPN 查找。 该工具为自定义服务器和数据库角色以及角色成员身份、数据库角色和用户权限编写脚本。 当前不支持包含的数据库，并且只为可能的 SQL Server 权限的一个子集编写脚本。 

#### <a name="encryption"></a>加密

使用本机还原选项将受  [透明数据加密](../../database/transparent-data-encryption-tde-overview.md)保护的数据库迁移   到托管实例时，请在数据库还原 *之前* 将相应的证书从源 SQL Server [迁移](../../managed-instance/tde-certificate-migrate.md)到目标 SQL 托管实例。 

#### <a name="system-databases"></a>系统数据库

不支持还原系统数据库。 若要迁移 (存储在 master 或 msdb 数据库中的实例级别对象) ，请使用 Transact-sql (T-sql) 来编写脚本，然后在目标托管实例上重新创建它们。 

## <a name="leverage-advanced-features"></a>利用高级功能 

请确保利用 SQL 托管实例提供的基于云的高级功能。 例如，你不再需要担心如何管理备份，因为服务会为你执行此功能。 你可以还原到 [保留期内的任意时间点](../../database/recovery-using-backups.md#point-in-time-restore)。 此外，您无需担心设置高可用性，因为 [在中内置了高可用性](../../database/high-availability-sla.md)。 

若要增强安全性，请考虑使用 [Azure Active Directory 身份验证](../../database/authentication-aad-overview.md)、 [审核](../../managed-instance/auditing-configure.md)、 [威胁检测](../../database/azure-defender-for-sql.md)、 [行级别安全性](/sql/relational-databases/security/row-level-security)和 [动态数据屏蔽](/sql/relational-databases/security/dynamic-data-masking)。

除了高级管理和安全功能，SQL 托管实例还提供了一组可帮助你 [监视和优化工作负荷](../../database/monitor-tune-overview.md)的高级工具。 [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md)允许以集中式方式监视大量托管实例。 [自动优化](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)  在托管实例中，会持续监视 SQL 计划执行统计信息的性能，并自动修复已识别的性能问题。 

只有在 [数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 更改为最新兼容级别 (150) 时，某些功能才可用。 

## <a name="migration-assets"></a>迁移资产 

有关更多帮助，请参阅为实际迁移项目开发的以下资源。

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[DBLoader 实用程序](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|可以使用 DBLoader 将分隔文本文件中的数据加载到 SQL Server 中。 此 Windows 控制台实用工具使用 SQL Server native client bulkload 接口，该接口适用于所有版本的 SQL Server，包括 Azure SQL MI。|
|[用于将本地 SQL Server 登录迁移到 Azure SQL 托管实例的实用工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|一个 PowerShell 脚本，它创建一个 T-sql 命令脚本来重新创建登录名，并选择 "从本地 SQL Server 到 Azure SQL 托管实例的数据库用户"。 该工具允许将 Windows AD 帐户自动映射到 Azure AD 帐户，还可以选择迁移 SQL Server 本机登录名。|
|[使用 Logman 的 Perfmon 数据收集自动化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|一种工具，用于收集执行数据以了解有助于迁移目标建议的基线性能。 此工具使用 logman.exe 创建命令，该命令将创建、启动、停止和删除在远程 SQL Server 上设置的性能计数器。|
|[白皮书-通过还原完整备份和差异备份，将数据库迁移到 Azure SQL 托管实例](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|本白皮书提供了一些指导和步骤，可帮助加速从 SQL Server 到 Azure SQL 托管实例的迁移，前提是你只有 (的完整备份和差异备份，并且没有日志备份功能) 。|

这些资源是作为 Data SQL Ninja 计划的一部分开发的，该计划由 Azure 数据组工程团队提供赞助。 Data SQL Ninja 计划的核心宗旨是解锁和加速复杂的现代化进程，并争取数据平台向 Microsoft Azure 数据平台迁移的机会。 如果你认为贵组织有意参与 Data SQL Ninja 计划，请联系帐户团队并让他们提交提名。


## <a name="next-steps"></a>后续步骤

若要开始将 SQL Server 迁移到 Azure SQL 托管实例，请参阅 [SQL Server 到 SQL 托管实例迁移指南](sql-server-to-managed-instance-guide.md)。

- 有关可用于帮助你完成各种数据库和数据迁移方案以及专业任务的 Microsoft 和第三方服务和工具的矩阵，请参阅 [数据迁移的服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要了解有关 Azure SQL 托管实例的详细信息，请参阅：
   - [Azure SQL 托管实例中的服务层](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server 和 Azure SQL 托管实例之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要了解有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [成本调整和大小调整工作负荷迁移到 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要评估应用程序访问层，请参阅 [数据访问迁移工具包 (预览) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 有关如何执行数据访问层 A/B 测试的详细信息，请参阅 [数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。