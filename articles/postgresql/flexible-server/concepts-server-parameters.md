---
title: 服务器参数 - Azure Database for PostgreSQL 灵活服务器
description: 描述 Azure Database for PostgreSQL 灵活服务器中的服务器参数
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d8690bd33f10744b6d9cc9cd671e1ee46b102793
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711909"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的服务器参数

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 为每个服务器都提供了一部分可配置的参数。 有关 Postgres 参数的详细信息，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/13/config-setting.html)。

## <a name="an-overview-of-postgresql-parameters"></a>PostgreSQL 参数概述 

Azure Database for PostgreSQL 服务器在创建时预先配置了每个参数的最佳默认值。 静态参数需要重启服务器，而需要超级用户访问权限的参数则无法由用户配置。 

若要了解可以查看或修改哪些参数，建议先转到 Azure 门户，然后转到“服务器参数”页。 还可以使用 `ALTER DATABASE` 或 `ALTER ROLE` 命令，按用户或数据库来配置参数。

>[!NOTE]
> 由于 Azure Database for PostgreSQL 是一种托管数据库服务，因此没有为用户提供查看或修改配置文件（例如 `postgresql.conf`）的主机或 OS 访问权限。 该文件的内容会根据“服务器参数”页中的参数更改自动更新。

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="服务器参数 - 门户":::

下面是一些参数的列表：


   | 参数名称             | 说明 |
|----------------------|--------|
| **max_connections** | 可以在 Postgres 灵活服务器上优化 max_connections，可将其设置为 5,000 个连接。 有关更多详细信息，请参阅[限制文档](concepts-limits.md)。 | 
| shared_buffers    | “shared_buffers”设置因所选的 SKU 而异（SKU 决定可用的内存）。 “常规用途”服务器有 2GB 的 shared_buffers 用于 2 个 vCore；“内存优化”服务器有 4GB 的 shared_buffers 用于 2 个 vCore。 当层级中的 vCore 数增加时，shared_buffers 设置大致以线性方式扩展。 | 
| shared_preload_libraries | 此参数可用于配置预定义的一组受支持的扩展。 请注意，我们始终会加载 `azure` 扩展（用于维护任务），以及 `pg_stat_statements` 扩展（你可以使用 pg_stat_statements.track 参数来控制该扩展是否处于活动状态）。 |
| connection_throttling | 对于因密码无效而登录失败的次数过多的情形，可按 IP 启用或禁用临时连接限制。 |
 | work_mem | 此参数指定写入到临时磁盘文件之前内部排序操作和哈希表要使用的内存量。 如果你的工作负荷很少有排序复杂的查询，并且有很多可用内存，则增大此参数的值可以允许 Postgres 在内存中进行更大的扫描，避免溢出到磁盘，这样速度会更快。  不过要小心，因为一个复杂的查询可能具有多个并发运行排序、哈希操作。 其中每一个操作将首先用尽这个参数的值所需允许的内存量，然后才会开始写入到基于磁盘的历史文件。 因此，在相对繁忙的系统上，总内存使用量将是单个 work_mem 参数值的倍数。 如果决定全局调整此值，可以使用以下公式来计算初始值：总 RAM * 0.25 /最大连接数。 Azure Database for PostgreSQL - 对于此参数，灵活服务器支持范围 4096 至 2097151。|
| effective_cache_size |effective_cache_size 参数根据操作系统和数据库自身来估算可用于磁盘缓存的内存量。 PostgreSQL 查询规划器确定了缓存在 RAM 中是否是固定的。 值越大，越有可能使用索引扫描；而如果值很小，则将使用顺序扫描。 建议将 effective_cache_size 设置为计算机的总 RAM 的 50%。 |
| maintenance_work_mem | maintenance_work_mem 参数基本上提供维护操作所要使用的最大内存量，如清空、创建索引、变更表和添加外键等操作。  此参数的默认值为 64 KB。 建议将此值设置为高于 work_mem；这样可以提高清空操作的性能。 |
| effective_io_concurrency | 设置 PostgreSQL 预期能够同时执行的并发磁盘 I/O 操作的数量。 提高此值将增加任何单个 PostgreSQL 会话尝试并行启动的 I/O 操作数。 允许的范围是 1 到 1000，0 表示禁止发出异步 I/O 请求。 目前，此设置只影响位图堆扫描。 |
 |require_secure_transport | 如果应用程序不支持与服务器建立 SSL 连接，则可以选择通过将此参数值设置为 `OFF` 来禁用从客户端进行的安全传输。 |

>[!NOTE]
> 如果纵向扩展或缩减 Azure Database for PostgreSQL - 灵活服务器 SKU 影响到了服务器的可用内存，你可能希望根据上述信息相应地调整内存全局参数，例如 work_mem 或 effective_cache_size。 

 
## <a name="next-steps"></a>后续步骤

有关支持的 PostgreSQL 扩展的信息，请参阅[扩展文档](concepts-extensions.md)。
