---
title: 备份和还原 - Azure Database for MySQL
description: 了解如何自动备份和还原 Azure Database for MySQL 服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.custom: references_regions
ms.openlocfilehash: a3ef9a8f56f091e7be93e940f9e359adef15cb7f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748211"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中进行备份和还原

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL 可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可以用来将服务器还原到某个时间点。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

## <a name="backups"></a>备份

Azure Database for MySQL 对数据文件和事务日志进行备份。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以[选择将其配置](howto-restore-server-portal.md#set-backup-configuration)为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

这些备份文件不公开给用户，因此无法导出。 这些备份只能用于 Azure Database for MySQL 中的还原操作。 可以使用 [mysqldump](concepts-migrate-dump-restore.md) 复制数据库。

备份类型和频率取决于服务器的后端存储。

### <a name="backup-type-and-frequency"></a>备份类型和频率

#### <a name="basic-storage-servers"></a>基本存储服务器

基本存储是支持[基本层服务器](concepts-pricing-tiers.md)的后端存储。 基本存储服务器上的备份是基于快照的。 每天执行一次完整数据库快照。 不会对基本存储服务器执行任何差异备份，所有快照备份都仅为完整数据库备份。

事务日志备份每五分钟进行一次。

#### <a name="general-purpose-storage-v1-servers-supports-up-to-4-tb-storage"></a>常规用途存储 v1 服务器（最高支持 4 TB 存储）

常规用途存储是支持[常规用途](concepts-pricing-tiers.md)和[内存优化层](concepts-pricing-tiers.md)服务器的后端存储。 对于常规用途存储容量最大达 4 TB 的服务器，完整备份每周进行一次。 差异备份一天进行两次。 事务日志备份每五分钟进行一次。 存储容量最大达 4 TB 的常规用途存储上的备份不是基于快照的，并且在备份时会消耗 IO 带宽。 对于 4 TB 存储上的大型数据库 (> 1 TB)，建议考虑：

- 为备份 IO 预配更多 IOP
- 或者，如果基础存储基础结构在你的首选 [Azure 区域](./concepts-pricing-tiers.md#storage)中可用，则可以迁移到支持存储容量最大达 16 TB 的常规用途存储。 支持最大 16 TB 存储的常规用途存储不产生额外的费用。 若要获得有关迁移到 16 TB 存储的帮助，请从 Azure 门户创建支持工单。

#### <a name="general-purpose-storage-v2-servers-supports-up-to-16-tb-storage"></a>常规用途存储 v2 服务器（最高支持 16 TB 存储）

在 [Azure 区域](./concepts-pricing-tiers.md#storage)的一个子集中，所有新预配的服务器都可支持存储容量最大达 16 TB 的常规用途存储。 换句话说，对于支持该常规用途存储的所有[区域](concepts-pricing-tiers.md#storage)，常规用途存储容量默认最大为 16 TB。 16 TB 存储服务器上的备份是基于快照的。 第一次快照备份在创建服务器后立即进行计划。 快照备份每日执行一次。 事务日志备份每五分钟进行一次。

有关基本和常规用途存储的详细信息，请参阅[存储文档](./concepts-pricing-tiers.md#storage)。

### <a name="backup-retention"></a>备份保留

根据服务器上的备份保持期设置来保留备份。 可以选择 7 到 35 天的保留期。 默认保持期为 7 天。 可以在服务器创建期间或以后通过使用 [Azure 门户](./howto-restore-server-portal.md#set-backup-configuration)或 [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration) 更新备份配置来设置保留期。

备份保留期控制可以往回检索多长时间的时间点还原，因为它基于可用备份。 从恢复的角度来看，备份保留期也可以视为恢复时段。 在备份保留期间内执行时间点还原所需的所有备份都保留在备份存储中。 例如，如果备份保留期设置为 7 天，则可认为恢复时段是最近 7 天。 在这种情况下，将保留在过去 7 天内还原服务器所需的所有备份。 备份保留期为 7 天：

- 常规用途存储 v1 服务器（最高支持 4 TB 存储）将保留最多 2 个完整数据库备份、所有差异备份和自最早的完整数据库备份以来执行的事务日志备份。
- 常规用途存储 v2 服务器（最高支持 16 TB 存储）将保留过去 8 天内的完整数据库快照和事务日志备份。

#### <a name="long-term-retention"></a>长期保留

此服务目前暂不对长期保留备份（超出 35 天）提供原生支持。 可以选择使用 mysqldump 来进行备份并存储备份，以便进行长期保留。 我们的支持团队已通过博客发布实现此操作的[分步指南文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157)来共享如何此目标。

### <a name="backup-redundancy-options"></a>备份冗余选项

使用 Azure Database for MySQL 时，可以灵活地在“常规用途”层和“内存优化”层中选择本地冗余或异地冗余备份存储。 当备份存储在异地冗余备份存储中时，这些备份不仅会存储在托管服务器所在的区域中，还会复制到[配对的数据中心](../best-practices-availability-paired-regions.md)。 这种异地冗余可以在发生灾难时提供更好的保护，并且可以将服务器还原到其他区域。 “基本”层仅提供本地冗余备份存储。

> [!NOTE]
>对于以下区域（印度中部、法国中部、阿拉伯联合酋长国北部、南非北部），常规用途存储 v2 存储为公共预览版。 如果在上述区域在常规用途存储 v2（最高支持 16 TB 存储）中创建源服务器，则不支持启用异地冗余备份。 

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>从本地冗余备份存储迁移到异地冗余备份存储

只能在服务器创建期间为备份配置本地冗余或异地冗余存储。 预配服务器以后，不能更改备份存储冗余选项。 若要将备份存储从本地冗余存储移到异地冗余存储，则创建新服务器并使用[转储和还原](concepts-migrate-dump-restore.md)来迁移数据是唯一受支持的选项。

### <a name="backup-storage-cost"></a>备份存储成本

Azure Database for MySQL 最高可以提供 100% 的已预配服务器存储作为备份存储，不收取任何额外费用。 超出的备份存储使用量按每月每 GB 标准收费。 例如，如果为服务器配置了 250 GB 的存储空间，则可以为服务器备份提供 250 GB 的额外存储空间，而不另外收费。 备份所消耗的存储量超过 250GB 将按照[定价模型](https://azure.microsoft.com/pricing/details/mysql/)收费。

可以通过使用 Azure 门户中提供的 Azure Monitor 中的[已使用的备份存储](concepts-monitoring.md)指标来监视服务器使用的备份存储。 使用的备份存储指标表示根据为服务器设置的备份保留期保留的所有完整数据库备份、差异备份和日志备份所消耗的存储的总和。 备份的频率由服务进行管控，已在前面进行了说明。 无论数据库的总大小如何，如果服务器上的事务性活动繁重，都会导致备份存储使用率增加。 对于异地冗余存储，备份存储使用率是本地冗余存储的两倍。

控制备份存储成本的主要方法是设置适当的备份保留期，并选择正确的备份冗余选项以满足恢复目标。 可以选择 7 到 35 天的保留期。 常规用途和内存优化服务器可以选择使用异地冗余存储进行备份。

## <a name="restore"></a>还原

在 Azure Database for MySQL 中进行还原时，会根据原始服务器的备份创建新的服务器并还原服务器中包含的所有数据库。

可以使用两种类型的还原：

- **时间点还原** 可通过任一备份冗余选项使用，并利用完整备份和事务日志备份的组合在原始服务器所在区域中创建一个新服务器。
- **异地还原** 仅在你为服务器配置了异地冗余存储时可用，它允许你利用最近进行的备份将服务器还原到其他区域。

服务器恢复的估计时间取决于以下几个因素：
* 数据库的大小
* 所涉及的事务日志数
* 需要重新播放以恢复到还原点的活动数量
* 还原到不同区域时的网络带宽
* 目标区域中正在处理的并行还原请求数
* 数据库的表中是否存在主键。 若要加快恢复速度，请考虑为数据库中的所有表添加主键。 若要检查表是否有主键，可以使用以下查询：
```sql
select tab.table_schema as database_name, tab.table_name from information_schema.tables tab left join information_schema.table_constraints tco on tab.table_schema = tco.table_schema and tab.table_name = tco.table_name and tco.constraint_type = 'PRIMARY KEY' where tco.constraint_type is null and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') and tab.table_type = 'BASE TABLE' order by tab.table_schema, tab.table_name;
```
对于较大或非常活跃的数据库，还原可能要花费几个小时。 如果某个区域出现长时间的服务中断，则灾难恢复可能会发起大量的异地还原请求。 存在很多请求时，单个数据库的恢复时间可能会增加。 大部分数据库还原操作可在 12 小时内完成。

> [!IMPORTANT]
> 删除的服务器只能在删除备份后的五天内还原。 只能从托管服务器的 Azure 订阅访问和还原数据库备份。 若要还原已删除的服务器，请参阅[所述步骤](howto-restore-dropped-server.md)。 为了防止服务器资源在部署后遭意外删除或意外更改，管理员可以利用[管理锁](../azure-resource-manager/management/lock-resources.md)。

### <a name="point-in-time-restore"></a>时间点还原

可以还原到备份保留期中的任意时间点，不管备份冗余选项如何。 新服务器在原始服务器所在的 Azure 区域中创建。 它在创建时，使用原始服务器在定价层、计算的代、vCore 数、存储大小、备份保留期和备份冗余选项方面的配置。

> [!NOTE]
> 还原操作完成后，有两个服务器参数重置为默认值（而不是从主服务器复制）
>
> - time_zone - 此值设置为默认值“SYSTEM”
> - event_scheduler - 还原的服务器上的 event_scheduler 设置为“OFF”
>
> 需要通过重新配置[服务器参数](howto-server-parameters.md)来设置这些服务器参数

多种情况下可以使用时间点还原。 例如，用户意外删除了数据、删除了重要的表或数据库，或者应用程序因为缺陷而意外地使用错误数据覆盖了正确数据。

可能需要等到下一个事务日志备份进行后，才能还原到上一个五分钟内的某个时间点。

### <a name="geo-restore"></a>异地还原

如果已将服务器配置为进行异地冗余备份，则可将服务器还原到另一 Azure 区域，只要服务在该区域可用即可。 
- 常规用途存储 v1 服务器（最高支持 4 TB 存储）可以还原到异地配对的区域，也可以还原到支持 Azure Database for MySQL 单一服务器服务的任何 Azure 区域。
- 常规用途存储 v2 服务器（最高支持 16 TB 存储）只能还原到支持常规用途存储 v2 服务器基础结构的 Azure 区域。 查看 [Azure Database for MySQL 定价层](./concepts-pricing-tiers.md#storage)，以获取受支持区域的列表。

当服务器因其所在的区域发生事故而不可用时，异地还原是默认的恢复选项。 如果区域中出现的大规模事件导致数据库应用程序不可用，可以根据异地冗余备份将服务器还原到任何其他区域中的服务器。 异地还原利用服务器的最新备份。 提取备份后，会延迟一段时间才会将其复制到其他区域中。 此延迟可能长达一小时，因此发生灾难时，会有长达 1 小时的数据丢失风险。

> [!IMPORTANT]
>如果对新建的服务器执行异地还原，则初始备份同步可能需要 24 小时以上，具体取决于数据大小，因为初始完整快照备份的复制时间要长得多。 后续快照备份是增量备份，因此，在创建服务器 24 小时后，还原速度会更快。 如果你要评估异地还原以定义 RTO，建议你进行等待，在服务器创建 24 小时后再评估异地还原，以便更好地进行估算。

在异地还原过程中，可以更改的服务器配置包括计算的代、vCore、备份保持期和备份冗余选项。 不支持在异地还原过程中更改定价层（“基本”、“常规用途”或“内存优化”）或存储大小。

估计的恢复时间取决于若干因素，包括数据库大小、事务日志大小、网络带宽，以及在同一区域同时进行恢复的数据库总数。 恢复时间通常少于 12 小时。

### <a name="perform-post-restore-tasks"></a>执行还原后任务

从任一恢复机制还原后，都应执行以下任务，然后用户和应用程序才能重新运行：

- 如果需要使用新服务器来替换原始服务器，则请将客户端和客户端应用程序重定向到新服务器
- 对于要进行连接的用户，请确保设置适当的 VNet 规则。 不会从源服务器复制这些规则。
- 确保设置适当的登录名和数据库级权限
- 视情况配置警报

## <a name="next-steps"></a>后续步骤

- 若要详细了解业务连续性，请参阅 [业务连续性概述](concepts-business-continuity.md)。
- 若要使用 Azure 门户还原到某个时间点，请参阅 [使用 Azure 门户将服务器还原到某个时间点](howto-restore-server-portal.md)。
- 若要使用 Azure CLI 还原到某个时间点，请参阅 [使用 CLI 将服务器还原到某个时间点](howto-restore-server-cli.md)。
