---
title: 使用 PostgreSQL 扩展
description: 使用 PostgreSQL 扩展
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6586375d7db71274f40eb62aeb24f9daad0d7c2e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688291"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中使用 PostgreSQL 扩展

如果将其用于扩展，PostgreSQL 的效果最佳。 事实上，我们自己的超大规模功能的一个关键元素是默认安装的 Microsoft 提供的 `citus` 扩展，它允许 Postgres 以透明方式在多个节点间分片数据。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>支持的扩展
标准 [`contrib`](https://www.postgresql.org/docs/12/contrib.html) 扩展和以下扩展已部署到启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的容器中：
- [`citus`](https://github.com/citusdata/citus)，v：9.4。 默认情况下，会加载 Citus extension by [Citus 数据](https://www.citusdata.com/) ，因为它将超大规模功能引入 PostgreSQL 引擎。 不支持从 Azure Arc PostgreSQL 超大规模服务器组删除 Citus 扩展。
- [`pg_cron`](https://github.com/citusdata/pg_cron)，v：1。2
- [`pgaudit`](https://www.pgaudit.org/)，v：1。4
- plpgsql，v：1。0
- [`postgis`](https://postgis.net)，v：3.0。2
- [`plv8`](https://plv8.github.io/)，v：2.3.14

对此列表进行的更新将随着时间的推移而公布。

> [!IMPORTANT]
> 尽管你可能会向你的服务器组提供不是上面列出的扩展，但在此预览版中，它不会保留在系统中。 这意味着在系统重新启动后，它将不可用，您需要再次将其打开。

本指南将介绍如何使用其中两个扩展：
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>需要将哪些扩展添加到 shared_preload_libraries 并创建？

|Extensions   |需要添加到 shared_preload_libraries  |需要创建 |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |否       |是        |
|`pg_audit`     |是       |是        |
|`plpgsql`      |是       |是        |
|`postgis`      |否       |是        |
|`plv8`      |否       |是        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>将扩展添加到 shared_preload_libraries
有关 shared_preload_libraries 的详细信息，请参阅 [此处](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)的 PostgreSQL 文档：
- 对于作为的一部分的扩展，不需要执行此步骤。 `contrib`
- 对于不需要 shared_preload_libraries 预加载的扩展，此步骤不是必需的。 对于这些扩展，您可以跳到下一个段落 [创建扩展](https://docs.microsoft.com/azure/azure-arc/data/using-extensions-in-postgresql-hyperscale-server-group#create-extensions)。

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>在创建服务器组时添加扩展
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>将扩展添加到已存在的实例
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>显示添加到 shared_preload_libraries 的扩展的列表
运行以下命令之一。

### <a name="with-an-azdata-cli-command"></a>使用 azdata CLI 命令
```console
azdata arc postgres server show -n <server group name>
```
滚动输出，并注意服务器组规范中的 engine\extensions 部分。 例如：
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>With kubectl
```console
kubectl describe postgresql-12s/postgres02
```
滚动输出，并注意服务器组规范中的 engine\extensions 部分。 例如：
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>创建扩展
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>显示已创建的扩展的列表
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>删除扩展
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`扩展
不需要将扩展添加到中 `PostGIS` `shared_preload_libraries` 。
& 规划，获取 MIT 的城市研究的 [示例数据](http://duspviz.mit.edu/tutorials/intro-postgis/) 。 `apt-get install unzip`根据需要运行以安装解压缩。

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

接下来，请连接到数据库，并创建 `PostGIS` 扩展：

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> 如果要使用包中的某个扩展 `postgis` (例如 `postgis_raster` ， `postgis_topology` ) ， `postgis_sfcgal` `fuzzystrmatch` 则需要先创建 postgis 扩展，然后再创建另一个扩展。 例如： `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

并创建架构：

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

现在，我们可以 `PostGIS` 将 coffee_shops 表分散起来，从而结合扩展功能：

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

让我们加载一些数据：

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

并 `geom` 在数据类型中用正确编码的纬度和经度填充字段 `PostGIS` `geometry` ：

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

现在，我们可以列出最接近 MIT (77 马萨诸塞州的咖啡店，42.359055，-71.093500) ：

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron`扩展

现在，让 `pg_cron` 我们通过将其添加到 shared_preload_libraries 来在 PostgreSQL 服务器组上启用：

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

服务器组将重新启动并完成扩展的安装。 可能需要2到3分钟的时间。

现在，我们可以再次连接，并创建 `pg_cron` 扩展：

```sql
CREATE EXTENSION pg_cron;
```

对于测试目的，允许创建一个表，该表 `the_best_coffee_shop` 采用前面表中的随机名称 `coffee_shops` ，并插入表内容：

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

我们可以使用 `cron.schedule` 另外几个 SQL 语句来获取随机表名称 (请注意，使用临时表来存储分布式查询结果) ，并将其存储在中 `the_best_coffee_shop` ：

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

现在，一分钟后，我们将获得一个不同的名称：

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

有关语法的完整详细信息，请参阅 [PG_CRON 自述文件](https://github.com/citusdata/pg_cron) 。


## <a name="next-steps"></a>后续步骤
- 阅读文档 [`plv8`](https://plv8.github.io/)
- 阅读文档 [`PostGIS`](https://postgis.net/)
- 阅读文档 [`pg_cron`](https://github.com/citusdata/pg_cron)
