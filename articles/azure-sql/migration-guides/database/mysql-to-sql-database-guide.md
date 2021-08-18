---
title: MySQL 到 Azure SQL 数据库：迁移指南
description: 本指南将介绍如何使用适用于 MySQL 的 SQL Server 迁移助手 (SSMA for MySQL) 将 MySQL 数据库迁移到 Azure SQL 数据库。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 03/19/2021
ms.openlocfilehash: 3b9f286796a832d075977cda2eda54f990bbb3c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743718"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>迁移指南：MySQL 到 Azure SQL 数据库
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

本指南将介绍如何使用适用于 MySQL 的 [SQL Server 迁移](https://azure.microsoft.com/migration/sql-server/)助手 (SSMA for MySQL) 将 MySQL 数据库[迁移](https://azure.microsoft.com/migration/migration-journey)到 Azure SQL 数据库。 

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。 

## <a name="prerequisites"></a>先决条件

开始将 MySQL 数据库迁移到 SQL 数据库之前，请执行以下操作：

- 验证源环境是否受支持。 目前支持 MySQL 5.6 和 5.7。 
- 下载并安装[适用于 MySQL 的 SQL Server 迁移助手](https://www.microsoft.com/download/details.aspx?id=54257)。
- 请确保具有连接以及访问源和目标的足够权限。

## <a name="pre-migration"></a>预迁移 

满足先决条件后，就已准备就绪，可以探索环境的拓扑并评估 [Azure 云迁移](https://azure.microsoft.com/migration)的可行性了。

### <a name="assess"></a>评估 

使用适用于 MySQL 的 SQL Server 迁移助手 (SSMA) 评审数据库对象和数据，并评估数据库是否适合迁移。 

若要创建评估，请执行以下操作：

1. 打开 [SSMA for MySQL](https://www.microsoft.com/download/details.aspx?id=54257)。 
1. 依次选择“文件”和“新建项目” 。 
1. 在“新建项目”窗格中，输入项目的名称和位置，然后在“迁移到”下拉列表中选择“Azure SQL 数据库”  。 
1. 选择“确定”。

   ![用于输入迁移项目名称、位置和目标的“新建项目”窗格的屏幕截图。](./media/mysql-to-sql-database-guide/new-project.png)

1. 选择“连接到 MySQL”选项卡，然后提供用于连接 MySQL 服务器的详细信息。 

   ![用于指定与源的连接的“连接到 MySQL”窗格的屏幕截图。](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. 在“MySQL 元数据资源管理器”窗格中，右键单击 MySQL 架构，然后选择“创建报表” 。 或者，可以选择右上方的“创建报表”选项卡。

   ![SSMA for MySQL 中“创建报表”链接的屏幕截图。](./media/mysql-to-sql-database-guide/create-report.png)

1. 查看 HTML 报表，了解转换统计信息、错误或警告。 分析报表以了解转换问题和解决方法。 
   另外，还可以在 Excel 中打开报表以获取 MySQL 对象的清单，以及了解执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。 例如： 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![SSMA 中示例转换报表的屏幕截图。](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>验证数据类型

验证默认的数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行以下操作： 

1. 选择“工具”，然后选择“项目设置” 。  
1. 选择“类型映射”选项卡。 

   ![SSMA for MySQL 中“类型映射”窗格的屏幕截图。](./media/mysql-to-sql-database-guide/type-mappings.png)

1. 可以通过在“MySQL 元数据资源管理器”窗格中选择表名来更改每个表的类型映射。 

### <a name="convert-the-schema"></a>转换架构 

若要转换架构，请执行以下操作： 

1. （可选）若要转换动态或专用查询，请右键单击节点并选择“添加语句”。 

1. 选择“连接到 Azure SQL 数据库”选项卡，然后执行以下操作：

   a. 输入用于连接到 SQL 数据库的详细信息。  
   b. 在下拉列表中选择目标 SQL 数据库。 也可以提供新名称（在这种情况下，将在目标服务器上创建一个数据库）。  
   c. 提供身份验证详细信息。  
   d. 选择“连接”。

   ![SSMA for MySQL 中“连接到 Azure SQL 数据库”窗格的屏幕截图。](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. 右键单击要使用的架构，然后选择“转换架构”。 或者，可以选择右上方的“转换架构”选项卡。

   ![“MySQL 元数据资源管理器”窗格上“转换架构”命令的屏幕截图。](./media/mysql-to-sql-database-guide/convert-schema.png)

1. 转换完成后，查看转换后的对象与起始对象并进行比较，以识别潜在问题并根据建议解决这些问题。 

   ![显示转换后的对象与起始对象进行比较的屏幕截图。](./media/mysql-to-sql-database-guide/table-comparison.png)

   将转换后的 Transact-SQL 文本与原始代码进行比较，并查看建议。

   ![显示转换后的查询与源代码进行比较的屏幕截图。](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. 在“输出”窗格中，选择“查看结果”，然后在“错误列表”窗格中查看所有错误  。 
1. 在本地保存该项目以进行脱机架构修正练习。 为此，请选择“文件” > “保存项目” 。 这样，你就有机会在将架构发布到 SQL 数据库之前，先对源架构和目标架构进行离线评估并执行修正。

   将转换后的过程与原始过程进行比较，如下所示： 

   ![显示转换后的过程与原始过程进行比较的屏幕截图。](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>迁移数据库 

评估数据库并解决所有差异后，可以运行迁移过程。 迁移涉及两个步骤：发布架构和迁移数据。 

若要发布架构并迁移数据，请执行以下操作： 

1. 发布架构。 在“Azure SQL 数据库元数据资源管理器”窗格中，右键单击数据库，然后选择“与数据库同步” 。 此操作会将 MySQL 架构发布到 SQL 数据库。

   ![用于查看数据库映射的“与数据库同步”窗格的屏幕截图。](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. 迁移数据。 在“MySQL 元数据资源管理器”窗格中，右键单击要迁移的 MySQL 架构，然后选择“迁移数据” 。 或者，可以选择右上方的“迁移数据”选项卡。

   若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

   ![“MySQL 元数据资源管理器”窗格上“迁移数据”命令的屏幕截图。](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 迁移完成后，查看“数据迁移报告”。
   
   ![数据迁移报表的屏幕截图。](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 SQL 数据库，并通过查看数据和架构来验证迁移。

   ![SQL Server Management Studio 的屏幕截图。](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

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
|[MySQL 到 SQL DB - 数据库比较实用工具](https://www.microsoft.com/download/details.aspx?id=103016)|数据库比较实用工具是可用于验证源和目标平台上的数据是否相同的 Windows 控制台应用程序。 可以使用该工具在所有或所选表、行和列中的行或列级别高效地比较数据。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="next-steps"></a>后续步骤 

- 若要帮助评估通过将工作负载迁移到 Azure 可节约的成本，请参阅 [Azure 总拥有成本计算器](https://aka.ms/azure-tco)。

- 如需一整套 Microsoft 与第三方服务和工具，以便在执行各种数据库和数据迁移场景及专门任务时可为你提供帮助，请查看[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 有关其他迁移指南，请参阅 [Azure 数据库迁移指南](https://datamigration.microsoft.com/)。 

- 有关迁移视频，请参阅[迁移之旅以及推荐的迁移和评估工具及服务的概述](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)。

- 有关更多[云迁移资源](https://azure.microsoft.com/migration/resources/)，请参阅[云迁移解决方案](https://azure.microsoft.com/migration)。
