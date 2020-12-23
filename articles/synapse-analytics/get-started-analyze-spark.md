---
title: 快速入门：开始使用 Spark 进行分析
description: 在本教程中，你将了解如何使用 Apache Spark 分析数据
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: ee4dc945f63180fd06f13287b22949d0ac1e3873
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862014"
---
# <a name="analyze-with-apache-spark"></a>使用 Apache Spark 进行分析

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>使用 Spark 分析 Blob 存储中的纽约市出租车数据

本教程介绍使用 Apache Spark for Azure Synapse 加载和分析数据的基本步骤。

1. 在“数据”中心，单击“添加新资源”（“已链接”的上方的加号按钮）>“浏览库”   。 
1. 找到“纽约市出租车和豪华轿车委员会 - 黄色出租车行程记录”，然后单击它。 
1. 在页面底部按“继续”，然后按“添加数据集” 。 
1. 现在，在“数据”中心的“已链接”下，右键单击“Azure Blob 存储”>>“示例数据集”>>“nyc_tlc_yellow”，然后选择“新建笔记本”   
1. 这将会使用以下代码创建新笔记本：
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. 在笔记本中，在“附加到”菜单中选择无服务器 Spark 池
1. 选择单元上的“运行”
1. 如果只想查看数据帧的架构，请通过以下代码运行单元：
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>将纽约市出租车数据加载到 Spark nyctaxi 数据库

SQLPOOL1 的表中有可用数据。 将其加载到名为 nyctaxi 的 Spark 数据库。

1. 在 Synapse Studio 中，转到“开发”中心。
1. 选择 + > “笔记本” 。
1. 在笔记本顶部，将“附加到”值设置为 Spark1 。
1. 选择“添加代码”以添加笔记本代码单元，并粘贴以下文本：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 转到“数据”中心，右键单击“数据库”，然后选择“刷新”  。 应看到以下数据库：

    - SQLPOOL1 (SQL)
    - nyctaxi (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和笔记本分析纽约市出租车数据

1. 返回到笔记本。
1. 创建新代码单元并输入以下文本。 然后运行该单元以显示我们加载到 nyctaxi Spark 数据库中的纽约市出租车数据。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 运行以下代码，执行之前在专用 SQL 池 SQLPOOL1 中所做的相同分析。 此代码将分析结果另存到名为 nyctaxi.passengercountstats 的表，然后可视化结果。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 在单元结果中，选择“图表”以查看直观呈现出来的数据。





## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>将 Spark 表中的数据加载到专用 SQL 池表

之前我们将数据从专用 SQL 池表 SQLPOOL1.dbo.Trip 复制到了 Spark 表 nyctaxi.trip 中 。 然后，我们使用 Spark 将数据聚合到了 Spark 表 nyctaxi.passengercountstats。 现在，我们会将数据从 nyctaxi.passengercountstats 复制到名为 SQLPOOL1.dbo.PassengerCountStats 的专用 SQL 池表中 。

在笔记本中运行以下单元。 此操作会将聚合的 Spark 表复制回专用 SQL 池表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 SQL 池分析数据](get-started-analyze-sql-on-demand.md)


