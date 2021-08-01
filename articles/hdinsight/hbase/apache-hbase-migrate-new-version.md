---
title: 将 HBase 群集迁移到新版本 - Azure HDInsight
description: 了解如何将 Azure HDInsight 中的 Apache HBase 群集迁移到较新版本。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 1d6f2d66a00601334a9cab4695b4e78c77a67917
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109654773"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>将 Apache HBase 群集迁移到新版本

本文介绍如何将 Azure HDInsight 上的 Apache HBase 群集更新为新版本。

仅当你为源群集和目标群集使用相同的 Microsoft Azure 存储帐户时，本文才适用。 若要为目标群集使用新的或不同的存储帐户进行升级，请参阅[使用新的存储帐户将 Apache HBase 迁移到新版本](apache-hbase-migrate-new-version-new-storage-account.md)。

升级时，停机时间应该只有几分钟。 停机是刷新所有内存中数据的步骤造成的，用于在新群集上配置和重启服务。 根据节点数目、数据量和其他变数，结果会有所不同。

## <a name="review-apache-hbase-compatibility"></a>检查 Apache HBase 兼容性

在升级 Apache HBase 之前，请确保源群集和目标群集上的 HBase 版本兼容。 查看 [HBase 参考指南](https://hbase.apache.org/book.html#upgrading)中的 HBase 版本兼容性对照表和发行说明，以确保应用程序与新版本兼容。

下面是兼容性对照表的示例。 Y 表示兼容，N 表示可能不兼容：

| 兼容性类型 | 主版本| 次版本 | 修补程序 |
| --- | --- | --- | --- |
| 客户端-服务器网络兼容性 | N | Y | Y |
| 服务器-服务器兼容性 | N | Y | Y |
| 文件格式兼容性 | N | Y | Y |
| 客户端 API 兼容性 | N | Y | Y |
| 客户端二进制文件兼容性 | N | N | Y |
| **服务器端受限的 API 兼容性** |  |  |  |
| Stable | N | Y | Y |
| 不断变化 | N | N | Y |
| 不稳定 | N | N | N |
| 依赖项兼容性 | N | Y | Y |
| 操作兼容性 | N | N | Y |

有关 HDInsight 版本和兼容性的详细信息，请参阅 [Azure HDInsight 版本](../hdinsight-component-versioning.md)。

## <a name="apache-hbase-cluster-migration-overview"></a>Apache HBase 群集迁移概述

若要升级 Azure HDInsight 上的 Apache HBase 群集，请完成以下基础步骤。 有关详细说明，请参阅详细步骤和命令。

准备源群集：
1. 停止数据引入。
1. 刷新 memstore 数据。
1. 从 Ambari 停止 HBase。
1. 对于具有加速写入的群集，请备份“Write Ahead Log (WAL)”目录。

准备目标群集：
1. 创建目标群集。
1. 从 Ambari 停止 HBase。
1. 在 HDFS 服务配置中更新 `fs.defaultFS` 以引用原始源群集容器。
1. 对于具有加速写入的群集，在 HBase 服务配置中更新 `hbase.rootdir` 以引用原始源群集容器。
1. 清理 Zookeeper 数据。

完成迁移：
1. 清理并迁移 WAL。
1. 将应用程序从目标群集的默认容器复制到原始源容器。
1. 从 Ambari 目标群集启动所有服务。
1. 验证 HBase。
1. 准备源群集。

## <a name="detailed-migration-steps-and-commands"></a>详细的迁移步骤和命令

使用这些详细步骤和命令迁移 Apache HBase 群集。

### <a name="prepare-the-source-cluster"></a>准备源群集

1. 停止引入到源 HBase 群集。
   
1. 刷新要升级的源 HBase 群集。
   
   HBase 将传入的数据写入名为“memstore”的内存中存储。 memstore 达到特定的大小后，HBase 会将其刷新到群集存储帐户中用作长期存储的磁盘中。 升级后删除源群集也会删除 memstore 中的任何数据。 若要保留数据，在升级之前，请手动将每个表的 memstore 刷新到磁盘。
   
   可以从 [Azure hbase-utils GitHub 存储库](https://github.com/Azure/hbase-utils/)运行 [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) 脚本来刷新 memstore 数据。
   
   还可以从 HDInsight 群集运行以下 HBase shell 命令来刷新 memstore 数据：
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. 在源群集 (`https://<OLDCLUSTERNAME>.azurehdinsight.net`) 上登录 [Apache Ambari](https://ambari.apache.org/)，然后停止 HBase 服务。
   
1. 在弹出确认提示时，选中方框以打开 HBase 的维护模式。
   
   有关连接和使用 Ambari 的详细信息，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari.md)。
   
1. 如果源 HBase 群集没有[加速写入](apache-hbase-accelerated-writes.md)功能，请跳过此步骤。 对于具有加速写入的源 HBase 群集，从源群集的任何 Zookeeper 节点或工作器节点上的 SSH 会话运行以下命令，从而备份 HDFS 下的 WAL 目录。
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```
   
### <a name="prepare-the-destination-cluster"></a>准备目标群集

1. 在 Microsoft Azure 门户中，使用与源群集相同的存储帐户 [设置新的目标 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)，但使用不同的容器名称：

   
1. 在新群集 (`https://<NEWCLUSTERNAME>.azurehdinsight.net`) 上登录 [Apache Ambari](https://ambari.apache.org/)，然后停止 HBase 服务。
   
1. 在“Services（服务）” > “HDFS” > “Configs（配置）” > “Advanced（高级）” > “Advanced core-site（高级核心站点）”下，将 `fs.defaultFS` HDFS 设置更改为指向原始源群集容器名称。 例如，以下屏幕截图中的设置应更改为 `wasbs://hbase-upgrade-old-2021-03-22`。
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="在 Ambari 中，选择“Services（服务）”>“HDFS”>“Configs（配置）”>“Advanced（高级）”>“Advanced core-site（高级核心站点）”，并更改容器名称。" border="false":::
   
1. 如果目标群集具有加速写入功能，请更改 `hbase.rootdir` 路径以指向原始源群集容器名称。 例如，以下路径应更改为 `hbase-upgrade-old-2021-03-22`。 如果群集没有加速写入，请跳过此步骤。
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="在 Ambari 中更改 HBase rootdir 的容器名称。" border="true":::
   
1. 在任何 Zookeeper 节点或工作器节点中运行以下命令，从而清除目标群集上的 Zookeeper 数据：
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
### <a name="clean-and-migrate-wal"></a>清理并迁移 WAL

根据源 HDI 版本以及源群集和目标群集是否具有加速写入运行以下命令。

- 目标群集始终是 HDI 版本 4.0，因为 HDI 3.6 为 Microsoft Azure 基本支持，不建议用于新群集。
- HDFS 复制命令为 `hdfs dfs <copy properties starting with -D> -cp <source> <destination> # Serial execution`。

> [!NOTE]  
> - 存储类型 WASB 的 `<source-container-fullpath>` 为 `wasbs://<source-container-name>@<storageaccountname>.blob.core.windows.net`。
> - 存储类型 Azure Data Lake Storage Gen2 的 `<source-container-fullpath>` 为 `abfs://<source-container-name>@<storageaccountname>.dfs.core.windows.net`。

- [源群集为 HDI 3.6 且具有加速写入，目标群集具有加速写入](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [源群集为 HDI 3.6 且不具有加速写入，目标群集具有加速写入](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [源群集为 HDI 3.6 且不具有加速写入，目标群集不具有加速写入](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes)。
- [源群集为 HDI 4.0 且具有加速写入，目标群集具有加速写入](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [源群集为 HDI 4.0 且不具有加速写入，目标群集具有加速写入](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes)。
- [源群集为 HDI 4.0 且不具有加速写入，目标群集不具有加速写入](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes)。

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>源群集为 HDI 3.6 或 HDI 4.0 且具有加速写入，目标群集具有加速写入

清理目标群集的 WAL FS 数据，并将 WAL 目录从源群集复制到目标群集的 HDFS 中。 通过在任何 Zookeeper 节点或工作器节点中运行以下命令来复制目录：

```bash   
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://mycluster/
```
#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>源群集为 HDI 3.6 且不具有加速写入，目标群集具有加速写入

清理目标群集的 WAL FS 数据，并将 WAL 目录从源群集复制到目标群集的 HDFS 中。 通过在任何 Zookeeper 节点或工作器节点中运行以下命令来复制目录：

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container>/hbase/*WALs hdfs://mycluster/hbasewal
```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>源群集为 HDI 3.6 且不具有加速写入，目标群集不具有加速写入

清理目标群集的 WAL FS 数据，并将源群集的 WAL 目录复制到目标群集的 HDFS 中。 通过在任何 Zookeeper 节点或工作器节点中运行以下命令来复制该目录：

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>源群集为 HDI 4.0 且不具有加速写入，目标群集具有加速写入

清理目标群集的 WAL FS 数据，并将 WAL 目录从源群集复制到目标群集的 HDFS 中。 通过在任何 Zookeeper 节点或工作器节点中运行以下命令来复制目录：

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wals/* hdfs://mycluster/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>源群集为 HDI 4.0 且不具有加速写入，目标群集不具有加速写入

清理目标群集的 WAL FS 数据，并将源群集的 WAL 目录复制到目标群集的 HDFS 中。 通过在任何 Zookeeper 节点或工作器节点中运行以下命令来复制该目录：

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
```

### <a name="complete-the-migration"></a>完成迁移

1. 使用 `sudo -u hdfs` 用户上下文，将文件夹 `/hdp/apps/<new-version-name>` 及其内容从 `<destination-container-fullpath>` 复制到 `<source-container-fullpath>` 下的 `/hdp/apps` 文件夹。 可以通过在目标群集上运行以下命令来复制文件夹：
   
   ```bash   
   sudo -u hdfs hdfs dfs -cp /hdp/apps/<hdi-version> <source-container-fullpath>/hdp/apps
   ```
   
   例如：
   ```bash
   sudo -u hdfs hdfs dfs -cp /hdp/apps/4.1.3.6 wasbs://hbase-upgrade-old-2021-03-22@hbaseupgrade.blob.core.windows.net/hdp/apps
   ```
   
1. 在目标群集上保存更改，然后重启 Ambari 指示的所有所需服务。
   
1. 将应用程序指向目标群集。
   
   > [!NOTE]  
   > 升级时，应用程序的静态 DNS 名称会更改。 请勿硬编码此 DNS 名称，可以在域名的 DNS 设置中配置一个指向群集名称的 CNAME。 另一种做法是使用应用程序的、无需重新部署即可更新的配置文件。
   
1. 开始引入。
   
1. 验证 HBase 一致性以及简单数据定义语言 (DDL) 和数据操作语言 (DML) 操作。
   
1. 如果目标群集没有问题，请删除源群集。

## <a name="next-steps"></a>后续步骤

若要详细了解 [Apache HBase](https://hbase.apache.org/) 以及如何升级 HDInsight 群集，请参阅以下文章：

- [将 HDInsight 群集升级到更新版本](../hdinsight-upgrade-cluster.md)
- [使用 Apache Ambari Web UI 监视和管理 Azure HDInsight](../hdinsight-hadoop-manage-ambari.md)
- [Azure HDInsight 版本](../hdinsight-component-versioning.md)
- [优化 Apache HBase](../optimize-hbase-ambari.md)
