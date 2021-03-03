---
title: Hive 工作负荷迁移到 Azure 存储中的新帐户
description: Hive 工作负荷迁移到 Azure 存储中的新帐户
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744574"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Hive 工作负荷迁移到 Azure 存储中的新帐户

了解如何使用脚本操作在 HDInsight 中的存储帐户之间复制 Hive 表。 当迁移到时，这可能很有用 [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) 。

若要在 HDInsight 4.0 上手动复制单个 Hive 表，请参阅 [Hive 导出/导入](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)。

## <a name="prerequisites"></a>先决条件

* 具有以下配置的新 HDInsight 群集：
  * 其默认文件系统位于目标存储帐户中。 请参阅 [将 azure 存储与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-blob-storage.md)。
  * 其群集版本必须与源群集的版本匹配。
  * 它使用新的外部 Hive 元存储 DB。 请参阅 [使用外部元数据存储](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)。
* 原始群集和新群集均可访问的存储帐户。 请参阅 [将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md) 和 [存储类型和功能](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) ，以允许辅助存储类型。

    下面是一些选项：
  * 将目标存储帐户添加到原始群集。
  * 将原始存储帐户添加到新群集。
  * 将中间存储帐户添加到原始群集和新群集。

## <a name="how-it-works"></a>工作原理

我们将运行一个脚本操作，将 Hive 表从原始群集导出到指定的 HDFS 目录。 请参阅 [脚本操作到正在运行的群集](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)。

然后，在新群集上运行另一个脚本操作，以从 HDFS 目录导入 Hive 表。

此脚本会将表重新创建到新群集的默认文件系统。 本机表还将在存储中复制其数据。 非本机表将仅按定义进行复制。 有关非本机表的详细信息，请参阅 [Hive 存储处理程序](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) 。

将保留不在 Hive 仓库目录中的外部表的路径。 其他表将复制到目标群集的默认 Hive 路径。 请参阅 Hive 属性 `hive.metastore.warehouse.external.dir` 和 `hive.metastore.warehouse.dir` 。

脚本将不保留目标群集中的自定义文件权限。

> [!NOTE]
>
> 本指南支持复制与 Hive 数据库、表和分区相关的元数据对象。 其他元数据对象必须手动重新创建。
>
> * 对于 `Views` ，hive `SHOW VIEWS` 在 HDInsight 4.0 上的 hive 2.2.0 支持命令。 用于 `SHOW CREATE TABLE` 查看定义。 对于更早版本的 Hive，请查询元存储 SQL DB 以显示视图。
> * 对于 `Materialized Views` ，请使用命令 `SHOW MATERIALIZED VIEWS` 、 `DESCRIBE FORMATTED` 和 `CREATE MATERIALIZED VIEW` 。 有关详细信息，请参阅 [具体化视图](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) 。
> * 对于 `Constraints` （在 HDInsight 4.0 上的 Hive 2.1.0 支持），请使用 `DESCRIBE EXTENDED` 列出表的约束，并使用 `ALTER TABLE` 来添加约束。 有关详细信息，请参阅 [Alter Table 约束](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) 。

## <a name="copy-hive-tables"></a>复制 Hive 表

1. 在具有以下字段的原始群集上应用 "export" 脚本操作。

    这将生成并执行中间 Hive 脚本。 它们将保存到指定的 `<hdfs-export-path>` 。

    还可以选择 `--run-script=false` 在手动执行之前使用对它们进行自定义。

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

2. 成功完成导出后，在新群集上使用以下字段应用 "import" 脚本操作。

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

## <a name="verification"></a>验证

下载脚本并 [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) 在每个群集的主节点上以 root 用户身份运行，并比较输出文件的内容 `/tmp/hive_contents.out` 。 请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="cleanup-additional-storage-usage"></a>清除其他存储使用情况

完成存储迁移并验证后，可以在指定的 HDFS 导出路径中删除数据。

选项是使用 HDFS 命令 `hdfs dfs -rm -R` 。

## <a name="option-reduce-additional-storage-usage"></a>选项：减少额外的存储使用量

由于 Hive，导出脚本操作可能会使存储使用加倍。 但是，可以通过手动迁移、一个数据库或表来限制额外的存储使用量。

1. 指定 `--run-script=false` 跳过生成的 Hive 脚本的执行。 Hive 导出和导入脚本仍将保存到导出路径中。

2. 在每个迁移的数据库或表后，按数据库或逐个表执行 Hive 导出和导入脚本的代码段。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [使用外部元数据存储](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [存储类型和功能](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [将脚本操作应用到正在运行的群集](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
