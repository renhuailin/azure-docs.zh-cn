---
title: 扩展 - Azure Database for PostgreSQL 灵活服务器
description: 了解 Azure Database for PostgreSQL 灵活服务器中可用的 Postgres 扩展
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 3ccbe4cfa7b6e4f485d16ffecbda5c3b7042b8e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745148"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的 PostgreSQL 扩展

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

PostgreSQL 支持使用扩展来扩展数据的功能。 扩展在单个包中将多个相关 SQL 对象捆绑在一起，可以使用命令在数据库中加载或删除该包。 在数据库中加载之后，扩展会如同内置功能一样运行。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 扩展
必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 若要安装特定扩展，请运行 [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) 命令。 此命令将打包的对象加载到数据库中。

用于 PostgreSQL 的 Azure 数据库支持下面列出的一部分键扩展。 还可以通过运行 `SHOW azure.extensions;` 获取此信息。 Azure Database for PostgreSQL 灵活服务器不支持本文档中未列出的扩展。 不能在 Azure Database for PostgreSQL 中创建或加载自己的扩展。

## <a name="postgres-13-extensions"></a>Postgres 13 扩展

以下扩展在 Azure Database for PostgreSQL 灵活服务器中提供，此类服务器的 Postgres 版本为 13。 

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.1.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.1.1           | Address Standardizer US 数据集示例|
> |[amcheck](https://www.postgresql.org/docs/13/amcheck.html)                    | 1.2             | 用于验证关系完整性的函数|
> |[bloom](https://www.postgresql.org/docs/13/bloom.html)                    | 1.0             | bloom 访问方法 - 基于签名文件的索引|
> |[btree_gin](https://www.postgresql.org/docs/13/btree-gin.html)                    | 1.3             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/13/btree-gist.html)                   | 1.5             | 支持在 GiST 中索引常见数据类型|
> |[citext](https://www.postgresql.org/docs/13/citext.html)                       | 1.6             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/13/cube.html)                         | 1.4             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/13/dblink.html)                       | 1.2             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/13/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[dict_xsyn](https://www.postgresql.org/docs/13/dict-xsyn.html)                     | 1.0             | 用于扩展同义词处理的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/13/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/13/fuzzystrmatch.html)                | 1.1             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/13/hstore.html)                       | 1.7             | 用于存储（键/值）对集的数据类型|
> |[intagg](https://www.postgresql.org/docs/13/intagg.html)                     | 1.1             | 整数聚合器和枚举器。 （已过时）|
> |[intarray](https://www.postgresql.org/docs/13/intarray.html)                     | 1.3             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/13/isn.html)                          | 1.2             | 用于国际产品编号标准的数据类型|
> |[lo](https://www.postgresql.org/docs/13/lo.html)                            | 1.1             | 大型对象维护 |
> |[ltree](https://www.postgresql.org/docs/13/ltree.html)                        | 1.2             | 用于分层树形结构的数据类型|
> |[pageinspect](https://www.postgresql.org/docs/13/pageinspect.html)                        | 1.8             | 在较低级别检查数据库页的内容|
> |[pg_buffercache](https://www.postgresql.org/docs/13/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | PostgreSQL 的作业计划程序|
> |[pg_freespacemap](https://www.postgresql.org/docs/13/pgfreespacemap.html)               | 1.2             | 检查可用空间映射 (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | 一种扩展，用于按时间或 ID 管理已分区表 |
> |[pg_prewarm](https://www.postgresql.org/docs/13/pgprewarm.html)                   | 1.2             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/13/pgstatstatements.html)           | 1.8             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/13/pgtrgm.html)                      | 1.5             | 基于三元匹配的文本相似度度量和索引搜索|
> |[pg_visibility](https://www.postgresql.org/docs/13/pgvisibility.html)                      | 1.2             | 检查可见性映射 (VM) 和页面级别的可见性信息|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.5             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/13/pgcrypto.html)                     | 1.3             | 加密函数| 
> |[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | 逻辑流式复制 |
> |[pgrowlocks](https://www.postgresql.org/docs/13/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/13/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[plpgsql](https://www.postgresql.org/docs/13/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[postgis](https://www.postgis.net/)                      | 3.1.1           | PostGIS geometry、geography |
> |[postgis_raster](https://www.postgis.net/)               | 3.1.1           | PostGIS 光栅类型和函数| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.1.1           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.1.1           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.1.1           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/13/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[sslinfo](https://www.postgresql.org/docs/13/sslinfo.html)                    | 1.2             | 有关 SSL 证书的信息|
> |[tsm_system_rows](https://www.postgresql.org/docs/13/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE 方法，接受行数作为限制|
> |[tsm_system_time](https://www.postgresql.org/docs/13/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE 方法，接受以毫秒为时间单位的限制|
> |[unaccent](https://www.postgresql.org/docs/13/unaccent.html)                     | 1.1             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/13/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (UUID)|

## <a name="postgres-12-extensions"></a>Postgres 12 扩展

以下扩展在 Azure Database for PostgreSQL 灵活服务器中提供，此类服务器的 Postgres 版本为 12。 

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Address Standardizer US 数据集示例|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | 用于验证关系完整性的函数|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | bloom 访问方法 - 基于签名文件的索引|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | 支持在 GiST 中索引常见数据类型|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | 用于扩展同义词处理的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | 用于存储（键/值）对集的数据类型|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | 整数聚合器和枚举器。 （已过时）|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | 用于国际产品编号标准的数据类型|
> |[lo](https://www.postgresql.org/docs/12/lo.html)                            | 1.1             | 大型对象维护 |
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | 用于分层树形结构的数据类型|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | 在较低级别检查数据库页的内容|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | PostgreSQL 的作业计划程序|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | 检查可用空间映射 (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | 一种扩展，用于按时间或 ID 管理已分区表 |
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | 基于三元匹配的文本相似度度量和索引搜索|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | 检查可见性映射 (VM) 和页面级别的可见性信息|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | 加密函数|
>|[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | 逻辑流式复制 |
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometry、geography |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS 光栅类型和函数| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | 有关 SSL 证书的信息|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE 方法，接受行数作为限制|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE 方法，接受以毫秒为时间单位的限制|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (UUID)|

## <a name="postgres-11-extensions"></a>Postgres 11 扩展

以下扩展在 Azure Database for PostgreSQL 灵活服务器中提供，此类服务器的 Postgres 版本为 11。 

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 数据集示例|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | 用于验证关系完整性的函数|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | bloom 访问方法 - 基于签名文件的索引|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支持在 GiST 中索引常见数据类型|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | 用于扩展同义词处理的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用于存储（键/值）对集的数据类型|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | 整数聚合器和枚举器。 （已过时）|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 用于国际产品编号标准的数据类型|
> |[lo](https://www.postgresql.org/docs/11/lo.html)                            | 1.1             | 大型对象维护 |
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 用于分层树形结构的数据类型|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | 在较低级别检查数据库页的内容|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | PostgreSQL 的作业计划程序|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | 检查可用空间映射 (FSM)|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | 一种扩展，用于按时间或 ID 管理已分区表 |
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 基于三元匹配的文本相似度度量和索引搜索|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | 检查可见性映射 (VM) 和页面级别的可见性信息|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 加密函数|
>|[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | 逻辑流式复制 |
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS 几何结构、地理以及光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | 有关 SSL 证书的信息|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 可操作整个表（包括交叉表）的函数|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  TABLESAMPLE 方法，接受行数作为限制|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE 方法，接受以毫秒为时间单位的限制|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (UUID)|


## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) 和 [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) 允许你从一个 PostgreSQL 服务器连接到另一个 PostgreSQL 服务器，或者连接到同一服务器中的另一个数据库。 灵活服务器支持任何 PostgreSQL 服务器的传入和传出连接。 发送服务器需要允许到接收服务器的出站连接。 同样，接收服务器需要允许来自发送服务器的连接。 

如果计划使用这两个扩展，建议使用 [VNet 集成](concepts-networking.md)部署服务器。 默认情况下，VNet 集成支持在 VNET 中的服务器之间建立连接。 还可以选择使用 [VNet 网络安全组](../../virtual-network/manage-network-security-group.md)来自定义访问权限。

## <a name="pg_prewarm"></a>pg_prewarm

pg_prewarm 扩展可将关系数据加载到缓存中。 预热缓存意味着查询在重启后第一次运行时响应时间更短。 自动预热功能当前在 Azure Database for PostgreSQL 灵活服务器中不可用。

## <a name="pg_cron"></a>pg_cron

[pg_cron](https://github.com/citusdata/pg_cron/) 是一项简单的、基于 cron 的 PostgreSQL 作业计划程序，作为扩展在数据库内运行。 pg_cron 扩展可用于在 PostgreSQL 数据库中运行计划性维护任务。 例如，可以定期运行表清空作业或删除旧的数据作业。

`pg_cron` 可以并行运行多个作业，但是一次最多只能运行一个作业实例。 如果第二次运行应在第一次运行完成之前开始，则第二次运行将排队，并在第一次运行完成后立即开始。 这样可以确保作业完全按计划的次数运行，并且不会与自己并发运行。

下面是一些示例：

在星期六凌晨 3:30 (GMT) 删除旧数据
```
SELECT cron.schedule('30 3 * * 6', $$DELETE FROM events WHERE event_time < now() - interval '1 week'$$);
```
在每天上午 10:00 (GMT) 运行清空作业
```
SELECT cron.schedule('0 10 * * *', 'VACUUM');
```

取消 pg_cron 中计划的所有任务
```
SELECT cron.unschedule(jobid) FROM cron.job;
```

## <a name="pg_stat_statements"></a>pg_stat_statements

[pg_stat_statements 扩展](https://www.postgresql.org/docs/current/pgstatstatements.html)已预加载到每个 Azure Database for PostgreSQL 灵活服务器上，以便为你提供跟踪 SQL 语句执行统计信息的方法。
设置 `pg_stat_statements.track`，它可以控制哪些语句由扩展计数，默认为 `top`，这意味着跟踪所有由客户端直接发布的语句。 另外两个跟踪级别为 `none` 和 `all`。 此设置可作为服务器参数配置。

查询执行信息 pg_stat_statements 提供的权限与记录每个 SQL 语句时对服务器性能的影响之间存在权衡。 如果不经常使用 pg_stat_statements 扩展，则建议将 `pg_stat_statements.track` 设置为 `none`。 请注意，某些第三方监视服务可能依赖 pg_stat_statements 来提供查询性能见解，因此，请确认这是否适合你。


## <a name="next-steps"></a>后续步骤

如果未看到要使用的扩展，请告诉我们。 若要支持现有请求或提出新反馈请求，请访问我们的[反馈论坛](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)。