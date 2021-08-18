---
title: SAP ASE 到 Azure SQL 数据库：迁移指南
description: 本指南介绍如何使用适用于 SAP Adapter Server Enterprise 的 SQL Server 迁移助手将 SAP ASE 数据库迁移到 Azure SQL 数据库。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 03/19/2021
ms.openlocfilehash: bcd9758fe77f2b5623c7aba1e145de07e6396e60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745755"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>迁移指南：SAP ASE 到 Azure SQL 数据库

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

本指南介绍如何使用适用于 SAP Adapter Server Enterprise 的 [SQL Server 迁移](https://azure.microsoft.com/migration/sql-server/)助手将 SAP Adapter Server Enterprise (ASE) 数据库[迁移](https://azure.microsoft.com/migration/migration-journey)到 Azure SQL 数据库。

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。 

## <a name="prerequisites"></a>先决条件 

开始将 SAP SE 数据库迁移到 SQL 数据库之前，请执行以下操作：

- 验证源环境是否受支持。 
- 下载并安装[适用于 SAP Adaptive Server Enterprise（之前称为 SAP Sybase ASE）的 SQL Server 迁移助手](https://www.microsoft.com/download/details.aspx?id=54256)。
- 请确保具有连接以及访问源和目标的足够权限。

## <a name="pre-migration"></a>迁移前

满足先决条件后，就已准备就绪，可以探索环境的拓扑并评估 [Azure 云迁移](https://azure.microsoft.com/migration)的可行性了。

### <a name="assess"></a>评估

使用[适用于 SAP Adaptive Server Enterprise（之前称为 SAP Sybase ASE）的 SQL Server 迁移助手 (SSMA)](https://www.microsoft.com/download/details.aspx?id=54256) 可评审数据库对象和数据、评估数据库是否适合迁移、将 Sybase 数据库对象迁移到 SQL 数据库，然后将数据迁移到 SQL 数据库。 若要了解详细信息，请查看 [适用于 Sybase 的 SQL Server 迁移助手 (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)。

若要创建评估，请执行以下操作： 

1. 打开 SSMA for Sybase。 
1. 依次选择“文件”和“新建项目” 。 
1. 在“新建项目”窗格中，输入项目的名称和位置，然后在“迁移到”下拉列表中选择“Azure SQL 数据库”  。 
1. 选择“确定”。
1. 在“连接到 Sybase”窗格中，输入 SAP 连接详细信息。 
1. 右键单击要迁移的 SAP 数据库，然后选择“创建报表”。 这会生成一个 HTML 报表。 或者，可以选择右上方的“创建报告”选项卡。
1. 查看 HTML 报告，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报告以获取 SAP ASE 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。 例如：

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>验证类型映射

执行架构转换之前，请验证默认数据类型映射，或者根据要求更改这些映射。 为此，可选择“工具” > “项目设置”，也可在 SAP ASE 元数据资源管理器中选择表，来更改每个表的类型映射  。

### <a name="convert-the-schema"></a>转换架构

若要转换架构，请执行以下操作：

1. （可选）若要转换动态或专用查询，请右键单击节点并选择“添加语句”。 
1. 选择“连接到 Azure SQL 数据库”选项卡，然后输入 SQL 数据库的详细信息。 可选择连接到现有数据库，也可提供新的名称。若是后者，则会在目标服务器上创建一个数据库。
1. 在“Sybase 元数据资源管理器”窗格中，右键单击要使用的 SAP ASE 架构，然后选“转换架构” 。 
1. 架构转换完成后，查看转换后的代码并将其与原始代码进行比较，以识别潜在问题。 

   架构转换后，可以在本地保存此项目供离线架构修正练习使用。 为此，请选择“文件” > “保存项目” 。 这样，你就有机会在将架构发布到 SQL 数据库之前，先对源和目标架构进行脱机评估并执行修正。

1. 在“输出”窗格中选择“查看结果”，然后在“错误列表”窗格中查看任何错误  。 
1. 在本地保存该项目以进行脱机架构修正练习。 为此，请选择“文件” > “保存项目” 。 这样，你就有机会在将架构发布到 SQL 数据库之前，先对源和目标架构进行脱机评估并执行修正。

## <a name="migrate-the-databases"></a>迁移数据库 

在满足必需的先决条件并完成与“预迁移”阶段相关的任务后，就可以运行架构和数据迁移了。

若要发布架构并迁移数据，请执行以下操作： 

1. 发布架构。 在“Azure SQL 数据库元数据资源管理器”窗格中，右键单击数据库，然后选择“与数据库同步” 。 此操作会将 SAP ASE 架构发布到 SQL 数据库。

1. 迁移数据。 在“SAP ASE 元数据资源管理器”窗格中，右键单击要迁移的 SAP ASE 数据库或对象，然后选择“迁移数据” 。 或者，可以选择右上方的“迁移数据”选项卡。 

   若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。 
1. 迁移完成后，查看“数据迁移报告”。 
1. 通过查看数据和架构来验证迁移。 为此，使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 SQL 数据库。

## <a name="post-migration"></a>迁移后 

成功完成迁移阶段后，需要完成一系列的迁移后任务，以确保一切都能尽量顺畅高效地正常进行。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，实现这一点需要对应用程序进行更改。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括以下活动：

1. **开发验证测试**：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。

1. **设置测试环境**：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。

1. **运行验证测试**：针对源和目标运行验证测试，然后分析结果。

1. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。


### <a name="optimize"></a>优化

迁移后阶段对于协调任何数据准确性问题、验证完整性以及解决工作负载的性能问题至关重要。

有关这些问题及其缓解步骤的详细信息，请参阅[迁移后验证和优化指南](/sql/relational-databases/post-migration-validation-and-optimization-guide)。


## <a name="next-steps"></a>后续步骤

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 要了解有关 Azure SQL 数据库的详细信息，请参阅：
   - [SQL 数据库概述](../../database/sql-database-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/)  

- 有关云迁移的框架和采用周期的详细信息，请参阅：
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [云迁移资源](https://azure.microsoft.com/migration/resources)

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)。
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。