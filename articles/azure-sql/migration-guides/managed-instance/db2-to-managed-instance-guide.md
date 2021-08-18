---
title: 从 Db2 迁移到 Azure SQL 托管实例：迁移指南
description: 本指南介绍如何使用适用于 Db2 的 SQL Server 迁移助手将 IBM Db2 数据库迁移到 Azure SQL 托管实例。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 05/14/2021
ms.openlocfilehash: 445384b3b9886130d1865a3ece6601cc7055573d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746629"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>迁移指南：从 Db2 迁移到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

本指南介绍如何使用适用于 Db2 的 SQL Server 迁移助手将 IBM Db2 数据库迁移到 Azure SQL 托管实例。 

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。 

## <a name="prerequisites"></a>先决条件 

要将 Db2 数据库迁移到 SQL 托管实例，需要以下条件：

- 验证[源环境是否受支持](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites)。
- 下载[适用于 Db2 的 SQL Server 迁移助手 (SSMA)](https://www.microsoft.com/download/details.aspx?id=54254)。
- [Azure SQL 托管实例](../../managed-instance/instance-create-quickstart.md)的目标实例。
- 连接以及访问源和目标的足够权限。 

## <a name="pre-migration"></a>预迁移

满足先决条件后，就已准备就绪，可以探索环境的拓扑并评估迁移的可行性了。 

### <a name="assess-and-convert"></a>评估和转换

使用 SQL Server 迁移助手创建评估。 

要创建评估，请执行以下步骤：

1. 打开 [Db2 的 SSMA](https://www.microsoft.com/download/details.aspx?id=54254)。 
1. 选择“文件” > “新建项目” 。 
1. 提供项目名称和保存项目的位置。 然后从下拉列表中选择“Azure SQL 托管实例”作为迁移目标，再选择“确定”。

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="显示要指定的项目详细信息的屏幕截图。":::


1. 在“连接到 Db2”上，输入值以查看 Db2 连接详细信息。 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="显示用于连接到 Db2 实例的选项的屏幕截图。":::


1. 右键单击要迁移的 Db2 架构，然后选择“创建报表”。 这将生成一个 HTML 报表。 或者，可以在选择架构后，从导航栏中选择“创建报表”。

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="屏幕截图，显示如何创建报表。":::

1. 查看 HTML 报表，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 Db2 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。

   例如：`drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`。 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="报表的屏幕截图，你查看此报表来识别任何错误或警告":::


### <a name="validate-data-types"></a>验证数据类型

验证默认数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行下列步骤： 

1. 在菜单中，选择“工具”。 
1. 选择“项目设置”。 
1. 选择“类型映射”选项卡。

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="该屏幕截图显示如何选择架构和类型映射。":::

1. 可通过在 Db2 元数据资源管理器中选择表来更改每个表的类型映射。 

### <a name="convert-schema"></a>转换架构 

要转换架构，请执行以下步骤：

1. （可选）向语句中添加动态或临时查询。 右键单击节点，然后选择“添加语句”。 
1. 选择“连接到 Azure SQL 托管实例”。 
    1. 输入连接详细信息以连接到 Azure SQL 托管实例。 
    1. 从下拉菜单列表中选择目标数据库，或者提供新的名称；如果是提供新名称，则将在目标服务器上创建一个数据库。 
    1. 提供身份验证详细信息。 
    1. 选择“连接”。

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="显示连接到 SQL Server 所需的详细信息的屏幕截图。":::


1. 右键单击架构，然后选择“转换架构”。 或者，可以在选择架构后，从顶部导航栏中选择“转换架构”。

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="显示选择和转换架构的屏幕截图。":::

1. 完成转换后，比较并查看架构的结构以识别潜在问题。 根据建议解决问题。

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="显示比较并查看架构的结构以识别潜在问题的屏幕截图。":::

1. 在“输出”窗格中，选择“查看结果” 。 在“错误列表”窗格中，查看错误。 
1. 在本地保存该项目以进行脱机架构修正练习。 在“文件”菜单中，选择“保存项目” 。 这样，你就有机会在将架构发布到 SQL 托管实例之前，先对源和目标架构进行脱机评估并执行修正。

## <a name="migrate"></a>Migrate

完成对数据库的评估并解决任何分歧后，下一步就是执行迁移过程。

要发布架构并迁移数据，请执行以下步骤：

1. 发布架构。 在 Azure SQL 托管实例元数据资源管理器中，从“数据库”节点中右键单击数据库 。 然后选择“与数据库同步”。

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="显示“与数据库同步”选项的屏幕截图。":::

1. 迁移数据。 在 Db2 元数据资源管理器中，右键单击要迁移的数据库或对象，然后选择“迁移数据” 。 或者，可在导航栏中选择“迁移数据”。 若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="显示选择架构和选择迁移数据的屏幕截图。":::

1. 提供 Db2 和 SQL 托管实例的连接详细信息。 
1. 迁移完成后，查看数据迁移报表。  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="显示在何处查看数据迁移报表的屏幕截图。":::

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 Azure SQL 托管实例服务的实例。 通过查看数据和架构来验证迁移：

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="显示比较 SQL Server Management Studio 中的架构的屏幕截图。":::

## <a name="post-migration"></a>迁移后 

成功完成迁移后还需要执行一系列的迁移后任务，以确保所有操作尽可能顺利高效。

### <a name="remediate-applications"></a>修正应用程序 

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，实现这一点需要对应用程序进行更改。


### <a name="perform-tests"></a>执行测试

测试包括以下活动：

1. 开发验证测试：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。
1. 设置测试环境：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
1. 运行验证测试：针对源和目标运行验证测试，然后分析结果。
1. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。

## <a name="advanced-features"></a>高级功能 

请确保充分利用 Azure SQL 托管实例提供的基于云的高级功能，例如[内置高可用性](../../database/high-availability-sla.md)、[威胁检测](../../database/azure-defender-for-sql.md)以及[监视和优化工作负载](../../database/monitor-tune-overview.md)。 

只有将[数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)更改为最新的兼容性级别后，某些 SQL Server 功能才可用。 

## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的：

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[Db2 zOS 数据资产发现和评估包](https://www.microsoft.com/download/details.aspx?id=103108)|在数据库上运行 SQL 脚本后，可以将结果导出到文件系统上的文件。 支持多种文件格式（包括 \*.csv），方便你在外部工具（如电子表格）中捕获结果。 如果你想要与未安装工作台的团队轻松共享结果，此方法会很有用。|
|[IBM Db2 LUW 清单脚本和项目](https://www.microsoft.com/download/details.aspx?id=103109)|此资产包含一个 SQL 查询，该查询可访问 IBM Db2 LUW 版本 11.1 系统表，并按架构和对象类型提供对象计数、每个架构中“原始数据”的粗略估计、每个架构中表的大小，以及 CSV 格式的存储结果。|
|[IBM Db2 到 SQL MI - 数据库比较实用工具](https://www.microsoft.com/download/details.aspx?id=103016)|数据库比较实用工具是可用于验证源和目标平台上的数据是否相同的 Windows 控制台应用程序。 可以使用该工具在所有或所选表、行和列中的行或列级别高效地比较数据。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="next-steps"></a>后续步骤

- 如需 Microsoft 与第三方服务和工具，以便在执行各种数据库和数据迁移方案时可为你提供帮助，请查看[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL 托管实例，请参阅：
   - [SQL 托管实例的概述](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅：
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负荷计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)。
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
