---
title: 备份和还原 - Azure Database for PostgreSQL - 单一服务器
description: 了解如何自动备份和还原 Azure Database for PostgreSQL 服务器 - 单一服务器。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/29/2021
ms.openlocfilehash: 585a2cf1a3dcbc1c45ae40b728b28ece505efee3
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110788359"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL - 单一服务器中进行备份和还原

Azure Database for PostgreSQL 可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可以用来将服务器还原到某个时间点。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

## <a name="backups"></a>备份

Azure Database for PostgreSQL 对数据文件和事务日志进行备份。 根据支持的最大存储大小，我们会进行完整备份和差异备份（最大 4 TB 的存储服务器）或快照备份（最大 16 TB 的存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以选择将其配置为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

无法导出这些备份文件。 这些备份只能用于 Azure Database for PostgreSQL 中的还原操作。 可以使用 [pg_dump](howto-migrate-using-dump-and-restore.md) 复制数据库。

### <a name="backup-frequency"></a>备份频率

#### <a name="servers-with-up-to-4-tb-storage"></a>存储容量最大 4 TB 的服务器

对于支持最多 4 TB 存储容量的服务器，每周进行一次完整备份。 差异备份一天进行两次。 事务日志备份每五分钟进行一次。


#### <a name="servers-with-up-to-16-tb-storage"></a>存储容量最大 16 TB 的服务器

