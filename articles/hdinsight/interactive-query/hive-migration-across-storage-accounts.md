---
title: 将 Hive 工作负荷迁移到 Azure 存储中的新帐户
description: 将 Hive 工作负荷迁移到 Azure 存储中的新帐户
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744574"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>将 Hive 工作负荷迁移到 Azure 存储中的新帐户

了解如何使用脚本操作在 HDInsight 中跨存储帐户复制 Hive 表。 这适用于迁移到 [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md)。

若要在 HDInsight 4.0 上手动复制单个 Hive 表，请参阅 [Hive 导出/导入](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)。

## <a name="prerequisites"></a>必备条件

* 具有以下配置的 HDInsight 群集：
  * 其默认文件系统位于目标存储帐户中。 请参阅[将 Azure 存储与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-blob-storage.md)。
  * 其群集版本必须与源群集版本匹配。
  * 它使用新的外部 Hive 元存储 DB。 请参阅[使用外部元数据存储](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)。
* 原始群集和新群集均可访问的一个存储帐户。 请参阅[将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md) 和[存储类型和功能](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)，了解允许的辅助存储类型。

    提供以下选择：
  * 将目标存储帐户添加到原始群集。
  * 将原始存储帐户添加到新群集。
  * 将中间存储帐户添加到原始群集和新群集。

## <a name="how-it-works"></a>工作原理

通过运行脚本操作将 Hive 表从原始群集导出到指定的 HDFS 目录。 请参阅[对运行中群集进行的脚本操作](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)。

然后，在新群集上运行另一个脚本操作，以从 HDFS 目录导入 Hive 表。

该脚本将在新集群的默认文件系统中重新创建表。 本机表还将复制存储中的数据。 非本机表将仅按定义进行复制。 有关非本机表的详细信息，请参阅 [Hive 存储处理程序](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers)。

将保留 Hive 仓库目录之外的外部表路径。 会将其他表复制到目标群集的默认 Hive 路径。 请参阅 Hive 属性 `hive.metastore.warehouse.external.dir` 和 `hive.metastore.warehouse.dir`。

脚本不会保留目标群集中的自定义文件权限。

> [!NOTE]
>
> 本指南支持复制与 Hive 数据库、表和分区相关的元数据对象。 其他元数据对象必须手动重新创建。
>
> * 对于 `Views`，自 Hive 2.2.0 起，Hive 支持在 HDInsight 4.0 上运行 `SHOW VIEWS` 命令。 使用 `SHOW CREATE TABLE` 来定义视图。 对于 Hive 的早期版本，可以查询元存储 SQL DB 来显示视图。
> * 对于 `Materialized Views`，使用命令 `SHOW MATERIALIZED VIEWS`、`DESCRIBE FORMATTED` 和 `CREATE MATERIALIZED VIEW`。 有关详细信息，请参阅[具体化视图](https://cwiki.apache.org/confluence/display/Hive/Materialized+views)。
> * 对于 `Constraints`，自 Hive 2.1.0 起，支持在 HDInsight 4.0 上使用 `DESCRIBE EXTENDED` 列出表的约束，使用 `ALTER TABLE` 添加约束。 有关详细信息，请参阅[更改表约束](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints)。

## <a name="copy-hive-tables"></a>复制 Hive 表

1. 将包含以下字段的“导出”脚本操作应用于原始群集。

    此操作将生成并执行中间 Hive 脚本。 脚本将保存到指定 `<hdfs-export-path>`。

    此外，也可以使用 `--run-script=false` 自定义脚本，之后再手动执行它们。

    |属性 | Value |
    |---|---|
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |节点类型|头|
    |parameters|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. 导出成功后，将包含以下字段的“导入”脚本操作应用于新群集。

    |属性 | Value |
    |---|---|
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |节点类型|头|
    |parameters|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>确认

下载脚本并在每个群集的主节点上以根用户身份 [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) 运行它，然后比较输出文件 `/tmp/hive_contents.out` 的内容。 请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="cleanup-additional-storage-usage"></a>清理其他存储使用量

完成存储迁移并验证后，可以删除指定 HDFS 导出路径中的数据。

可以选择使用 HDFS 命令 `hdfs dfs -rm -R`。

## <a name="option-reduce-additional-storage-usage"></a>选项：减少额外的存储使用量

由于 Hive，导出脚本操作可能导致存储使用量加倍。 但是，可以通过一次手动迁移一个数据库或表来限制额外的存储使用量。

1. 指定 `--run-script=false` 可跳过执行生成的 Hive 脚本。 Hive 导出和导入脚本仍将保存到导出路径中。

2. 对逐个数据库或表执行 Hive 导出和导入脚本的代码片段，在迁移每个数据库或表之后手动清理导出路径。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [使用外部元数据存储](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [存储类型和功能](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [将脚本操作应用到正在运行的群集](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
