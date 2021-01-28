---
title: 使用 Apache Spark 实现数据可视化
description: 使用 Apache Spark 和 Azure Synapse Analytics 笔记本创建丰富的数据可视化
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942264"
---
# <a name="analyze-data-with-apache-spark"></a>用 Apache Spark 分析数据

在本教程中，你将了解如何使用 Azure 开放式数据集和 Apache Spark 执行探索数据分析。 然后，你可以在 Azure Synapse Analytics 的 Synapse Studio 笔记本中显示结果。

具体而言，我们将分析 [纽约 (NYC) 出租车](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 数据集。 数据通过 Azure 开放数据集提供。 此数据集的子集包含有关以下内容的信息：每次行程的相关信息、开始和结束时间、地点、成本和其他有趣的属性。
  
## <a name="before-you-begin"></a>在开始之前
按照[创建 Apache Spark 池教程](../articles/../quickstart-create-apache-spark-pool-studio.md)创建 Apache Spark 池。 

## <a name="download-and-prepare-the-data"></a>下载并准备数据
1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅[创建笔记本](../quickstart-apache-spark-notebook.md#create-a-notebook)。 
   
   > [!Note]
   > 由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 上下文。

2. 在本教程中，我们将使用几个不同的库来帮助我们直观显示数据集。 若要执行此分析，请导入以下库： 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. 由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 通过打开的数据集 API 检索数据，创建 Spark 数据帧。 在这里，我们将使用 Spark 数据帧 *架构读取* 属性来推断数据类型和架构。

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. 读取数据后，我们需要执行一些初始筛选来清理数据集。 我们可能删除不需要的列，并添加提取重要信息的列。 此外，我们将筛选出数据集内的异常。

   ```python
   # Filter the dataset 
   from pyspark.sql.functions import *

   filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                               .filter((df.passengerCount > 0)\
                                   & (df.tipAmount >= 0)\
                                   & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                   & (df.tripDistance > 0) & (df.tripDistance <= 200))

   filtered_df.createOrReplaceTempView("taxi_dataset")
   ```

## <a name="analyze-data"></a>分析数据
作为数据分析人员，您可以使用多种工具来帮助您从数据中提取见解。 在本教程的此部分中，我们将逐步介绍 Azure Synapse Analytics 笔记本中提供的几个有用工具。 在此分析中，我们想要了解在所选时间段内产生更多出租车提示的因素。

### <a name="apache-spark-sql-magic"></a>Apache Spark SQL 幻 
首先，我们将使用 Azure Synapse 笔记本 Apache Spark SQL 和魔棒命令来执行探索性数据分析。 查询完成后，我们将使用内置功能对结果进行可视化 ```chart options``` 。 

1. 在笔记本中，创建一个新单元格，并复制以下代码。 使用此查询，我们想要了解平均笔尖量在我们选择的时间段内的变化情况。 此查询还将帮助我们确定其他有用的见解，包括每天的最小/最大 tip 数量和平均费用金额。
   
   ```sql
   %%sql
   SELECT 
       day_of_month
       , MIN(tipAmount) AS minTipAmount
       , MAX(tipAmount) AS maxTipAmount
       , AVG(tipAmount) AS avgTipAmount
       , AVG(fareAmount) as fareAmount
   FROM taxi_dataset 
   GROUP BY day_of_month
   ORDER BY day_of_month ASC
   ```

2. 查询运行完成后，可以通过切换到 "图表" 视图来可视化结果。 此示例通过 ```day_of_month``` 将字段指定为键并将指定为值来创建折线图 ```avgTipAmount``` 。 做出选择后，选择 " **应用** " 以刷新图表。 
   
## <a name="visualize-data"></a>可视化数据
除了内置的笔记本图表选项外，还可以使用常用的开放源代码库来创建自己的可视化效果。 在下面的示例中，我们将使用 Seaborn 和 Matplotlib。 这些常用于数据可视化的 Python 库。 

> [!Note]
> 默认情况下，Azure Synapse Analytics 中的每个 Apache Spark 池都包含一组常用和默认的库。 可以在 [Azure Synapse 运行时](../spark/apache-spark-version-support.md) 文档中查看库的完整列表。 此外，若要使第三方或本地生成的代码可用于你的应用程序，你可以将 [库安装](../spark/apache-spark-azure-portal-add-libraries.md) 到一个 Spark 池上。

1. 为了更轻松、更便宜，我们将数据集缩减到一倍。 我们将使用内置 Apache Spark 采样功能。 此外，Seaborn 和 Matplotlib 都需要 Pandas 数据帧或 NumPy 数组。 若要获取 Pandas 数据帧，请使用 ```toPandas()``` 命令转换数据帧。

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. 我们想要了解数据集中的提示分布。 我们将使用 Matplotlib 创建一个直方图，以显示 tip 数量和计数的分布情况。 根据分发情况，我们可以看到，对于小于或等于 $10 的数量，这些提示将会呈倾斜。

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![提示直方图。](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. 接下来，我们想要了解给定行程的提示与一周中的哪一天之间的关系。 使用 Seaborn 创建一个框须图，用于汇总一周中每一天的趋势。 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![显示每日提示的分布图。](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. 我们的另一个假设可能是乘客数与出租车总笔尖量之间存在积极关系。 若要验证此关系，请运行以下代码以生成一个框须图，以演示每个乘客计数的提示分布。 
   
   ```python
   # How many passengers tipped by various amounts 
   ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
   ax2.set_title('Tip amount by Passenger count')
   ax2.set_xlabel('Passenger count')
   ax2.set_ylabel('Tip Amount ($)')
   ax2.set_ylim(0,30)
   plt.suptitle('')
   plt.show()
   ```
   ![图：显示箱形图。](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. 最后，我们想要了解费用金额与 tip 量之间的关系。 根据结果，我们可以看到，在人们不提示的情况下，有几个观察值。 但是，我们还会在整体费用和刀尖量之间看到正面关系。
   
   ```python
   # Look at the relationship between fare and tip amounts

   ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
   ax.set_title('Tip amount by Fare amount')
   ax.set_xlabel('Fare Amount ($)')
   ax.set_ylabel('Tip Amount ($)')
   plt.axis([-2, 80, -2, 20])
   plt.suptitle('')
   plt.show()
   ```
   ![Tip 量的散点图。](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>关闭 Spark 实例

完成应用程序的运行后，关闭笔记本以释放资源。 关闭该选项卡，或从笔记本底部的 "状态" 面板中选择 " **结束会话** "。

## <a name="see-also"></a>另请参阅

- [概述：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)
- [使用 Apache SparkML 构建机器学习模型](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark 官方文档](https://spark.apache.org/docs/latest/)