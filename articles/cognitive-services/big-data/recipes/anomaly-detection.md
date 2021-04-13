---
title: 操作方法：使用大数据认知服务执行预测性维护
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用大数据认知服务执行分布式异常检测
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a583d74ae0bf421a7a863a65442d250489a2f8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775385"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>操作方法：使用大数据认知服务执行预测性维护

本操作方法介绍如何使用 Apache Spark 中的 Azure Synapse Analytics 和认知服务对 IoT 设备执行预测性维护。 接下来，我们将随着 [CosmosDB 和 Synapse Link](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) 示例执行操作。 为简单起见，对于此操作方法，我们将直接从 CSV 文件读取数据，而不再通过 CosmosDB 和 Synapse Link 获取流式数据。 强烈建议你仔细查看 Synapse Link 示例。

## <a name="hypothetical-scenario"></a>假设方案

假设方案是在发电厂，其中 IoT 设备用来监视[汽轮机](https://en.wikipedia.org/wiki/Steam_turbine)。 IoTSignals 集合包含每个涡轮的每分钟转速 (RPM) 和兆瓦功率 (MW)。 我们要分析来自汽轮机的信号，并检测异常信号。

数据可能随机出现异常。 在这些情况下，RPM 值将会升高，而 MW 输出将会下降，以保护电路。 目的是查看相同时间针对不同信号的数据变化情况。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Azure Synapse 工作区](../../../synapse-analytics/quickstart-create-workspace.md)配备[无服务器 Apache Spark 池](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

## <a name="setup"></a>设置

### <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../cognitive-services-apis-create-account-cli.md) 创建翻译资源。 也可执行以下操作：

- 查看 [Azure 门户](https://portal.azure.com/)中的现有资源。

记下该资源的终结点和密钥，在本指南中将需要它。

## <a name="enter-your-service-keys"></a>输入服务密钥

首先，让我们添加你的密钥和位置。

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>将数据读取到 DataFrame

接下来，让我们将 IoTSignals 文件读取到 DataFrame。 在 Synapse 工作区中打开一个新笔记本，并在该文件中创建一个 DataFrame。

```python
df_signals = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>使用 Spark 中的认知服务运行异常检测

目的是查找 IoT 设备信号输出异常值的实例，以便我们可以了解哪种情况下会出错，并执行预测性维护。 为此，让我们使用 Spark 上的异常检测器：

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

我们来看看数据。

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

此单元格生成的结果应如下所示：

| timestamp           |   值 | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | False       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | False       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | False       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>直观显示其中一个设备的异常

IoTSignals.csv 包含来自多个 IoT 设备的信号。 我们将重点关注某个特定设备，并直观显示该设备的异常输出。

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

现在，我们已创建了表示特定设备异常的数据帧，可以直观显示这些异常：

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

如果运行成功，输出将如下所示：

![异常检测器演示](../media/anomaly-output.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 认知服务、Azure Synapse Analytics 和 Azure CosmosDB 执行大规模预测性维护。 有关详细信息，请参阅 [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) 上的完整示例。
