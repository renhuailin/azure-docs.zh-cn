---
title: Access 到 Azure SQL 数据库：迁移指南
description: 本指南介绍如何使用适用于 Access 的 SQL Server 迁移助手（适用于 Access 的 SSMA）将 Microsoft Access 数据库迁移到 Azure SQL 数据库。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 03/19/2021
ms.openlocfilehash: 8363fb2dbd80f55cdae4c064177bed63ed988559
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725566"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>迁移指南：Access 到 Azure SQL 数据库

本指南介绍如何使用适用于 Access 的 [SQL Server 迁移](https://azure.microsoft.com/migration/sql-server/)助手（适用于 Access 的 SSMA）将 Microsoft Access 数据库[迁移](https://azure.microsoft.com/migration/migration-journey)到 Azure SQL 数据库。

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。 

## <a name="prerequisites"></a>先决条件

开始将 Access 数据库迁移到 SQL 数据库之前，请执行以下操作：

- 验证源环境是否受支持。 
- 下载并安装[适用于 Access 的 SQL Server 迁移助手](https://www.microsoft.com/download/details.aspx?id=54255)。
- 确保具有连接和足够的权限来访问源和目标。

## <a name="pre-migration"></a>迁移前

满足先决条件后，就已准备就绪，可以探索环境的拓扑并评估 [Azure 云迁移](https://azure.microsoft.com/migration)的可行性了。


### <a name="assess"></a>评估 

使用适用于 Access 的 SSMA 查看数据库对象和数据，并评估数据库是否适合迁移。 

若要创建评估，请执行以下操作： 

1. 打开[适用于 Access 的 SSMA](https://www.microsoft.com/download/details.aspx?id=54255)。 
1. 依次选择“文件”和“新建项目” 。 
1. 提供项目名称和项目的位置，然后从下拉列表中选择“Azure SQL 数据库”作为迁移目标。 
1. 选择“确定”。 

   ![用于输入迁移项目名称和位置的“新建项目”窗格的屏幕截图。](./media/access-to-sql-database-guide/new-project.png)

1. 选择“添加数据库”，然后选择要添加到新项目中的数据库。 

   ![适用于 Access 的 SSMA 中“添加数据库”选项卡的屏幕截图。](./media/access-to-sql-database-guide/add-databases.png)

1. 在“Access 元数据资源管理器”窗格中，右键单击数据库，然后选择“创建报表” 。 或者，可以选择右上方的“创建报表”选项卡。

   ![Access 元数据资源管理器中“创建报表”命令的屏幕截图。](./media/access-to-sql-database-guide/create-report.png)

1. 查看 HTML 报告，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 Access 对象的清单，并了解执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。 例如：

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![SSMA 中的示例数据库报表评估的屏幕截图。](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>验证数据类型

验证默认的数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行以下操作：

1. 在适用于 Access 的 SSMA 中，选择“工具”，然后选择“项目设置” 。 
1. 选择“类型映射”选项卡。 

   ![适用于 Access 的 SSMA 中“类型映射”窗格的屏幕截图。](./media/access-to-sql-database-guide/type-mappings.png)

1. 可以通过在“Access 元数据资源管理器”窗格中选择表名来更改每个表的类型映射。


### <a name="convert-the-schema"></a>转换架构

若要转换数据库对象，请执行以下操作： 

1. 选择“连接到 Azure SQL 数据库”选项卡，然后执行以下操作：

   a. 输入用于连接到 SQL 数据库的详细信息。  
   b. 在下拉列表中选择目标 SQL 数据库。 也可输入新名称，这将在目标服务器上创建一个数据库。  
   c. 提供身份验证详细信息。   
   d. 选择“连接”  。

   ![用于输入连接详细信息的“连接到 Azure SQL 数据库”窗格的屏幕截图。](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. 在“Access 元数据资源管理器”窗格中，右键单击数据库，然后选择“转换架构” 。 或者，你可以选择数据库，然后选择“转换架构”选项卡。

   ![“Access 元数据资源管理器”窗格上“转换架构”命令的屏幕截图。](./media/access-to-sql-database-guide/convert-schema.png)

1. 转换完成后，将转换后的对象与原始对象进行比较，以识别潜在问题并根据建议解决这些问题。

   ![显示转换后的对象与源对象进行比较的屏幕截图。](./media/access-to-sql-database-guide/table-comparison.png)

    将转换后的 Transact-SQL 文本与原始代码进行比较，并查看建议。

   ![显示转换后的查询与源代码进行比较的屏幕截图。](./media/access-to-sql-database-guide/query-comparison.png) 

1. （可选）若要转换单个对象，请右键单击该对象，再选择“转换架构”。 转换后的对象在“Access 元数据资源管理器”中显示为粗体文本： 

   ![显示“Access 元数据资源管理器”中对象已转换的屏幕截图。](./media/access-to-sql-database-guide/converted-items.png)
 
1. 在“输出”窗格中，选择“查看结果”图标，然后在“错误列表”窗格中查看错误  。 
1. 在本地保存该项目以进行脱机架构修正练习。 为此，请选择”文件” > ”保存项目” 。 这样，你就有机会在将架构发布到 SQL 数据库之前，先对源架构和目标架构进行离线评估并执行修正。

## <a name="migrate-the-databases"></a>迁移数据库

评估数据库并解决所有差异后，可以运行迁移过程。 迁移数据是一个大容量加载操作，它在事务中将多行数据移动到 Azure SQL 数据库。 每个事务中要加载到 SQL 数据库中的行数可在项目设置中进行配置。

若要使用适用于 Access 的 SSMA 发布架构并迁移数据，请执行以下步骤： 

1. 如果尚未执行此操作，请选择“连接到 Azure SQL 数据库”并提供连接详细信息。 

1. 发布架构。 在“Azure SQL 数据库元数据资源管理器”窗格中，右键单击要处理的数据库，然后选择“与数据库同步” 。 此操作会将 MySQL 架构发布到 SQL 数据库。

1. 在“与数据库同步”窗格上，查看源项目与目标项目之间的映射：

   ![用于检查数据库同步的“与数据库同步”窗格的屏幕截图。](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. 在“Access 元数据资源管理器”窗格上，勾选要迁移的项目旁边的复选框。 若要迁移整个数据库的数据，请勾选数据库旁边的复选框。 

1. 迁移数据。 右键单击要迁移的数据库或对象，然后选择“迁移数据”。 或者，可以选择右上方的“迁移数据”选项卡。  

   若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

    ![“Access 元数据资源管理器”窗格上“迁移数据”命令的屏幕截图。](./media/access-to-sql-database-guide/migrate-data.png)

1. 迁移完成后，查看数据迁移报表。  

    ![“迁移数据报表”窗格的屏幕截图，显示要查看的示例报表。](./media/access-to-sql-database-guide/migrate-data-review.png)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 Azure SQL 数据库，并查看数据和架构来验证迁移。

   ![用于在 SSMA 中验证迁移的 SQL Server Management Studio 对象资源管理器的屏幕截图。](./media/access-to-sql-database-guide/validate-data.png)

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

## <a name="migration-assets"></a>迁移资产 

若要获得完成此迁移场景的更多帮助，请参阅以下资源。 它是为支持实际迁移项目编制的。

| 标题 | 说明 |
| --- | --- |
| [数据工作负荷评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130) | 为指定工作负载提供建议的“最适合”目标平台、云就绪性和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。 |

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

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
- 有关如何执行数据访问层 A/B 测试的信息，请参阅[数据库实验助手概述](/sql/dea/database-experimentation-assistant-overview)。