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
ms.date: 03/24/2021
ms.openlocfilehash: 1c6f676ba6f4c689a34baf2119a4179e3a5200ad
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113038257"
---
# <a name="analyze-with-apache-spark"></a>使用 Apache Spark 进行分析

本教程介绍使用 Apache Spark for Azure Synapse 加载和分析数据的基本步骤。

## <a name="create-a-serverless-apache-spark-pool"></a>创建无服务器 Apache Spark 池

1. 在 Synapse Studio 的左侧窗格中，选择“管理” > “Apache Spark 池” 。
1. 选择“新建” 
1. 对于“Apache Spark 池名称”，请输入“Spark1” 。
1. 对于“节点大小”，请输入“小” 。
1. 对于“节点数”，请将最小值设置为 3，将最大值设置为 3
1. 选择“查看 + 创建” > “创建”。 你的 Apache Spark 池将在几秒钟内准备就绪。

## <a name="understanding-serverless-apache-spark-pools"></a>了解无服务器 Apache Spark 池

无服务器 Spark 池是一种用于指示用户要如何使用 Spark 的方式。 开始使用池时，会根据需要创建 Spark 会话。 池控制该会话将使用多少个 Spark 资源，以及会话在自动暂停之前的持续时间。 您需要为在该会话期间使用的 spark 资源付费，而不是池本身。 通过这种方式，Spark 池允许使用 Spark，无需担心管理群集。 这类似于无服务器 SQL 池的工作方式。

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>使用 Spark 池分析纽约市出租车数据

1. 在 Synapse Studio 中，转到“开发”中心
2. 创建新的 Notebook
3. 创建新代码单元并将以下代码粘贴到该单元中。
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. 在笔记本的“附加到”菜单中，选择之前创建的 Spark1 无服务器 Spark 池 。
1. 选择单元上的“运行”。 如果需要，Synapse 将启动新的 Spark 会话来运行此单元格。 如果需要新的 Spark 会话，最初将需要大约两秒钟的时间来创建。 
1. 如果只想查看数据帧的架构，请通过以下代码运行单元：

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>将纽约市出租车数据加载到 Spark nyctaxi 数据库

数据可通过名为 df 的数据帧进行访问。 将其加载到名为 nyctaxi 的 Spark 数据库。

1. 在笔记本中新增一个代码单元，然后输入以下代码：

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和笔记本分析纽约市出租车数据

1. 创建新代码单元并输入以下代码。 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 运行该单元以显示我们加载到 nyctaxi Spark 数据库中的纽约市出租车数据。
1. 创建新代码单元并输入以下代码。 我们会分析此数据，并将结果保存到名为“nyctaxi.passengercountstats”的表中。

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


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用专用 SQL 池分析数据](get-started-analyze-sql-pool.md)
