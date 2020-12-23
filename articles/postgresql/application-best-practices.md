---
title: 应用开发最佳做法-Azure Database for PostgreSQL
description: 了解使用 Azure Database for PostgreSQL 生成应用的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364561"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>使用 Azure Database for PostgreSQL 生成应用程序的最佳做法

下面是一些最佳做法，可帮助你使用 Azure Database for PostgreSQL 生成云就绪应用程序。 这些最佳做法可以减少应用开发时间。

## <a name="configuration-of-application-and-database-resources"></a>应用程序和数据库资源的配置

### <a name="keep-the-application-and-database-in-the-same-region"></a>使应用程序和数据库位于同一区域中
在 Azure 中部署应用程序时，请确保所有依赖项都位于同一区域中。 跨区域或可用性区域分布实例会造成网络延迟，这可能会影响应用程序的总体性能。

### <a name="keep-your-postgresql-server-secure"></a>确保 PostgreSQL 服务器安全
将 PostgreSQL 服务器配置为 [安全](./concepts-security.md) 且无法公开访问。 使用以下选项之一来保护服务器：
- [防火墙规则](./concepts-firewall-rules.md)
- [虚拟网络](./concepts-data-access-and-security-vnet.md)
- [Azure 专用链接](./concepts-data-access-and-security-private-link.md)

为安全，必须始终通过 SSL 连接到 PostgreSQL 服务器，并将 PostgreSQL 服务器和应用程序配置为使用 TLS 1.2。 了解[如何配置 SSL/TLS](./concepts-ssl-connection-security.md)。

### <a name="tune-your-server-parameters"></a>优化服务器参数
对于读取密集型工作负荷优化服务器参数，`tmp_table_size` 和 `max_heap_table_size` 有助于优化性能。 若要计算这些变量所需的值，请查看每连接内存值总计和基本内存。 每连接内存参数（不包括 `tmp_table_size`）的总和与基本内存一起构成了服务器的总内存。

