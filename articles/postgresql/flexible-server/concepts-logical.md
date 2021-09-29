---
title: 逻辑复制和逻辑解码 - Azure Database for PostgreSQL 灵活服务器
description: 了解如何在 Azure Database for PostgreSQL 灵活服务器中使用逻辑复制和逻辑解码
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 5030abe5dbc93a1b04588d548d353701a5a77ad4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559259"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的逻辑复制和逻辑解码



Azure Database for PostgreSQL 灵活服务器支持以下逻辑数据提取和复制方法：
1. **逻辑复制**
   1. 使用 PostgreSQL [本机逻辑复制](https://www.postgresql.org/docs/12/logical-replication.html)复制数据对象。 逻辑复制允许对数据复制（包括表级数据复制）进行精细控制。
   2. 使用 [pglogical](https://github.com/2ndQuadrant/pglogical) 扩展来提供逻辑流式复制和其他功能，例如，复制数据库的初始架构、TRUNCATE 支持、复制 DDL 的功能，等等。 
2. 逻辑解码：通过对预写日志 (WAL) 的内容进行[解码](https://www.postgresql.org/docs/12/logicaldecoding-explanation.html)来实现。 

## <a name="comparing-logical-replication-and-logical-decoding"></a>比较逻辑复制和逻辑解码
逻辑复制和逻辑解码具有一些相似之处。 它们均
* 允许从 Postgres 复制数据
* 使用[预写日志 (WAL)](https://www.postgresql.org/docs/current/wal.html) 作为更改源
* 使用[逻辑复制槽](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)发送数据。 槽表示更改流。
* 使用表的[副本标识属性](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY)确定可发送出去的更改内容
* 不复制 DDL 更改


这两种技术存在不同之处：逻辑复制 
* 允许指定要复制的一个或一组表
* 在 PostgreSQL 实例之间复制数据

逻辑解码 
* 提取数据库中所有表的更改 
* 不能在 PostgreSQL 实例之间直接发送数据。

>[!NOTE]
> 此时，灵活服务器不支持跨区域只读副本。 根据工作负载的类型，你可以选择使用逻辑复制功能进行跨区域灾难恢复 (DR)。

## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>逻辑复制和逻辑解码的先决条件

1. 转到门户中的服务器参数页。
2. 将服务器参数 `wal_level` 设置为 `logical`。
3. 如果要使用 pglogical 扩展，请搜索 `shared_preload_libaries` 参数，然后从下拉框中选择 `pglogical`。
4. 请将 `max_worker_processes` 参数值更新为一个至少 16 的数字。 否则，可能会遇到类似 `WARNING: out of background worker slots` 的问题。
5. 保存更改并重启服务器以应用 `wal_level` 更改。
6. 确认 PostgreSQL 实例允许来自连接资源的网络流量。
7. 授予管理员用户复制权限。
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```
8. 你可能希望确保你使用的角色对要复制的架构具有[权限](https://www.postgresql.org/docs/current/sql-grant.html)。 否则，可能会遇到类似 `Permission denied for schema` 的错误。 

## <a name="using-logical-replication-and-logical-decoding"></a>使用逻辑复制和逻辑解码

### <a name="native-logical-replication"></a>本机逻辑复制
逻辑复制使用术语“发布服务器”和“订阅服务器”。 
* 发布服务器是从中发送数据的 PostgreSQL 数据库。 
* 订阅服务器是向其发送数据的 PostgreSQL 数据库。

下面是可用于尝试逻辑复制的一些示例代码。

1. 连接到发布服务器数据库。 创建表并添加一些数据。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. 为表创建发布。
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. 连接到订阅服务器数据库。 使用发布服务器上的同一架构创建表。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 创建将连接到之前创建的发布的订阅。
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. 现在，可以在订阅服务器上查询表。 你将看到它已从发布服务器接收数据。
   ```SQL
   SELECT * FROM basic;
   ```
   可将更多的行添加到发布服务器的表中，并查看订阅服务器上的更改。

   如果看不到数据，请启用 `azure_pg_admin` 的登录权限并检查表内容。 
   ```SQL 
   ALTER ROLE azure_pg_admin login;
   ```


请访问 PostgreSQL 文档，详细了解[逻辑复制](https://www.postgresql.org/docs/current/logical-replication.html)。

### <a name="pglogical-extension"></a>pglogical 扩展

下面是在提供者数据库服务器和订阅者上配置 pglogical 的示例。 有关更多详细信息，请参阅 pglogical 扩展文档。 此外，请确保已执行上面列出的先决条件任务。

1. 在提供者数据库服务器和订阅者数据库服务器的数据库中安装 pglogical 扩展。
    ```SQL
   \C myDB
   CREATE EXTENSION pglogical;
   ```
2. 在提供者（源/发布者）数据库服务器上，创建提供者节点。
   ```SQL
   select pglogical.create_node( node_name := 'provider1', 
   dsn := ' host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
3. 创建一个复制集。
   ```SQL
   select pglogical.create_replication_set('myreplicationset');
   ```
4. 将数据库中的所有表添加到该复制集。
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('myreplicationset', '{public}'::text[]);
   ```

   也可将特定架构（例如 testUser）中的表添加到默认的复制集，这是替代方法。
   ```SQL
   SELECT pglogical.replication_set_add_all_tables('default', ARRAY['testUser']);
   ```

5. 在订阅者数据库服务器上，创建一个订阅者节点。
   ```SQL
   select pglogical.create_node( node_name := 'subscriber1', 
   dsn := ' host=mySubscriberServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPasword' );
   ```
6. 创建一个订阅以开始同步和复制过程。
    ```SQL
   select pglogical.create_subscription (
   subscription_name := 'subscription1',
   replication_sets := array['myreplicationset'],
   provider_dsn := 'host=myProviderServer.postgres.database.azure.com port=5432 dbname=myDB user=myUser password=myPassword');
   ```
7. 然后即可验证订阅状态。
   ```SQL
   SELECT subscription_name, status FROM pglogical.show_subscription_status();
   ```

### <a name="logical-decoding"></a>逻辑解码
可以通过流式处理协议或 SQL 接口使用逻辑解码。 

#### <a name="streaming-protocol"></a>流式处理协议
我们通常倾向于通过流式处理协议使用更改。 你可以创建自己的使用者/连接器，或者使用 [Debezium](https://debezium.io/) 之类的第三方服务。 

请访问 wal2json 文档，查看[将流式处理协议与 pg_recvlogical 配合使用的示例](https://github.com/eulerto/wal2json#pg_recvlogical)。

#### <a name="sql-interface"></a>SQL 接口 
以下示例将 SQL 接口与 wal2json 插件配合使用。
 
1. 创建槽。
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. 发出 SQL 命令。 例如：
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 使用更改。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   输出如下所示：
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 用完槽后，请将其删除。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

请访问 PostgreSQL 文档，详细了解[逻辑解码](https://www.postgresql.org/docs/current/logicaldecoding.html)。


## <a name="monitoring"></a>监视
必须监视逻辑解码。 必须删除任何未使用的复制槽。 在读取更改之前，槽会一直保存到 Postgres WAL 日志和相关的系统目录。 如果订阅服务器或使用者失败或者未经正确配置，则未使用的日志将不断堆积，直至填满存储。 此外，未使用的日志会增大事务 ID 换行的风险。 这两种情况可能会导致服务器不可用。 因此，逻辑复制槽被持续使用是至关重要的。 如果不再使用某个逻辑复制槽，请立即将其删除。

pg_replication_slots 视图中的“active”列指示是否有使用者连接到某个槽。
```SQL
SELECT * FROM pg_replication_slots;
```
针对使用的事务 ID 上限和使用的存储灵活服务器指标[设置警报](howto-alert-on-metrics.md)，以便在值超过正常阈值时通知你 。 

## <a name="limitations"></a>限制
* 逻辑复制限制适用，如[此处](https://www.postgresql.org/docs/12/logical-replication-restrictions.html)所述。
* **只读副本** - 灵活服务器当前不支持 Azure Database for PostgreSQL 只读副本。
* **槽和 HA 故障转移** - 主服务器上的逻辑复制槽在辅助 AZ 中的备用服务器上不可用。 如果服务器使用区域冗余高可用性选项，则此选项适用于你。 如果故障转移到备用服务器，则备用服务器上的逻辑复制槽将不可用。

## <a name="next-steps"></a>后续步骤
* 详细了解[网络选项](concepts-networking.md)
* 了解灵活服务器中提供的[扩展](concepts-extensions.md)
* 深入了解[高可用性](concepts-high-availability.md)

