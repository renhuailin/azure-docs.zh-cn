---
title: 从 Db2 到 Azure VM 上的 SQL Server：迁移指南
description: 本指南介绍了如何使用 Db2 的 SQL Server 迁移助手将 IBM Db2 数据库迁移到 Azure VM 上的 SQL Server。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 05/14/2021
ms.openlocfilehash: d82d423051af6a0cea8ab8b8fa5646ee80accf51
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746618"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>迁移指南：从 IBM Db2 到 Azure VM 上的 SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

本指南介绍了如何使用 Db2 的 SQL Server 迁移助手将用户数据库从 IBM Db2 迁移到 Azure VM 上的 SQL Server。 

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。 

## <a name="prerequisites"></a>先决条件

要将 Db2 数据库迁移到 SQL Server，需要：

- 验证[源环境是否受支持](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)。
- [适用于 Db2 的 SQL Server 迁移助手 (SSMA)](https://www.microsoft.com/download/details.aspx?id=54254)。
- 源环境与 Azure 中 SQL Server VM 之间的[连接](../../virtual-machines/windows/ways-to-connect-to-sql.md)。 
- [Azure VM 上的目标 SQL Server](../../virtual-machines/windows/create-sql-vm-portal.md)。 

## <a name="pre-migration"></a>预迁移

满足先决条件后即可查看环境中的拓扑并评估迁移的可行性。 

### <a name="assess"></a>评估 

使用 DB2 的 SSMA 查看数据库对象和数据并评估数据库是否适合迁移。 

要创建评估，请执行以下步骤：

1. 打开 [Db2 的 SSMA](https://www.microsoft.com/download/details.aspx?id=54254)。 
1. 选择“文件” > “新建项目” 。 
1. 提供项目名称和保存项目的位置。 然后从下拉列表中选择一个 SQL Server 迁移目标，再选择“确定”。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="该屏幕截图显示要指定的项目详细信息。":::


1. 在“连接到 Db2”上，输入值以查看 Db2 连接详细信息。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="显示用于连接到 Db2 实例的选项的屏幕截图。":::


1. 右键单击要迁移的 Db2 架构，然后选择“创建报表”。 这将生成一个 HTML 报表。 或者，可以在选择架构后，从导航栏中选择“创建报表”。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="屏幕截图，显示如何创建报表。":::

1. 查看 HTML 报表，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 Db2 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。

   例如：`drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`。 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="该报表屏幕截图显示任何错误或警告。":::


### <a name="validate-data-types"></a>验证数据类型

验证默认数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行下列步骤： 

1. 在菜单中，选择“工具”。 
1. 选择“项目设置”。 
1. 选择“类型映射”选项卡。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="该屏幕截图显示如何选择架构和类型映射。":::

1. 可通过在 Db2 元数据资源管理器中选择表来更改每个表的类型映射。 

### <a name="convert-schema"></a>转换架构 

要转换架构，请执行以下步骤：

1. （可选）向语句中添加动态或临时查询。 右键单击节点，然后选择“添加语句”。 
1. 选择“连接到 SQL Server”。 
    1. 输入连接详细信息，以连接到 Azure VM 上的 SQL Server 实例。 
    1. 选择连接到目标服务器上的现有数据库，或提供新名称以在目标服务器上创建新数据库。 
    1. 提供身份验证详细信息。 
    1. 选择“连接”。

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="该屏幕截图显示在 Azure VM 上连接至 SQL Server 所需的详细信息。":::

1. 右键单击架构，然后选择“转换架构”。 或者，可以在选择架构后，从顶部导航栏中选择“转换架构”。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="显示选择和转换架构的屏幕截图。":::

1. 完成架构转换后，比较并查看架构的结构以发现潜在问题。 根据建议解决问题。 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="显示比较并查看架构的结构以识别潜在问题的屏幕截图。":::

1. 在“输出”窗格中，选择“查看结果” 。 在“错误列表”窗格中，查看错误。 
1. 在本地保存该项目以进行脱机架构修正练习。 在“文件”菜单中选择“保存项目” 。 这样即有机会在将架构发布到 Azure VM 上的 SQL Server 之前，先对源和目标架构进行脱机评估并修正。

## <a name="migrate"></a>Migrate

完成对数据库的评估并解决任何分歧后，下一步就是执行迁移过程。

要发布架构并迁移数据，请执行以下步骤：

1. 发布架构。 在 SQL Server 元数据资源管理器的数据库节点中，右键单击数据库。  然后选择“与数据库同步”。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="显示“与数据库同步”选项的屏幕截图。":::

1. 迁移数据。 在 Db2 元数据资源管理器中，右键单击要迁移的数据库或对象，然后选择“迁移数据” 。 或者，可在导航栏中选择“迁移数据”。 若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="显示选择架构和选择迁移数据的屏幕截图。":::

1. 提供 Db2 和 SQL Server 实例的连接详细信息。 
1. 迁移完成后，查看“数据迁移报表”：  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="显示在何处查看数据迁移报表的屏幕截图。":::

1.  使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 Azure VM 上 SQL Server 的实例。 通过查看数据和架构来验证迁移。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="显示比较 SQL Server Management Studio 中的架构的屏幕截图。":::

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

## <a name="migration-assets"></a>迁移资产 

如需更多帮助，请参阅以下资源，这些资源是为支持实际迁移项目而开发的：

|资产  |说明  |
|---------|---------|
|[数据工作负荷评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130)| 此工具为给定的工作负荷提供了建议的“最佳匹配”目标平台、云就绪和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。|
|[Db2 zOS 数据资产发现和评估包](https://www.microsoft.com/download/details.aspx?id=103108)|在数据库上运行 SQL 脚本后，可以将结果导出到文件系统上的文件。 支持多种文件格式（包括 \*.csv），方便你在外部工具（如电子表格）中捕获结果。 如果你想要与未安装工作台的团队轻松共享结果，此方法会很有用。|
|[IBM Db2 LUW 清单脚本和项目](https://www.microsoft.com/download/details.aspx?id=103109)|此资产包含一个 SQL 查询，该查询可访问 IBM Db2 LUW 版本 11.1 系统表，并按架构和对象类型提供对象计数、每个架构中“原始数据”的粗略估计、每个架构中表的大小，以及 CSV 格式的存储结果。|
|[IBM Db2 到 SQL Server - 数据库比较实用工具](https://www.microsoft.com/download/details.aspx?id=103016)|数据库比较实用工具是可用于验证源和目标平台上的数据是否相同的 Windows 控制台应用程序。 可以使用该工具在所有或所选表、行和列中的行或列级别高效地比较数据。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="next-steps"></a>后续步骤

迁移后，请查看[迁移后验证和优化指南](/sql/relational-databases/post-migration-validation-and-optimization-guide)。 

如需 Microsoft 与第三方服务和工具以了解各种数据库和数据迁移方案，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

有关视频内容，请参阅[迁移过程概述](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)。
