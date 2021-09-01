---
title: 将 Apache Ambari Hive 视图用于 Azure HDInsight 中的 Apache Hadoop
description: 了解如何从 Web 浏览器中使用 Hive 视图来提交 Hive 查询。 Hive 视图是随基于 Linux 的 HDInsight 群集提供的 Ambari Web UI 的一部分。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 484e6ef3623e560f526b637e745d9849f6a8cfb1
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "122652541"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>将 Apache Ambari Hive 视图与 HDInsight 中的 Apache Hadoop 配合使用

[!INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

了解如何使用 Apache Ambari Hive 视图运行 Hive 查询。 Hive 视图允许从 Web 浏览器创作、优化和运行 Hive 查询。

## <a name="prerequisites"></a>先决条件

HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

## <a name="run-a-hive-query"></a>运行 Hive 查询

1. 在 [Azure 门户](https://portal.azure.com/)中，选择群集。  有关说明，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。 群集会在新的门户视图中打开。

1. 从 **群集仪表板** 中，选择“Ambari 视图”。 当提示进行身份验证时，请使用在创建群集时所提供的群集登录名（默认为 `admin`）帐户名称和密码。 还可以在浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/views`，其中 `CLUSTERNAME` 是群集的名称。

1. 在视图列表中，选择“Hive 视图”。

    :::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png" alt-text="Apache Ambari 选择 Apache Hive 视图" border="true":::

    Hive 视图页面类似于下图：

    :::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png" alt-text="Hive 视图查询工作表图像" border="true":::

1. 将以下 HiveQL 语句从“查询”选项卡粘贴到工作表中：

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    这些语句执行以下操作：

    |语句 | 说明 |
    |---|---|
    |DROP TABLE|删除表和数据文件（如果该表已存在）。|
    |CREATE EXTERNAL TABLE|在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义。 数据保留在原始位置。|
    |ROW FORMAT|演示如何设置数据格式。 在此情况下，每个日志中的字段以空格分隔。|
    |STORED AS TEXTFILE LOCATION|显示数据的存储位置，并且数据已存储为文本。|
    |SELECT|选择 t4 列包含值 [ERROR] 的所有行的计数。|

   > [!IMPORTANT]  
   > 将“数据库”选择保留为“默认”。 本文档中的示例使用 HDInsight 附带的默认数据库。

1. 若要启动查询，请选择工作表下方的“执行”。 按钮变为橙色，文本更改为“停止”。

1. 完成查询后，“结果”选项卡显示操作结果。 以下文本是查询结果：

    ```output
    loglevel       count
    [ERROR]        3
    ```

    可使用“日志”选项卡查看创建的作业的日志记录信息。

   > [!TIP]  
   > 从“结果”选项卡下的“操作”下拉对话框下载或保存结果。

### <a name="visual-explain"></a>Visual explain

要显示查询计划的可视化效果，选择工作表下方的“可视化说明”选项卡。

查询的“可视化说明”视图可帮助理解复杂查询的流。

### <a name="tez-ui"></a>Tez UI

若要显示查询的 Tez UI，请选择工作表下方的“Tez UI”选项卡。

> [!IMPORTANT]  
> Tez 不用于解析所有查询。 无需使用 Tez 即可解析许多查询。

## <a name="view-job-history"></a>查看作业历史记录

“作业”选项卡显示 Hive 查询的历史记录。

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png" alt-text="Apache Hive 视图“作业”选项卡历史记录" border="true":::

## <a name="database-tables"></a>数据库表

可使用“表”选项卡处理 Hive 数据库内的表。

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png" alt-text="Apache Hive“表”选项卡的图像" border="true":::

## <a name="saved-queries"></a>已保存的查询

在“查询”选项卡中，可以按需要保存查询。 保存查询后，可通过“已保存的查询”选项卡对其重复进行使用。

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png" alt-text="Apache Hive 视图“已保存的查询”选项卡" border="true":::

> [!TIP]  
> 保存的查询存储在默认群集存储中。 可在路径 `/user/<username>/hive/scripts` 下找到保存的查询。 它们存储为纯文本 `.hql` 文件。
>
> 如果删除群集但保留存储，可使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)或 Data Lake 存储资源管理器（通过 [Azure 门户](https://portal.azure.com)）等实用工具来检索查询。

## <a name="user-defined-functions"></a>用户定义的函数

可以通过用户定义函数 (UDF) 扩展 Hive。 使用 UDF 实现 HiveQL 中不容易建模的功能或逻辑。

使用 Hive 视图顶部的“UDF”选项卡，声明并保存一组 UDF。 可以在 **查询编辑器** 中使用这些 UDF。

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png" alt-text="Apache Hive 视图“UDF”选项卡显示" border="true":::

“插入 UDF”按钮显示在“查询编辑器”的底部 。 此项将显示 Hive 视图中定义的 UDF 的下拉列表。 选择一个 UDF 可向查询添加 HiveQL 语句以启用 UDF。

例如，如果定义了具有以下属性的 UDF：

* 资源名称：myudfs

* 资源路径：/myudfs.jar

* UDF 名称：myawesomeudf

* UDF 类名称：com.myudfs.Awesome

使用“插入 UDF”按钮将显示名为 myudfs 的条目，以及为该资源定义的每个 UDF 的另一下拉列表 。 本例中为 myawesomeudf。 选择此条目会在查询的开头添加以下内容：

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

然后便可在查询中使用 UDF。 例如，`SELECT myawesomeudf(name) FROM people;`。

有关如何在 HDInsight 中将 UDF 与 Hive 配合使用的详细信息，请参阅以下文章：

* [在 HDInsight 中将 Python 与 Apache Hive 和 Apache Pig 配合使用](python-udf-hdinsight.md)
* [将 Java UDF 与 HDInsight 中的 Apache Hive 配合使用](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive 设置

可以更改各种 Hive 设置，例如将 Hive 的执行引擎从 Tez（默认）更改为 MapReduce。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 中 Hive 的常规信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Apache Beeline 客户端与 Apache Hive 配合使用](apache-hadoop-use-hive-beeline.md)
