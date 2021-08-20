---
title: 教程：在一小时内了解多变量异常情况检测
titleSuffix: Azure Cognitive Services
description: 多变量异常情况检测的端到端教程。
services: cognitive-services
author: juaduan
manager: conhua
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/27/2021
ms.author: juaduan
ms.openlocfilehash: bdebf242dcea8e7640d68afcb21d7ba7e2e49576
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292655"
---
# <a name="tutorial-learn-multivariate-anomaly-detection-in-one-hour"></a>教程：在一小时内了解多变量异常情况检测

具有多变量异常情况检测 (MVAD) 功能的异常检测器是一个高级 AI 工具，能够以非监督方式检测一组指标中的异常情况。

一般情况下，可以通过以下步骤来使用 MVAD：

  1. 创建支持 Azure 上的 MVAD 的异常检测器资源。
  1. 准备数据。
  1. 训练 MVAD 模型。
  1. 查询模型的状态。
  1. 使用已训练的 MVAD 模型检测异常情况。
  1. 检索并解释推理结果。

在本教程中，你将：

> [!div class="checklist"]
> * 了解如何以正确的格式准备数据。
> * 了解如何使用 MVAD 进行训练和推理。
> * 了解输入参数以及如何解释推理结果中的输出。

## <a name="1-create-an-anomaly-detector-resource-that-supports-mvad"></a>1. 创建支持 MVAD 的异常检测器资源

* 如果你没有 Azure 订阅，可以[免费创建](https://azure.microsoft.com/free/cognitive-services)一个订阅
* 拥有 Azure 订阅后，在 Azure 门户中[创建异常检测器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)以获取 API 密钥和 API 终结点。

> [!NOTE]
> 在预览阶段，MVAD 仅在有限的几个区域中可用。 请将[异常检测器的新增功能](../whats-new.md)加入书签，以随时了解 MVAD 在各个区域的推出情况。 也可以提交 GitHub 问题或通过 [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) 联系我们，以请求提供该工具在特定区域中的推出情况。

## <a name="2-data-preparation"></a>2. 数据准备

然后，需要准备训练数据（和推理数据）。

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]

### <a name="tools-for-zipping-and-uploading-data"></a>用于压缩和上传数据的工具

在本部分，我们将分享一些示例代码和工具，你可以复制它们并对其进行编辑，然后将其添加到你自己的用于处理 MVAD 输入数据的应用程序逻辑中。

#### <a name="compressing-csv-files-in-nix"></a>在 \*nix 中压缩 CSV 文件

```bash
zip -j series.zip series/*.csv
```

#### <a name="compressing-csv-files-in-windows"></a>在 Windows 中压缩 CSV 文件

* 导航到包含所有 CSV 文件的文件夹。
* 选择所需的所有 CSV 文件。
* 右键单击其中一个 CSV 文件并选择 `Send to`。
* 从下拉列表中选择 `Compressed (zipped) folder`。
* 将 zip 文件重命名为所需的名称。

#### <a name="python-code-zipping--uploading-data-to-azure-blob-storage"></a>使用 Python 代码压缩数据并将其上传到 Azure Blob 存储

可以参考[此文档](../../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)来了解如何将文件上传到 Azure Blob。

或者，可以参考以下可用于为你执行压缩和上传的示例代码。 可以复制本部分提供的 Python 代码并将其另存为 .py 文件（例如 `zipAndUpload.py`），然后按下面所示使用命令行运行此文件：

* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx`

    此命令将 `foo\bar` 中的所有 CSV 文件压缩成名为 `test123.zip` 的单个 zip 文件。 它将 `test123.zip` 上传到 Blob 中的 `container_xxx` 容器。
* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx -r` 

    此命令的作用与前面所述的命令相同，但成功上传后它会删除 zip 文件 `test123.zip`。 

参数：

* `--source-folder`、`-s`：包含 CSV 文件的源文件夹的路径
* `--zipfile-name`、`-z`：zip 文件的名称
* `--connection-string`、`-c`：Blob 的连接字符串
* `--container-name`、`-n`：容器的名称
* `--remove-zipfile`、`-r`：如果启用此参数，则会删除 zip 文件