在 [Azure 区域](./concepts-pricing-tiers.md#storage)的子集中，所有新预配的服务器最多可以支持 16 TB 的存储容量。 这些大型存储服务器上的备份是基于快照的。 第一次完整快照备份在创建服务器后立即进行计划。 第一次完整快照备份将作为服务器的基准备份保留。 后续快照备份仅为差异备份。 差异快照备份不按固定计划进行。 在一天之内，将执行三次差异快照备份。 事务日志备份每五分钟进行一次。 

> [!NOTE]
> 对最多配置 4TB 存储配置的[副本服务器](./concepts-read-replicas.md)执行自动备份。

### <a name="backup-retention"></a>备份保留

根据服务器上的备份保持期设置来保留备份。 可以选择 7 到 35 天的保留期。 默认保持期为 7 天。 可以在服务器创建期间或以后通过使用 [Azure 门户](./howto-restore-server-portal.md#set-backup-configuration)或 [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration) 更新备份配置来设置保留期。 

备份保留期控制可以往回检索多长时间的时间点还原，因为它基于可用备份。 从恢复的角度来看，备份保留期也可以视为恢复时段。 在备份保留期间内执行时间点还原所需的所有备份都保留在备份存储中。 例如，如果备份保留期设置为 7 天，则认为恢复时段是最近 7 天。 在这种情况下，将保留在过去 7 天内还原服务器所需的所有备份。 备份保留期为 7 天：
- 存储容量最大达 4 TB 的服务器将保留最多 2 个完整数据库备份、所有差异备份和自最早的完整数据库备份以来执行的事务日志备份。
- 存储容量最大达 16 TB 的服务器将保留完整数据库快照、所有差异快照和过去 8 天的事务日志备份。

### <a name="backup-redundancy-options"></a>备份冗余选项

使用 Azure Database for PostgreSQL 时，可以灵活地在“常规用途”层和“内存优化”层中选择本地冗余或异地冗余备份存储。 当备份存储在异地冗余备份存储中时，这些备份不仅会存储在托管服务器所在的区域中，还会复制到[配对的数据中心](../best-practices-availability-paired-regions.md)。 这样可以在发生灾难时提供更好的保护，并且可以将服务器还原到其他区域。 “基本”层仅提供本地冗余备份存储。

> [!IMPORTANT]
> 只能在服务器创建期间为备份配置本地冗余或异地冗余存储。 预配服务器以后，不能更改备份存储冗余选项。

### <a name="backup-storage-cost"></a>备份存储成本

Azure Database for PostgreSQL 最高可以提供 100% 的已预配服务器存储作为备份存储，不收取任何额外费用。 超出的备份存储使用量按每月每 GB 标准收费。 例如，如果为服务器配置了 250 GB 的存储空间，则可以为服务器备份提供 250 GB 的额外存储空间，而不另外收费。 备份所消耗的存储量超过 250GB 将按照[定价模型](https://azure.microsoft.com/pricing/details/postgresql/)收费。

可以使用 Azure 门户中提供的 Azure Monitor 中的[已使用的备份存储](concepts-monitoring.md)指标来监视服务器使用的备份存储。 “已使用的备份存储”指标表示基于为服务器设置的备份保留期所保留的所有完整数据库备份、差异备份和日志备份所消耗的存储的总和。 备份的频率由服务进行管控，已在前面进行了说明。 无论数据库的总大小如何，如果服务器上的事务性活动繁重，都会导致备份存储使用率增加。 对于异地冗余存储，备份存储使用率是本地冗余存储的两倍。 

控制备份存储成本的主要方法是设置适当的备份保留期，并选择正确的备份冗余选项以满足恢复目标。 可以选择 7 到 35 天的保留期。 常规用途和内存优化服务器可以选择使用异地冗余存储进行备份。

## <a name="restore"></a>还原

在 Azure Database for PostgreSQL 中进行还原时，会根据原始服务器的备份创建新的服务器。 

可以使用两种类型的还原：

- **时间点还原**：可以与任一备份冗余选项配合使用，所创建的新服务器与原始服务器位于同一区域。
- **异地还原**：只能在已将服务器配置为进行异地冗余存储的情况下使用，用于将服务器还原到另一区域。

估计的恢复时间取决于若干因素，包括数据库大小、事务日志大小、网络带宽，以及在同一区域同时进行恢复的数据库总数。 恢复时间因上次数据备份和需要执行恢复量的不同而异。 通常不到 12 个小时。

> [!NOTE] 
> 如果源 PostgreSQL 服务器使用客户管理的密钥进行加密，请参阅这篇[文档](concepts-data-encryption-postgresql.md)来了解额外的注意事项。 

> [!NOTE]
> 若要还原已删除的 PostgreSQL 服务器，请遵循[此处](howto-restore-dropped-server.md)的过程。

### <a name="point-in-time-restore"></a>时间点还原

可以还原到备份保留期中的任意时间点，不管备份冗余选项如何。 新服务器在原始服务器所在的 Azure 区域中创建。 它在创建时，使用原始服务器在定价层、计算的代、vCore 数、存储大小、备份保留期和备份冗余选项方面的配置。

多种情况下可以使用时间点还原。 例如，用户意外删除了数据、删除了重要的表或数据库，或者应用程序因为缺陷而意外地使用错误数据覆盖了正确数据。

可能需要等到下一个事务日志备份进行后，才能还原到上一个五分钟内的某个时间点。

若要还原已删除的表，请执行以下操作： 
1. 使用时间点方法还原源服务器。
2. 使用 `pg_dump` 从还原的服务器转储表。
3. 在原始服务器上重命名源表。
4. 在原始服务器上，使用 psql 命令行来导入表。
5. 可以视需要选择删除还原的服务器。

>[!Note]
> 建议不要在同一时间为同一台服务器创建多个还原。 

### <a name="geo-restore"></a>异地还原

如果已将服务器配置为进行异地冗余备份，则可将服务器还原到另一 Azure 区域，只要服务在该区域可用即可。 支持存储容量最大达 4 TB 的服务器可以还原到异地配对区域，也可以还原到支持存储容量最大达 16 TB 的任何区域。 对于支持存储容量最大达 16 TB 的服务器，也可以在支持 16 TB 服务器的任何区域中还原异地备份。 查看 [Azure Database for PostgreSQL 定价层](concepts-pricing-tiers.md)，以获取受支持区域的列表。

当服务器因其所在的区域发生事故而不可用时，异地还原是默认的恢复选项。 如果区域中出现的大规模事件导致数据库应用程序不可用，可以根据异地冗余备份将服务器还原到任何其他区域中的服务器。 提取备份后，会延迟一段时间才会将其复制到其他区域中。 此延迟可能长达一小时，因此发生灾难时，会有长达 1 小时的数据丢失风险。

在异地还原过程中，可以更改的服务器配置包括计算的代、vCore、备份保持期和备份冗余选项。 不支持更改定价层（“基本”、“常规用途”或“内存优化”）或存储大小。

> [!NOTE]
> 如果源服务器使用基础结构双重加密，那么对于还原服务器，会有一些限制，包括可用区域。 如需了解更多详情，请参阅[基础结构双重加密](concepts-infrastructure-double-encryption.md)。

### <a name="perform-post-restore-tasks"></a>执行还原后任务

从任一恢复机制还原后，都应执行以下任务，然后用户和应用程序才能重新运行：

- 如果需要使用新的服务器来替换原始服务器，请将客户端和客户端应用程序重定向到新服务器。 将用户名也更改为 `username@new-restored-server-name`。
- 对于要进行连接的用户，请确保设置适当的服务器级防火墙规则和 VNet 规则。 不会从源服务器复制这些规则。
- 确保设置适当的登录名和数据库级权限
- 视情况配置警报

## <a name="next-steps"></a>后续步骤

- 了解如何使用  [Azure 门户](howto-restore-server-portal.md)进行还原。
- 了解如何使用  [Azure CLI](howto-restore-server-cli.md) 进行还原。
- 若要详细了解业务连续性，请参阅 [业务连续性概述](concepts-business-continuity.md)。