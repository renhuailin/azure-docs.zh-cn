---
title: 将 SQL Server 迁移到 Azure SQL 托管实例：迁移指南
description: 本指南介绍如何将 SQL Server 数据库迁移至 Azure SQL 托管实例。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 06/25/2021
ms.openlocfilehash: 9d1ae8214467d38958597136877baca0309b2ffc
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675258"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>迁移指南：将 SQL Server 到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

本指南帮助你将 SQL Server 实例迁移至 Azure SQL 托管实例。 

你可以迁移在本地或以下位置运行的 SQL Server： 

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS) 
- 计算引擎 (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform - GCP) 

如需更多迁移信息，请参阅[迁移概述](sql-server-to-managed-instance-overview.md)。 有关其他迁移指南，请参阅[数据库迁移](/data-migration)。 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="迁移流程":::

## <a name="prerequisites"></a>先决条件 

若要将 SQL Server 迁移至 Azure SQL 托管实例，请确保已执行以下操作： 

- 选定[迁移方法](sql-server-to-managed-instance-overview.md#compare-migration-options)和适用于此方法的相应工具。
- 在可以连接到源 SQL Server 的计算机上安装[数据迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)。
- 创建一个目标 [Azure SQL 托管实例](../../managed-instance/instance-create-quickstart.md)
- 配置连接和适当权限以访问源和目标。 
- 查看 [Azure SQL 托管实例中可用](../../database/features-comparison.md)的 SQL Server 数据库引擎功能。 


## <a name="pre-migration"></a>迁移前

验证你的源环境是否受支持后，开始预迁移阶段。 发现所有现有数据源，评估迁移可行性，确定可能会妨碍迁移的任何阻碍性问题。  

### <a name="discover"></a>发现

在“发现”阶段，扫描网络以查明你的组织使用的所有 SQL Server 实例和功能。 

使用 [Azure Migrate](../../../migrate/migrate-services-overview.md) 来评估本地服务器的迁移适用性，执行基于性能的大小调整，并提供在 Azure 中运行服务器的成本估算。 

或者，使用  [Microsoft 评估和规划工具包（“MAP 工具包”）](https://www.microsoft.com/download/details.aspx?id=7826)来评估当前的 IT 基础结构。 该工具包提供了功能强大的清单、评估和报告工具，可以简化迁移规划过程。 

若要详细了解可用于“发现”阶段的工具，请参阅[可用于数据迁移方案的服务和工具](../../../dms/dms-tools-matrix.md)。 

发现数据源后，评估可迁移到 Azure SQL 托管实例的任何本地 SQL Server 实例，以确定迁移阻碍或兼容性问题。
继续执行以下步骤，以评估并迁移数据库到 Azure SQL 托管实例：

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-sql-managed-instance-steps.png" alt-text="迁移到 Azure SQL 托管实例的步骤":::

- [评估 SQL 托管实例兼容性](#assess)，应确保不存在可能阻止迁移的阻塞性问题。
  此步骤还包括创建[性能基线](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline)来确定源 SQL Server 实例上的资源用量。 如需部署大小合适的托管实例并验证迁移之后的性能是否未受影响，则需要执行此步骤。
- [选择应用连接选项](../../managed-instance/connect-application-instance.md)。
- [部署到大小最适合的托管实例](#deploy-to-an-optimally-sized-managed-instance)，选择托管实例的技术特征（vCore 数目、内存量）和性能层（“业务关键”、“常规用途”）。
- [选择迁移方法](sql-server-to-managed-instance-overview.md#compare-migration-options)，然后使用脱机迁移或联机迁移选项在迁移数据库的地方进行迁移。
- [监视并修正应用程序](#monitor-and-remediate-applications)，以确保性能符合预期。


### <a name="assess"></a>评估 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

首先，确定 SQL 托管实例是否与应用程序的数据库要求相符。 SQL 托管实例旨在轻松地直接迁移大多数使用 SQL Server 的现有应用程序。 但是，有时可能需要用到一些目前尚不支持的功能，而实现某种解决方法的成本过高。 

你可以使用数据迁移助手（4.1 及更高版本）来评估要获取的数据库： 

- [Azure 目标建议](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 建议](/sql/dma/dma-sku-recommend-sql-db)

若要使用“数据库迁移评估”来评估你的环境，请执行以下步骤： 

1. 打开[数据迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)。 
1. 选择“文件”，然后选择“新建评估” 。 
1. 指定一个项目名称，选择“SQL Server”作为源服务器类型，然后选择“Azure SQL 托管实例”作为目标服务器类型。 
1. 选择要生成的评估报告的类型， 例如，数据库兼容性和功能奇偶一致性。 根据评估类型，源 SQL Server 上所需的权限可能有所不同。  在运行评估之前，DMA 会突出显示所选顾问所需的权限。
    - “功能奇偶一致性”类别提供了一套全面的建议、Azure 中可用的替代项以及缓解步骤来帮助你计划迁移项目 （需要 sysadmin 权限）。
    - “兼容性问题”类别标识了可能会阻止迁移的“部分支持或完全不支持”功能兼容性问题，以及用于解决这些问题的建议（需要 `CONNECT SQL`、`VIEW SERVER STATE` 和 `VIEW ANY DEFINITION` 权限）。
1. 指定 SQL Server 的源连接详细信息并连接到源数据库。
1. 选择“开始评估”。 
1. 完成此过程时后，选择并查看针对迁移阻止和功能奇偶一致性问题的评估报告。 还可以将评估报告导出到文件，以便与组织中的其他团队或人员共享。 
1. 确定可以最大程度地减少迁移后工作的数据库兼容性级别。  
1. 确定最适合本地工作负载的 Azure SQL 托管实例 SKU。 

若要了解详细信息，请参阅[使用数据迁移助手进行 SQL Server 迁移评估](/sql/dma/dma-assesssqlonprem)。

如果 SQL 托管实例不适合你的工作负载，则 Azure VM 上的 SQL Server 可能是适用于你的业务的可行替代目标。

#### <a name="scaled-assessments-and-analysis"></a>规模化评估和分析

数据迁移助手支持执行规模化评估以及对评估报告进行合并以进行分析。 如果你有多个服务器和数据库需要进行规模化评估和分析以提供更广泛的数据资产视图，请单击以下链接来了解详细信息。

- [使用 PowerShell 执行规模化评估](/sql/dma/dma-consolidatereports)
- [使用 Power BI 分析评估报告](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>还可以使用 [DMA 命令行实用程序](/sql/dma/dma-commandline)自动对多个数据库运行大规模评估，并将结果上传到 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) 以进一步进行分析和目标准备。

### <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到大小最适合的托管实例

根据发现和评估阶段中的信息，创建适当大小的目标 SQL 托管实例。 为此，你可以使用 [Azure 门户](../../managed-instance/instance-create-quickstart.md)、[PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md) 或 [Azure 资源管理器 (ARM) 模板](../../managed-instance/create-template-quickstart.md)。

SQL 托管实例是为要迁移到云中的本地工作负载量身定制的。 它引入了一个[购买模型](../../database/service-tiers-vcore.md)，在为工作负载选择适当的资源级别时可提供更高的灵活性。 在本地环境中，你可能习惯于使用物理核心和 IO 带宽来调整这些工作负荷的大小。 托管实例的购买模型以虚拟核心 (vCore) 为依据，同时单独提供更多存储和 IO 资源。 借助 vCore 模型可以更方便地根据当前在本地使用的计算资源，来了解云中的计算要求。 使用此新模型可以适当地调整云中目标环境的大小。 下面介绍了一些可帮助你选择适当服务层级和特征的常规指导：

- 根据基线 CPU 使用率，可以预配一个与 SQL Server 上使用的核心数匹配的托管实例，但请注意，可能需要缩放 CPU 特征才能匹配[安装了托管实例的 VM 的特征](../../managed-instance/resource-limits.md#hardware-generation-characteristics)。
- 根据基线内存使用率选择[具有匹配内存的服务层级](../../managed-instance/resource-limits.md#hardware-generation-characteristics)。 无法直接选择内存量，因此，需要选择 vCore 数目与内存匹配（例如，在 Gen5 中为 5.1 GB/vCore）的托管实例。
- 根据文件子系统的基线 IO 延迟，在“常规用途”（延迟大于 5 毫秒）与“业务关键”（延迟小于 3 毫秒）服务层级之间进行选择。
- 根据基线吞吐量预先分配数据或日志文件的大小，以获得预期的 IO 性能。

可在部署时选择计算和存储资源，稍后再使用 [Azure 门户](../../database/scale-resources.md)对其进行更改，而不必让应用程序关闭：

:::image type="content" source="media/sql-server-to-managed-instance-overview/managed-instance-sizing.png" alt-text="托管实例大小调整":::

若要了解如何创建 VNet 基础结构和托管实例，请参阅[创建托管实例](../../managed-instance/instance-create-quickstart.md)。

> [!IMPORTANT]
> 请务必根据[托管实例 VNet 要求](../../managed-instance/connectivity-architecture-overview.md#network-requirements)保留目标 VNet 和子网。 任何不兼容性问题都可能导致无法创建新实例或使用已创建的实例。 详细了解如何[新建](../../managed-instance/virtual-network-subnet-create-arm-template.md)网络和[配置现有](../../managed-instance/vnet-existing-add-subnet.md)网络。

## <a name="migrate"></a>迁移

完成与预迁移阶段相关的任务后，便可以执行架构和数据迁移。 

使用所选的[迁移方法](sql-server-to-managed-instance-overview.md#compare-migration-options)迁移你的数据。

SQL 托管实例面向需要从本地或 Azure VM 数据库实现迁移大量数据库的用户方案。 如果需要直接迁移定期使用实例级功能和/或跨数据库功能的应用程序的后端，则托管实例是最佳选择。 若采用此方案，可将整个实例转移到 Azure 中对应的环境，而无需重新构建应用程序。

若要转移 SQL 实例，需要认真规划：

- 迁移需要并置的所有数据库（在同一个实例上运行的数据库）。
- 迁移应用程序依赖的实例级对象，包括登录名、凭据、SQL 代理作业和操作员，以及服务器级触发器。

SQL 托管实例是一项托管服务，可便于将一些常规 DBA 活动委托给平台，就像平台中已内置这些活动一样。 因此，不需要迁移某些实例级数据，例如日常备份的维护作业或 Always On 配置，因为系统中内置了[高可用性](../../database/high-availability-sla.md)。

SQL 托管实例支持以下数据库迁移选项（目前仅支持这些迁移方法）：

- Azure 数据库迁移服务 - 几乎无需停机即可实现迁移。
- 本机 `RESTORE DATABASE FROM URL` - 使用来自 SQL Server 的本机备份，且需要停机一段时间。

本指南介绍了两个最常用的选项 - Azure 数据库迁移服务 (DMS) 以及本机备份和还原。

### <a name="database-migration-service"></a>数据库迁移服务

若要使用 DMS 进行迁移，请执行以下步骤：

1. 如果是第一次执行此操作，请在订阅中注册 Microsoft.DataMigration 资源提供程序。
1. 在你选择的所需位置（最好与目标 Azure SQL 托管实例位于同一区域）创建 Azure 数据库迁移服务实例，并选择一个现有虚拟网络或创建新的虚拟网络来托管 DMS 实例。
1. 创建 DMS 实例后，创建一个新的迁移项目，将源服务器类型指定为“SQL Server”，并将目标服务器类型指定为“Azure SQL 数据库托管实例”。 在项目创建边栏选项卡中选择活动类型 - 在线或离线数据迁移。 
1.  在“迁移源”详细信息页上指定源 SQL Server 详细信息，在“迁移目标”详细信息页上指定目标 Azure SQL 托管实例详细信息 。 选择“**下一页**”。
1. 选择要迁移的数据库。 
1. 提供配置设置以指定包含数据库备份文件的 SMB 网络共享。 将 Windows 用户凭据用于可访问网络共享的 DMS。 提供 Azure 存储帐户详细信息。 
1. 查看迁移摘要，然后选择“运行迁移”。 然后，你可以监视迁移活动，并检查数据库迁移进度。
1. 还原数据库后，选择“启动直接转换”。 若在 SMB 网络共享中提供了结尾日志备份并将其还原到目标位置，迁移过程就会复制该结尾日志备份。 
1. 停止进入源数据库的所有传入流量，并将连接字符串更新为新的 Azure SQL 托管实例数据库。 

有关此迁移选项的详细分步教程，请参阅[使用 DMS 在线将 SQL Server 迁移到 Azure SQL 托管实例](../../../dms/tutorial-sql-server-managed-instance-online.md)。 
   

### <a name="backup-and-restore"></a>备份和还原 

可让 Azure SQL 托管实例实现快速轻松的数据库迁移的关键功能之一是对存储在 [Azure 存储](https://azure.microsoft.com/services/storage/)上的数据库备份 (`.bak`) 文件进行本机还原。 备份和还原是基于数据库大小的异步操作。 

下图高度概括了该过程：

![此图显示了 SQL Server 使用标记为“备份/上传到 URL”箭头指向 Azure 存储，以及标记为“从 URL 还原”的第二个箭头从 Azure 存储指向 SQL 的托管实例。](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> 进行备份、将其上传到 Azure 存储以及对 Azure SQL 托管实例执行本机还原操作所花的时间取决于数据库的大小。 请为针对大数据库的操作预留足够的停机时间。 

下表提供了可以根据所运行的源 SQL Server 版本使用的方法的详细信息：

|步骤|SQL 引擎和版本|备份/还原方法|
|---|---|---|
|将备份放入 Azure 存储|早于 2012 SP1 CU2|将 .bak 文件直接上传到 Azure 存储|
| |2012 SP1 CU2 - 2016|使用已弃用的 [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) 语法直接备份|
| |2016 和更高版本|使用 [WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接备份|
|从 Azure 存储还原到托管实例| |[使用 SAS CREDENTIAL 执行 RESTORE FROM URL](../../managed-instance/restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - 使用本机还原选项将[透明数据加密](../../database/transparent-data-encryption-tde-overview.md)保护的数据库迁移到托管实例时，需要先迁移本地或 Azure VM SQL Server 中的相应证书，再还原数据库。 有关详细步骤，请参阅[将 TDE 证书迁移到托管实例](../../managed-instance/tde-certificate-migrate.md)。
> - 不支持还原系统数据库。 要迁移实例级对象（存储在 master 或 msdb 数据库中），我们建议编写 T-SQL 脚本，并在目标实例上运行这些脚本。

要使用备份和还原进行迁移，请执行以下步骤： 

1. 将数据库备份到 Azure blob 存储。 例如，在 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 中使用[备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。 使用 [Microsoft Azure Tools](https://go.microsoft.com/fwlink/?LinkID=324399) 支持早于 SQL Server 2012 SP1 CU2 的数据库。 
1. 使用 SQL Server Management Studio 连接到 Azure SQL 托管实例。 
1. 使用共享访问签名创建凭据，以通过数据库备份访问 Azure Blob 存储帐户。 例如：

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

1. 还原完成后，请在 SQL Server Management Studio 的“对象资源管理器”中查看数据库。 

若要详细了解此迁移选项，请参阅[使用 SSMS 将数据库还原到 Azure SQL 托管实例](../../managed-instance/restore-sample-database-quickstart.md)。

> [!NOTE]
> 数据库还原操作是异步且可重试的。 如果连接中断或某些超时过期，SQL Server Management Studio 中可能会显示错误。 Azure SQL 数据库将在后台继续尝试还原数据库，可以使用 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 视图来跟踪还原进度。


## <a name="data-sync-and-cutover"></a>数据同步和直接转换

当使用将数据更改持续从源复制/同步到目标的迁移选项时，源数据和架构可能会变化并偏离目标。 在数据同步过程中，请确保在迁移过程中捕获对源的所有更改并将其应用到目标。 

验证源和目标上的数据是否相同后，可以从源环境直接转换到目标环境。 请务必与业务/应用程序团队一起规划直接转换过程，以确保在直接转换过程中的最小中断不会影响业务连续性。 

> [!IMPORTANT]
> 有关与使用 DMS 进行迁移时执行直接转换相关的特定步骤的详细信息，请参阅[执行直接转换迁移](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover)。


## <a name="post-migration"></a>迁移后

成功完成迁移阶段后，执行一系列的迁移后任务，确保一切顺利高效地运行。 

迁移后阶段对于协调任何数据准确性问题、验证完整性以及解决工作负载的性能问题至关重要。 

### <a name="monitor-and-remediate-applications"></a>监视并修正应用程序 
迁移到托管实例后，应跟踪应用程序的行为和工作负载的性能。 此过程包括以下活动：

- 将[托管实例上运行的工作负载的性能](sql-server-to-managed-instance-performance-baseline.md#compare-performance)与[在源 SQL Server 实例上创建的性能基线](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline)进行比较。
- 持续[监视工作负荷的性能](sql-server-to-managed-instance-performance-baseline.md#monitor-performance)，以识别潜在问题并做出改善。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括以下活动：

1. 开发验证测试：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。
1. 设置测试环境：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
1. 运行验证测试：针对源和目标运行验证测试，然后分析结果。
1. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。


## <a name="leverage-advanced-features"></a>利用高级功能 

请确保充分利用 SQL 托管实例提供的基于云的高级功能，例如[内置高可用性](../../database/high-availability-sla.md)、[威胁检测](../../database/azure-defender-for-sql.md)以及[监视和优化工作负载](../../database/monitor-tune-overview.md)。 

使用 [Azure SQL Analytics](../../../azure-sql/database/monitor-tune-overview.md) 可以集中监视大量托管实例。

只有将[数据库兼容性级别](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)更改为最新的兼容性级别 (150) 后，某些 SQL Server 功能才可用。 


## <a name="next-steps"></a>后续步骤

- 有关在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请参阅[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL 托管实例，请参阅：
   - [Azure SQL 托管实例中的服务层级](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server 与 Azure SQL 托管实例之间的差异](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。
