---
title: DB2 到 SQL 托管实例：迁移指南
description: 本指南介绍如何使用适用于 DB2 的 SQL Server 迁移助手将 DB2 数据库迁移到 Azure SQL 托管实例。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 9ad838b8c5f54d3ecdd5c8ce56b197cdb6cec1ba
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563855"
---
# <a name="migration-guide-db2-to-sql-managed-instance"></a>迁移指南：DB2 到 SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

本指南介绍如何使用适用于 DB2 的 SQL Server 迁移助手将 DB2 数据库迁移到 Azure SQL 托管实例。 

有关其他方案，请参阅[数据库迁移指南](https://datamigration.microsoft.com/)。

## <a name="prerequisites"></a>先决条件 

要将 DB2 数据库迁移到 SQL 托管实例，需要以下条件：

- 验证源环境是否受支持。
- 下载 [DB2 SQL Server 迁移助手 (SSMA)](https://www.microsoft.com/download/details.aspx?id=54254)。
- 一个目标 [Azure SQL 托管实例](../../database/single-database-create-quickstart.md)。


## <a name="pre-migration"></a>预迁移

满足先决条件后，就可以发现环境的拓扑并评估迁移的可行性。 

### <a name="assess-and-convert"></a>评估和转换

使用 SQL Server 迁移助手 (SSMA) 创建评估。 

要创建评估，请执行以下步骤：

1. 打开 DB2 SQL Server 迁移助手 (SSMA)。 
1. 选择“文件”，然后选择“新建项目”。 
1. 提供项目名称和项目的保存位置，然后从下拉列表中选择 Azure SQL 托管实例作为迁移目标。 选择“确定”。 

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="提供项目详细信息，然后选择“确定”以保存。":::


1. 为“连接到 DB2”对话框上的 DB2 连接详细信息输入值。 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="“连接到 DB2 ”实例":::


1. 右键单击要迁移的 DB2 架构，然后选择“创建报表”。 这将生成一个 HTML 报表。 或者，可以在选择架构后，从导航栏中选择“创建报表”。 

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="右键单击架构，然后选择“创建报表”":::

1. 查看 HTML 报表，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 DB2 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。

   例如：`drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`。 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="查看报表以确定任何错误或警告":::


### <a name="validate-data-types"></a>验证数据类型

验证默认的数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行下列步骤： 

1. 在菜单中，选择“工具”。 
1. 选择“项目设置”。 
1. 选择“类型映射”选项卡。 

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="选择架构，然后选择类型映射":::

1. 可以通过在“DB2 元数据资源管理器”中选择表来更改每个表的类型映射。 

### <a name="schema-conversion"></a>架构转换 

要转换架构，请执行以下步骤：

1. （可选）向语句中添加动态或临时查询。 右键单击节点，然后选择“添加语句”。 
1. 选择“连接到 Azure SQL 数据库”。 
    1. 输入连接详细信息以连接到 Azure SQL 托管实例。  
    1. 从下拉菜单选择目标数据库。 
    1. 选择“连接”  。 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="填写详细信息以连接到 SQL Server":::


1. 右键单击架构，然后选择“转换架构”。 或者，可以在选择架构后，从顶部导航栏中选择“转换架构”。 

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="右键单击架构，然后选择“转换架构”":::

1. 转换完成后，比较并查看架构的结构，以识别潜在问题并根据建议解决这些问题。 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="比较并查看架构的结构，以识别潜在问题并根据建议解决这些问题。":::

1. 在本地保存该项目以进行脱机架构修正练习。 在“文件”菜单中选择“保存项目”。 


## <a name="migrate"></a>Migrate

完成对数据库的评估并解决任何分歧后，下一步就是执行迁移过程。

要发布架构并迁移数据，请执行以下步骤：

1. 发布架构：右键单击“Azure SQL 托管实例元数据资源管理器”中的“数据库”节点中的数据库，然后选择“与数据库同步”  。

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="右键单击数据库，然后选择“与数据库同步”":::

1. 迁移数据：右键单击“DB2 元数据资源管理器”中的架构，然后选择“迁移数据”。 

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="右键单击架构，然后选择“迁移数据”":::

1. 提供 DB2 和 SQL 托管实例的连接详细信息。 
1. 查看“数据迁移报表”。 

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="查看“数据迁移报表”":::

1. 使用 SQL Server Management Studio 连接到 SQL 托管实例，并通过查看数据和架构来验证迁移。 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="在 SSMS 中比较架构":::

## <a name="post-migration"></a>迁移后 

成功完成迁移阶段后，需要执行一系列的迁移后任务，确保一切尽可能顺利高效地运行。

### <a name="remediate-applications"></a>修正应用程序 

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，实现这一点需要对应用程序进行更改。


### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括以下活动：

1. 开发验证测试：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。
1. 设置测试环境：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
1. 运行验证测试：针对源和目标运行验证测试，然后分析结果。
1. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。


## <a name="leverage-advanced-features"></a>利用高级功能 

请确保充分利用 Azure SQL 托管实例提供的基于云的高级功能，例如[内置高可用性](../../database/high-availability-sla.md)、[威胁检测](../../database/azure-defender-for-sql.md)以及[监视和优化工作负载](../../database/monitor-tune-overview.md)。 


只有将[数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)更改为最新的兼容性级别 (150) 后，某些 SQL Server 功能才可用。 

## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的：

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[DB2 zOS 数据资产发现和评估包](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|在数据库上运行 SQL 脚本后，可以将结果导出到文件系统上的文件。 支持多种文件格式（包括 *.csv），方便你在外部工具（如电子表格）中捕获结果。 如果你想要与未安装工作台的团队轻松共享结果，此方法会很有用。|
|[IBM DB2 LUW 清单脚本和项目](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|此资产包含一个 SQL 查询，该查询可访问 IBM DB2 LUW 版本 11.1 系统表，并按架构和对象类型提供对象计数、每个架构中“原始数据”的粗略估计、每个架构中表的大小，以及 CSV 格式的存储结果。|
|[Azure 上的 DB2 LUW 纯扩展 - 设置指南](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|本指南用作 DB2 实现计划的起点。 尽管业务要求有所不同，但均适用相同的基本模式。 此体系结构模式还可用于 Azure 上的 OLAP 应用程序。|

这些资源是作为 Data SQL Ninja 计划的一部分开发的，该计划由 Azure 数据组工程团队提供赞助。 Data SQL Ninja 计划的核心宗旨是解锁和加速复杂的现代化进程，并争取数据平台向 Microsoft Azure 数据平台迁移的机会。 如果你认为贵组织有意参与 Data SQL Ninja 计划，请联系帐户团队并让他们提交提名。


## <a name="next-steps"></a>后续步骤

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL 托管实例，请参阅：
   - [SQL 托管实例的概述](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。