```python
import os
import argparse
import shutil
import sys

from azure.storage.blob import BlobClient
import zipfile


class ZipError(Exception):
    pass


class UploadError(Exception):
    pass


def zip_file(root, name):
    try:
        z = zipfile.ZipFile(name, "w", zipfile.ZIP_DEFLATED)
        for f in os.listdir(root):
            if f.endswith("csv"):
                z.write(os.path.join(root, f), f)
        z.close()
        print("Compress files success!")
    except Exception as ex:
        raise ZipError(repr(ex))


def upload_to_blob(file, conn_str, cont_name, blob_name):
    try:
        blob_client = BlobClient.from_connection_string(conn_str, container_name=cont_name, blob_name=blob_name)
        with open(file, "rb") as f:
            blob_client.upload_blob(f, overwrite=True)
        print("Upload Success!")
    except Exception as ex:
        raise UploadError(repr(ex))


if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--source-folder", "-s", type=str, required=True, help="path to source folder")
    parser.add_argument("--zipfile-name", "-z", type=str, required=True, help="name of the zip file")
    parser.add_argument("--connection-string", "-c", type=str, help="connection string")
    parser.add_argument("--container-name", "-n", type=str, help="container name")
    parser.add_argument("--remove-zipfile", "-r", action="store_true", help="whether delete the zip file after uploading")
    args = parser.parse_args()

    try:
        zip_file(args.source_folder, args.zipfile_name)
        upload_to_blob(args.zipfile_name, args.connection_string, args.container_name, args.zipfile_name)
    except ZipError as ex:
        print(f"Failed to compress files. {repr(ex)}")
        sys.exit(-1)
    except UploadError as ex:
        print(f"Failed to upload files. {repr(ex)}")
        sys.exit(-1)
    except Exception as ex:
        print(f"Exception encountered. {repr(ex)}")

    try:
        if args.remove_zipfile:
            os.remove(args.zipfile_name)
    except Exception as ex:
        print(f"Failed to delete the zip file. {repr(ex)}")
```

## <a name="3-train-an-mvad-model"></a>3. 训练 MVAD 模型

下面是示例请求正文以及用于训练 MVAD 模型的 Python 示例代码。

```json
// Sample Request Body
{
    "slidingWindow": 200,
    "alignPolicy": {
        "alignMode": "Outer",
        "fillNAMethod": "Linear", 
        "paddingValue": 0
    },
    // This could be your own ZIP file of training data stored on Azure Blob and a SAS url could be used here
    "source": "https://aka.ms/AnomalyDetector/MVADSampleData", 
    "startTime": "2021-01-01T00:00:00Z", 
    "endTime": "2021-01-02T12:00:00Z", 
    "displayName": "Contoso model"
}
```

```python
# Sample Code in Python
########### Python 3.x #############
import http.client, urllib.request, urllib.parse, urllib.error, base64

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '{API key}',
}

params = urllib.parse.urlencode({})

try:
    conn = http.client.HTTPSConnection('{endpoint}')
    conn.request("POST", "/anomalydetector/v1.1-preview/multivariate/models?%s" % params, "{request body}", headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################
```

响应代码 `201` 指示请求成功。

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

## <a name="4-get-model-status"></a>4. 获取模型状态

由于训练 API 是异步的，因此在调用训练 API 后不会立即获得模型状态。 但是，可以按 API 键（这将列出所有模型）或模型 ID（这将列出有关特定模型的信息）查询模型的状态。

### <a name="list-all-the-models"></a>列出所有模型

有关请求 URL 和请求头的信息，可参考[此页](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/ListMultivariateModel)。 请注意，此处只会返回按更新时间排序的 10 个模型，但你可以通过在请求 URL 中设置 `$skip` 和 `$top` 参数来访问其他模型。 例如，如果请求 URL 为 `https://{endpoint}/anomalydetector/v1.1-preview/multivariate/models?$skip=10&$top=20`，则会跳过最新的 10 个模型，并返回接下来的 20 个模型。

示例响应如下 

```json
{
    "models": [
         {
             "createdTime":"2020-12-01T09:43:45Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:46:13Z",
             "modelId":"b4c1616c-33b9-11eb-824e-0242ac110002",
             "status":"READY",
             "variablesCount":18
         },
         {
             "createdTime":"2020-12-01T09:43:30Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:45:10Z",
             "modelId":"ab9d3e30-33b9-11eb-a3f4-0242ac110002",
             "status":"READY",
             "variablesCount":18
         }
    ],
    "currentCount": 1,
    "maxCount": 50, 
    "nextLink": "<link to more models>"
}
```

响应包含 4 个字段：`models`、`currentCount`、`maxCount` 和 `nextLink`。