### <a name="use-environment-variables-for-connection-information"></a>使用环境变量获取连接信息
不要将数据库凭据保存在应用程序代码中。 根据前端应用程序的设置，请遵循相关指南来设置环境变量。若要使用应用服务，请参阅[如何配置应用设置](../app-service/configure-common.md#configure-app-settings) 和 Azure Kuberentes 服务的详细信息，请参阅 [如何使用 Kubernetes 密码](https://kubernetes.io/docs/concepts/configuration/secret/)。

## <a name="performance-and-resiliency"></a>性能和复原能力
下面是一些可以用来帮助调试应用程序性能问题的工具和做法。

### <a name="use-connection-pooling"></a>使用连接池
使用连接池时，将在启动时建立一组固定的连接，并保持这些连接。 这还有助于减少服务器上由于在数据库服务器上建立动态新连接而导致的内存碎片。 如果应用程序框架或数据库驱动程序支持连接池，则可以在应用程序端对其进行配置。 如果不支持，另一个建议的选项是利用代理连接池服务，例如，在应用程序外部运行的 [PgBouncer](https://pgbouncer.github.io/) 或 [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) ，并连接到数据库服务器。 PgBouncer 和 Pgpool 都是基于社区的工具，可用于 Azure Database for PostgreSQL。

### <a name="retry-logic-to-handle-transient-errors"></a>用来处理暂时性错误的重试逻辑
你的应用程序可能会遇到暂时性错误：到数据库的连接间歇性断开或丢失。 在这种情况下，服务器在 5 到 10 秒内重试一两次后就会启动并运行。 良好的做法是在第一次重试前等待 5 秒。 然后，每次重试都逐步延长等待时间，最多 60 秒。 限制最大重试次数。达到该次数时，应用程序会认为操作失败，随后你就可以进一步进行调查。 有关详细信息，请参阅[如何排查连接错误](./concepts-connectivity.md)。

### <a name="enable-read-replication-to-mitigate-failovers"></a>启用读取复制以缓解故障转移问题
对于故障转移场景，可以使用[数据传入复制](./concepts-read-replicas.md)。 使用读取副本时，不会发生源服务器和副本服务器之间的自动故障转移。你会注意到源和副本之间的滞后，因为复制是异步的。 网络延迟可能受许多因素影响，例如，在源服务器上运行的工作负荷的大小，以及数据中心之间的延迟。 大多数情况下，副本延迟在几秒钟到几分钟之间。


## <a name="database-deployment"></a>数据库部署

### <a name="configure-cicd-deployment-pipeline"></a>配置 CI/CD 部署管道
有时，你需要将更改部署到数据库。 在这种情况下，你可以通过 PostgreSQL 服务器的 [GitHub 操作](https://github.com/Azure/postgresql/blob/master/README.md) 来使用持续集成 (CI) 通过对其运行自定义脚本来更新数据库。

### <a name="define-manual-database-deployment-process"></a>定义手动数据库部署过程
在手动部署数据库的过程中，请执行以下步骤以最大程度地减少停机时间或降低部署失败的风险：

- 使用 pg_dump 在新数据库上创建生产数据库的副本。
- 使用数据库所需的新架构更改或更新来更新新数据库。
- 将生产数据库置于只读状态。 在部署完成之前，不应当对生产数据库执行写入操作。
- 使用步骤 1 中新更新的数据库测试你的应用程序。
- 部署你的应用程序更改，并确保应用程序目前正在使用具有最新更新的新数据库。
- 保留旧的生产数据库，以便回滚更改。 然后，你可以评估是删除旧的生产数据库，还是根据需要将其导出到 Azure 存储。

>  [!NOTE]
> 如果应用程序类似于电子商务应用，并且不能将其置于只读状态，请在进行备份后直接在生产数据库上部署更改。 这些更改应在非高峰时间（此时发往应用的流量较小）进行，以最大程度地降低影响，因为某些用户可能会遇到请求失败的情况。 请确保应用程序代码还处理所有失败的请求。

## <a name="database-schema-and-queries"></a>数据库架构和查询
下面是构建数据库架构和查询时需要注意的一些技巧。

### <a name="use-bigint-or-uuid-for-primary-keys"></a>使用 BIGINT 或 UUID 作为主键
构建自定义应用程序或某些框架时，它们可能使用 `INT` 而不是 `BIGINT` 作为主键。 当你使用时 ```INT``` ，你可以在其中，数据库中的值超出数据类型的存储容量的风险 ```INT``` 。 对现有生产应用程序进行此更改可能会消耗更多的开发时间。 另一种方法是使用 [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) 作为主键。例如，此标识符使用自动生成的128位字符串 ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` 。 了解有关 [PostgreSQL 数据类型](https://www.postgresql.org/docs/8.1/datatype.html)的详细信息。

### <a name="use-indexes"></a>使用索引

Postgres 中有许多类型的 [索引](https://www.postgresql.org/docs/9.1/indexes.html) ，可通过不同方式使用。 使用索引有助于服务器查找和检索特定行的速度要快于不使用索引的情况。 但索引还会增加数据库服务器的开销，因此应避免索引太多。

### <a name="use-autovacuum"></a>使用 autovacuum
可以在 Azure Database for PostgreSQL 服务器上使用 autovacuum 优化服务器。 PostgreSQL 允许更高的数据库并发，但每次更新都会导致插入和删除。 对于删除，记录已进行了软标记，稍后会清除。 为执行这些任务，PostgreSQL 将运行一个清扫作业。 如果不时常运行清扫作业，累积的死元组可能会导致以下问题：

- 数据膨胀，例如数据库和表变大。
- 更大的非最优索引。
- I/O 增加。

了解有关 [如何优化 autovacuum 的](howto-optimize-autovacuum.md)详细信息。

### <a name="use-pg_stats_statements"></a>使用 pg_stats_statements
Pg_stat_statements 是 PostgreSQL 扩展，默认情况下在 Azure Database for PostgreSQL 中启用  。 该扩展提供了一种方法来跟踪服务器执行的所有 SQL 语句的执行统计信息。 请参阅 [如何使用 pg_statement](howto-optimize-query-stats-collection.md)。


### <a name="use-the-query-store"></a>使用查询存储
Azure Database for PostgreSQL 中的[查询存储](./concepts-query-store.md)功能提供了用于跟踪查询统计信息的更高效的方法。 建议使用此功能作为使用 pg_stats_statements 的替代方法  。

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>优化大容量插入并使用暂时性数据
如果工作负荷操作涉及到瞬态数据或者需要批量插入大型数据集，可以考虑使用无日志记录表。 它在默认情况下提供原子性和持久性。 原子性、一致性、隔离性和持久性组成了 ACID 属性。 请参阅 [如何优化大容量插入](howto-optimize-bulk-inserts.md)。

## <a name="next-steps"></a>后续步骤
[Postgres 指南](http://postgresguide.com/)
