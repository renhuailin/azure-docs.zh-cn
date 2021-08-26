---
title: 异常检测器 Python 多变量客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 5b0dfd51ac3de7f7abea41f18f8bf43dfa14580c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802533"
---
开始使用适用于 Python 的异常检测器多变量客户端库。 请按照以下步骤操作，以使用服务提供的算法安装软件包。 新的多变量异常情况检测 API 使开发人员能够轻松地集成高级 AI 来检测指标组中的异常，且无需机器学习知识或标记的数据。 不同信号之间的依赖关系和相互关联会自动计为关键因素。 这可以帮助你主动防范复杂系统发生故障。

使用适用于 Python 的异常检测器多变量客户端库，以便：

* 检测一组时序中的系统级异常。
* 当任何单独的时序都不能告知太多信息时，而你不得不查看所有信号来检测问题。
* 使用数十到数百种不同类型的传感器对昂贵的物理资产进行预测维护，以测量系统运行状况的各个方面。

[库参考文档](/python/api/azure-ai-anomalydetector/azure.ai.anomalydetector) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [包 (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/) | [示例代码](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)

## <a name="prerequisites"></a>先决条件

* [Python 3.x](https://www.python.org/)
* [Pandas 数据分析库](https://pandas.pydata.org/)
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 拥有 Azure 订阅后，可在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="创建异常检测器资源"  target="_blank">创建异常检测器资源</a>来获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到异常检测器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。


## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install pandas
pip install --upgrade azure-ai-anomalydetector
```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

 创建新的 Python 文件并导入以下库。

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

将密钥的变量创建为环境变量，同时创建时序数据文件的路径，以及订阅的 Azure 位置。 

> [!NOTE]
> 始终可以在两个密钥之间选择一个使用。 这是为了实现安全的密钥轮换。 针对本快速入门，请使用第一个密钥。 

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```



## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 Python 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [模型](#train-the-model)
* [检测异常](#detect-anomalies)
* [导出模型](#export-model)
* [删除模型](#delete-model)

## <a name="authenticate-the-client"></a>验证客户端

若要实例化新的异常检测器客户端，需要传递异常探测器订阅密钥和关联的终结点。 我们还将建立一个数据源。  

若要使用异常检测器多变量 API，需要先训练你自己的模型。 训练数据是一组满足以下要求的多个时间序列：

每个时间序列都应是一个 CSV 文件，该文件有 2 列（且仅有 2 列），“timestamp”和“value”（全部小写）作为标题行。 “timestamp”值应符合 ISO 8601 标准；“value”可为整数，也可为带有任意小数位的小数。 例如：

|timestamp | 值|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

每个 CSV 文件应根据要用于模型训练的不同变量进行命名。 例如，“temperature.csv”和“humidity.csv”。 所有 CSV 文件都应压缩到一个没有任何子文件夹的 zip 文件中。 zip 文件可采用任何所需名称。 zip 文件应上传到 Azure Blob 存储。 为 zip 文件生成 Blob SAS（共享访问签名）URL 后，可使用该 URL 进行训练。 请查看此文档，了解如何从 Azure Blob 存储生成 SAS URL。

```python
class MultivariateSample():

def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
```

## <a name="train-the-model"></a>定型模型

首先，我们将对模型进行训练，在训练时检查模型的状态以确定训练何时完成，然后检索在转到检测阶段时需要的最新模型 ID。

```python
def train(self, start_time, end_time):
    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...(it may take a few minutes)")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    while model_status != ModelStatus.READY and model_status != ModelStatus.FAILED:
        model_info = self.ad_client.get_multivariate_model(trained_model_id).model_info
        model_status = model_info.status
        time.sleep(10)

    if model_status == ModelStatus.FAILED:
        print("Creating model failed.")
        print("Errors:")
        if model_info.errors:
            for error in model_info.errors:
                print("Error code: {}. Message: {}".format(error.code, error.message))
        else:
            print("None")
        return None

    if model_status == ModelStatus.READY:
        # Model list after training
        new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
        print("Done.\n--------------------")
        print("{:d} available models after training.".format(len(new_model_list)))

    # Return the latest model id
    return trained_model_id
```

## <a name="detect-anomalies"></a>检测异常

使用 `detect_anomaly` 和 `get_dectection_result` 确定数据源中是否存在异常。 你将需要传递刚刚训练的模型的模型 ID。

```python
def detect(self, model_id, start_time, end_time):
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        while r.summary.status != DetectionStatus.READY and r.summary.status != DetectionStatus.FAILED:
            r = self.ad_client.get_detection_result(result_id)
            time.sleep(2)

        if r.summary.status == DetectionStatus.FAILED:
            print("Detection failed.")
            print("Errors:")
            if r.summary.errors:
                for error in r.summary.errors:
                    print("Error code: {}. Message: {}".format(error.code, error.message))
            else:
                print("None")
            return None
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    return r
```

## <a name="export-model"></a>导出模型

> [!NOTE]
> Export 命令的作用在于使容器化环境中能够运行 Anomaly Detector 多变量模型。 目前不支持多变量，但将来会添加支持。

如果要导出模型，请使用 `export_model` 并传递要导出的模型的模型 ID：

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>删除模型

若要删除模型，请使用 `delete_multivariate_model` 并传递要删除的模型的模型 ID：

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>运行应用程序

在运行应用程序之前，我们需要添加一些代码来调用新创建的函数。

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

在运行之前，对照生成本快速入门的[完整示例代码](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)检查项目，这会很有帮助。

我们还提供了[详细的 Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) 来帮助你入门。

使用 `python` 命令和文件名运行应用程序。


## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与资源组相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../../overview-multivariate.md)
* [使用异常检测器 API 时的最佳做法](../../concepts/best-practices-multivariate.md)。