* `models` 包含创建时间、上次更新时间、模型 ID、显示名称、变量计数，以及每个模型的状态。
* `currentCount` 包含已训练的多变量模型数量。
* `maxCount` 是此异常检测器资源支持的最大模型数量。
* `nextLink` 可用于提取其他模型。

### <a name="get-models-by-model-id"></a>按模型 ID 获取模型

[此页](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetMultivariateModel)介绍了用于按模型 ID 查询模型信息的请求 URL。 示例响应如下所示

```json
{
        "modelId": "45aad126-aafd-11ea-b8fb-d89ef3400c5f",
        "createdTime": "2020-06-30T00:00:00Z",
        "lastUpdatedTime": "2020-06-30T00:00:00Z",
        "modelInfo": {
          "slidingWindow": 300,
          "alignPolicy": {
            "alignMode": "Outer",
            "fillNAMethod": "Linear",
            "paddingValue": 0
          },
          "source": "<TRAINING_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS>",
          "startTime": "2019-04-01T00:00:00Z",
          "endTime": "2019-04-02T00:00:00Z",
          "displayName": "Devops-MultiAD",
          "status": "READY",
          "errors": [],
          "diagnosticsInfo": {
            "modelState": {
              "epochIds": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
              "trainLosses": [0.6291328072547913, 0.1671326905488968, 0.12354248017072678, 0.1025966405868533, 
                              0.0958492755889896, 0.09069952368736267,0.08686016499996185, 0.0860302299260931,
                              0.0828735455870684, 0.08235538005828857],
              "validationLosses": [1.9232804775238037, 1.0645641088485718, 0.6031560301780701, 0.5302737951278687, 
                                   0.4698025286197664, 0.4395163357257843, 0.4182931482799006, 0.4057914316654053, 
                                   0.4056498706340729, 0.3849248886108984],
              "latenciesInSeconds": [0.3398594856262207, 0.3659665584564209, 0.37360644340515137, 
                                     0.3513407707214355, 0.3370304107666056, 0.31876277923583984, 
                                     0.3283309936523475, 0.3503587245941162, 0.30800247192382812,
                                     0.3327946662902832]
            },
            "variableStates": [
              {
                "variable": "ad_input",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              {
                "variable": "ad_ontimer_output",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              // More variables
            ]
          }
        }
      }
```

你将收到有关所查询模型的更详细信息。 响应包含有关该模型、其训练参数和诊断信息的元信息。 诊断信息对于调试和跟踪训练进度而言非常有用。

* `epochIds` 指示模型已训练的时期数（总共 100 个时期）。 例如，如果模型仍处于正在训练状态，则 `epochId` 可能为 `[10, 20, 30, 40, 50]`，即，该模型已完成第 50 个训练时期，还有一半的时期尚待完成。
* `trainLosses` 和 `validationLosses` 用于检查优化进度是否开始收敛，如果是，则这两项损失应逐渐下降。
* `latenciesInSeconds` 包含每个时期的时间成本，每隔 10 个时期就会记录一次。 在此示例中，第 10 个时期大约花费了 0.34 秒。 这有助于估算训练完成时间。
* `variableStates` 汇总有关每个变量的信息。 它是按 `filledNARatio` 的降序排名的列表。 它告知每个变量使用了多少个数据点，而 `filledNARatio` 告知缺失多少个数据点。 通常，我们需要尽可能地减少 `filledNARatio`。
缺失的数据点过多会降低模型准确度。
* 数据处理期间发生的错误将包含在 `errors` 字段中。

## <a name="5-inference-with-mvad"></a>5. 使用 MVAD 进行推理

若要执行推理，只需在包含推理数据、开始时间和结束时间的 zip 文件中提供 Blob 源即可。

推理也是异步的，因此不会立即返回结果。 请注意，需将包含 `resultId` 的响应头中的结果链接保存在某个变量中，以便事后知道可在何处获取结果。

失败通常是由模型问题或数据问题造成的。 如果模型未准备就绪或数据链接无效，则无法执行推理。 确保训练数据和推理数据一致，即，它们应是完全相同的变量，但具有不同的时间戳。 两者的变量数不同或者使用一组不同的变量进行推理将通不过数据验证并发生错误。 数据验证受到推迟，以便你仅在查询结果时才收到错误消息。

## <a name="6-get-inference-results"></a>6. 获取推理结果

需要使用 `resultId` 来获取结果。 可以在提交推理请求时从响应头中获取 `resultId`。 [此页](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetDetectionResult)提供了有关如何查询推理结果的说明。 

