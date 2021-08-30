---
title: PgBouncer - Azure Database for PostgreSQL - 灵活服务器
description: 本文概述了内置 PgBouncer 扩展。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 86543f160a9ffc725a9512069bac39e6e222d543
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745092"
---
# <a name="pgbouncer-in-azure-database-for-postgresql---flexible-server"></a>PgBouncer - Azure Database for PostgreSQL - 灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL –灵活服务器将 [PgBouncer](https://github.com/pgbouncer/pgbouncer) 作为内置连接池解决方案予以提供。 这是一项可选服务，可以在每个数据库服务器上启用，并支持公共和私有访问。 PgBouncer 与 Postgres 数据库服务器在同一个虚拟机上运行。 Postgres 使用基于进程的连接模型，这使得维护许多空闲连接的成本非常昂贵。 因此，一旦服务器运行的连接数超过几千个，Postgres 本身就会遭遇资源限制问题。 PgBouncer 的主要好处在于，可以改进数据库服务器上的空闲连接和生存期较短的连接。

PgBouncer 使用更轻量的模型，该模型使用异步 I/O，并且仅在需要时（即在打开的事务中或查询处于活动状态时）使用实际的 Postgres 连接。 此模型可以更轻松地以低开销支持数千个连接，并允许以低开销扩展到最多 10,000 个连接。

启用后，PgBouncer 将在数据库服务器上的端口 6432 上运行。 你可以将应用程序的数据库连接配置更改为使用同一主机名，但将端口更改为 6432，便可开始使用 PgBouncer 并受益于改进的空闲连接缩放功能。

> [!Note]
> 公共访问网络和专用访问网络中的常规用途和内存优化计算层都支持 PgBouncer。

## <a name="enabling-and-configuring-pgbouncer"></a>启用和配置 PgBouncer

若要启用 PgBouncer，可以导航到 Azure 门户中的“服务器参数”边栏选项卡，然后搜索“PgBouncer”并将“pgbouncer.enabled”设为“true”，即可启用 PgBouncer。 无需重启服务器。 但是，若要设置其他 PgBouncer 参数，请参阅限制部分。

你可以配置 PgBouncer，并设置以下参数：

| 参数名称             | 说明 | 默认 | 
|----------------------|--------|-------------|
| pgbouncer.default_pool_size | 将此参数值设置为每个用户/数据库对的连接数      | 50       | 
| pgBouncer.max_client_conn | 将此参数值设置为要支持的 PgBouncer 客户端连接的最大数量。     | 5000     | 
| pgBouncer.pool_mode | 将此参数值设置为事务池的事务（此为大多数工作负载的建议设置）。      | TRANSACTION     |
| pgBouncer.min_pool_size | 如果低于此数量，请将更多服务器连接添加到池。    |   0（禁用）   |
| pgbouncer.ignore_startup_parameters | PgBouncer 可忽略的以逗号分隔的参数列表。 例如，可以让 PgBouncer 忽略 `extra_float_digits` 参数。|   |
| pgbouncer.query_wait_timeout | 允许查询等待执行的最长时间（以秒为单位）。 如果在此时间段内未将查询分配给服务器，客户端将断开连接。 | 120s |
| pgBouncer.stats_users | 可选。 将此参数值设置为现有用户的名称，以便能够登录到特殊的 PgBouncer 统计数据库（名为“PgBouncer”）。    |      |

有关 PgBouncer 配置的更多详细信息，请参阅 [pgbouncer.ini](https://www.pgbouncer.org/config.html)。

> [!Note] 
> PgBouncer 的升级将由 Azure 管理。

## <a name="switching-your-application-to-use-pgbouncer"></a>切换应用程序以使用 PgBouncer

若要开始使用 PgBouncer，请执行以下步骤：
1. 连接到数据库服务器，但使用端口 6432，而不是常规端口 5432 -- 验证此连接是否有效
```azurecli-interactive
psql "host=myPgServer.postgres.database.azure.com port=6432 dbname=postgres user=myUser password=myPassword sslmode=require"
```
2. 在 QA 环境中针对 PgBouncer 测试你的应用程序，以确保不存在任何兼容性问题。 PgBouncer 项目提供兼容性矩阵，因此我们建议对大多数用户使用“事务池”： https://www.PgBouncer.org/features.html#sql-feature-map-for-pooling-modes 。
3. 更改生产应用程序以连接到端口 6432 而不是 5432，并监视所有可能指向任何兼容性问题的应用程序错误。

> [!Note] 
> 即使已启用 PgBouncer，你仍然可以使用相同的主机名直接通过端口 5432 连接到数据库服务器。

## <a name="pgbouncer-in-zone-redundant-high-availability"></a>PgBouncer 的区域冗余高可用性

在区域冗余高可用性配置服务器中，主服务器将运行 PgBouncer。 你可以通过端口 6432 连接到主服务器的 PgBouncer。 故障转移后，PgBouncer 将在新升级的备用服务器（即新的主服务器）上重新启动。 因此，应用程序连接字符串将在故障转移后保持不变。 

## <a name="using-pgbouncer-with-other-connection-pools"></a>将 PgBouncer 与其他连接池一起使用

在某些情况下，你可能已经有一个应用程序端连接池，或者已经在应用程序端（例如 AKS side car）上设置了 PgBouncer。 在这些情况下，使用内置 PgBouncer 仍然有用，因其可以提供空闲连接缩放效益。

在数据库服务器上联合使用应用程序端池与 PgBouncer 是一件非常有益的事。 这种情况下，应用程序端池有利于减少初始连接延迟（因为初始化连接的初始往返速度要快得多），而数据库端 PgBouncer 则可提供空闲连接缩放。

## <a name="limitations"></a>限制
 
* PgBouncer 目前不支持可突发服务器计算层。 
* 如果将计算层从常规用途或内存优化更改为可突发层，则你将无法使用 PgBouncer。
* 每当在执行缩放操作、HA 故障转移或重新启动期间重启服务器时，PgBouncer 也会随服务器虚拟机一同重启。 因此，必须重新建立现有连接。
* 由于已知问题，门户不会显示所有 PgBouncer 参数。 启用 PgBouncer 并保存参数后，必须退出参数屏幕（例如单击“概述”），然后返回到“参数”页。 
  
## <a name="next-steps"></a>后续步骤

- 了解[网络概念](./concepts-networking.md)
- 灵活服务器 - [概述](./overview.md)
