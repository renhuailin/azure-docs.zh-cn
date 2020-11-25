---
title: 用 Apache Spark 实现数据的可视化效果
description: 使用 Apache Spark 和 Azure Synapse Analytics 笔记本创建丰富的数据可视化
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919497"
---
# <a name="analyze-data-with-apache-spark"></a>用 Apache Spark 分析数据

在本教程中，你将了解如何使用 Azure 开放式数据集和 Apache Spark 执行探索数据分析，然后在 Azure Synapse Studio 笔记本中直观呈现结果。

具体而言，我们将分析 [纽约 (NYC) 出租车](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 数据集。 数据通过 Azure 开放数据集提供。 此数据集的子集包含有关黄色出租车行程的信息，其中包括每个行程的相关信息、开始时间和结束时间以及地点、成本和其他有趣的属性。
  
## <a name="before-you-begin"></a>准备阶段
- 按照[创建 Apache Spark 池教程](../articles/../quickstart-create-apache-spark-pool-studio.md)创建 Apache Spark 池 

## <a name="download-and-prepare-the-data"></a>下载并准备数据
1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅 [创建笔记本](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook)。 
   
> [!Note]
> 
> 由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 上下文。
>

2. 在本教程中，我们将使用几个不同的库来帮助我们直观显示数据集。 若要进行此分析，我们需要导入以下库： 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. 由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 通过使用开放数据集 API 检索数据，创建 Spark 数据帧。 在这里，我们将使用 Spark 数据帧 *架构读取* 属性来推断数据类型和架构。

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. 读取数据后，我们需要执行一些初始筛选来清理数据集。 我们可以删除不需要的列，并添加提取重要信息的其他列。 此外，我们还将筛选出数据集内的异常。

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

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL 幻 
首先，我们将使用 Synapse 笔记本 Apache Spark SQL 和魔棒命令来执行探索性数据分析。 获得查询后，我们将使用内置功能对结果进行可视化 ```chart options``` 。 

1. 在笔记本中创建一个新单元格，并复制以下代码。 使用此查询，我们想要了解平均笔尖量在所选时间段内的变化情况。 此查询还将帮助我们确定其他有用的见解，包括每天的最小/最大 tip 数量和平均费用金额。
   
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

2. 查询完成运行后，可以通过切换到 " **图表" 视图** 来可视化结果。 在此示例中，我们将通过 **line chart** 将 ```day_of_month``` 字段指定为 **键** 并将指定 ```avgTipAmount``` 为 **值** 来创建折线图。 做出选择后，单击 " **应用** " 以刷新图表。 
   
## <a name="visualize-data"></a>可视化数据
除了内置的笔记本图表选项外，还可以使用常用的开源库来创建自己的可视化效果。 在下面的示例中，我们将使用 Seaborn 和 Matplotlib，它们常用于数据可视化的 Python 库。 

> [!Note]
> 
> 默认情况下，每个 Azure Synapse Analytics Apache Spark 池都包含一组常用和默认的库。 可以在 [Azure Synapse 运行时](../spark/apache-spark-version-support.md)中查看库的完整列表。 文档。 此外，若要使第三方或本地生成的代码可用于你的应用程序，你可以将 [库安装](../spark/apache-spark-azure-portal-add-libraries.md) 到你的一个 Spark 池 (预览) 。
>

1. 为了更轻松、更便宜，我们将对数据集进行抽样。 我们将使用内置 Apache Spark 采样功能。 此外，Seaborn 和 Matplotlib 都需要 Pandas 数据帧或 Numpy 数组。 若要获取 Pandas 数据帧，我们将使用 ```toPandas()``` 命令转换我们的数据帧。

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. 首先，我们想要了解数据集中的提示分布。 我们将使用 Matplotlib 创建一个直方图，用于显示 tip 数量和计数的分布。 根据分发情况，我们可以看到，对于小于或等于 $10 的数量，这些提示将会降低。

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![提示直方图](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. 接下来，我们想要了解给定行程的提示与一周中的哪一天之间的关系。 我们将使用 Seaborn 创建一个显示一周中每一天的趋势的盒须图。 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![每日分发提示数](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. 现在，还有一个假设，就是乘客数与出租车总笔尖量之间存在积极关系。 为了验证这一关系，我们将运行以下代码来生成一个框须图，说明每个乘客计数的提示分布情况。 
   
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
![箱形图](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. 最后，我们想要了解费用金额 tip 量之间的关系。 根据结果，我们可以看到，在人们不提示的情况下，有几个观察值。 但是，我们还会在整体费用和刀尖量之间看到正面关系。
   
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
![散点图](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>关闭 Spark 实例

运行完应用程序后，通过关闭选项卡或在笔记本底部的状态面板中选择“结束会话”，关闭笔记本以释放资源。

## <a name="see-also"></a>另请参阅

- [概述：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)
- [使用 Apache SparkML 构建机器学习模型](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 官方文档](https://spark.apache.org/docs/latest/)