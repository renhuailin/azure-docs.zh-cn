---
title: Hive 从 3.6 迁移到 4.0 的故障排除 - Azure HDInsight
description: 从 HDInsight 3.6 到 4.0 迁移 Hive 工作负载的故障排除指南
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/12/2021
ms.openlocfilehash: eb19f3bd726efe018b4c593f324eb1cacd2cc2c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722290"
---
# <a name="troubleshooting-guide-for-migration-of-hive-workloads-from-hdinsight-36-to-hdinsight-40"></a>从 HDInsight 3.6 到 HDInsight 4.0 迁移 Hive 工作负载的故障排除指南

本文提供了客户在将 Hive 工作负载从 HDInsight 3.6 迁移到 HDInsight 4.0 时所面临的一些最常见问题的解答。

## <a name="reduce-latency-when-running-describe-table_name"></a>降低运行 `DESCRIBE TABLE_NAME` 时的延迟

解决方法：
* 增加可在一个批处理中从元存储检索的最大对象数（表/分区）。 将它设置为一个较大的数字（默认值为 300），直到达到令人满意的延迟级别。 此数值越大，需要往返 Hive 元存储服务器的次数就越少，但是，这也可能导致客户端需要更高的内存。

    ```hive.metastore.batch.retrieve.max=2000```
* 重新启动 Hive 和所有过时服务

## <a name="unable-to-query-gzipped-text-file-if-skipheaderlinecount-and-skipfooterlinecount-are-set-for-table"></a>如果为表设置了 skip.header.line.count 和 skip.footer.line.count，则无法查询 Gzip 压缩的文本文件

此问题在 Interactive Query 4.0 中得到了修复，但在 Interactive Query 3.1.0 中仍然存在

解决方法：
* 在不使用 ```"skip.header.line.count"="1"``` 和 ```"skip.footer.line.count"="1"``` 的情况下创建表，然后从排除了查询中的标头/页脚行的原始表创建视图。

## <a name="unable-to-use-unicode-characters"></a>无法使用 Unicode 字符

解决方法：
1. 连接到你的群集的 Hive 元存储数据库。

2. 使用以下命令获取 `TBLS` 和 `TABLE_PARAMS` 表的备份：
    ```sql
        select * into tbls_bak from tbls;
        select * into table_params_bak from table_params;
    ```

3. 手动将受影响的列类型更改为 `nvarchar(max)`。
    ```sql 
        alter table TABLE_PARAMS alter column PARAM_VALUE nvarchar(max);
        alter table TBLS alter column VIEW_EXPANDED_TEXT nvarchar(max) null;    
        alter table TBLS alter column VIEW_ORIGINAL_TEXT nvarchar(max) null;
    ```

## <a name="create-table-as-select-ctas-creates-a-new-table-with-same-uuid"></a>CREATE TABLE AS SELECT (CTAS) 会创建一个具有相同 UUID 的新表

Hive 3.1 (HDInsight 4.0) 提供内置 UDF 来生成唯一的 UUID。 Hive UUID () 方法将生成唯一的 ID，即使使用 CTAS 也是如此。 使用方法如下所示。
```hql
create table rhive as
select uuid() as UUID
from uuid_test
```

## <a name="hive-job-output-format-differs-from-hdinsight-36"></a>Hive 作业输出格式不同于 HDInsight 3.6

这是由 HDInsight 3.6 和 HDInsight 4.0 之间的 WebHCat(Templeton) 的差异所致。

* Hive Rest API - 添加 ```arg=--showHeader=false -d arg=--outputformat=tsv2 -d```

* .NET SDK - 初始化 ```HiveJobSubmissionParameters``` 的参数
    ```csharp
    List<string> args = new List<string> { { "--showHeader=false" }, { "--outputformat=tsv2" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SELECT clientid,market from hivesampletable LIMIT 10",
                    Defines = defines,
                    Arguments = args
                };
    ```

## <a name="reduce-hive-internal-table-creation-latency"></a>降低 Hive 内部表创建延迟

1. 从 Advanced hive-site 和 Advanced hivemetastore-site 中，删除 ```hive.metastore.transactional.event.listeners``` 的值 ```org.apache.hive.hcatalog.listener.DbNotificationListener```。 

2. 如果 ```hive.metastore.event.listeners``` 有值，请删除它。

