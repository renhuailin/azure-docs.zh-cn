---
title: 使用 PostgreSQL 扩展
description: 使用 PostgreSQL 扩展
titleSuffix: Azure Arc-enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 831b3e220afe826b5190588b8855b72a8d648916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743838"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在已启用 Azure Arc 的 PostgreSQL 超大规模服务器组中使用 PostgreSQL 扩展

如果将其用于扩展，PostgreSQL 的效果最佳。 事实上，我们自己的超大规模功能的一个关键元素是默认安装的 Microsoft 提供的 `citus` 扩展，它允许 Postgres 以透明方式在多个节点间对数据进行分片。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>支持的扩展
标准 [`contrib`](https://www.postgresql.org/docs/12/contrib.html) 扩展和以下扩展已经部署到已启用 Azure Arc 的 PostgreSQL 超大规模服务器组的容器中：
- [`citus`](https://github.com/citusdata/citus)，版本：10.0。 默认情况下，会加载 [Citus Data](https://www.citusdata.com/) 提供的 Citus 扩展，因为它会将超大规模功能引入到 PostgreSQL 引擎。 不支持从 Azure Arc PostgreSQL 超大规模服务器组中删除 Citus 扩展。
- [`pg_cron`](https://github.com/citusdata/pg_cron)，版本：1.3
- [`pgaudit`](https://www.pgaudit.org/)，版本：1.4
- plpgsql，版本：1.0
- [`postgis`](https://postgis.net)，版本：3.0.2
- [`plv8`](https://plv8.github.io/)，版本：2.3.14
- [`pg_partman`](https://github.com/pgpartman/pg_partman)，版本：4.4.1
- [`tdigest`](https://github.com/tvondra/tdigest)，版本：1.0.1

对此列表进行的更新将随着时间的推移而公布。

> [!IMPORTANT]
> 尽管你可能会向服务器组引入上面所列之外的扩展，但在此预览版中，它不会保留在系统中。 这意味着它在系统重启后将不可用，你需要再次将其引入。

本指南将介绍如何使用其中两个扩展：
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>哪些扩展需要添加到 shared_preload_libraries，哪些扩展需要创建？

|扩展   |需要添加到 shared_preload_libraries  |需要创建 |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |否       |是        |
|`pg_audit`     |是       |是        |
|`plpgsql`      |是       |是        |
|`postgis`      |否       |是        |
|`plv8`      |否       |是        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>添加扩展到 `shared_preload_libraries`
有关什么是 `shared_preload_libraries` 的详细信息，请阅读[此处](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)的 PostgreSQL 文档：
- 对于作为 `contrib` 的一部分的扩展，不需要此步骤
- 对于 shared_preload_libraries 不需要预加载的扩展，不需要此步骤。 如需了解这些扩展，可以跳到下一个段：[创建扩展](#create-extensions)。

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>在创建服务器组时添加扩展
```azurecli
az postgres arc-server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>将扩展添加到已存在的实例
```azurecli
az postgres arc-server server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>显示添加到 shared_preload_libraries 的扩展的列表
运行以下任一命令。

### <a name="with-cli-command"></a>使用 CLI 命令
```azurecli
az postgres arc-server show -n <server group name>
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
### <a name="with-kubectl"></a>使用 kubectl
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
使用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>显示已创建的扩展的列表
使用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>删除扩展
使用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS` 扩展
不需要将 `PostGIS` 扩展添加到 `shared_preload_libraries`。
从 MIT 的城市研究和规划部门获取[示例数据](http://duspviz.mit.edu/tutorials/intro-postgis/)。 根据需要运行 `apt-get install unzip` 以安装解压缩。

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

让我们连接到数据库，并创建 `PostGIS` 扩展：

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> 如果要使用 `postgis` 包中的某个扩展（例如 `postgis_raster`、`postgis_topology`、`postgis_sfcgal`、`fuzzystrmatch`...），则需要先创建 postgis 扩展，然后再创建另一个扩展。 例如：`CREATE EXTENSION postgis`；`CREATE EXTENSION postgis_raster`；

创建架构：

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

现在，我们可以通过使 coffee_shops 表分散来将 `PostGIS` 与横向扩展功能结合起来：

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

让我们加载一些数据：

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

并在 `geom` 字段中填充 `PostGIS` `geometry` 数据类型中正确编码的纬度和经度：

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

现在，我们可以列出距离 MIT（马萨诸塞州大街 77 号，电话：42.359055、-71.093500）最近的咖啡店：

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron` 扩展

现在，让我们通过将其添加到 shared_preload_libraries，在 PostgreSQL 服务器组上启用 `pg_cron`：

```azurecli
az postgres arc-server update -n pg2 -ns arc --extensions pg_cron
```

服务器组将重启并完成扩展的安装。 这可能需要 2-3 分钟。

现在，我们可以再次连接，并创建 `pg_cron` 扩展：

```sql
CREATE EXTENSION pg_cron;
```

出于测试目的，让我们创建一个表 `the_best_coffee_shop`，该表采用我们先前的 `coffee_shops` 表中的一个随机名称，并插入表内容：

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

我们可以使用 `cron.schedule` 以及一些 SQL 语句来获取随机表名称（注意使用临时表存储分布式查询结果），并将其存储在 `the_best_coffee_shop` 中：

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

有关语法的完整详细信息，请参阅 [pg_cron README](https://github.com/citusdata/pg_cron)。


## <a name="next-steps"></a>后续步骤
- 阅读有关 [`plv8`](https://plv8.github.io/) 的文档
- 阅读有关 [`PostGIS`](https://postgis.net/) 的文档
- 阅读有关 [`pg_cron`](https://github.com/citusdata/pg_cron) 的文档