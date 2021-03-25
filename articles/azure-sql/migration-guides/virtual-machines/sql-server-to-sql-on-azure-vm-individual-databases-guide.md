---
title: 将 SQL Server 迁移到 Azure VM 上的 SQL Server（迁移指南）
description: 请按照本指南将单个 SQL Server 数据库迁移到 Azure 虚拟机 (VM) 上的 SQL Server。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: d95da29b732e2d520b3413628c9b4a1c403abed6
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488218"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>迁移指南：SQL Server 到 Azure VM 上的 SQL Server 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

本迁移指南介绍如何通过以下方式发现、评估用户数据库并将其从 SQL Server 迁移到 Azure 虚拟机 (VM) 上的 SQL Server 实例：使用备份和还原和利用[数据库迁移助手 (DMA)](/sql/dma/dma-overview) 进行评估的日志传送功能  。 

你可以迁移在本地或以下位置运行的 SQL Server：

- 虚拟机上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 关系数据库服务 (AWS RDS) 
- 计算引擎 (Google Cloud Platform - GCP)

有关其他迁移策略的信息，请参阅 [SQL Server VM 迁移概述](sql-server-to-sql-on-azure-vm-migration-overview.md)。

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="迁移流程":::

## <a name="prerequisites"></a>先决条件

迁移到 Azure VM 上的 SQL Server 需要以下各项： 

- [数据库迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)。
- [Azure Migrate 项目](../../../migrate/create-manage-projects.md)。
- Azure VM 上已准备好的[目标 SQL Server](../../virtual-machines/windows/create-sql-vm-portal.md)，其版本与源 SQL Server 相同或更高。
- [Azure 与本地之间的连接性](/azure/architecture/reference-architectures/hybrid-networking)。
- [选择适当的迁移策略](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)。

## <a name="pre-migration"></a>迁移前

在开始迁移之前，请先了解 SQL 环境的拓扑并评估所需迁移的可行性。 

### <a name="discover"></a>发现

Azure Migrate 会评估是否适合迁移本地计算机，会根据性能进行大小调整，并提供有关在本地运行的成本估算。 若要规划迁移，请使用 Azure Migrate 来[确定现有数据源以及 SQL Server 实例所用功能的相关详细信息](../../../migrate/concepts-assessment-calculation.md)。 此过程涉及扫描网络，以使用所用版本和功能标识组织中的所有 SQL Server 实例。 

> [!IMPORTANT]
> 为 SQL Server 实例选择目标 Azure 虚拟机时，请务必考虑 [Azure VM 上 SQL Server 的性能准则](../../virtual-machines/windows/performance-guidelines-best-practices.md)。

