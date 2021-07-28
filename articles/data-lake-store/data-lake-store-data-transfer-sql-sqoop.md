---
title: 在 Data Lake Storage Gen1 和 Azure SQL 之间复制数据 - Sqoop | Microsoft Docs
description: 使用 Sqoop 在 Azure SQL 数据库和 Azure Data Lake Storage Gen1 之间复制数据
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109181"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>使用 Sqoop 在 Data Lake Storage Gen1 和 Azure SQL 数据库之间复制数据

了解如何使用 Apache Sqoop 在 Azure SQL 数据库和 Azure Data Lake Storage Gen1 之间导入和导出数据。

## <a name="what-is-sqoop"></a>什么是 Sqoop？

大数据应用程序非常适合用于处理未结构化和半结构化数据，例如日志和文件。 但也可能需要处理关系数据库中存储的结构化数据。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) 是一种用于在关系数据库和大数据存储库（例如 Data Lake Storage Gen1）之间传输数据的工具。 可使用该工具将数据从关系数据库管理系统 (RDBMS)（例如 Azure SQL 数据库）中导入到 Data Lake Storage Gen1。 之后，可使用大数据工作负荷转换和分析数据，然后将此数据返回导出到 RDBMS。 在本文中，将 Azure SQL 数据库中的一个数据库用作要向（从）其导入/导出的关系数据库。

## <a name="prerequisites"></a>先决条件

在开始之前，必须满足以下条件：

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 本文假定用户的群集是具有 Data Lake Storage Gen1 访问权限的 HDInsight Linux 群集。
* **Azure SQL 数据库**。 有关如何在 Azure SQL 数据库中创建数据库的说明，请参阅[在 Azure SQL 数据库中创建数据库](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>在数据库中创建示例表

1. 首先，在数据库中创建两个示例表。 使用 [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) 或 Visual Studio 连接到数据库，并运行以下查询。

    **创建表 1**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **创建表 2**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. 运行以下命令，将一些示例数据添加到表 1。 **表 2** 留空。 稍后，将表 1 数据导入到 Data Lake Storage Gen1。 然后，从 Data Lake Storage Gen1 将数据导出到表 2。

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>使用具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集中的 Sqoop

HDInsight 群集已经具有可用的 Sqoop 包。 如果已经将 HDInsight 群集配置为将 Data Lake Storage Gen1 用作额外的存储，则可使用 Sqoop（不作任何配置更改）在关系数据库（例如 Azure SQL 数据库）和 Data Lake Storage Gen1 帐户之间导入/导出数据。

1. 本文假设已经创建 Linux 群集，因此，应使用 SSH 连接到该群集。 请参阅[连接到基于 Linux 的 HDInsight 群集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 验证是否可从此群集访问 Data Lake Storage Gen1 帐户。 从 SSH 提示符处运行以下命令：

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   此命令提供了 Data Lake Storage Gen1 帐户中文件/文件夹的列表。

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>从 Azure SQL 数据库将数据导入到 Data Lake Storage Gen1

1. 导航至 Sqoop 包可用的目录。 通常，此位置为 `/usr/hdp/<version>/sqoop/bin`。

1. 将数据从 **表 1** 导入到 Data Lake Storage Gen1 帐户。 使用以下语法：

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   sql-database-server-name 占位符表示运行数据库的服务器的名称。 **sql-database-name** 占位符表示实际的数据库名称。

   例如，

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. 验证数据是否已经传输到 Data Lake Storage Gen1 帐户。 运行下面的命令：

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   你会看到以下输出。

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   每个 **part-m-** _ 文件对应源表（表 1）中的一行。*你可以查看 part-m-* 文件的内容进行验证。

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>从 Data Lake Storage Gen1 将数据导出到 Azure SQL 数据库

1. 将数据从 Data Lake Storage Gen1 帐户导出到 Azure SQL 数据库中的空表（表 2）。 使用以下语法。

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   例如，

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. 验证数据是否已经上传到 SQL 数据库表。 使用 [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) 或 Visual Studio 连接到 Azure SQL 数据库，并运行以下查询。

    ```tsql
    SELECT * FROM TABLE2
    ```

   此命令应会显示以下输出。

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>使用 Sqoop 时的性能注意事项

有关为将数据复制到 Data Lake Storage Gen1 而对 Sqoop 作业进行的性能优化的信息，请参阅 [Sqoop 性能博客文章](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)。

## <a name="next-steps"></a>后续步骤

* [将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)