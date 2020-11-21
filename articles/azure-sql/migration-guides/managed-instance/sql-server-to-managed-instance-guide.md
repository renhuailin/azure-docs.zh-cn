---
title: SQL Server SQL 托管实例-迁移指南
description: 按照此指南将 SQL Server 数据库迁移到 Azure SQL 托管实例。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5c20fbbe25b51160f42f233d30c39ccaec0f5cac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026038"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>迁移指南： SQL 托管实例 SQL Server
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

本指南可帮助你将 SQL Server 实例迁移到 Azure SQL 托管实例。 

你可以迁移 SQL Server 本地或上运行的： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS)  
- 计算引擎 (Google Cloud Platform GCP)   
- 适用于 SQL Server (Google Cloud Platform 的云 SQL – GCP)  

有关迁移的详细信息，请参阅 [迁移概述](sql-server-to-managed-instance-overview.md)。 对于其他方案，请参阅 [数据库迁移指南](https://datamigration.microsoft.com/)。

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="迁移过程":::

## <a name="prerequisites"></a>先决条件 

若要将 SQL Server 迁移到 Azure SQL 托管实例，请确保完成以下先决条件： 

- 选择 [迁移方法](sql-server-to-managed-instance-overview.md#compare-migration-options) 以及所选方法所需的相应工具
- 在可连接到源的计算机上安装 [数据迁移助手 (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595) SQL Server


## <a name="pre-migration"></a>预迁移

验证源环境是否受支持后，请从预迁移阶段开始。 发现所有现有数据源，评估迁移可行性，并识别可能会妨碍迁移的任何阻碍性问题。  

### <a name="discover"></a>发现

在 "发现" 阶段，扫描网络以确定组织使用的所有 SQL Server 实例和功能。 

使用 [Azure Migrate](../../../migrate/migrate-services-overview.md) 评估本地服务器的迁移适用性、执行基于性能的大小调整，并提供在 Azure 中运行这些服务所需的成本估计。 

另外，还可以使用 [Microsoft 评估和规划工具包 ("地图工具包" ) ](https://www.microsoft.com/download/details.aspx?id=7826) 来评估当前 IT 基础结构。 该工具包提供了一个功能强大的清单、评估和报告工具，用于简化迁移规划过程。 

有关可用于发现阶段的工具的详细信息，请参阅 [可用于数据迁移方案的服务和工具](../../../dms/dms-tools-matrix.md)。 

### <a name="assess"></a>评估 

发现数据源后，评估可迁移到 Azure SQL 托管实例的任何本地 SQL Server 实例 (s) ，以确定迁移阻止程序或兼容性问题。 

你可以使用数据迁移助手 (版本4.1 和更高版本) 来评估要获取的数据库： 

- [Azure 目标建议](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 建议](/sql/dma/dma-sku-recommend-sql-db)

若要使用数据库迁移评估评估你的环境，请执行以下步骤： 

1. 打开 [ (DMA) 数据迁移助手 ](https://www.microsoft.com/download/details.aspx?id=53595)。 
1. 选择 " **文件** "，然后选择 " **新建评估**"。 
1. 指定项目名称，选择 "SQL Server" 作为 "源服务器类型"，然后选择 "Azure SQL 托管实例作为目标服务器类型"。 
1. 选择要生成的评估报告) 类型 (。 例如，数据库兼容性和功能奇偶校验。 根据评估类型，SQL Server 源上所需的权限可能有所不同。  在运行评估之前，DMA 将突出显示所选顾问所需的权限。
    - **功能奇偶校验** 类别在 Azure 中提供了一套全面的建议、备选方案，以及可帮助你规划迁移项目的缓解步骤。 需要 (sysadmin 权限) 
    - **兼容性问题** 类别标识部分支持或不受支持的功能兼容性问题，这些问题可能会阻止迁移，并 `CONNECT SQL` 提供 (、 `VIEW SERVER STATE` 和 `VIEW ANY DEFINITION` 权限所需的建议) 。
1. 指定 SQL Server 的源连接详细信息并连接到源数据库。
1. 选择 " **开始评估**"。 
1. 完成此过程后，选择并查看针对迁移阻止和功能奇偶校验问题的评估报告。 评估报表还可以导出到可与组织中的其他团队或人员共享的文件。 
1. 确定最大程度减少迁移后工作的数据库兼容性级别。  
1. 确定用于本地工作负荷的最佳 Azure SQL 托管实例 SKU。 

若要了解详细信息，请参阅 [使用数据迁移助手执行 SQL Server 迁移评估](/sql/dma/dma-assesssqlonprem)。

如果 SQL 托管实例不适合你的工作负荷，Azure Vm 上的 SQL Server 可能是适用于你的企业的可行的替代目标。 

#### <a name="scaled-assessments-and-analysis"></a>扩展的评估和分析

数据迁移助手支持执行扩展的评估和合并评估报表以进行分析。 如果有多个服务器和数据库需要大规模评估和分析以提供更大的数据空间视图，请单击以下链接了解更多信息。

- [使用 PowerShell 执行缩放评估](/sql/dma/dma-consolidatereports)
- [使用 Power BI 分析评估报告](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>使用 [DMA 的命令行实用程序，还可以使用 DMA 的命令行实用程序](/sql/dma/dma-commandline) ，自动对多个数据库运行大规模评估，这还允许将结果上传到 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) 进行进一步分析和目标准备。

### <a name="create-a-performance-baseline"></a>创建性能基线

如果需要将 SQL 托管实例上工作负荷的性能与 SQL Server 上运行的原始工作负荷进行比较，请创建要用于比较的性能基线。 有关详细信息，请参阅 [性能基线](sql-server-to-managed-instance-performance-baseline.md) 。 

### <a name="create-sql-managed-instance"></a>创建 SQL 托管实例 

根据 "发现和评估" 阶段中的信息，创建适当大小的目标 SQL 托管实例。 可以使用 [Azure 门户](../../managed-instance/instance-create-quickstart.md)、 [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)或 [Azure 资源管理器 (ARM) 模板](/azure/azure-sql/managed-instance/create-template-quickstart)来实现此目的。 


## <a name="migrate"></a>迁移

完成与预迁移阶段相关的任务后，便可以执行架构和数据迁移。 

使用所选 [迁移方法](sql-server-to-managed-instance-overview.md#compare-migration-options)迁移数据。 

本指南介绍了两种最常用的选项-Azure 数据库迁移服务 (DM) 以及本机备份和还原。 

### <a name="database-migration-service"></a>数据库迁移服务

若要使用 DMS 执行迁移，请执行以下步骤：

1. 如果你是第一次执行此项，请在订阅中注册 **microsoft.datamigration** 资源提供程序。
1. 在所选的所需位置创建 Azure 数据库迁移服务实例 (最好与目标 Azure SQL 托管实例在同一区域中) 并选择现有虚拟网络，或创建新的虚拟网络来托管 DMS 实例。
1. 创建 DMS 实例后，创建新的迁移项目，并将源服务器类型指定为 **SQL Server** ，将目标服务器类型指定为 **Azure SQL 数据库托管实例**。 选择 "项目创建" 边栏选项卡-"联机" 或 "脱机数据迁移" 中的活动类型。 
1.  在 "迁移 **源** 详细信息" 页上指定源 SQL Server 详细信息，并在 " **迁移目标** 详细信息" 页上指定目标 Azure SQL 托管实例详细信息。 选择“**下一页**”。
1. 选择要迁移的数据库。 
1. 提供配置设置以指定包含数据库备份文件的 **SMB 网络共享** 。 通过 DMS 使用 Windows 用户凭据来访问网络共享。 提供 **Azure 存储帐户的详细信息**。 
1. 查看迁移摘要，然后选择 " **运行迁移**"。 然后，你可以监视迁移活动，并检查数据库迁移的进度。
1. 还原数据库后，选择 " **开始** 转换"。 一旦在 SMB 网络共享中提供结尾日志备份并将其还原到目标上，迁移过程就会复制结尾日志备份。 
1. 停止进入源数据库的所有传入流量，并将连接字符串更新为新的 Azure SQL 托管实例数据库。 

有关此迁移选项的详细分步教程，请参阅 [使用 DMS 将 SQL Server 迁移到 AZURE SQL 托管实例联机](/azure/dms/tutorial-sql-server-managed-instance-online)。 
   


### <a name="backup-and-restore"></a>备份和还原 

Azure SQL 托管实例的主要功能之一是在 Azure 存储上存储的数据库备份 () 文件的本机还原 `.bak` 。 [Azure Storage](https://azure.microsoft.com/services/storage/) 备份和还原是基于数据库大小的异步操作。 

下图高度概括了该过程：

![此图显示了 SQL Server 使用标记为“备份/上传到 URL”箭头指向 Azure 存储，以及标记为“从 URL 还原”的第二个箭头从 Azure 存储指向 SQL 的托管实例。](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> 进行备份的时间，将其上传到 Azure 存储，并根据数据库的大小对 Azure SQL 托管实例执行本机还原操作。 为大数据库提供足够的停机时间。 


若要使用备份和还原进行迁移，请执行以下步骤： 

1. 将数据库备份到 Azure blob 存储。 例如，在[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)中使用 "[备份到 url](/sql/relational-databases/backup-restore/sql-server-backup-to-url) "。 使用 [Microsoft Azure 工具](https://go.microsoft.com/fwlink/?LinkID=324399) 支持早于 SQL SERVER 2012 SP1 CU2 的数据库。 
1. 使用 SQL Server Management Studio 连接到 Azure SQL 托管实例。 
1. 使用共享访问签名创建凭据，以使用数据库备份访问 Azure Blob 存储帐户。 例如：

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. 从 Azure 存储 blob 容器还原备份。 例如： 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. 还原完成后，请在 SQL Server Management Studio 中查看 **对象资源管理器** 中的数据库。 

若要了解有关此迁移选项的详细信息，请参阅 [使用 SSMS 将数据库还原到 AZURE SQL 托管实例](https://docs.microsoft.com/azure/azure-sql/managed-instance/restore-sample-database-quickstart)。

> [!NOTE]
> 数据库还原操作是异步且可重试的。 如果连接中断或某些超时过期，SQL Server Management Studio 中可能会显示错误。 Azure SQL 数据库将在后台继续尝试还原数据库，可以使用 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 视图来跟踪还原进度。



## <a name="data-sync-and-cutover"></a>数据同步和切换

使用连续复制/同步从源到目标的数据更改的迁移选项时，源数据和架构可能会发生更改，并会与目标保持同步。 在数据同步过程中，请确保在迁移过程中捕获对源的所有更改并将其应用到目标。 

验证源和目标上的数据是否相同后，可以从源环境切换到目标环境。 请务必规划企业/应用程序团队的转换过程，以确保在切换时的最小中断不会影响业务连续性。 

> [!IMPORTANT]
> 有关使用 DMS 作为迁移的一部分执行转换的特定步骤的详细信息，请参阅 [执行迁移](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover)转换。


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

请确保充分利用 SQL 托管实例提供的基于云的高级功能，例如 [内置的高可用性](../../database/high-availability-sla.md)、 [威胁检测](../../database/advanced-data-security.md)、 [监视和优化工作负荷](../../database/monitor-tune-overview.md)。 

[Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) 允许以集中式方式监视大量托管实例。

某些 SQL Server 功能只有在 [数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 更改为最新兼容级别 (150) 时才可用。 


## <a name="next-steps"></a>后续步骤

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
