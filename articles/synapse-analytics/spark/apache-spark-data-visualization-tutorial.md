---
title: 使用 Apache Spark 实现数据可视化
description: 使用 Apache Spark 和 Azure Synapse Analytics 笔记本创建丰富数据可视化效果
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: beef6768f9b2fb05efb77c16c32b0acbe46d1e85
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732485"
---
# <a name="analyze-data-with-apache-spark"></a>使用 Apache Spark 分析数据

在本教程中，你将了解如何使用 Azure 开放数据集和 Apache Spark 执行探索性数据分析。 然后，你可以在 Azure Synapse Analytics 的 Synapse Studio 笔记本中将结果可视化。

具体而言，我们会分析[纽约市 (NYC) 出租车](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)数据集。 数据通过 Azure 开放数据集提供。 此数据集子集包含有关黄色出租车行程的信息：有关每次行程、开始和结束时间、位置、成本和其他令人感兴趣的属性的信息。
  
## <a name="before-you-begin"></a>在开始之前
按照[创建 Apache Spark 池教程](../articles/../quickstart-create-apache-spark-pool-studio.md)创建 Apache Spark 池。 

## <a name="download-and-prepare-the-data"></a>下载并准备数据
1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅[创建笔记本](../quickstart-apache-spark-notebook.md#create-a-notebook)。 
   
   > [!Note]
   > 由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 上下文。

2. 在本教程中，我们将使用几个不同的库来直观地显示数据集。 若要执行此分析，请导入以下库： 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. 由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 通过开放数据集 API 检索数据，创建 Spark 数据帧。 在这里，我们使用 Spark 数据帧“基于读取的架构”属性来推断数据类型和架构。

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. 读取数据后，我们需要执行一些初始筛选来清理数据集。 我们可能会删除不需要的列，并添加可提取重要信息的列。 此外，我们会筛选掉数据集内的异常。

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
作为数据分析人员，你可以使用多种工具从数据中提取见解。 在本教程的此部分，我们将逐步介绍 Azure Synapse Analytics 笔记本中提供的几个有用工具。 在此分析中，我们想要了解在所选时间段内司机获得较多出租车小费的因素。

### <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
首先，我们将使用 Azure Synapse 笔记本通过 Apache Spark SQL 和 magic 命令来执行探索性数据分析。 进行查询以后，我们将使用内置的 ```chart options``` 功能将结果可视化。 

1. 在笔记本中，创建一个新单元格并复制以下代码。 我们想要使用此查询来了解平均小费金额在所选时间段内的变化情况。 此查询还将帮助我们确定其他有用的见解，包括每天的最小/最大小费金额和平均车费金额。
   
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

2. 查询运行完以后，我们可以通过切换到图表视图来可视化结果。 此示例通过将 ```day_of_month``` 字段指定为键并将 ```avgTipAmount``` 指定为值来创建折线图。 做出选择后，请选择“应用”以刷新图表。 
   
## <a name="visualize-data"></a>可视化数据
除了内置的笔记本图表选项外，你还可以使用常用的开放源代码库来创建自己的可视化效果。 在下面的示例中，我们将使用 Seaborn 和 Matplotlib。 这两个都是常用于数据可视化的 Python 库。 

> [!Note]
> 默认情况下，Azure Synapse Analytics 中的每个 Apache Spark 池都包含一组常用的和默认的库。 可以在 [Azure Synapse 运行时](../spark/apache-spark-version-support.md)文档中查看库的完整列表。 此外，若要使第三方或本地生成的代码可用于你的应用程序，可[将库安装](../spark/apache-spark-azure-portal-add-libraries.md)到某个 Spark 池中。

1. 为了降低开发的难度和费用，我们将降低数据集的采样。 我们将使用内置的 Apache Spark 采样功能。 此外，Seaborn 和 Matplotlib 都需要 Pandas 数据帧或 NumPy 数组。 若要获取 Pandas 数据帧，请使用 ```toPandas()``` 命令来转换数据帧。

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. 我们想要了解数据集中小费的分布情况。 我们将使用 Matplotlib 创建一个直方图，以显示小费金额和计数的分布情况。 根据分布情况，我们可以看到，小费倾向于 10 美元或 10 美元以下的金额。

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![小费直方图。](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. 接下来，我们想要了解给定行程的小费与一周中的某一天的关系。 请使用 Seaborn 创建一个箱线图，用于汇总一周中每一天的趋势。 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![显示每日小费分布情况的示意图。](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. 我们的另一个假设是乘客数与出租车小费总金额之间存在正相关的关系。 为了验证此关系，请运行以下代码以生成一个箱线图，以展示按乘客计数的小费分布情况。 
   
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
   ![显示了一个箱线图的示意图。](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. 最后，我们想要了解车费金额与小费金额之间的关系。 从结果来看，我们可以看到几个人们不付小费的案例。 但是，我们也看到，在车费总金额和小费总金额之间存在正相关关系。
   
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
   ![小费金额散点图。](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>关闭 Spark 实例

运行完应用程序以后，请关闭笔记本以释放资源。 请关闭选项卡，或者在笔记本底部的状态面板中选择“结束会话”。

## <a name="see-also"></a>另请参阅

- [概述：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)
- [使用 Apache SparkML 构建机器学习模型](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark 官方文档](https://spark.apache.org/docs/latest/)