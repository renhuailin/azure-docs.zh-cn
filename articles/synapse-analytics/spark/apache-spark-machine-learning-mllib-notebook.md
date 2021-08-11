---
title: 教程：使用 Apache Spark MLlib 生成机器学习应用
description: 该教程介绍如何使用 Apache Spark MLlib 创建机器学习应用，该应用通过逻辑回归使用分类对数据集进行分析。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.custom: subject-rbac-steps
ms.openlocfilehash: c0be8241b438a010a44c4b9dbabbb05d5ac290b3
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217082"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>教程：使用 Apache Spark MLlib 和 Azure Synapse Analytics 构建机器学习应用

本文介绍如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/) 创建机器学习应用程序，该应用程序对 Azure 开放数据集执行简单的预测分析。 Spark 提供内置机器学习库。 此示例通过逻辑回归使用分类。

SparkML 和 MLlib 是核心 Spark 库，提供许多可用于机器学习任务的实用工具，包括适用于以下任务的实用工具：

- 分类
- 回归
- 群集
- 主题建模
- 单值分解 (SVD) 和主体组件分析 (PCA)
- 假设测试和计算示例统计信息

## <a name="understand-classification-and-logistic-regression"></a>了解分类和逻辑回归

*分类* 是一种常见的机器学习任务，是将输入数据按类别排序的过程。 它是一种分类算法的作业，旨在算出如何将标签分配到提供的输入数据。 例如，可以联想机器学习算法，该算法接受股票信息作为输入并将股票划分为两个类别：应该卖出的股票和应该保留的股票。

逻辑回归是可用于分类的算法。 Spark 的逻辑回归 API 可用于 *二元分类*，或将输入数据归类到两组中的一组。 有关逻辑回归的详细信息，请参阅[维基百科](https://en.wikipedia.org/wiki/Logistic_regression)。

总之，逻辑回归的过程会产生一个逻辑函数，可用于预测输入向量属于一个组或另一个组的概率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC 出租车数据的预测分析示例

在此示例中，使用 Spark 对纽约的出租车行程提示数据执行一些预测分析。 数据通过 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)提供。 此数据集的子集包含有关黄色出租车行程的信息，其中包括有关每次行程、开始和结束时间、位置、成本和其他感兴趣属性的信息。

> [!IMPORTANT]
> 从存储位置拉取这些数据可能会产生额外的费用。

在下面的步骤中，你将开发一个模型来预测特定行程是否包含提示。

## <a name="create-an-apache-spark-machine-learning-model"></a>创建 Apache Spark 机器学习模型

1. 使用 PySpark 内核创建笔记本。 有关说明，请参阅[创建笔记本](../quickstart-apache-spark-notebook.md#create-a-notebook)。
2. 导入此应用程序所需的类型。 将以下代码复制并粘贴到空白单元格中，然后按 Shift+Enter。 或者使用代码左侧的蓝色播放图标运行该单元格。

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    由于使用的是 PySpark 内核，因此不需要显式创建任何上下文。 运行第一个代码单元格时，系统会自动创建 Spark 上下文。

## <a name="construct-the-input-dataframe"></a>构造输入数据帧

由于原始数据是 Parquet 格式，因此可以使用 Spark 上下文直接将文件作为数据帧提取到内存中。 虽然以下步骤中的代码使用默认选项，但如果需要，可以强制映射数据类型和其他架构属性。

1. 通过将代码粘贴到新单元格，运行以下行来创建 Spark 数据帧。 此步骤会通过开放数据集 API 检索数据。 拉取所有这些数据将生成约 15 亿行。 

   根据无服务器 Apache Spark 池的大小，原始数据可能会太大或需要花费太长时间来操作。 可以将此数据筛选为较小的数据。 下面的代码示例使用 `start_date` 和 `end_date` 来应用一个会返回单个月份数据的筛选器。

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. 简单筛选的缺点在于，从统计的角度来看，它可能会导致数据偏差。 另一种方法是使用 Spark 中内置的采样。 

   如果在上述代码之后应用以下代码，则数据集将减少到大约 2000 行。 你可以使用此采样步骤来代替简单筛选，也可将其与简单筛选结合使用。

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. 现在可以查看数据以查看读取的内容。 通常最好使用子集而不是完整集查看数据，具体取决于数据集的大小。 

   以下代码提供了两种查看数据的方法。 第一种方法是基本方法。 第二种方法提供了更丰富的网格体验，以及以图形方式将数据可视化的功能。

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. 根据生成的数据集大小和多次试验或运行笔记本的需要，你可能希望在工作区本地缓存数据集。 有三种方法可以执行显式缓存：

   - 将数据帧作为文件本地保存。
   - 将数据帧另存为临时表或视图。
   - 将数据帧另存为永久表。

以下代码示例中包含了这些方法中的前两种方法。

创建临时表或视图可提供访问数据的不同路径，但仅在 Spark 实例会话期间有效。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>准备数据

原始格式的数据通常不适合直接传递给模型。 必须对数据执行一系列操作，使其变为模型可以使用的状态。

在以下代码中，执行四类操作：

- 通过筛选删除离群值或错误值。
- 删除不需要的列。
- 创建从原始数据派生的新列，使模型更有效地工作。 此操作有时称为特征化。
- 标记。 因为在进行二进制分类（给定行程中是否有提示）时，需要将提示数量转换为值 0 或 1。

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

然后，对数据进行第二次传递以添加最终特征。

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>创建逻辑回归模型

最后一项任务是将已标记的数据转换为可通过逻辑回归进行分析的格式。 逻辑回归算法的输入需是一组标签/特征矢量对，其中特征矢量是表示输入点的数字矢量 。 

因此，你需要将分类列转换为数字。 具体而言，需要将 `trafficTimeBins` 和 `weekdayString` 列转换为整数表示形式。 执行该转换的方法有多种。 以下示例采用 `OneHotEncoder` 方法，此方法很常见。

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

此操作会生成一个新的数据帧（其中所有列都采用正确的格式）来训练模型。

## <a name="train-a-logistic-regression-model"></a>训练逻辑回归模型

第一个任务是将数据集拆分为训练集、测试集或验证集。 此处的拆分是任意拆分。 试验不同的拆分设置，了解这些设置是否影响模型。

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

现在有了两个数据帧，下一个任务就是创建模型公式并针对训练数据帧运行公式。 然后，可针对测试数据帧进行验证。 试验不同版本的模型公式，了解不同组合的影响。

> [!Note]
> 若要保存模型，请将“存储 Blob 数据参与者”角色分配到 Azure SQL 数据库服务器资源范围。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。 只有具有所有者权限的成员才能执行此步骤。

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

此单元格的输出为：

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>创建预测的直观表示形式

现在可以构造一个最终可视化效果，以帮助推理此测试的结果。 [ROC 曲线](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是查看结果的一种方法。

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![此图显示了提示模型中逻辑回归的 ROC 曲线。](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>关闭 Spark 实例

应用程序运行完成后，通过关闭该选项卡，关闭笔记本以释放资源。或者在笔记本底部的状态面板中选择“结束会话”。

## <a name="see-also"></a>另请参阅

- [概述：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>后续步骤

- [.NET for Apache Spark 文档](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Apache Spark 官方文档](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> 某些 Apache Spark 官方文档依赖于使用 Spark 控制台，但该控制台对于 Azure Synapse Analytics 中的 Apache Spark 不可用。 请改用[笔记本](../quickstart-apache-spark-notebook.md)或 [IntelliJ](../spark/intellij-tool-synapse.md) 体验。