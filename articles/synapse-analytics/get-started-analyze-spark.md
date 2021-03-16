---
title: 快速入门：开始使用 Spark 进行分析
description: 在本教程中，你将了解如何使用 Apache Spark 分析数据。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423707"
---
# <a name="analyze-with-apache-spark"></a>使用 Apache Spark 进行分析

本教程介绍使用 Apache Spark for Azure Synapse 加载和分析数据的基本步骤。

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>使用 Spark 分析 Blob 存储中的纽约市出租车数据

1. 在“数据”中心中，单击“+”按钮以添加新资源，然后单击 >>“浏览库”   。 
1. 找到“纽约市出租车和豪华轿车委员会 - 黄色出租车行程记录”，然后单击它。 
1. 在页面底部按“继续”，然后按“添加数据集” 。 
1. 片刻之后，在“链接”下的“数据”中心中，右键单击“Azure Blob 存储”>>“示例数据集”>>“nyc_tlc_yellow”，然后选择“新建笔记本”和“加载到数据帧”    。
1. 这将会使用以下代码创建新笔记本：
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. 在笔记本的“附加到”菜单中，选择之前创建的 Spark1 无服务器 Spark 池 。
1. 选择单元上的“运行”。 如果需要，Synapse 将启动新的 Spark 会话来运行此单元格。 如果需要新的 Spark 会话，最初将需要大约两秒钟的时间来创建。 
1. 如果只想查看数据帧的架构，请通过以下代码运行单元：
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>将纽约市出租车数据加载到 Spark nyctaxi 数据库

SQLPOOL1 的表中提供数据。 将其加载到名为 nyctaxi 的 Spark 数据库。

1. 在 Synapse Studio 中，转到“开发”中心。
1. 选择 + > “笔记本” 。
1. 在笔记本顶部，将“附加到”值设置为 Spark1 。
1. 在新笔记本的第一个代码单元中，输入以下代码：


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. 运行该脚本。 可能需要 2-3 分钟。
1. 在“数据”中心的“工作区”选项卡中，右键单击“数据库”，然后选择“刷新”   。 现在，应会在列表中看到数据库 nyctaxi (Spark)。


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和笔记本分析纽约市出租车数据

1. 返回到笔记本。
1. 创建新代码单元并输入以下代码。 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 运行该单元以显示我们加载到 nyctaxi Spark 数据库中的纽约市出租车数据。
1. 创建新代码单元并输入以下代码。 然后运行该单元，执行之前在专用 SQL 池 SQLPOOL1 中所做的相同分析。 此代码显示分析结果并将分析结果保存到名为 nyctaxi.passengercountstats 的表中。


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

之前我们将数据从专用 SQL 池表 SQLPOOL1.dbo.Trip 复制到了 Spark 表 nyctaxi.trip 中 。 然后，你将数据聚合到了 Spark 表 nyctaxi.passengercountstats。 现在，将数据从 nyctaxi.passengercountstats 复制到名为 SQLPOOL1.dbo.PassengerCountStats 的专用 SQL 池表中 。

1. 创建新代码单元并输入以下代码。 在笔记本中运行该单元。 此操作会将聚合的 Spark 表复制回专用 SQL 池表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 SQL 池分析数据](get-started-analyze-sql-on-demand.md)
