---
title: 使用 Spark 进行数据探索和建模 - Team Data Science Process
description: 展示 HDInsight Spark 上的 Spark MLlib 工具包的数据探索和建模功能。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: a55a6457ba39be43481d5b3cdee3dfa2f9d0dcbf
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285258"
---
# <a name="data-exploration-and-modeling-with-spark"></a>使用 Spark 进行数据探索和建模

了解如何使用 HDInsight Spark 训练机器学习模型以使用 Spark MLlib 预测出租车费用。

本示例展示了 [Team Data Science Process](./index.yml) 中的各个步骤。 NYC 出租车行程和车费 2013 数据集的一个子集用于加载、探索和准备数据。 然后，使用 Spark MLlib，对二元分类和回归模型进行训练，以预测是否将为行程支付小费并估算小费金额。

## <a name="prerequisites"></a>先决条件

需要一个 Azure 帐户和一个 Spark 1.6（或 Spark 2.0）HDInsight 群集来完成本演练。 有关如何满足这些要求的说明，请参阅[在 Azure HDInsight 上使用 Spark 的数据科学的概述](spark-overview.md)。 该主题还包含此处使用的 NYC 2013 出租车数据的说明以及有关如何在 Spark 群集上执行来自 Jupyter 笔记本的代码的说明。 

### <a name="spark-clusters-and-notebooks"></a>Spark 群集和笔记本

本演练中提供的设置步骤和代码适用于 HDInsight Spark 1.6。 但是，Jupyter 笔记本是针对 HDInsight Spark 1.6 和 Spark 2.0 群集提供的。 包含这些笔记本的 GitHub 存储库的 [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) 中提供了这些笔记本的说明和链接。 而且，此处和位于链接笔记本中的代码是泛型代码，应适用于任何 Spark 群集。 如果不使用 HDInsight Spark，群集设置和管理步骤可能与此处所示内容稍有不同。 为方便起见，下面提供了适用于 Spark 1.6 的 Jupyter 笔记本的链接（要在 Jupyter 笔记本服务器的 pySpark 内核中运行）和适用于 Spark 2.0 的 Jupyter 笔记本的链接（要在 Jupyter 笔记本服务器的 pySpark3 内核中运行）：

- [Spark 1.6 笔记本](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/)：提供有关如何使用多种不同算法执行数据探索、建模和评分的信息。
- [Spark 2.0 笔记本](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/)：提供有关如何执行回归和分类任务的信息。 数据集可能有所不同，但步骤和概念适用于各种数据集。

