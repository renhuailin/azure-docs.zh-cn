---
title: 将默认 Hive 元存储迁移到 Azure HDInsight 上的外部元存储
description: 将默认 Hive 元存储迁移到 Azure HDInsight 上的外部元存储
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 825204fe40125a65e8e6f27c6973417813700a9e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744394"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>将默认 Hive 元存储 DB 迁移到外部元存储 DB

本文介绍如何将元数据从默认的 [元存储 DB](../hdinsight-use-external-metadata-stores.md#default-metastore) 迁移到 HDInsight 上的外部 SQL 数据库。 

## <a name="why-migrate-to-external-metastore-db"></a>迁移到外部元存储 DB 的原因

* 默认元存储 DB 仅限基本 SKU，无法处理生产规模工作负荷。

* 外部元存储 DB 使客户能够通过添加共享同一元存储 DB 的新 HDInsight 群集，对 Hive 计算资源进行水平缩放。

* 对于 HDInsight 3.6 到4.0 的迁移，在升级 Hive 架构版本之前，必须将元数据迁移到外部元存储 DB。 请参阅 [将工作负荷从 hdinsight 3.6 迁移到 hdinsight 4.0](./apache-hive-migrate-workloads.md)。

由于默认元存储 DB 具有有限的计算能力，因此，我们建议在迁移元数据时，从群集上的其他作业中使用低。

源数据库和目标数据库必须使用相同的 HDInsight 版本和相同的存储帐户。 如果将 HDInsight 版本从3.6 升级到4.0，请首先完成本文中的步骤。 然后，按照 [此处](./apache-hive-migrate-workloads.md)的官方升级步骤进行操作。

## <a name="prerequisites"></a>先决条件

如果使用 [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)，则 Hive 表位置可能依赖于 Azure Data Lake Storage Gen1 的群集 HDFS 配置。 运行以下脚本操作，使这些位置可移植到其他群集。 请参阅 [脚本操作到正在运行的群集](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)。

此操作类似于将符号链接替换为其完整路径。

|属性 | Value |
|---|---|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|节点类型|头|
|parameters|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>使用 sqlpackage 进行导出/导入迁移

仅2020-10-15 之后创建的 An HDInsight 群集使用支持 Hive 默认元存储 DB 的 SQL 导出/导入 `sqlpackage` 。

1. 将 [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) 安装到群集。

2. 通过执行以下命令，将默认的元存储 DB 导出到 BACPAC 文件。

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. 保存 BACPAC 文件。 下面是一个选项。

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. 按照 [此处](../../azure-sql/database/database-import.md)列出的步骤将 BACPAC 文件导入到新数据库。

5. 新数据库已准备好，可以 [在新的 HDInsight 群集上配置为外部元存储 DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)。

## <a name="migrate-using-hive-script"></a>使用 Hive 脚本迁移

2020-10-15 之前创建的群集不支持默认元存储 DB 的导出/导入。

对于此类群集，请遵循使用带有[外部 HIVE 元存储 DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)的第二个群集[跨存储帐户复制 Hive 表](./hive-migration-across-storage-accounts.md)指南。 第二个群集可以使用相同的存储帐户，但必须使用新的默认文件系统。

### <a name="option-to-shallow-copy"></a>选项为 "浅" 复制
使用上述指南 "深度" 复制表时，存储消耗会加倍。 需要手动清理源存储容器中的数据。
如果表是非事务性的，则可以改为 "浅" 复制这些表。 默认情况下，HDInsight 3.6 中的所有 Hive 表都是非事务性的，但只有外部表在 HDInsight 4.0 中是非事务性的。 必须对事务表进行深层复制。 请按照以下步骤来浅表复制非事务性表：

1. 在源群集的主头节点上执行脚本 [hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) ，以便为每个 hive 表生成 DDL。
2. DDL 将写入名为的本地 Hive 脚本 `/tmp/hdi_hive_ddls.hql` 。 在使用外部 Hive 元存储 DB 的目标群集上执行此。

## <a name="verify-that-all-hive-tables-are-imported"></a>验证是否已导入所有 Hive 表

下面的命令使用元存储 DB 上的 SQL 查询来打印所有 Hive 表及其数据位置。 比较新群集和旧群集之间的输出，验证新元存储 DB 中是否缺少任何表。

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>延伸阅读

* [将工作负荷从 HDInsight 3.6 迁移到4。0](./apache-hive-migrate-workloads.md)
* [跨存储帐户迁移 Hive 工作负荷](./hive-migration-across-storage-accounts.md)
* [连接到 HDInsight 上的 Beeline](../hadoop/connect-install-beeline.md)
* [排查权限错误 Create Table](./interactive-query-troubleshoot-permission-error-create-table.md)
