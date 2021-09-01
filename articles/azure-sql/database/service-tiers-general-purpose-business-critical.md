---
title: 常规用途和业务关键服务层级
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 本文讨论 Azure SQL 数据库和 Azure SQL 托管实例使用的基于 vCore 的购买模型中的常规用途和业务关键服务层级。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 7/7/2021
ms.openlocfilehash: 56bd4dcc121b5ebd2ac48f772bd0793dffccd50d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289921"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL 数据库和 Azure SQL 托管实例服务层级
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 Azure SQL 数据库和 Azure SQL 托管实例中都提供了两个 [vCore](service-tiers-vcore.md) 服务层级：

- [常规用途](service-tier-general-purpose.md)层级是不太占用预算的层级，旨在满足性能和可用性要求一般的大多数工作负载的需求。
- [业务关键](service-tier-business-critical.md)层级旨在满足可用性要求苛刻的性能敏感型工作负载的需求。

Azure SQL 数据库还提供“超大规模”服务层级： 

- [超大规模](service-tier-hyperscale.md)层级旨在满足大多数业务工作负载的需求，提供高度可缩放的存储、读取扩展、快速缩放和快速数据库还原功能。

## <a name="service-tier-comparison"></a>服务层级比较

下表描述了各服务层级之间的主要差别。

