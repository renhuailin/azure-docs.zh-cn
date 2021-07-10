---
title: 教程：使用适用于 Apache Spark 的 Microsoft 机器学习生成机器学习应用程序（预览）
description: 了解如何使用适用于 Apache Spark 的 Microsoft 机器学习在 Azure Synapse Analytics 中创建机器学习应用程序。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 8a4c5892e5b7b542177376fcd0adae76527957a8
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578074"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>教程：使用适用于 Apache Spark 的 Microsoft 机器学习生成机器学习应用程序（预览）

本文介绍如何使用适用于 Apache Spark 的 Microsoft 机器学习 ([MMLSpark](https://github.com/Azure/mmlspark)) 创建机器学习应用程序。 MMLSpark 可通过添加许多深度学习和数据科学工具（例如 [Azure 认知服务](../../cognitive-services/big-data/cognitive-services-for-big-data.md)、[OpenCV](https://opencv.org/)、[LightGBM](https://github.com/Microsoft/LightGBM) 等）来扩展 Apache Spark 的分布式机器学习解决方案。  使用 MMLSpark 可以基于各种 Spark 数据源生成功能强大且高度可缩放的预测模型和分析模型。
Synapse Spark 提供内置的 MMLSpark 库，包括：

- [Vowpal Wabbit](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) – 用于机器学习的库服务，可以实现文本分析，例如在推文中进行情绪分析。
- [Spark 上的认知服务](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) - 在 SparkML 管道中整合 Azure 认知服务的功能，以便为异常情况检测等认知数据建模服务派生解决方案设计。
- [LightGBM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – LightGBM 是一种使用基于树的学习算法的梯度提升框架。 根据设计，它是分布式的，可以提升效率。
- 条件 KNN - 支持条件查询的可缩放 KNN 模型。
- [Spark 上的 HTTP](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – 在集成 Spark 时用于协调分布式微服务，并提供基于 HTTP 协议的易访问性。

本教程将演示几个有关在 MMLSpark 中使用 Azure 认知服务的示例，包括 

- 文本分析 - 获取一组句子的情绪。
- 计算机视觉 - 获取与一组图像相关联的标记（单字说明）。
- 必应图像搜索 - 在 Web 中搜索与自然语言查询相关的图像。
- 异常检测器 - 检测时间序列数据中的异常。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件 

- [Azure Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 Azure Data Lake Storage Gen2 存储帐户配置为默认存储。 你需要成为所使用的 Data Lake Storage Gen2 文件系统的存储 Blob 数据参与者。
- Azure Synapse Analytics 工作区中的 Spark 池。 有关详细信息，请参阅[在 Azure Synapse 中创建 Spark 池](../get-started-analyze-spark.md)。
- [在 Azure Synapse 中配置认知服务](./tutorial-configure-cognitive-services-synapse.md)教程中所述的预配置步骤。


## <a name="get-started"></a>入门
若要开始，请导入 mmlspark 并配置服务密钥。 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
cognitive_service_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_SERVICE_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME") 
# A Bing Search v7 subscription key
bingsearch_service_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_BING_SEARCH_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME")
# An Anomaly Dectector subscription key
anomalydetector_key = mssparkutils.credentials.getSecret("ADD_YOUR_KEY_VAULT_NAME", "ADD_YOUR_ANOMALY_KEY","ADD_YOUR_KEY_VAULT_LINKED_SERVICE_NAME")


```


## <a name="text-analytics-sample"></a>文本分析示例

[文本分析](../../cognitive-services/text-analytics/index.yml)服务提供了几种从文本中提取智能见解的算法。 例如，我们可以找到给定输入文本的情绪。 服务将返回介于 0.0 和 1.0 之间的分数，其中低分数表示负面情绪，高分表示正面情绪。 此示例使用三个简单的句子，并返回每个句子的情绪。

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia") # Set the location of your cognitive service
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>预期结果

|text | 情绪|
|--|--|
| I am frustrated by this rush hour traffic! | 消极 |
| this is a dog | 中立 |
| 今天天气晴朗，我真高兴！ | 积极 |

## <a name="computer-vision-sample"></a>计算机视觉示例
[计算机视觉](../../cognitive-services/computer-vision/index.yml)会分析图像以识别结构，例如人脸、对象和自然语言说明。 在此示例中，我们将标记以下图像。 标记是对图像中可识别的对象、人物、风景和动作等事物的单个词说明。


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia") # Set the location of your cognitive service
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>预期结果

|image | 标记|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skating, person, man, outdoor, riding, sport, skateboard, young, board, shirt, air, park, boy, side, jumping, ramp, trick, doing, flying] |

## <a name="bing-image-search-sample"></a>必应图像搜索示例
[必应图像搜索](../../cognitive-services/bing-image-search/overview.md)在 Web 中搜索以检索与用户自然语言查询相关的图像。 在此示例中，我们将使用一个文本查询来查找带引号的图像。 它返回包含与我们的查询相关的照片的图像 URL 列表。


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>预期结果

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>异常情况检测器示例

[异常检测器](../../cognitive-services/anomaly-detector/index.yml)对于检测时间序列数据中的不规则性非常有用。 在此示例中，我们使用该服务来查找整个时间序列中的异常情况。

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>预期结果

|timestamp | 值 | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826.0|false|
|1972-02-01T00:00:00Z|799.0|false|
|1972-03-01T00:00:00Z|890.0|false|
|1972-04-01T00:00:00Z|900.0|false|
|1972-05-01T00:00:00Z|766.0|false|
|1972-06-01T00:00:00Z|805.0|false|
|1972-07-01T00:00:00Z|821.0|false|
|1972-08-01T00:00:00Z|20000.0|是|
|1972-09-01T00:00:00Z|883.0|false|
|1972-10-01T00:00:00Z|898.0|false|
|1972-11-01T00:00:00Z|957.0|false|
|1972-12-01T00:00:00Z|924.0|false|
|1973-01-01T00:00:00Z|881.0|false|
|1973-02-01T00:00:00Z|837.0|false|
|1973-03-01T00:00:00Z|9000.0|是|


## <a name="speech-to-text-sample"></a>语音转文本示例

[语音转文本](../../cognitive-services/speech-service/index-text-to-speech.yml)服务将语音音频的流或文件转换为文本。 本示例将一个音频文件转录为文本。 

```python
# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("northeurope") # Set the location of your cognitive service
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```
### <a name="expected-results"></a>预期结果

|url | DisplayText |
|--|--|
| `https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav` | 自定义语音识别提供了一些工具，通过比较音频数据和自定义语音门户的相应识别结果，你可以直观地检查模型的识别质量。 你可以播放上传的音频，并确定提供的识别结果是否正确。 使用此工具，可以快速检查 Microsoft 的基准语音转文本模型或经过训练的自定义模型的质量，而无需转录任何音频数据。|

## <a name="clean-up-resources"></a>清理资源
为了确保关闭 Spark 实例，请结束任何已连接的会话（笔记本）。 达到 Apache Spark 池中指定的空闲时间时，池将会关闭。 也可以从笔记本右上角的状态栏中选择“停止会话”。

![显示“停止会话”的屏幕截图](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>后续步骤

* [查看 Synapse 示例笔记本](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [MMLSpark GitHub 存储库](https://github.com/Azure/mmlspark)
