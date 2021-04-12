---
title: 将默认 Hive 元存储迁移到 Azure HDInsight 上的外部元存储
description: 将默认 Hive 元存储迁移到 Azure HDInsight 上的外部元存储
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566109"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>将默认 Hive 元存储数据库迁移到外部元存储数据库

本文介绍如何将元数据从 Hive 的[默认元存储数据库](../hdinsight-use-external-metadata-stores.md#default-metastore)迁移到 HDInsight 上的外部 SQL 数据库。 

## <a name="why-migrate-to-external-metastore-db"></a>迁移到外部元存储数据库的原因

* 默认元存储数据库仅限于基本 SKU，无法处理生产规模工作负荷。

* 使用外部元存储数据库，用户能够通过添加新的 HDInsight 群集共享相同的元存储数据库来水平扩展 Hive 计算资源。

* 对于 HDInsight 3.6 到 4.0 的迁移，在升级 Hive 架构版本之前，必须将元数据迁移到外部元存储数据库。 请参阅[将工作负荷从 HDInsight 3.6 迁移到 HDInsight 4.0](./apache-hive-migrate-workloads.md)。

由于默认元存储数据库计算能力有限，因此，我们建议在迁移元数据时，尽量少在群集上进行其他作业。

源数据库和目标数据库都必须使用相同的 HDInsight 版本和相同的存储帐户。 如果将 HDInsight 版本从 3.6 升级到 4.0，请先完成本文中的步骤。 然后，按照[此处](./apache-hive-migrate-workloads.md)的官方升级步骤执行操作。

## <a name="prerequisites"></a>先决条件

如果使用 [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)，则 Hive 表位置可能取决于群集对 Azure Data Lake Storage Gen1 的 HDFS 配置。 运行以下脚本操作，使这些位置可移植到其他群集。 请参阅[将脚本操作应用到正在运行的群集](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)。

此操作类似于将符号链接替换为其完整路径。

|属性 | Value |
|---|---|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|节点类型|头|
|parameters|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>使用 sqlpackage 通过导出/导入实现迁移

仅在 2020 年 10 月 15 日后创建的 HDInsight 群集通过使用 `sqlpackage` 支持 Hive 默认元存储数据库的 SQL 导出/导入。

1. 将 [sqlpackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) 安装到群集。

2. 通过执行以下命令，将默认的元存储数据库导出到 BACPAC 文件。

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. 保存 BACPAC 文件。 以下是一个选项。

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. 按照[此处](../../azure-sql/database/database-import.md)列出的步骤将 BACPAC 文件导入到新数据库。

5. 新数据库已准备就绪，可以[在新的 HDInsight 群集上配置为外部元存储数据库](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)。

## <a name="migrate-using-hive-script"></a>使用 Hive 脚本迁移

2020 年 10 月 15 日之前创建的群集不支持默认元存储数据库的导出/导入。

对于此类群集，请使用第二个群集和[外部 Hive 元存储数据库](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)，按照指南[在存储帐户中复制 Hive 表](./hive-migration-across-storage-accounts.md)执行操作。 第二个群集可以使用相同的存储帐户，但必须使用新的默认文件系统。

### <a name="option-to-shallow-copy"></a>“浅表”复制选项
使用上述指南对表进行“深层”复制时，存储消耗会加倍。 需要手动清理源存储容器中的数据。
相反，如果是非事务性表，则可以对表进行“浅表”复制。 默认情况下，HDInsight 3.6 中的所有 Hive 表都是非事务性的，但在 HDInsight 4.0 中只有外部表是非事务性的。 必须对事务表进行深层复制。 请按照以下步骤对非事务性表进行浅表复制：

1. 在源群集的主头节点上执行脚本 [hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) 以便为每个 Hive 表生成 DDL。
2. 将 DDL 写入名为 `/tmp/hdi_hive_ddls.hql` 的 Hive 本地脚本。 在使用外部 Hive 元存储数据库的目标群集上执行此操作。

## <a name="verify-that-all-hive-tables-are-imported"></a>验证是否已导入所有 Hive 表

以下命令使用元存储数据库上的 SQL 查询来打印所有的 Hive 表及其数据位置。 比较新群集和旧群集之间的输出，以验证新元存储数据库中是否缺少任何表。

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>延伸阅读

* [将工作负荷从 HDInsight 3.6 迁移到 HDInsight 4.0](./apache-hive-migrate-workloads.md)
* [跨存储帐户迁移 Hive 工作负荷](./hive-migration-across-storage-accounts.md)
* [连接到 HDInsight 上的 Beeline](../hadoop/connect-install-beeline.md)
* [排查 Create Table 权限错误](./interactive-query-troubleshoot-permission-error-create-table.md)