|-| 资源类型 | 常规用途 | 超大规模 | 业务关键 |
|:---:|:---:|:---:|:---:|:---:|
| **最适用于** | |  提供以预算导向的、均衡的计算和存储选项。 | 大多数业务工作负荷。 自动缩放存储大小，最大可达 100 TB，流畅的垂直和水平计算缩放，快速数据库还原。 | 事务率较高、IO 延迟较低的 OLTP 应用程序。 使用多个同步更新的副本提供最高故障复原能力和快速故障转移。|
| **在以下资源类型中可用：** ||SQL 数据库/ SQL 托管实例 | 单一 Azure SQL 数据库 | SQL 数据库/ SQL 托管实例 |
| **计算大小**| SQL 数据库 | 1 - 80 个 vCore | 1 - 80 个 vCore | 1 - 128 个 vCore |
| | SQL 托管实例 | 4、8、16、24、32、40、64、80 个 vCore | 不适用 | 4、8、16、24、32、40、64、80 个 vCore |
| | SQL 托管实例池 | 2、4、8、16、24、32、40、64、80 个 vCore | 不适用 | 不适用 |
| **存储类型** | 全部 | 远程存储 | 分层的远程和本地 SSD 存储 | 本地 SSD 存储 |
| **数据库大小** | SQL 数据库 | 1 GB – 4 TB | 40 GB - 100 TB | 1 GB – 4 TB |
| | SQL 托管实例  | 32 GB – 8 TB | 不适用 | 32 GB – 4 TB |
| **存储大小** | SQL 数据库 | 1 GB – 4 TB | 40 GB - 100 TB | 1 GB – 4 TB |
| | SQL 托管实例  | 32 GB – 8 TB | 不适用 | 32 GB – 4 TB |
| **TempDB 大小** | SQL 数据库 | [每个 vCore 32 GB](resource-limits-vcore-single-databases.md) | [每个 vCore 32 GB](resource-limits-vcore-single-databases.md) | [每个 vCore 32 GB](resource-limits-vcore-single-databases.md) |
| | SQL 托管实例  | [每个 vCore 24 GB](../managed-instance/resource-limits.md#service-tier-characteristics) | 空值 | 最大 4 TB - [受存储大小限制](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **日志写入吞吐量** | SQL 数据库 | 单一数据库：[每个 vCore 4.5 MB/秒（最大 50 MB/秒）](resource-limits-vcore-single-databases.md) <br> 弹性池：[每个 vCore 6 MB/秒（最大 62.5 MB/秒）](resource-limits-vcore-elastic-pools.md)| 100 MB/秒 | 单一数据库：[每个 vCore 12 MB/秒（最大 96 MB/秒）](resource-limits-vcore-single-databases.md) <br> 弹性池：[每个 vCore 15 MB/秒（最大 120 MB/秒）](resource-limits-vcore-elastic-pools.md)|
| | SQL 托管实例 | [每个 vCore 3 MB/秒（最大 22 MB/秒）](../managed-instance/resource-limits.md#service-tier-characteristics) | 空值 | [每个 vCore 4 MB/秒（最大 48 MB/秒）](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**可用性**|全部| 99.99% |  [99.95%（具有一个次要副本），99.99%（具有更多副本）](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99.99% <br/> [99.995%（具有区域冗余单一数据库）](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**备份**|全部|RA-GRS，1-35 天（默认为 7 天） | RA-GRS，7 天，快速时间点恢复 (PITR) | RA-GRS，1-35 天（默认为 7 天） |
|**内存中 OLTP** | | 空值 | 部分支持。 支持内存优化表类型、表变量和原生编译模块。 | 可用 |
|**只读副本**| | 0 内置 <br> 0 - 4 使用[异地复制](active-geo-replication-overview.md) | 0 - 4 内置 | 1 内置，包含在价格中 <br> 0 - 4 使用[异地复制](active-geo-replication-overview.md) |
|**定价/计费** | SQL 数据库 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>IOPS 不收取费用。 | [每个副本的 vCore 和已用存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>尚未收费的 IOPS。 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>IOPS 不收取费用。 |
|| SQL 托管实例 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/managed/)收费。 <br/>IOPS 不收取费用| 不适用 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/managed/)收费。 <br/>IOPS 不收取费用。| 
|**折扣模型**| | [预留实例](reserved-capacity-overview.md)<br/>[Azure 混合权益](../azure-hybrid-benefit.md)（在开发/测试订阅中不可用）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和[即用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅| [Azure 混合权益](../azure-hybrid-benefit.md)（在开发/测试订阅中不可用）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和[即用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅| [预留实例](reserved-capacity-overview.md)<br/>[Azure 混合权益](../azure-hybrid-benefit.md)（在开发/测试订阅中不可用）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和[即用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅|

有关详细信息，请参阅 [Azure SQL 数据库 (vCore)](resource-limits-vcore-single-databases.md)、[单一 Azure SQL 数据库 (DTU)](resource-limits-dtu-single-databases.md)、[共用 Azure SQL 数据库 (DTU)](resource-limits-dtu-single-databases.md) 和 [Azure SQL 托管实例](../managed-instance/resource-limits.md)页，了解服务层级之间的详细差异。

> [!NOTE]
> 有关“超大规模”服务层级的信息，请参阅[“超大规模”服务层级](service-tier-hyperscale.md)。 有关基于 vCore 的购买模型与基于 DTU 的购买模型的比较，请参阅[购买模型和资源](purchasing-models.md)。

## <a name="data-and-log-storage"></a>数据和日志存储

以下因素会影响用于数据和日志文件的存储量，并适用于“常规用途”和“业务关键”层级。 有关“超大规模”服务层级中的数据和日志存储的详细信息，请参阅[“超大规模”服务层级](service-tier-hyperscale.md)。

- 每个计算大小支持一个最大数据大小（默认值为 32 GB）。
- 配置最大数据大小时，将自动为日志文件额外添加 30% 的存储。
- 可以选择介于 1 GB 与受支持最大存储大小之间的任意最大数据大小，以 1 GB 为增量。
- 在“常规用途”服务层级中，`tempdb` 使用本地 SSD 存储，此存储成本包含在 vCore 价格中。
- 在“业务关键”服务层级中，`tempdb` 与数据和日志文件共享本地 SSD 存储，`tempdb` 存储成本包含在 vCore 价格中。
- SQL 托管实例的最大存储大小必须指定为 32 GB 的倍数。

> [!IMPORTANT]
> 在“常规用途”和“业务关键”层级中，你将按照为数据库、弹性池或托管实例配置的最大存储大小付费。 在“超大规模”层级中，你将按照分配的数据存储付费。

若要监视 SQL 数据库中的当前已分配和已用数据存储大小，请分别使用 allocated_data_storage 和 storage Azure Monitor [指标](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) 。 若要监视 SQL 托管实例的已消耗实例存储大小总计，请使用 storage_space_used_mb [指标](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlmanagedinstances)。 若要使用 T-SQL 监视数据库中单个数据文件和日志文件的当前已分配和已用存储大小，请使用 [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) 视图和 [FILEPROPERTY(... , 'SpaceUsed')](/sql/t-sql/functions/fileproperty-transact-sql) 函数。

> [!TIP]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](file-space-manage.md)。

## <a name="backups-and-storage"></a>备份和存储

为数据库备份分配存储，以支持 SQL 数据库和 SQL 托管实例的[时间点还原 (PITR)](recovery-using-backups.md) 和[长期保留 (LTR)](long-term-retention-overview.md) 功能。 此存储独立于数据和日志文件存储，并单独计费。

- PITR：在“常规用途”和“业务关键”层级中，各个数据库备份将自动复制到[读取访问异地冗余 (RA-GRS) 存储](../../storage/common/geo-redundant-design.md)。 创建新备份时，存储大小动态递增。 存储将由完整备份、差异备份和事务日志备份使用。 存储消耗量取决于数据库变化率以及为备份配置的保留期。 对于 SQL 数据库，可为每个数据库单独配置 1 到 35 天的保留期；对于 SQL 托管实例，可配置 0 到 35 天的保留期。 提供与配置的最大数据大小相等的备份存储量，不收取额外费用。
- LTR：你还可以选择将完整备份的长期保留期配置为最多 10 年。 如果设置了 LTR 策略，则这些备份将自动存储在 RA-GRS 存储中，但你可以控制备份的复制频率。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 所选配置决定了多少存储将用于 LTR 备份。 有关详细信息，请参阅[长期备份保留](long-term-retention-overview.md)。

## <a name="next-steps"></a>后续步骤

有关 vCore 服务层级中提供的特定计算大小和存储大小的详细信息，请参阅： 

- [Azure SQL 数据库的基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)。
- [Azure SQL 数据库中共用数据库的基于 vCore 的资源限制](resource-limits-vcore-elastic-pools.md)。
- [Azure SQL 托管实例的基于 vCore 的资源限制](../managed-instance/resource-limits.md)。