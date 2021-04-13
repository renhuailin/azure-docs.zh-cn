---
title: 操作指南：使用适用于大数据的认知服务进行智能艺术探索
titleSuffix: Azure Cognitive Services
description: 此操作指南展示了如何使用 Azure 搜索和 MMLSpark 创建可搜索的艺术数据库。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363283"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>操作指南：使用适用于大数据的认知服务进行智能艺术探索

在本例中，我们将使用适用于大数据的认知服务将智能注释添加到大都会艺术博物馆 (MET) 的开放存取集合。 这使我们可以使用 Azure 搜索创建智能搜索引擎，甚至无需手动注释。 

## <a name="prerequisites"></a>先决条件

* 必须具有计算机视觉和认知搜索的订阅密钥。 按照[创建认知服务帐户](../../cognitive-services-apis-create-account.md)中的说明订阅计算机视觉并获取密钥。
  > [!NOTE]
  > 有关定价信息，请参阅 [Azure 认知搜索](https://azure.microsoft.com/services/search/#pricing)。

## <a name="import-libraries"></a>导入库

运行以下命令，为此操作指南导入库。

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>设置订阅密钥

运行以下命令设置服务密钥的变量。 插入计算机视觉和 Azure 认知搜索的订阅密钥。

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>读取数据

运行以下命令，从 MET 的开放存取集合加载数据。

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>分析图像

运行以下命令，在 MET 的开放存取艺术品集合上使用计算机视觉。 因此，你将获取艺术品的视觉特征。

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>创建搜索索引

运行以下命令将结果写入 Azure 搜索，以使用计算机视觉中的丰富元数据创建艺术品搜索引擎。

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>查询搜索索引

运行以下命令以查询 Azure 搜索索引。

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>后续步骤

了解如何使用[认知服务对大数据进行异常情况检测](anomaly-detection.md)。