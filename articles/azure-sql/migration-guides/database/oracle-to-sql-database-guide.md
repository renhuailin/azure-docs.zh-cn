---
title: Oracle 到 Azure SQL 数据库：迁移指南
description: 本指南介绍如何使用适用于 Oracle 的 SQL Server 迁移助手将 Oracle 架构迁移到 Azure SQL 数据库。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 08/25/2020
ms.openlocfilehash: 85f65fa17e1fac05bd9c9040e77b2fbde104b176
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961954"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>迁移指南：Oracle 到 Azure SQL 数据库

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

本指南介绍如何使用适用于 Oracle 的 [SQL Server 迁移](https://azure.microsoft.com/en-us/migration/sql-server/)助手 (SSMA for Oracle) 将 Oracle 架构[迁移](https://azure.microsoft.com/migration/migration-journey)到 Azure SQL 数据库。

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。

## <a name="prerequisites"></a>先决条件

开始将 Oracle 架构迁移到 SQL 数据库之前：

- 验证源环境是否受支持。
- 下载 [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258)。
- 拥有目标 [SQL 数据库](../../database/single-database-create-quickstart.md)实例。
- 获取 [SSMA for Oracle 的必需权限](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)和[提供程序](/sql/ssma/oracle/connect-to-oracle-oracletosql)。
 
## <a name="pre-migration"></a>预迁移

满足先决条件后，就已准备就绪，可以探索环境的拓扑并评估 [Azure 云迁移](https://azure.microsoft.com/migration)的可行性了。 迁移过程的这一部分涉及到对需要迁移的数据库进行清点、评估这些数据库是否存在潜在的迁移问题或阻碍因素，然后处理你可能已发现的任何项目。

### <a name="assess"></a>评估

借助 SSMA for Oracle，可查看数据库对象和数据、评估数据库是否适合迁移、将数据库对象迁移到 SQL 数据库，最后将数据迁移到数据库。

若要创建评估：

1. 打开 [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258)。
1. 依次选择“文件”和“新建项目” 。
1. 输入项目名称和保存项目的位置。 然后从下拉列表中选择“Azure SQL 数据库”作为迁移目标，然后选择“确定” 。

   ![显示“连接到 Oracle”的屏幕截图。](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. 选择“连接到 Oracle”。 在“连接到 Oracle”对话框上，为 Oracle 连接详细信息输入相应的值。

1. 选择要迁移的 Oracle 架构。

   ![显示选择 Oracle 架构的屏幕截图。](./media/oracle-to-sql-database-guide/select-schema.png)

1. 在 Oracle 元数据资源管理器中，右键单击要迁移的 Oracle 架构，然后选择“创建报表”来生成 HTML 报表 。 或者，可选择一个数据库，然后选择“创建报表”选项卡。

   ![显示“创建报表”的屏幕截图。](./media/oracle-to-sql-database-guide/create-report.png)

1. 查看 HTML 报表，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 Oracle 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。

   有关示例，请参阅 `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`。

   ![显示“评估报表”的屏幕截图。](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>验证数据类型

验证默认的数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行下列步骤：

1. 在 SSMA for Oracle 中，选择“工具”，然后选择“项目设置” 。
1. 选择“类型映射”选项卡。

   ![显示“类型映射”的屏幕截图。](./media/oracle-to-sql-database-guide/type-mappings.png)

1. 通过在“Oracle 元数据资源管理器”中选择表，可以更改每个表的类型映射。

### <a name="convert-the-schema"></a>转换架构

若要转换架构：

1. （可选）向语句中添加动态或临时查询。 右键单击节点，然后选择“添加语句”。
1. 选择“连接到 Azure SQL 数据库”选项卡。
    1. 在“SQL 数据库”中，输入连接详细信息以连接数据库。
    1. 从下拉菜单中选择目标 SQL 数据库实例，或输入新的名称；如果是提供新名称，则将在目标服务器上创建一个数据库。
    1. 输入身份验证详细信息，然后选择“连接”。

    ![显示“连接到 Azure SQL 数据库”的屏幕截图。](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. 在“Oracle 元数据资源管理器”中，右键单击 Oracle 架构，然后选择“转换架构” 。 或者，可选择架构，然后选择“转换架构”选项卡。

   ![显示“转换架构”的屏幕截图。](./media/oracle-to-sql-database-guide/convert-schema.png)

1. 转换完成后，查看转换后的对象并将其与起始对象进行比较，以识别潜在问题并根据建议解决这些问题。

   ![显示查看建议架构的屏幕截图。](./media/oracle-to-sql-database-guide/table-mapping.png)

1. 将转换后的 Transact-SQL 文本与原始存储过程进行比较，并查看建议。

   ![显示查看建议的屏幕截图。](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. 在“输出”窗格中，选择“查看结果”，然后在“错误列表”窗格中查看错误 。
1. 在本地保存该项目以进行脱机架构修正练习。 在“文件”菜单中，选择“保存项目” 。 通过此步骤，你就有机会在将架构发布到 SQL 数据库之前，先对源架构和目标架构进行离线评估并执行修正。

## <a name="migrate"></a>Migrate

评估数据库并解决所有差异后，下一步就是运行迁移过程。 迁移涉及两个步骤：发布架构和迁移数据。

发布架构和迁移数据：

1. 发布架构：在“Azure SQL 数据库元数据资源管理器”中右键单击“数据库”节点中的数据库，然后选择“与数据库同步”  。

   ![显示“与数据库同步”的屏幕截图。](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. 检查源项目与目标之间的映射。

   ![显示“与数据库同步”检查的屏幕截图。](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. 迁移数据：在“Oracle 元数据资源管理器”中右键单击要迁移的数据库或对象，然后选择“迁移数据” 。 或者，可选择“迁移数据”选项卡。若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库、展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

   ![显示“迁移数据”的屏幕截图。](./media/oracle-to-sql-database-guide/migrate-data.png)

1. 输入 Oracle 和 SQL 数据库的连接详细信息。
1. 迁移完成后，查看“数据迁移报告”。

   ![屏幕截图，显示数据迁移报表。](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 SQL 数据库实例，并查看数据和架构来验证迁移。

   ![显示 SQL Server Management Studio 中验证情况的屏幕截图。](./media/oracle-to-sql-database-guide/validate-data.png)

此外，还可使用 SQL Server Integration Services 来执行迁移。 若要了解更多信息，请参阅以下文章：

- [SQL Server Integration Services 入门](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services for Azure 和混合数据移动](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>迁移后

成功完成迁移阶段后，需要完成一系列的迁移后任务，以确保一切都能尽量顺畅高效地正常进行。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，需要对应用程序进行更改才能完成此任务。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 是 Visual Studio Code 的扩展，可用于分析 Java 源代码并检测数据访问 API 调用和查询。 该工具包提供了一个单窗格视图，其中显示了支持新数据库后端需要满足的要求。 有关详细信息，请参阅[从 Oracle 迁移 Java 应用程序](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)博客文章。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括以下活动：

1. **开发验证测试**：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。
1. **设置测试环境**：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
1. **运行验证测试**：针对源和目标运行验证测试，然后分析结果。
1. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。

### <a name="optimize"></a>优化

迁移后阶段对于协调任何数据准确性问题、验证完整性以及解决工作负载的性能问题至关重要。

> [!NOTE]
> 有关这些问题及其缓解步骤的详细信息，请参阅[迁移后验证和优化指南](/sql/relational-databases/post-migration-validation-and-optimization-guide)。

## <a name="migration-assets"></a>迁移资产

若要获得完成此迁移方案的更多帮助，请参阅以下资源。 这些资源是为支持实际迁移项目而开发的。

| **标题/链接**                                                                                                                                          | **说明**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [数据工作负载评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 此工具为给定工作负载提供建议的“最适合”目标平台、云就绪性和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。                                                          |
| [Oracle 清单脚本项目](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | 该资产包含一个 PL/SQL 查询，它会命中 Oracle 系统表，并按架构类型、对象类型和状态提供对象计数。 它还提供每个架构中“原始数据”和表大小的粗略估算，结果以 CSV 格式存储。                                                                                                               |
| [自动进行 SSMA Oracle 评估收集和整合](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | 这组资源使用 .csv 文件作为输入（项目文件夹中的 sources.csv），来生成在控制台模式下运行 SSMA 评估所需的 xml 文件。 source.csv 是客户根据现有 Oracle 实例的清单提供的。 输出文件为 AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml 和 VariableValueFile.xml。|
| [SSMA for Oracle 的常见错误及其解决方法](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | 借助 Oracle，可在 WHERE 子句中分配非标量条件。 但是，SQL Server 不支持这种类型的条件。 因此，SSMA for Oracle 不会在 WHERE 子句中使用非标量条件转换查询。 相反，它会生成错误 O2SS0001。 可在该白皮书中更详细地了解问题及其解决方法。          |
| [Oracle 到 SQL Server 迁移手册](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | 本文档重点介绍在将 Oracle 架构迁移到最新版 SQL Server 数据库时所涉及的任务。 如果迁移需要更改特性/功能，则必须仔细考量每项更改可能对使用数据库的应用程序造成的影响。                                                     |
|[Oracle 到 SQL DB - 数据库比较实用工具](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA for Oracle 测试工具是推荐用于自动验证数据库对象转换和数据迁移的工具，它是数据库比较功能的超集。<br /><br />如果要查找备用的数据验证选项，则可使用数据库比较实用工具沿所有或所选表、行和列中的行或列级别比较数据。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="next-steps"></a>后续步骤

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要了解有关 SQL 数据库的详细信息，请参阅：
  - [Azure SQL 数据库概述](../../database/sql-database-paas-overview.md)
  - [Azure 总拥有成本 (TCO) 计算器](https://azure.microsoft.com/pricing/tco/calculator/)

- 有关云迁移的框架和采用周期的详细信息，请参阅：
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [云迁移资源](https://azure.microsoft.com/migration/resources)

- 有关视频内容，请参阅：
    - [迁移之旅及推荐用于执行评估和迁移的工具和服务的概述](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