3. DbNotificationListener 仅在使用 REPL 命令时才需要，如果不需要，可以将它删除。

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-reduce-internal-table-creation-latency.png" alt-text="降低 HDInsight 4.0 中的内部表延迟" border="true":::

## <a name="change-hive-default-table-location"></a>更改 Hive 默认表位置

这是针对 HDInsight 4.0 (Hive 3.1) 有意设计的行为更改。 此更改的主要原因是出于文件权限控制目的。 

若要在自定义位置下创建外部表，请在 create table 语句中指定位置。

## <a name="disable-acid-in-hdinsight-40"></a>在 HDInsight 4.0 中禁用 ACID

建议在 HDInsight 4.0 中启用 ACID。 Hive 中最近的大部分增强（包括功能和性能）只对 ACID 表可用。

在 HDInsight 4.0 上禁用 ACID 的步骤：
1. 在 Ambari 中更改以下 Hive 配置：

    ```text
    hive.strict.managed.tables=false
    hive.support.concurrency=false; 
    hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DummyTxnManager;
    hive.enforce.bucketing=false;
    hive.compactor.initiator.on=false;
    hive.compactor.worker.threads=0;
    hive.create.as.insert.only=false;
    metastore.create.as.acid=false;
    ```

2. 重新启动 Hive 服务。

> [!IMPORTANT]
> Microsoft 建议不要将相同的数据/存储与 HDInsight 3.6 和 HDInsight 4.0 托管表共享。这是不受支持的方案。

* 通常情况下，应该在 HDInsight 4.0 集群上创建任何 Hive 表之前设置上述配置。 创建托管表后，我们不应禁用 ACID。 这可能会导致数据丢失或结果不一致。 因此，建议在创建新群集时只设置一次，之后不要更改。

* 在创建表之后禁用 ACID 这一操作存在风险，但如果要执行此操作，请遵循以下步骤以避免潜在的数据丢失或不一致：

    1. 使用 CTAS 命令 ```create external table e_t1 select * from m_t1``` 创建具有相同架构的外部表并从原始托管表复制数据。    
    2. 使用 ```drop table m_t1``` 删除托管表。
    3. 使用建议的配置禁用 ACID。        
    4. 再次使用 CTAS 命令 ```create table m_t1 select * from e_t1``` 创建 m_t1 并从外部表复制数据。        
    5. 使用 ```drop table e_t1``` 删除外部表。

在禁用 ACID 之前，请确保所有托管表都已转换为外部表并已被删除。 此外，请在完成每一步骤之后比较架构和数据，从而避免出现任何差异。

## <a name="create-hive-external-table-with-755-permission"></a>创建具有 755 权限的 Hive 外部表

可以通过以下两个选项之一来解决此问题：

1. 手动将文件夹权限设置为 757 或 777，从而允许 Hive 用户写入目录。

2. 将“Hive Authorization Manager”从 ```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider``` 更改为 ```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly```。

MetaStoreAuthzAPIAuthorizerEmbedOnly 会有效禁用安全检查，因为 Hive 元存储并未嵌入在 HDInsight 4.0 中。 但是，这可能会带来其他潜在的问题。 使用此选项时请谨慎操作。

## <a name="permission-errors-in-hive-job-after-upgrading-to-hdinsight-40"></a>升级到 HDInsight 4.0 后 Hive 作业中的权限错误

* 在 HDInsight 4.0 中，所有带有 Hive 组件的群集形状都配置有一个新的授权提供程序：```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider```

* 应该将 HDFS 文件权限分配给要访问的文件的 Hive 用户。 错误消息会提供解决此问题所需的详细信息。

* 还可以切换到 HDInsight 3.6 Hive 群集中使用的 ```MetaStoreAuthzAPIAuthorizerEmbedOnly``` 提供程序。
```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly```

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-job-permission-errors.png" alt-text="将授权设置为 MetaStoreAuthzAPIAuthorizerEmbedOnly" border="true":::

## <a name="unable-to-query-table-with-opencsvserde"></a>无法使用 OpenCSVSerde 查询表

从 `csv` 格式表中读取数据可能会引发异常，例如：
```text
MetaException(message:java.lang.UnsupportedOperationException: Storage schema reading not supported)
```

解决方法：

* 通过 Ambari UI 在 `Custom hive-site` 中添加配置`metastore.storage.schema.reader.impl`=`org.apache.hadoop.hive.metastore.SerDeStorageSchemaReader`

* 重新启动所有过时的 Hive 服务

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
