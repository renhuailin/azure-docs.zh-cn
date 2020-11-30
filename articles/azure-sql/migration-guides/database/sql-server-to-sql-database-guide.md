---
title: SQL Server 到 SQL 数据库-迁移指南
description: 按照此指南将 SQL Server 数据库迁移到 Azure SQL Database。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: ee4abaf3c9f6aa70ba14920711c8917994254649
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326958"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>迁移指南： SQL Database SQL Server
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

本指南可帮助你将 SQL Server 实例迁移到 Azure SQL Database。 

你可以迁移 SQL Server 本地或上运行的： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS)  
- 计算引擎 (Google Cloud Platform GCP)   
- 适用于 SQL Server (Google Cloud Platform 的云 SQL – GCP)  

有关迁移的详细信息，请参阅 [迁移概述](sql-server-to-sql-database-overview.md)。 对于其他方案，请参阅 [数据库迁移指南](https://datamigration.microsoft.com/)。

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="迁移过程":::

## <a name="prerequisites"></a>先决条件 

若要将 SQL Server 迁移到 Azure SQL 数据库，请确保满足以下先决条件： 

- 选择的 [迁移方法](sql-server-to-sql-database-overview.md#compare-migration-options) 和相应的工具 
- [数据迁移助手 (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595) 安装在可连接到源的计算机上 SQL Server
- 目标 [AZURE SQL 数据库](../../database/single-database-create-quickstart.md)


## <a name="pre-migration"></a>预迁移

验证源环境是否受支持后，请从预迁移阶段开始。 发现所有现有数据源，评估迁移可行性，并识别可能会妨碍迁移的任何阻碍性问题。 

### <a name="discover"></a>发现

在 "发现" 阶段，扫描网络以确定组织使用的所有 SQL Server 实例和功能。 

使用 [Azure Migrate](../../../migrate/migrate-services-overview.md) 评估本地服务器的迁移适用性、执行基于性能的大小调整，并提供在 Azure 中运行这些服务所需的成本估计。 

另外，还可以使用 [Microsoft 评估和规划工具包 ("地图工具包" ) ](https://www.microsoft.com/download/details.aspx?id=7826) 来评估当前 IT 基础结构。 该工具包提供了一个功能强大的清单、评估和报告工具，用于简化迁移规划过程。 

有关可用于发现阶段的工具的详细信息，请参阅 [可用于数据迁移方案的服务和工具](../../../dms/dms-tools-matrix.md)。 

### <a name="assess"></a>评估 

发现数据源后，评估可迁移到 Azure SQL Database 的任何本地 SQL Server 数据库 () ，以确定迁移阻止程序或兼容性问题。 

你可以使用数据迁移助手 (版本4.1 和更高版本) 来评估要获取的数据库： 

- [Azure 目标建议](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 建议](/sql/dma/dma-sku-recommend-sql-db)

若要使用数据库迁移评估评估你的环境，请执行以下步骤： 

1. 打开 [ (DMA) 数据迁移助手 ](https://www.microsoft.com/download/details.aspx?id=53595)。 
1. 选择 " **文件** "，然后选择 " **新建评估**"。 
1. 指定项目名称，选择 "SQL Server" 作为 "源服务器类型"，然后选择 "Azure SQL 数据库" 作为 "目标服务器类型"。 
1. 选择要生成的评估报告) 类型 (。 例如，数据库兼容性和功能奇偶校验。 根据评估类型，SQL Server 源上所需的权限可能有所不同。  在运行评估之前，DMA 将突出显示所选顾问所需的权限。
    - **功能奇偶校验** 类别在 Azure 中提供了一套全面的建议、备选方案，以及可帮助你规划迁移项目的缓解步骤。 需要 (sysadmin 权限) 
    - **兼容性问题** 类别标识部分支持或不受支持的功能兼容性问题，这些问题可能会阻止迁移，并 `CONNECT SQL` 提供 (、 `VIEW SERVER STATE` 和 `VIEW ANY DEFINITION` 权限所需的建议) 。
1. 指定 SQL Server 的源连接详细信息并连接到源数据库。
1. 选择 " **开始评估**"。 
1. 完成此过程后，请选择并查看针对迁移阻止和功能奇偶校验问题的评估报告。 评估报表还可以导出到可与组织中的其他团队或人员共享的文件。 
1. 确定最大程度减少迁移后工作的数据库兼容性级别。  
1. 确定用于本地工作负荷的最佳 Azure SQL 数据库 SKU。 

若要了解详细信息，请参阅 [使用数据迁移助手执行 SQL Server 迁移评估](/sql/dma/dma-assesssqlonprem)。

如果评估遇到多个阻止程序来确认你的数据库未准备好进行 Azure SQL 数据库迁移，则还可以考虑：

- 如果有多个实例范围的依赖项，则[AZURE SQL 托管实例](../managed-instance/sql-server-to-managed-instance-overview.md)
- 如果 SQL 数据库和 SQL 托管实例的目标都不合适，请[在 Azure 虚拟机上 SQL Server](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) 。 



#### <a name="scaled-assessments-and-analysis"></a>扩展的评估和分析
数据迁移助手支持执行扩展的评估和合并评估报表以进行分析。 

如果有多个服务器和数据库需要大规模评估和分析以提供更广泛的数据空间视图，请参阅以下链接了解详细信息：

- [使用 PowerShell 执行缩放评估](/sql/dma/dma-consolidatereports)
- [使用 Power BI 分析评估报告](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> 如果对多个数据库（尤其是大型数据库）运行大规模评估，还可以使用 [DMA 命令行实用程序](/sql/dma/dma-commandline) 自动执行，并将其上传到 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) ，以便进一步分析和实现目标。

## <a name="migrate"></a>迁移

完成与预迁移阶段相关的任务后，便可以执行架构和数据迁移。 

使用所选 [迁移方法](sql-server-to-sql-database-overview.md#compare-migration-options)迁移数据。 

本指南介绍了两种最常用的选项-数据迁移助手和 Azure 数据库迁移服务。 

### <a name="data-migration-assistant-dma"></a>数据迁移助手 (DMA)

若要使用 DMA 将数据库从 SQL Server 迁移到 Azure SQL 数据库，请执行以下步骤： 

1. 下载并安装 [数据库迁移助手](https://www.microsoft.com/download/details.aspx?id=53595)。
1. 创建一个新项目，并选择 " **迁移** " 作为项目类型。
1. 将 "源服务器类型" 设置为 " **SQL Server** "，将 "目标服务器类型" 设置为 " **Azure SQL 数据库**"，选择 "将迁移范围作为 **架构和数据** " 并选择 " **创建**"
1. 在迁移项目中，指定源服务器详细信息，如服务器名称、用于连接到服务器的凭据以及要迁移的源数据库。
1. 在 "目标服务器详细信息" 中，指定 Azure SQL 数据库服务器名称、用于连接到服务器的凭据以及要迁移到的目标数据库。
1. 选择架构对象并将其部署到目标 Azure SQL 数据库。
1. 最后，选择 " **启动数据迁移** " 并监视迁移的进度。

有关详细教程，请参阅 [使用数据迁移助手将本地 SQL Server 或 Azure vm 上的 SQL Server 迁移到 AZURE SQL Database](/sql/dma/dma-migrateonpremsqltosqldb)。 


> [!NOTE]
> - 在导入过程中，将数据库缩放到更高的服务层和计算大小，以通过提供更多资源来最大程度地提高导入速度。 然后，可以在导入成功后进行缩减。</br>
> - 导入的数据库的兼容级别基于源数据库的兼容性级别。 


### <a name="azure-database-migration-service-dms"></a>Azure 数据库迁移服务 (DMS)

若要使用 DMS 将数据库从 SQL Server 迁移到 Azure SQL 数据库，请执行以下步骤：

1. 如果尚未这样做，请在订阅中注册 **microsoft.datamigration** 资源提供程序。 
1. 在所选的所需位置创建 Azure 数据库迁移服务实例， (最好与目标 Azure SQL 数据库) 在同一区域中。 选择现有的虚拟网络，或创建一个新的虚拟网络来托管 DMS 实例。
1. 创建 DMS 实例后，创建新的迁移项目，并将源服务器类型指定为 " **SQL Server** "，并将目标服务器类型指定为 " **Azure SQL 数据库**"。 在 "迁移项目创建" 边栏选项卡中选择 " **脱机数据迁移** " 作为 "活动类型"。
1. 在 "迁移 **源** 详细信息" 页上指定源 SQL Server 详细信息，并在 " **迁移目标** 详细信息" 页上指定目标 Azure SQL 数据库详细信息。
1. 映射用于迁移的源数据库和目标数据库，然后选择要迁移的表。
1. 查看迁移摘要，然后选择 " **运行迁移**"。 然后，你可以监视迁移活动，并检查数据库迁移的进度。

有关详细教程，请参阅 [使用 DMS 将 SQL Server 迁移到 AZURE SQL Database](../../../dms/tutorial-sql-server-to-azure-sql.md)。 

## <a name="data-sync-and-cutover"></a>数据同步和切换

使用连续复制/同步从源到目标的数据更改的迁移选项时，源数据和架构可能会发生更改，并会与目标保持同步。 在数据同步过程中，请确保在迁移过程中捕获对源的所有更改并将其应用到目标。 

验证源和目标上的数据是否相同后，可以从源环境切换到目标环境。 请务必规划企业/应用程序团队的转换过程，以确保在切换时的最小中断不会影响业务连续性。 

> [!IMPORTANT]
> 有关使用 DMS 作为迁移的一部分执行转换的特定步骤的详细信息，请参阅 [执行迁移](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover)转换。


## <a name="post-migration"></a>迁移后

成功完成迁移阶段后，请完成一系列的迁移后任务，以确保一切顺利有效地运行。 

迁移后阶段对于协调任何数据准确性问题和验证完整性以及解决工作负荷的性能问题至关重要。 

### <a name="remediate-applications"></a>修正应用程序 

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，需要更改应用程序。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括以下活动：

1. 开发验证测试：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。
1. 设置测试环境：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
1. 运行验证测试：针对源和目标运行验证测试，然后分析结果。
1. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。

   > [!NOTE]
   > 为帮助开发和运行迁移后验证测试，请考虑使用合作伙伴 [QuerySurge](https://www.querysurge.com/company/partners/microsoft) 提供的数据质量解决方案。 


## <a name="leverage-advanced-features"></a>利用高级功能 

请确保利用 SQL 数据库提供的基于云的高级功能，例如 [内置的高可用性](../../database/high-availability-sla.md)、 [威胁检测](../../database/azure-defender-for-sql.md)以及 [监视和优化工作负荷](../../database/monitor-tune-overview.md)。 

某些 SQL Server 功能只有在 [数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 更改为最新兼容级别 (150) 时才可用。 

若要了解详细信息，请参阅 [迁移后管理 AZURE SQL 数据库](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>后续步骤

- 有关可用于帮助你完成各种数据库和数据迁移方案以及专业任务的 Microsoft 和第三方服务和工具的矩阵，请参阅 [数据迁移的服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要了解有关 SQL 数据库的详细信息，请参阅：
    - [Azure SQL Database 概述](../../database/sql-database-paas-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要了解有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [成本调整和大小调整工作负荷迁移到 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [数据访问迁移工具包 (预览) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 有关如何执行数据访问层 A/B 测试的详细信息，请参阅 [数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。