[!INCLUDE [delete-cluster-warning](../../hdinsight/includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> 下面的说明与使用 Spark 1.6 相关。 对于 Spark 2.0 版本，请使用上面所述和链接的笔记本。 

## <a name="setup"></a>设置

Spark 能够读取和写入 Azure 存储 Blob（也称为 WASB）。 因此，存储在该处的任何现有数据都可以使用 Spark 处理，并将结果再次存储在 WASB 中。

若要在 WASB 中保存模型或文件，需要正确指定路径。 可使用开头为以下内容的路径，引用附加到 Spark 群集的默认容器：“wasb:///”。 其他位置通过“wasb://”引用。

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>在 WASB 中为存储位置设置目录路径

以下代码示例指定要读取的数据位置，以及用于保存模型输出的模型存储目录的路径：

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>导入库

设置还要求导入必需的库。 设置 Spark 上下文并使用以下代码导入必要的库：

```python
# IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>预设 Spark 上下文和 PySpark magic

与 Jupyter 笔记本一起提供的 PySpark 内核具有预设上下文。 因此在开始处理正在开发的应用程序前无需显式设置 Spark 或 Hive 上下文。 这些上下文默认可用。 这些上下文包括：

* sc - 用于 Spark 
* sqlContext - 用于 Hive

PySpark 内核提供一些预定义的“magic”，这是可以结合 %% 调用的特殊命令。 在这些代码示例中使用的此类命令有两个。

* **%%local** 指定本地执行后续行中的代码。 代码必须是有效的 Python 代码。
* **%%sql -o \<variable name>** 针对 sqlContext 执行 Hive 查询。 如果传递了 -o 参数，则查询的结果以 Pandas 数据帧的形式保存在 %%local Python 上下文中。

有关 Jupyter 笔记本内核和预定义“magic”的详细信息，请参阅[适用于装有 HDInsight 上的 HDInsight Spark Linux 群集的 Jupyter 笔记本的内核](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)。

## <a name="load-the-data"></a>加载数据

数据科学过程的第一步是从源引入数据以供分析，使其驻留在数据探索和建模环境中。 在本演练中环境为 Spark。 本部分包含要完成一系列任务的代码：

* 引入数据样本以供建模
* 读取输入数据集（以 .tsv 文件的形式存储）
* 格式化并清理数据
* 在内存中创建并缓存对象（RDD 或数据帧）
* 在 SQL 上下文中将其注册为临时表。

下面是用于数据引入的代码。

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**输出：**

执行以上单元格所花的时间：51.72 秒

## <a name="explore-the-data"></a>浏览数据

将数据引入到 Spark 中后，数据科学过程的下一步是通过探索和可视化更深入地了解数据。 在本部分中，我们使用 SQL 查询检查出租车数据，并绘制目标变量和预期特征以供视觉检查。 具体而言，我们绘制出租车行程中乘客数的频率、小费金额的频率以及小费如何随支付金额和类型而变化。

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>绘制出租车小费样本中乘客数频率的直方图

此代码和后续代码段使用 SQL magic 查询样本，使用本地 magic 绘制数据。

* **SQL magic (`%%sql`)** HDInsight PySpark 内核支持针对 sqlContext 的轻松内联 HiveQL 查询。 (-o VARIABLE_NAME) 参数在 Jupyter 服务器上将 SQL 查询的输出保留为 Pandas 数据帧。 此设置使输出在本地模式下可用。
* **`%%local` magic** 用于在 Jupyter 服务器上本地运行代码，该服务器是 HDInsight 群集的头节点。 通常，将 `%%local` magic 与 `%%sql` magic 和 -o 参数结合使用。 -o 参数将本地保留 SQL 查询的输出，然后 %%local magic 将触发下一组代码片段，以针对本地保留的 SQL 查询输出本地运行

该输出在运行代码后自动可视化。

此查询通过乘客数检索行程。 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

此代码从查询输出创建本地数据帧，并绘制数据。 `%%local` magic 创建本地数据帧 `sqlResults`，可用于使用 matplotlib 进行绘制。 

> [!NOTE]
> 此 PySpark magic 在本演练中多次使用。 如果数据量很大，应采样以创建本地内存可容纳的数据帧。

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

下面是用于按乘客数绘制行程的代码

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**输出：**

![按乘客数的行程频率](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

可通过使用笔记本中的“类型”菜单按钮从多个不同类型的可视化（表、饼图、线图、面积图或条形图）中选择。 此处显示了条形图。

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>绘制小费金额以及小费金额如何随乘客数和车费金额变化的直方图。

使用 SQL 查询采样数据。

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

此代码单元格使用 SQL 查询创建三个数据图。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**输出：** 

![小费金额分布](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![按乘客数的小费金额](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![按车费金额的小费金额](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>准备数据

本部分介绍并提供使数据准备好在 ML 建模中使用的过程的代码。 它介绍如何执行以下任务：

* 通过将小时装入交通时间存储桶来创建新特征
* 为分类特征编制索引并进行编码
* 创建标签点对象用于输入到 ML 函数中
* 创建数据的随机子采样，并将其拆分为训练集和测试集
* 特征缩放
* 在内存中缓存对象

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>通过将小时装入交通时间存储桶来创建新特征

此代码显示如何通过将小时装入交通时间存储桶创建新特征，以及如何在内存中缓存生成的数据帧。 当重复使用弹性分布式数据集 (RDD) 和数据帧时，缓存导致执行时间改善。 相应地，我们在演练中的多个阶段缓存 RDD 和数据帧。 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**输出：**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>为分类特征编制索引并进行编码以输入到建模函数中

本部分介绍如何为分类特征编制索引或进行编码以输入到建模函数中。 MLlib 的建模和预测函数需要带有分类输入数据的特征在使用前编制索引或进行编码。 根据模型，需要以不同方式为它们编制索引或进行编码：  

* **基于树的建模** 需要将类别编码为数值（例如，具有三个类别的特征可能会使用 0、1、2 进行编码）。 此算法由 MLlib 的 [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) 函数提供。 此函数将标签的字符串列编码为标签索引列，它们将按标签频率进行排序。 虽然使用输入和数据处理的数值编制索引，但可指定基于树的算法，将其视为类别进行相应处理。 
* **逻辑和线性回归模型** 需要独热编码，例如，具有三个类别的特征可扩展为三个特征列，每个根据观察的类别包含 0 或 1。 MLlib 提供 [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 函数用于执行独热编码。 此编码器将标签索引列映射到二元向量列，该列最多只有单个值。 此编码允许将预期数值特征的算法（如逻辑回归）应用到分类特征。

下面是用于为分类特征编制索引和编码的代码：

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

执行以上单元格所花的时间：1.28 秒

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>创建标签点对象用于输入到 ML 函数中

本部分包含的代码显示如何将分类文本数据编制索引为标签点数据类型，并对其编码，以便它可用于训练和测试 MLlib 逻辑回归和其他分类模型。 标签点对象是弹性分布式数据集 (RDD)，格式化为 MLlib 中的大多数 ML 算法所需的输入数据。 [标签点](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)是本地向量，可能密集，也可能稀疏，与标签/响应相关联。  

本部分包含的代码显示如何将分类文本数据编制索引为[标签点](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)数据类型，并对其编码，以便它可用于训练并测试 MLlib 逻辑回归和其他分类模型。 标签点对象是弹性分布式数据集 (RDD)，它们包含标签（目标/响应变量）和特征向量。 此格式是 MLlib 中许多 ML 算法输入时所需的格式。

下面是用于针对二元分类为文本特征编制索引和编码的代码。

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

下面是用于针对线性回归分析为分类文本特征编码和编制索引的代码。

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>创建数据的随机子采样，并将其拆分为训练集和测试集

此代码创建数据的随机采样（此处使用 25%）。 尽管由于数据集的大小，本示例不需要此操作，但我们演示了如何在此处采样，以便你知道如何在需要时针对自己的问题使用它。 当样本很大时，采样可以在训练模型时节省大量时间。 接下来我们将样本拆分为训练部分（此处为 75%）和测试部分（此处为 25%），用于分类和回归建模。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

执行以上单元格所花的时间：0.24 秒

### <a name="feature-scaling"></a>特征缩放

特征缩放（也称为数据规范化）确保具有广泛分散的值的特征不在目标函数中得到过多权重。 用于特征缩放的代码使用 [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.classification.LogisticRegressionWithLBFGS.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS
) 将特征缩放为单位差异。 它由 MLlib 提供，用于使用随机梯度下降 (SGD) 的线性回归，随机梯度下降是一种用于训练范围广泛的其他机器学习模型（如正则化回归或支持向量机 (SVM)）的流行算法。

> [!NOTE]
> 我们发现，LinearRegressionWithSGD 算法对特征缩放很敏感。

下面是用于缩放变量以用于正则化线性 SGD 算法的代码。

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

执行以上单元格所花的时间：13.17 秒

### <a name="cache-objects-in-memory"></a>在内存中缓存对象

可通过缓存用于分类、回归和缩放特征的输入数据时间帧来缩短训练和测试 ML 算法所需的时间。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：** 

执行以上单元格所花的时间：0.15 秒

## <a name="train-a-binary-classification-model"></a>训练二元分类模型

本部分介绍如何将二元分类任务的三个模型用于预测是否为某个出租车行程支付小费。 显示的模型包括：

* 正则化逻辑回归 
* 随机林模型
* 梯度提升树

每个模型生成代码部分拆分为多个步骤： 

1. 带有一个参数集的 **模型训练** 数据
2. 测试数据集上的使用指标的 **模型评估**
3. 在 blob 中 **保存模型** 以供将来使用

### <a name="classification-using-logistic-regression"></a>使用逻辑回归的分类

本部分中的代码显示如何使用 [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) 训练、评估和保存逻辑回归模型，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。

**使用 CV 和超参数扫描训练逻辑回归模型**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：** 

系数：[0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

截距：-0.0111216486893

执行以上单元格所花的时间：14.43 秒

**通过标准指标评估二元分类模型**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**输出：** 

PR 下的面积 = 0.985297691373

ROC 下的面积 = 0.983714670256

摘要统计信息

精度 = 0.984304060189

召回率 = 0.984304060189

F1 分数 = 0.984304060189

执行以上单元格所花的时间：57.61 秒

**绘制 ROC 曲线。**

在上一个单元格中，*predictionAndLabelsDF* 注册为表 *tmp_results*。 *tmp_results* 可用于执行查询并将结果输出到 sqlResults 数据帧中用于绘图。 代码如下。

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

下面是进行预测并绘制 ROC 曲线的代码。

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**输出：**

![Logistic regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>随机林分类

本部分中的代码显示如何训练、评估和保存随机林模型，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

ROC 下的面积 = 0.985297691373

执行以上单元格所花的时间：31.09 秒

### <a name="gradient-boosting-trees-classification"></a>梯度提升树分类

本部分中的代码显示如何训练、评估和保存渐变提升树模型，该模型在 NYC 出租车行程和车费数据集中预测某个行程是否支付小费。

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

ROC 下的面积 = 0.985297691373

执行以上单元格所花的时间：19.76 秒

## <a name="train-a-regression-model"></a>训练回归模型

本部分介绍如何针对基于其他小费特征预测某个出租车行程支付的小费金额的回归任务使用三个模型。 显示的模型包括：

* 正则化线性回归
* 随机林
* 梯度提升树

简介中介绍了这些模型。 每个模型生成代码部分拆分为多个步骤： 

1. 带有一个参数集的 **模型训练** 数据
2. 测试数据集上的使用指标的 **模型评估**
3. 在 blob 中 **保存模型** 以供将来使用

### <a name="linear-regression-with-sgd"></a>使用 SGD 的线性回归

本部分中的代码显示如何使用缩放特征训练使用随机梯度下降线性回归 (SGD) 进行优化的线性回归，以及如何在 Azure Blob 存储 (WASB) 中评分、评估和保存模型。

> [!TIP]
> 根据我们的经验，LinearRegressionWithSGD 模型的收敛可能出现问题，需要仔细更改/优化参数以获取有效的模型。 变量的缩放对收敛帮助很大。

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

系数：[0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

截距：0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

执行以上单元格所花的时间：58.42 秒

### <a name="random-forest-regression"></a>随机林回归

本部分中的代码显示如何训练、评估和保存随机林回归，它将预测 NYC 出租车行程数据的小费金额。

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

RMSE = 0.891209218139

R-sqr = 0.759661334921

执行以上单元格所花的时间：49.21 秒

### <a name="gradient-boosting-trees-regression"></a>梯度提升树回归

本部分中的代码显示如何训练、评估和保存梯度提升树模型，该模型预测 NYC 出租车行程数据的小费金额。

**训练和评估**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**输出：**

RMSE = 0.908473148639

R-sqr = 0.753835096681

执行以上单元格所花的时间：34.52 秒

**图**

*tmp_results* 在上一个单元格中注册为 Hive 表。 表中的结果输出到 *sqlResults* 数据帧中用于绘图。 代码如下

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

下面是用于使用 Jupyter 服务器绘制数据的代码。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**输出：**

![Actual-vs-predicted-tip-amounts](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>从内存中清理对象

使用 `unpersist()` 删除内存中缓存的对象。

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>保存模型

要按照[对 Spark 生成的机器学习模型评分和评估](spark-model-consumption.md)主题中所述内容使用和评分独立数据集，必须复制此处所创建的保存模型的这些文件名，并将其粘贴到 Consumption Jupyter 笔记本。 下面是打印该处所需模型文件的路径的代码。

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>后续步骤

现在已使用 Spark MlLib 创建了回归和分类模型，可了解如何评分和评估这些模型。 高级数据浏览和建模笔记本深入探讨到包括交叉验证、超参数扫描和模型评估。 

**使用模型：** 若要了解如何评分和评估在本主题中创建的分类和回归模型，请参阅 [评分和评估 Spark 构建的机器学习模型](spark-model-consumption.md)。

**交叉验证和超参数扫描**：请参阅 [使用 Spark 进行高级数据探索和建模](spark-advanced-data-exploration-modeling.md)，了解如何使用交叉验证和超参数扫描训练模型
