---
title: 扩展 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 介绍有关使用 Azure Database for PostgreSQL - 超大规模 (Citus) 中的扩展来扩展数据库功能的功能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 27c7ae33dc889f7bbc29bb5e396fae3a6cdea67d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726170"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 中的 PostgreSQL 扩展

PostgreSQL 还支持使用扩展来扩展数据库功能。 扩展允许在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载后，扩展可以像内置功能那样运行。 有关 PostgreSQL 扩展的详细信息，请参阅[将相关对象打包到扩展中](https://www.postgresql.org/docs/current/static/extend-extensions.html)。

## <a name="use-postgresql-extensions"></a>使用 PostgreSQL 扩展

必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 若要安装特定扩展，请通过 psql 工具运行 [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) 命令，将打包的对象加载到数据库中。

> [!NOTE]
> 如果 `CREATE EXTENSION` 失败并出现权限被拒绝错误，请改为尝试 `create_extension()` 函数。 例如：
>
> ```sql
> SELECT create_extension('postgis');
> ```

Azure Database for PostgreSQL - 超大规模 (Citus) 目前支持部分关键扩展（已在此处列出）。 所列扩展以外的扩展不受支持。 不能使用 Azure Database for PostgreSQL 创建自己的扩展。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 支持的扩展

下表列出了用于 PostgreSQL 的 Azure 数据库目前支持的标准 PostgreSQL 扩展。 还可以通过运行 `SELECT * FROM pg_available_extensions;` 获取此信息。

服务器组中安装的每个扩展的版本有时不同于 PostgreSQL 版本（11、12 或 13）。 下表按数据库版本列出了扩展版本。

### <a name="citus-extension"></a>Citus 扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Citus 分布式数据库。 | 9.5-2 | 10.0-3 | 10.0-3 |

### <a name="data-types-extensions"></a>数据类型扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | 提供不区分大小写的字符串类型。 | 1.5 | 1.6 | 1.6 |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | 提供用于多维数据集的数据类型。 | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | 提供 HyperLogLog 数据结构。 | 2.15 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | 提供用于存储键值对集的数据类型。 | 1.5 | 1.6 | 1.7 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | 提供用于国际产品编号标准的数据类型。 | 1.2 | 1.2 | 1.2 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | 大型对象维护。 | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | 提供用于分层树形结构的数据类型。 | 1.1 | 1.1 | 1.2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | 用于表示线段或浮点间隔的数据类型。 | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | 用于基于排名的统计信息（如分位数和剪裁方式）在线累积的数据类型。 | 1.0 | 1.0 | 1.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Top-n JSONB 的类型。 | 2.3.1 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>全文搜索扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | 提供用于整数的文本搜索字典模板。 | 1.0 | 1.0 | 1.0 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 用于扩展同义词处理的文本搜索字典模板。 | 1.0 | 1.0 | 1.0 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | 删除了词素中重音（附加符号）的文本搜索字典。 | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>函数扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 用于自动增加字段的函数。 | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | 提供一种计算地球表面上的大圆距离的方法。 | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | 提供多个函数，用于确定字符串间的相似性和差异。 | 1.1 | 1.1 | 1.1 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 用于跟踪表更改者的函数。 | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | 整数聚合器和枚举器（已过时）。 | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | 提供用于操作无 null 整数数组的函数和运算符。 | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 用于跟踪上次修改时间的函数。 | 1.0 | 1.0 | 1.0 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 按时间或 ID 管理已分区表。 | 4.5.1 | 4.5.1 | 4.5.1 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | 提供函数和运算符，用于基于三元匹配确定字母数字文本的相似性。 | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | 提供加密函数。 | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 用于实现引用完整性的函数（已过时）。 | 1.0 | 1.0 | 1.0 |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | 提供可操作整个表（包括交叉表）的函数。 | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | 触发了更改通知。 | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 用于实现按时间顺序查看的函数。 | 1.0 | | |
> | [uuid ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | 生成全局唯一标识符 (UUID)。 | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>索引类型扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom 访问方法 - 基于签名文件的索引。 | 1.0 | 1.0 | 1.0 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | 提供示例 GIN 运算符类，该类对特定数据类型实现类似 B-tree 的行为。 | 1.3 | 1.3 | 1.3 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | 提供实施 B-tree 的 GiST 索引运算符类。 | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>语言扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL 可加载过程语言。 | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>其他扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL 的管理函数。 | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | 用于验证关系完整性的函数。 | 1.1 | 1.2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 一个支持从数据库会话中连接到其他 PostgreSQL 数据库的模块。 有关此扩展的信息，请参阅“dblink and postgres_fdw”部分。 | 1.2 | 1.2 | 1.2 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | 用于平面文件访问的外部数据包装器。 | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 在较低级别检查数据库页的内容。 | 1.7 | 1.7 | 1.8 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | 提供一种方法用于实时检查共享缓冲区缓存的当前状况。 | 1.3 | 1.3 | 1.3 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL 的作业计划程序。 | 1.3 | 1.3 | 1.3 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 检查可用空间映射 (FSM)。 | 1.2 | 1.2 | 1.2 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | 提供一种方法用于将相关数据加载到缓冲区缓存中。 | 1.2 | 1.2 | 1.2 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | 提供一种方法用于跟踪服务器执行的所有 SQL 语句的执行统计信息。 有关此扩展的信息，请参阅“pg_stat_statements”部分。 | 1.6 | 1.7 | 1.8 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | 检查可见性映射 (VM) 和页面级别的可见性信息。 | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | 提供一种显示行级锁定信息的方法。 | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | 提供一种显示元组级别统计信息的方法。 | 1.5 | 1.5 | 1.5 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | 外部数据包装器，用于访问外部 PostgreSQL 服务器中存储的数据。 有关此扩展的信息，请参阅“dblink and postgres_fdw”部分。| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | 有关 TLS/SSL 证书的信息。 | 1.2 | 1.2 | 1.2 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE 方法，接受行数作为限制。 | 1.0 | 1.0 | 1.0 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE 方法，接受以毫秒为时间单位的限制。 | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 查询和 XSLT。 | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>PostGIS 扩展

> [!div class="mx-tableFixed"]
> | **扩展名** | **说明** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL 的空间和地理对象。 | 2.5.5 | 3.0.3 | 3.0.3 |
> | address\_standardizer, address\_standardizer\_data\_us | 用于将地址分析成构成元素。 用于支持地理编码地址规范化步骤。 | 2.5.5 | 3.0.3 | 3.0.3 |
> | postgis\_sfcgal | PostGIS SFCGAL 函数。 | 2.5.5 | 3.0.3 | 3.0.3 |
> | postgis\_tiger\_geocoder | PostGIS tiger 地理编码器和反向地理编码器。 | 2.5.5 | 3.0.3 | 3.0.3 |
> | postgis\_topology | PostGIS 拓扑空间类型和函数。 | 2.5.5 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg\_stat\_statements 扩展](https://www.postgresql.org/docs/current/pgstatstatements.html)已预加载在每个 Azure Database for PostgreSQL 服务器上，提供了一种跟踪 SQL 语句执行统计信息的方法。

设置 `pg_stat_statements.track` 控制扩展要计数的语句。 其默认值为 `top`，这意味着要跟踪客户端直接发出的所有语句。 另外两个跟踪级别为 `none` 和 `all`。 此设置可通过 [Azure 门户](./howto-configure-server-parameters-using-portal.md)或 [Azure CLI](./howto-configure-server-parameters-using-cli.md) 作为服务器参数进行配置。

查询执行信息 pg_stat_statements 提供的权限与记录每个 SQL 语句时对服务器性能的影响之间存在权衡。 如果不经常使用 pg_stat_statements 扩展，则建议将 `pg_stat_statements.track` 设置为 `none`。 某些第三方监视服务可能依赖 pg_stat_statements 来提供查询性能见解，因此，请确认这是否适合你。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw

可以使用 dblink 和 postgres\_fdw 从一台 PostgreSQL 服务器连接到另一台服务器，或者连接到同一服务器中的另一个数据库。  接收服务器需要允许来自发送服务器的连接通过其防火墙。  若要使用这些扩展在 Azure Database for PostgreSQL 服务器或超大规模 (Citus) 服务器组之间进行连接，请将“允许 Azure 服务和资源访问此服务器组(或服务器)”设置为“开”。  如果要使用这些扩展来循环回到同一服务器，则也需要启用此设置。
“允许 Azure 服务和资源访问此服务器组”设置可在针对超大规模 (Citus) 服务器组的“Azure 门户”页中的“网络”下找到 。  当前，尚不支持来自 Azure Database for PostgreSQL 单服务器和超大规模 (Citus) 的出站连接，但与其他 Azure Database for PostgreSQL 服务器和超大规模 (Citus) 服务器组的连接除外。
