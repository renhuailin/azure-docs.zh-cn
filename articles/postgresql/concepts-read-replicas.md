---
title: 只读副本 - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍 Azure Database for PostgreSQL（单一服务器）中的只读副本功能。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: dc19b95e891235ac35c703adef50a23a9f70fbdb
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706790"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的只读副本

使用只读副本功能可将数据从 Azure Database for PostgreSQL 服务器复制到只读服务器。 副本通过 PostgreSQL 引擎的本机物理复制技术以异步方式更新。 可将主服务器中的数据最多复制到 5 个副本。

副本是新的服务器，可以像管理普通的 Azure Database for PostgreSQL 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

了解如何[创建和管理副本](howto-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本
只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 “读取”工作负载可以隔离到副本，而“写入”工作负载可以定向到主服务器。 只读副本也可以部署在不同的区域，并且在发生灾难恢复时，可以将其提升为读/写服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，因此它们不能直接减少主服务器上的写入容量负担。

### <a name="considerations"></a>注意事项
此功能适用于可接受滞后的方案，旨在减轻查询负担。 它不适用于同步复制方案，此类方案要求副本数据是最新数据。 主服务器与副本之间将存在明显的延迟。 这可能是几分钟甚至几小时，具体取决于工作负荷以及主服务器与副本之间的延迟。 副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。 

> [!NOTE]
> 对于大多数工作负荷，只读副本提供来自主服务器的准实时更新。 但是，对于持久的大量写入密集型主工作负荷，复制滞后时间可能会持续增长，并且可能永远都无法赶上主服务器。 这也可能会增加主服务器上的存储使用量，因为在副本收到 WAL 文件之前，这些文件不会被删除。 如果这种情况持续存在，则可以选择在写入密集型工作负荷完成后删除并重新创建只读副本，以使副本恢复到相对于滞后的良好状态。
> 异步只读副本不适合这种繁重的写入工作负荷。 评估应用程序的只读副本时，请在完整的应用工作负荷周期，通过其高峰时间和非高峰时间来监视副本上的滞后时间，以获取工作负荷周期各个点可能的滞后时间和预期的 RTO/RPO。
> 
## <a name="cross-region-replication"></a>跨区域复制
可以在与主服务器不同的区域中创建只读副本。 跨区域复制对于灾难恢复规划或使数据更接近用户等方案非常有用。

>[!NOTE]
> 基本层服务器仅支持相同区域复制。

你可以在任何 [Azure Database for PostgreSQL 区域](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)中拥有主服务器。 主服务器可以在其配对区域或通用副本区域中拥有副本。 下图显示了哪些副本区域可用，具体取决于你的主要区域。

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="读取副本区域":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>通用副本区域
无论主服务器位于何处，始终可以在以下任何区域中创建读取副本。 下面是通用副本区域：

澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、东亚、美国东部、美国东部2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、西欧、美国西部、美国西部2、美国中部。

### <a name="paired-regions"></a>配对区域
除通用副本区域外，还可以在主服务器的 Azure 配对区域中创建读取副本。 如果你不知道所在区域的配对，可以从 [Azure 配对区域](../best-practices-availability-paired-regions.md)一文中了解更多信息。

如果你使用跨区域副本进行灾难恢复规划，建议你在配对区域而不是其他某个区域中创建副本。 配对区域可避免同时更新，并优先考虑物理隔离和数据驻留。  

需要考虑以下限制： 

* 区域可用性： Azure Database for PostgreSQL 在法国中部、阿拉伯联合酋长国北部和德国中部提供。 但是，它们的配对区域不可用。
    
* 单向对：某些 Azure 区域仅在一个方向上配对。 这些区域包括印度西部、巴西南部。 
   这意味着印度西部的主服务器可以在印度南部创建副本。 但是，印度南部的主服务器无法在印度西部创建副本。 这是因为印度西部的次要区域是印度南部，但印度南部的次要区域不是印度西部。


## <a name="create-a-replica"></a>创建副本
启动“创建副本”工作流时，将创建空白的 Azure Database for PostgreSQL 服务器。 新服务器中填充了主服务器上的数据。 创建时间取决于主服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

每个副本都启用了存储[自动增长](concepts-pricing-tiers.md#storage-auto-grow)。 自动增长功能允许副本与复制到它的数据保持同步，并防止由于存储空间不足错误而导致的复制中断。

只读副本功能使用 PostgreSQL 的物理复制，而不使用逻辑复制。 使用复制槽位的流复制是默认的操作模式。 必要时，使用日志传送来跟上进度。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

如果源 PostgreSQL 服务器是用客户管理的密钥加密的，请参阅 [文档](concepts-data-encryption-postgresql.md) 以了解其他注意事项。

## <a name="connect-to-a-replica"></a>连接到副本
创建副本时，该副本不会继承主服务器的防火墙规则或 VNet 服务终结点。 必须单独为副本设置这些规则。

副本从主服务器继承管理员帐户。 主服务器上的所有用户帐户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for PostgreSQL 服务器上连接一样。 对于名称为 **my replica**、管理员用户名为 **myadmin** 的服务器，可以使用 psql 连接到副本：

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制
Azure Database for PostgreSQL 提供了两个用于监视复制的指标。 这两个指标是 **副本的最大滞后时间** 和 **副本滞后时间**。 若要了解如何查看这些指标，请参阅[只读副本操作指南文章](howto-read-replicas-portal.md)的“监视副本”部分。

“副本的最大滞后时间”指标显示主服务器与滞后时间最长的副本之间的滞后时间（以字节为单位）。 此指标仅适用于主服务器，仅当至少有一个只读副本已连接到主服务器且主服务器处于流式复制模式时才可用。 如果副本正在使用主服务器的存档日志，在文件传输复制模式下与主服务器保持同步，则滞后信息不会显示详细信息。

“副本滞后时间”指标显示的是自上次重放事务以来所经历的时间。 如果主服务器上未发生任何事务，则该指标会反映此滞后时间。 此指标仅适用于副本服务器。 “副本滞后时间”是从 `pg_stat_wal_receiver` 视图计算得出的：

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

请设置警报，以便在副本滞后时间达到工作负荷不可接受的值时收到通知。 

若要获取更多见解，请直接查询主服务器，以获取所有副本上的复制滞后情况（以字节为单位）。

> [!NOTE]
> 如果主服务器或只读副本重启，“副本滞后时间”指标中会反映重启以及跟上进度所花费的时间。

## <a name="stop-replication--promote-replica"></a>停止复制/提升副本
你可以随时停止主服务器与副本之间的复制。 停止操作将导致副本重新启动，并将副本提升为独立的可读写服务器。 独立服务器中的数据是停止复制时副本服务器上可用的数据。 主服务器上的任何后续更新都不会传播到副本。 但是，副本服务器可能已经累积了尚未应用的日志。 在重启过程中，副本会在接受客户端连接之前应用所有挂起的日志。  

### <a name="considerations"></a>注意事项
- 在只读副本上停止复制之前，请检查复制滞后时间，确保副本包含所需的全部数据。 
- 由于只读副本必须应用所有挂起的日志，才能成为独立的服务器，因此在停止复制时，写入繁重的工作负荷的 RTO 可能较高，因为副本上可能会有明显的延迟。 计划提升副本时，请注意这一点。
- 提升后的副本服务器不能再次成为副本。
- 如果将副本提升为主服务器，则无法与旧的主服务器重新建立复制。 如果要返回到旧的主区域，可以使用新名称建立新的副本服务器，或删除旧的主服务器，然后使用旧的主名称创建副本。
- 如果你有多个只读副本，并将其中一个提升为主服务器，则其他副本服务器仍连接到旧的主服务器。 你可能需要从新的已提升服务器重新创建副本。

停止复制后，副本会失去指向其以前的主服务器和其他副本的所有链接。

了解如何[停止复制到副本](howto-read-replicas-portal.md)。

## <a name="failover-to-replica"></a>故障转移到副本

在主服务器发生故障的情况下，系统不会自动故障转移到只读副本。 

由于复制是异步的，因此在主服务器和副本之间可能存在明显的滞后。 滞后时间量受许多因素影响，例如，主服务器上运行的工作负荷类型，以及主服务器和副本服务器之间的延迟。 在一般情况下，如果运行名义上的写入工作负荷，副本滞后时间预期为几秒钟到几分钟。 但是，如果主服务器运行非常繁重的写入密集型工作负荷并且副本的跟进速度不够快，则滞后时间可能会长得多。 可以使用指标“副本滞后时间”跟踪每个副本的复制滞后时间。 该指标显示的是自副本上的上次重放事务以来所经历的时间。 建议观察一段时间的副本滞后时间，以便确定平均滞后时间。 可以针对副本滞后时间设置警报，这样，当它超出预期范围时，系统就会通知你采取行动。

> [!Tip]
> 如果故障转移到副本，则取消副本与主服务器之间的链接时的滞后时间会指示丢失了多少数据。

一旦决定要故障转移到某个副本， 

1. 请停止将数据复制到副本<br/>
   若要使副本服务器成为独立服务器并且能够接受写入，必须执行此步骤。 在此过程中，副本服务器会重启，并且副本服务器与主服务器之间的链接会取消。 启动停止复制操作后，后端进程通常需要几分钟的时间来应用尚未应用的所有残留日志，并将数据库作为可读写服务器打开。 请参阅本文的[停止复制](#stop-replication--promote-replica)部分，了解此操作的潜在影响。
    
2. 将应用程序指向（以前的）副本<br/>
   每个服务器都有唯一的连接字符串。 更新应用程序连接字符串，使之指向（以前的）副本而不是主服务器。
    
如果应用程序成功处理了读取和写入操作，则表明故障转移已完成。 应用程序经历的停机时间取决于何时检测到问题并完成上面的步骤 1 和 2。

### <a name="disaster-recovery"></a>灾难恢复

当出现重大灾难事件（如可用性区域级别或区域故障）时，可以通过提升读取副本来执行灾难恢复操作。 在 UI 门户中，可以导航到只读副本服务器。 然后单击“复制”选项卡，并且可以停止副本以将其提升为独立服务器。 或者，你可以使用 [Azure CLI](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) 来停止和提升副本服务器。

## <a name="considerations"></a>注意事项

本部分汇总了有关只读副本功能的注意事项。

### <a name="prerequisites"></a>先决条件
只读副本和[逻辑解码](concepts-logical.md)都依赖 Postgres 预写日志 (WAL) 来获取信息。 这两个功能需要来自 Postgres 的不同级别的日志记录。 逻辑解码需要的日志记录的级别比只读副本需要的更高。

若要配置正确的日志记录级别，请使用 Azure 复制支持参数。 Azure 复制支持有三个设置选项：

* **关闭** - 在 WAL 中包含最少的信息。 大多数 Azure Database for PostgreSQL 服务器上都不提供此设置。  
* **副本** - 比“关闭”详细。 这是运行[只读副本](concepts-read-replicas.md)所需的最低日志记录级别。 此设置是大多数服务器上的默认设置。
* **逻辑** - 比“副本”详细。 这是运行逻辑解码所需的最低日志记录级别。 使用此设置时，只读副本也可以运行。


### <a name="new-replicas"></a>新副本
只读副本创建为新的 Azure Database for PostgreSQL 服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置
副本是通过使用与主服务器相同的计算和存储设置创建的。 创建副本后，可以更改多个设置，包括存储和备份保留期。

创建副本时或之后，防火墙规则、虚拟网络规则和参数设置不会从主服务器继承到副本。

### <a name="scaling"></a>扩展
缩放 vCore 或者在“常规用途”和“内存优化”之间缩放：
* PostgreSQL 要求辅助服务器上的 `max_connections` 设置[大于或等于主服务器上的设置](https://www.postgresql.org/docs/current/hot-standby.html)，否则辅助服务器将不会启动。
* 在 Azure Database for PostgreSQL 中，所允许的每台服务器的最大连接数已固定到计算 sku，因为连接会占用内存。 可以详细了解 [max_connections 与计算 sku 之间的映射](concepts-limits.md)。
* 纵向扩展：先纵向扩展副本的计算，然后纵向扩展主服务器。 此顺序可防止因违反 `max_connections` 要求而出现错误。
* 纵向缩减：先纵向缩减主服务器的计算，然后纵向缩减副本。 如果尝试将副本缩放至低于主服务器的级别，将会出现错误，因为这样会违反 `max_connections` 要求。

缩放存储：
* 所有副本都启用了存储自动增长，以防止存储空间已满的副本出现复制问题。 无法禁用此设置。
* 你也可以手动纵向扩展存储，就像在任何其他服务器上一样


### <a name="basic-tier"></a>基本层
基本层服务器仅支持相同区域复制。

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL 要求](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS)只读副本上的 `max_prepared_transactions` 参数值大于或等于主服务器上的值，否则副本不会启动。 如果要更改主服务器上的 `max_prepared_transactions`，请先在副本上进行相应更改。

### <a name="stopped-replicas"></a>停止的副本
如果停止主服务器与只读副本之间的复制，副本会重启以应用更改。 已停止的副本将成为可接受读取和写入的独立服务器。 独立服务器不能再次成为副本。

### <a name="deleted-primary-and-standalone-servers"></a>删除的主服务器和独立服务器
删除主服务器后，其所有只读副本将成为独立服务器。 副本将会重启以反映此项更改。

## <a name="next-steps"></a>后续步骤
* 了解如何[在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。
* 了解如何[通过 Azure CLI 和 REST API 创建和管理只读副本](howto-read-replicas-cli.md)。