示例响应如下所示

```json
 {
        "resultId": "663884e6-b117-11ea-b3de-0242ac130004",
        "summary": {
          "status": "READY",
          "errors": [],
          "variableStates": [
            {
              "variable": "ad_input",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            {
              "variable": "ad_ontimer_output",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            // more variables
          ],
          "setupInfo": {
            "source": "https://aka.ms/AnomalyDetector/MVADSampleData",
            "startTime": "2019-04-01T00:15:00Z",
            "endTime": "2019-04-01T00:40:00Z"
          }
        },
        "results": [
          {
            "timestamp": "2019-04-01T00:15:00Z",
            "errors": [
              {
                "code": "InsufficientHistoricalData",
                "message": "historical data is not enough."
              }
            ]
          },
          // more results
          {
            "timestamp": "2019-04-01T00:20:00Z",
            "value": {
              "contributors": [],
              "isAnomaly": false,
              "severity": 0,
              "score": 0.17805261260751692
            }
          },
          // more results
          {
            "timestamp": "2019-04-01T00:27:00Z",
            "value": {
              "contributors": [
                {
                  "contributionScore": 0.0007775013367514271,
                  "variable": "ad_ontimer_output"
                },
                {
                  "contributionScore": 0.0007989604079048129,
                  "variable": "ad_series_init"
                },
                {
                  "contributionScore": 0.0008900927229851369,
                  "variable": "ingestion"
                },
                {
                  "contributionScore": 0.008068144477478554,
                  "variable": "cpu"
                },
                {
                  "contributionScore": 0.008222036467507165,
                  "variable": "data_in_speed"
                },
                {
                  "contributionScore": 0.008674941549594993,
                  "variable": "ad_input"
                },
                {
                  "contributionScore": 0.02232242629793674,
                  "variable": "ad_output"
                },
                {
                  "contributionScore": 0.1583773213660846,
                  "variable": "flink_last_ckpt_duration"
                },
                {
                  "contributionScore": 0.9816531517495176,
                  "variable": "data_out_speed"
                }
              ],
              "isAnomaly": true,
              "severity": 0.42135109874230336,
              "score": 1.213510987423033
            }
          },
          // more results
        ]
      }
```

响应包含结果状态、变量信息、推理参数和推理结果。

* `variableStates` 列出推理请求中每个变量的信息。
* `setupInfo` 是为此次推理提交的请求正文。
* `results` 包含检测结果。 有三种典型类型的检测结果。
    1. 错误代码 `InsufficientHistoricalData`。 通常只会在处理前几个时间戳时才发生此错误，因为模型以基于时窗的方式推理数据，它需要使用历史数据来做出决策。 在处理前几个时间戳时，历史数据不足，因此无法对其执行推理。 在这种情况下，可以忽略错误消息。
    1. `"isAnomaly": false` 指示当前时间戳不存在异常。
        * `severity ` 指示异常情况的相对严重性；对于正常数据，它始终为 0。
        * `score` 是模型在做出决策时所依据的原始模型输出，即使对于正常数据点，它也可能是非零值。
    1. `"isAnomaly": true` 指示当前时间戳有异常。
        * `severity ` 指示异常情况的相对严重性；对于异常数据，它始终大于 0。
        * `score` 是模型在做出决策时所依据的原始模型输出。 `severity` 是从 `score` 派生的值。 每个数据点都有 `score`。
        * `contributors` 是一个列表，其中包含每个变量的贡献分数。 贡献分数越高表示根本原因的可能性越高。 此列表通常用于解释异常情况以及诊断根本原因。

> [!NOTE]
> 一个常见失误是将 `isAnomaly`=`true` 的所有数据点视为异常。 这最终可能导致出现过多的误报。
> 应同时使用 `isAnomaly` 和 `severity`（或 `score`）筛选出不严重的异常情况，并（选择性地）使用组合来检查异常情况的持续时间，以消除随机干扰。 有关 `severity` 与 `score` 之间的差异，请参阅最佳做法文档中的[常见问题解答](../concepts/best-practices-multivariate.md#faq)。

## <a name="next-steps"></a>后续步骤

* [最佳做法：使用多变量异常检测器 API 时建议遵循的做法](../concepts/best-practices-multivariate.md)
* [快速入门：使用异常检测器多变量客户端库](../quickstarts/client-libraries-multivariate.md)