有关其他发现工具，请参阅可用于数据迁移方案的[服务和工具](../../../dms/dms-tools-matrix.md#business-justification-phase)。


### <a name="assess"></a>评估

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

发现所有数据源之后，请使用[数据迁移助手 (DMA)](/sql/dma/dma-overview) 来评估要迁移到 Azure VM 上 SQL Server 实例的本地 SQL Server 实例，以了解源实例和目标实例之间的差距。 


> [!NOTE]
> 如果不升级 SQL Server 的版本，请跳过此步骤并转到[迁移](#migrate)部分。 


#### <a name="assess-user-databases"></a>评估用户数据库 

数据迁移助手 (DMA) 可检测可能会影响新版本 SQL Server 中数据库功能的兼容性问题，从而帮助你迁移到新式数据平台。 DMA 为目标环境提供性能和可靠性改进建议，并可便于将架构、数据和登录对象从源服务器迁移到目标服务器。

请参阅[评估](/sql/dma/dma-migrateonpremsql)了解详细信息。 


> [!IMPORTANT]
>根据评估类型，源 SQL Server 上所需的权限可能有所不同。 
   > - 对于功能奇偶一致性顾问，提供的用于连接到源 SQL Server 数据库的凭据必须是 sysadmin 服务器角色的成员。
   > - 对于兼容性问题顾问，提供的凭据必须至少具有 `CONNECT SQL`、`VIEW SERVER STATE` 和 `VIEW ANY DEFINITION` 权限。
   > - 在运行评估之前，DMA 会突出显示所选顾问所需的权限。


#### <a name="assess-applications"></a>评估应用程序

通常，应用程序层访问用户数据库来保存和修改数据。  DMA 可以通过两种方式评估应用程序的数据访问层： 

- 使用捕获的[扩展事件](/sql/relational-databases/extended-events/extended-events)或对用户数据库的 [SQL Server Profiler 跟踪](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler)。 还可以使用[数据库实验助手](/sql/dea/database-experimentation-assistant-capture-trace)创建跟踪日志，该跟踪日志还可以用于 A/B 测试。

- 使用 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT)，该工具包在代码中提供发现和评估 SQL 查询的功能，并用于将应用程序源代码从一个数据库平台迁移到另一个数据库平台。 该工具支持各种常用的文件类型，包括 C# 和 Java、XML 和纯文本。 有关如何执行 DAMT 评估的指南，请参阅[使用 DMAT](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) 博客。

在评估用户数据库期间，使用 DMA [导入](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess)捕获的跟踪文件或 DAMT 文件。 


#### <a name="scale-assessments"></a>规模评估

如果有多个服务器需要进行 DMA 评估，则可以通过[命令行接口](/sql/dma/dma-commandline)自动执行该过程。 使用该接口，可以提前为迁移范围内的每个 SQL Server 实例准备评估命令。 

对于跨大型资产的摘要报告，现在可以[将数据迁移助手 (DMA) 评估合并到 Azure Migrate 中](/sql/dma/dma-assess-sql-data-estate-to-sqldb)。

#### <a name="refactor-databases-with-dma"></a>利用 DMA 重构数据库

系统基于 DMA 评估结果，可能会为你提供一系列建议，以确保用户数据库在迁移后能够正常运行。 DMA 提供了有关受影响对象的详细信息以及用于解决每个问题的资源。 建议在生产迁移之前解决所有中断性变更和行为更改。

对于不推荐使用的功能，如果想要避免进行这些更改并加快迁移速度，可以选择在其原始[兼容性](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)模式下运行用户数据库。 但是，这会阻止[升级数据库兼容性](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades)，直到解决不推荐使用的项为止。

在[迁移后](#post-migration)阶段，强烈建议编写所有 DMA 修补程序的脚本并将其应用于目标 SQL Server 数据库。

> [!CAUTION]
> 并非所有 SQL Server 版本都支持所有兼容性模式。 检查[目标 SQL Server 版本](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)是否支持所选的数据库兼容性。 例如，SQL Server 2019 不支持兼容性级别为 90 的数据库（也就是 SQL Server 2005）。 这些数据库至少需要升级到兼容性级别 100。
>

## <a name="migrate"></a>Migrate

完成预迁移步骤后，即可迁移用户数据库和组件。 使用首选的[迁移方法](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)迁移数据库。  

下面提供使用备份和还原执行迁移的步骤，或使用备份和还原以及日志传送来实现最短停机时间迁移的步骤。 

### <a name="backup-and-restore"></a>备份和还原

若要使用备份和还原执行标准迁移，请按照下列步骤操作： 

1. 根据你的要求，建立与 Azure VM 上目标 SQL Server 的连接。 请参阅[连接到 Azure 上的 SQL Server 虚拟机 (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)。
1. 暂停/停止那些使用要迁移的数据库的任何应用程序。 
1. 使用[单用户模式](/sql/relational-databases/databases/set-a-database-to-single-user-mode)确保用户数据库处于非活动状态。 
1. 执行到本地位置的完整数据库备份。
1. 使用远程桌面、[Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview)或 [AZCopy 命令行实用程序](../../../storage/common/storage-use-azcopy-v10.md)将本地备份文件复制到 VM（建议使用大于 2 TB 的备份）。
1. 将完整的数据库备份还原到 Azure VM 上的 SQL Server。

### <a name="log-shipping--minimize-downtime"></a>日志传送（最大限度地减少停机时间）

要使用备份、还原和日志传送执行最短的停机时间迁移，请按照下列步骤操作： 

1. 根据你的要求，建立与 Azure VM 上目标 SQL Server 的连接。 请参阅[连接到 Azure 上的 SQL Server 虚拟机 (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)。
1. 确保要迁移的本地用户数据库处于完整恢复模式或大容量日志恢复模式。
1. 执行到本地位置的完整数据库备份，并修改现有的任何完整数据库备份作业，以使用 [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) 关键字来保留日志链。
1. 使用远程桌面、[Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview)或 [AZCopy 命令行实用程序](../../../storage/common/storage-use-azcopy-v10.md)将本地备份文件复制到 VM（建议使用大于 1 TB 的备份）。
1. 在 Azure VM 的 SQL Server 上还原完整数据库备份。
1. 在本地数据库和 Azure VM 上的目标 SQL Server 之间设置[日志传送](/sql/database-engine/log-shipping/configure-log-shipping-sql-server)。 请不要重新初始化数据库，因为在前面的步骤中已经完成了此操作。
1. 直接转换到目标服务器。 
   1. 暂停/停止那些使用要迁移的数据库的应用程序。 
   1. 使用[单用户模式](/sql/relational-databases/databases/set-a-database-to-single-user-mode)确保用户数据库处于非活动状态。 
   1. 准备就绪后，对本地数据库执行日志传送控制的[故障转移](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server)，将其故障转移到 Azure VM 上的目标 SQL Server。



### <a name="migrating-objects-outside-user-databases"></a>迁移用户数据库外部的对象

要在迁移后无缝操作用户数据库，可能还需要其他 SQL Server 对象。 

下表提供了可在迁移用户数据库之前或之后完成的一系列组件和建议的迁移方法： 


| **功能** | 组件 | **迁移方法** |
| --- | --- | --- |
| **数据库** | 建模  | SQL Server Management Studio 的脚本 |
|| TempDB | 计划将 TempDB 移动到 [Azure VM 临时磁盘 (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) 以获得最佳性能。 请确保选择具有足够的本地 SSD 的 VM 大小来容纳 TempDB。 |
|| 具有文件流的用户数据库 |  使用[备份和还原](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore)方法进行迁移。 DMA 不支持具有文件流的数据库。 |
| **安全性** | SQL Server 和 Windows 登录名 | 使用 DMA [迁移用户登录名](/sql/dma/dma-migrateserverlogins)。 |
|| SQL Server 角色 | SQL Server Management Studio 的脚本 |
|| 加密提供程序 | 建议[改用 Azure Key Vault 服务](../../virtual-machines/windows/azure-key-vault-integration-configure.md)。 此过程使用 [SQL VM 资源提供程序](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)。 |
| **服务器对象** | 备份设备 | 通过 [Azure 备份服务](../../../backup/backup-sql-server-database-azure-vms.md)用数据库备份进行替换，或将备份写入 [Azure 存储](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md)（SQL Server 2012 SP1 CU2 及更高版本）。 此过程使用 [SQL VM 资源提供程序](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)。|
|| 链接服务器 | 使用 SQL Server Management Studio 编写脚本。 |
|| 服务器触发器 | 使用 SQL Server Management Studio 编写脚本。 |
| **复制** | 本地发布 | 使用 SQL Server Management Studio 编写脚本。 |
|| 本地订阅服务器 | 使用 SQL Server Management Studio 编写脚本。 |
| **Polybase** | Polybase | 使用 SQL Server Management Studio 编写脚本。 |
| **Management** | 数据库邮件 | 使用 SQL Server Management Studio 编写脚本。 |
| **SQL Server 代理** | 作业 | 使用 SQL Server Management Studio 编写脚本。 |
|| 警报 | 使用 SQL Server Management Studio 编写脚本。 |
|| 运算符 | 使用 SQL Server Management Studio 编写脚本。 |
|| 代理 | 使用 SQL Server Management Studio 编写脚本。 |
| **操作系统** | 文件存储、文件共享 | 记下 SQL Server 使用的任何其他文件或文件共享并在 Azure VM 目标上对其进行复制。 |


## <a name="post-migration"></a>迁移后

成功完成迁移阶段后，请执行一系列的迁移后任务，确保一切功能尽可能顺利高效地运行。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，实现这一点需要对应用程序进行更改。

将任何数据库迁移助手建议的修补程序应用于用户数据库。 建议为这些程序编写脚本，以确保一致性并允许实现自动化。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括执行以下活动：

1. **开发验证测试。**  使用 SQL 查询来测试数据库迁移。 创建对源数据库和目标数据库都运行的验证查询。 验证查询应涵盖已定义的范围。
2. **设置测试环境。**  测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
3. **运行验证测试。**  针对源和目标运行验证测试，然后分析结果。
4. **运行性能测试。**  针对源和目标运行性能测试，然后分析和比较结果。

> [!TIP]
> 借助[数据库实验助手 (DEA)](/sql/dea/database-experimentation-assistant-overview) 评估目标 SQL Server 的性能。
>

### <a name="optimize"></a>优化

对于协调任何与数据准确性和完整性有关的问题以及解决潜在的工作负载性能问题，迁移后阶段至关重要。

有关这些问题的详细信息和用于缓解这些问题的具体步骤，请参阅以下资源：

- [迁移后验证和优化指南。](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [优化 Azure SQL 虚拟机中的性能](../../virtual-machines/windows/performance-guidelines-best-practices.md)。
- [Azure 成本优化中心](https://azure.microsoft.com/overview/cost-optimization/)。

## <a name="next-steps"></a>后续步骤

- 若要检查适用于 SQL Server 的服务的可用性，请参阅 [Azure 全球基础结构中心](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- 如需在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助的 Microsoft 与第三方服务和工具的矩阵，请查看[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)一文。

- 若要详细了解 Azure SQL，请参阅：
   - [部署选项](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/) 


- 有关云迁移的框架和采用周期的详细信息，请参阅
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 有关许可的信息，请参阅
   - [通过 Azure 混合权益自带许可](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [获得对 SQL Server 2008 和 SQL Server 2008 R2 的免费外延支持](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- 若要评估应用程序访问层，请参阅 [Data Access Migration Toolkit（预览版）](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手](/sql/dea/database-experimentation-assistant-overview)。