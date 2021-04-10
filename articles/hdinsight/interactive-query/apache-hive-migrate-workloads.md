---
title: 将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0
description: 了解如何将 HDInsight 3.6 上的 Apache Hive 工作负荷迁移到 HDInsight 4.0。
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566064"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>将 Azure HDInsight 3.6 Hive 工作负荷迁移到 HDInsight 4.0

与 HDInsight 3.6 相比，HDInsight 4.0 具有几项更为显著的优势。 此处提供了 [HDInsight 4.0 中的新增功能概述](../hdinsight-version-release.md)。

本文介绍了将 Hive 工作负载从 HDInsight 3.6 迁移到 4.0 的步骤，包括

* Hive 元存储复制和架构升级
* 安全迁移以与 ACID 兼容
* 保留 Hive 安全策略

新旧 HDInsight 群集必须有权访问相同的存储帐户。

将 Hive 表迁移到新的存储帐户需要作为一个单独的步骤进行。 请参阅[跨存储帐户迁移 Hive](./hive-migration-across-storage-accounts.md)。

## <a name="steps-to-upgrade"></a>升级步骤

### <a name="1-prepare-the-data"></a>1.准备数据

* HDInsight 3.6 默认情况下不支持 ACID 表。 但是，如果存在 ACID 表，则对它们运行“MAJOR”压缩。 有关压缩的详细信息，请参阅 [Hive 语言手册](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact)。

* 如果使用 [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)，则 Hive 表位置可能取决于群集的 HDFS 配置。 运行以下脚本操作，使这些位置可移植到其他群集。 请参阅[将脚本操作应用到正在运行的群集](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)。

    |属性 | Value |
    |---|---|
    |Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |节点类型|头|
    |parameters||

### <a name="2-copy-the-sql-database"></a>2.复制 SQL 数据库

* 如果群集使用默认的 Hive 元存储，请根据本[指南](./hive-default-metastore-export-import.md)将元数据导出到外部元存储。 然后，创建外部元存储的副本以进行升级。

* 如果群集使用外部 Hive 元存储，则创建一个它的副本。 选项包括[导出/导入](../../azure-sql/database/database-export.md)和[时间点还原](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)。

### <a name="3-upgrade-the-metastore-schema"></a>3.升级元存储架构

此步骤使用 HDInsight 4.0 中的 [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) 来升级元存储架构。

> [!Warning]
> 此步骤是不可逆的。 请仅在元存储的副本上运行此步骤。

1. 创建临时 HDInsight 4.0 群集以访问 4.0 Hive `schematool`。 可以在此步骤中使用[默认的 Hive 元存储](../hdinsight-use-external-metadata-stores.md#default-metastore)。

1. 从 HDInsight 4.0 群集执行 `schematool` 以升级目标 HDInsight 3.6 元存储：

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > 此实用程序使用客户端 `beeline` 在 `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` 中执行 SQL 脚本。
    >
    > 这些脚本中的 SQL 语法不一定与其他客户端工具兼容。 例如，[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) 和 [Azure 门户上的查询编辑器](../../azure-sql/database/connect-query-portal.md)都要求在每个命令后输入关键字 `GO`。
    >
    > 如果有脚本因资源容量或事务超时而失败，则纵向扩展 SQL 数据库。

1. 使用查询 `select schema_version from dbo.version` 验证最终版本。

    输出应与 HDInsight 4.0 群集中以下 bash 命令的输出一致。

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. 删除临时的 HDInsight 4.0 群集。

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4.部署新的 HDInsight 4.0 群集

创建新的 HDInsight 4.0 群集，[选择升级后的 Hive 元存储](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)和相同的存储帐户。

* 新群集不需要具有相同的默认文件系统。

* 如果元存储包含驻留在多个存储帐户中的表，则需要将这些存储帐户添加到新群集来访问这些表。 请参阅[将其他存储帐户添加到 HDInsight](../hdinsight-hadoop-add-storage.md)。

* 如果 Hive 作业因存储无法访问而失败，请验证表位置是否在添加到群集的存储帐户中。

    使用下面的 Hive 命令来标识表位置：

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5.转换表以符合 ACID

HDInsight 4.0 上的托管表必须符合 ACID 标准。 在 HDInsight 4.0 上运行 `strictmanagedmigration`，以将所有非 ACID 托管表转换为具有属性 `'external.table.purge'='true'` 的外部表。 从头节点执行：

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保护 Hive

HDInsight 可以选择使用 HDInsight 企业安全性套餐 (ESP) 与 Azure Active Directory 相集成。 ESP 使用 Kerberos 和 Apache Ranger 来管理群集中特定资源的权限。 可使用以下步骤，将针对 HDInsight 3.6 中的 Hive 部署的 Ranger 策略迁移到 HDInsight 4.0：

1. 在 HDInsight 3.6 群集中导航到 Ranger 服务管理器面板。
2. 导航到名为 **HIVE** 的策略，并将该策略导出到某个 JSON 文件。
3. 确保导出的策略 JSON 中引用的所有用户都存在于新群集中。 如果该策略 JSON 中引用的某个用户不存在于新群集中，请将该用户添加到新群集，或者从策略中删除引用。
4. 在 HDInsight 4.0 群集中导航到“Ranger 服务管理器”面板。
5. 导航到名为 **HIVE** 的策略，并导入步骤 2 中导出的 Ranger 策略 JSON。

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>HDInsight 4.0 中可能需要更改应用程序的 Hive 变动

* 请参阅[使用 Hive Warehouse Connector 进行其他配置](./apache-hive-warehouse-connector.md)，以在 Spark 和 Hive for ACID 表之间共享元存储。

* HDInsight 4.0 使用[基于存储的授权](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server)。 如果修改文件权限或以不同于 Hive 的用户身份创建文件夹，可能会遇到基于存储权限的 Hive 错误。 若要修复此错误，请向用户授予 `rw-` 访问权限。 请参阅 [HDFS 权限指南](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)。

* `HiveCLI` 替换为 `Beeline`。

请参阅 [HDInsight 4.0 公告](../hdinsight-version-release.md)，了解更多变动。

## <a name="further-reading"></a>延伸阅读

* [HDInsight 4.0 通告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探讨](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)