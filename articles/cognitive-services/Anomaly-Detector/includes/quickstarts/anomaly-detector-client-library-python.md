---
title: 异常检测器 Python 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 216c45bf097718f6a696e64c8bd9c8718fc0185e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444987"
---
适用于 Python 的异常检测器客户端库入门。 请按照以下步骤操作，以使用服务提供的算法安装软件包。 通过异常检测器服务，可以对时序数据自动使用最佳适配模型，从而查找器其中的异常，不限行业、场景或数据量。

使用适用于 Python 的异常检测器客户端库，可实现以下操作：

* 以批请求的形式检测整个时序数据集中的异常
* 在时序中检测最新数据点的异常状态
* 检测数据集中的趋势更改点。

[库参考文档](https://go.microsoft.com/fwlink/?linkid=2090370) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [包 (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [在 GitHub 上查找示例代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>先决条件

* [Python 3.x](https://www.python.org/)
* [Pandas 数据分析库](https://pandas.pydata.org/)
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 拥有 Azure 订阅后，可在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="创建异常检测器资源"  target="_blank">创建异常检测器资源</a>来获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到异常检测器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。


## <a name="setting-up"></a>设置

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

 创建新的 Python 文件并导入以下库。

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

将密钥的变量创建为环境变量，同时创建时序数据文件的路径，以及订阅的 Azure 位置。 例如，`westus2`。

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>安装客户端库

在安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>对象模型

异常检测器客户端是 [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py) 对象，该对象使用你的密钥向 Azure 进行身份验证。 客户端可使用 [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26) 对整个数据集进行异常情况检测，或者使用 [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87) 对最新的数据点进行异常情况检测。 [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) 函数可检测在趋势中标记更改的点。

时序数据作为一系列 [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370) 对象进行发送。 `DetectRequest` 对象包含描述数据的属性（例如 `TimeGranularity`）以及异常情况检测的参数。

异常检测器响应是 [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse)、[EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 或 [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107) 对象，具体取决于所使用的方法。

## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 Python 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [从文件加载时序数据集](#load-time-series-data-from-a-file)
* [在整个数据集中检测异常](#detect-anomalies-in-the-entire-data-set)
* [检测最新数据点的异常状态](#detect-the-anomaly-status-of-the-latest-data-point)
* [检测数据集中的更改点](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>验证客户端

将 Azure 位置变量添加到终结点，并使用密钥对客户端进行身份验证。

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>从文件加载时序数据

从 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) 下载此快速入门中的示例数据：
1. 在浏览器中，右键单击“原始”。
2. 单击“将链接另存为”。
3. 将文件另存为 .csv 文件，保存到你的应用程序目录。

此时序数据的格式为 .csv 文件，它将被发送到异常检测器 API。

使用 Pandas 库的 `read_csv()` 方法加载数据文件，并构建一个空的列表变量来存储数据系列。 循环访问该文件，并将数据作为 `TimeSeriesPoint` 对象追加。 此对象将包含 .csv 数据文件的行中的时间戳和数字值。

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

使用时序创建一个 `DetectRequest` 对象，以及其数据点的`TimeGranularity`（或周期）。 例如，`TimeGranularity.daily`。

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>在整个数据集中检测异常

调用 API，以便使用客户端的 `detect_entire_series` 方法检测整个时序数据中的异常。 存储返回的 [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 对象。 循环访问响应的 `is_anomaly` 列表，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

调用异常检测器 API，以便使用客户端的 `detect_last_point` 方法确定最新数据点是否异常，并存储返回的 `LastDetectResponse` 对象。 响应的 `is_anomaly` 值是一个布尔值，用于指定该点的异常状态。  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>检测数据集中的更改点

调用 API，以使用客户端的 `detect_change_point` 方法检测时序数据中的更改点。 存储返回的 `ChangePointDetectResponse` 对象。 循环访问响应的 `is_change_point` 列表，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于趋势更改点的索引。

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>运行应用程序

使用 `python` 命令和文件名运行应用程序。

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
