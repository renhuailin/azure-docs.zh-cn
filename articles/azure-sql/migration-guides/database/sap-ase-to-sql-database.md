---
title: SAP ASE 到 Azure SQL 数据库：迁移指南
description: 本指南介绍如何使用适用于 SAP Adapter Server Enterprise 的 SQL Server 迁移助手将 SAP ASE 数据库迁移到 Azure SQL 数据库。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564578"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>迁移指南：SAP ASE 到 Azure SQL 数据库
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

本指南介绍如何使用适用于 SAP Adapter Server Enterprise 的 SQL Server 迁移助手将 SAP ASE 数据库迁移到 Azure SQL 数据库。

有关其他迁移指南，请参阅[数据库迁移](https://datamigration.microsoft.com/)。 

## <a name="prerequisites"></a>先决条件 

要将 SAP SE 数据库迁移到 Azure SQL 数据库，需要：

- 验证源环境是否受支持。 
- [适用于 SAP Adaptive Server Enterprise（之前称为 SAP Sybase ASE）的 SQL Server 迁移助手](https://www.microsoft.com/en-us/download/details.aspx?id=54256)。 

## <a name="pre-migration"></a>预迁移

满足先决条件后，就可以发现环境的拓扑并评估迁移的可行性。

### <a name="assess"></a>评估

使用[适用于 SAP Adaptive Server Enterprise（之前称为 SAP Sybase ASE）的 SQL Server 迁移助手 (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) 来评审数据库对象和数据、评估数据库是否适合迁移、将 Sybase 数据库对象迁移到 Azure SQL 数据库，然后将数据迁移到 Azure SQL 数据库。 若要了解详细信息，请查看[适用于 Sybase 的 SQL Server 迁移助手 (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)。

要创建评估，请执行以下步骤： 

1. 打开“SSMA for Sybase”。 
1. 选择“文件”，然后选择“新建项目”。 
1. 提供项目名称和项目的保存位置，然后从下拉列表中选择Azure SQL 数据库作为迁移目标。 选择“确定”。
1. 在“连接到 Sybase”对话框上，为 SAP 连接详细信息输入相应的值。 
1. 右键单击要迁移的 SAP 数据库，然后选择“创建报表”。 这会生成一个 HTML 报表。
1. 查看 HTML 报表，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 DB2 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。

   例如：`drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`。 


### <a name="validate-type-mappings"></a>验证类型映射

执行架构转换之前，请验证默认数据类型映射，或者根据要求更改这些映射。 为此，可导航到“工具”菜单并选择“项目设置”；也可以在“SAP ASE 元数据资源管理器”中选择表，来更改每个表的类型映射。


### <a name="convert-schema"></a>转换架构

要转换架构，请执行以下步骤：

1. （可选）若要转换动态或即席查询，请右键单击节点，然后选择“添加语句”。 
1. 在顶行导航栏中选择“连接到 Azure SQL 数据库”，并提供 Azure SQL 数据库详细信息。 可以选择连接到现有数据库，也可以提供新名称，如果采取后一种做法，将在目标服务器上创建一个数据库。
1. 在“Sybase 元数据资源管理器”中右键单击 SAP ASE 架构，然后选择“转换架构”。 或者，可从顶行导航栏中选择“转换架构”。 
1. 比较并查看架构的结构来识别潜在问题。 

   架构转换后，可以在本地保存此项目供离线架构修正练习使用。 在“文件”菜单中选择“保存项目”。 这样，你就有机会在将架构发布到 Azure SQL 数据库之前，先对源和目标架构进行脱机评估并执行修正。

若要了解详细信息，请查看[转换架构](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql)


## <a name="migrate"></a>Migrate 

在满足必需的先决条件并完成与“预迁移”阶段相关的任务后，就可以执行架构和数据迁移了。

若要发布架构并迁移数据，请执行以下步骤： 

1. 在“Azure SQL 数据库元数据资源管理器”中右键单击数据库，然后选择“与数据库同步”。  此操作会将 SAP ASE 架构发布到 Azure SQL 数据库实例。
1. 在“SAP ASE 元数据资源管理器”中右键单击 SAP ASE 架构，然后选择“迁移数据”。  或者，可从顶行导航栏中选择“迁移数据”。  
1. 迁移完成后，查看“数据迁移报表”： 
1. 通过使用 Azure SQL Database Management Studio (SSMS) 查看 Azure SQL 数据库实例上的数据和架构来验证迁移。


## <a name="post-migration"></a>迁移后 

成功完成“迁移”阶段后，需要完成一系列的迁移后任务，以确保一切都能尽量顺利高效地进行。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，实现这一点需要对应用程序进行更改。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括执行以下活动：

1. 开发验证测试。 要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。

2. 设置测试环境。 测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。

3. 运行验证测试。 针对源和目标运行验证测试，然后分析结果。

4. 运行性能测试。 针对源和目标运行性能测试，然后分析和比较结果。

### <a name="optimize"></a>优化

迁移后阶段对于协调任何数据准确性问题、验证完整性以及解决工作负载的性能问题至关重要。

> [!NOTE]
> 有关这些问题及其具体缓解步骤的更详细信息，请参阅[迁移后验证和优化指南](/sql/relational-databases/post-migration-validation-and-optimization-guide)。


## <a name="next-steps"></a>后续步骤

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 要了解有关 Azure SQL 数据库的详细信息，请参阅：
   - [SQL 数据库概述](../../database/sql-database-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
