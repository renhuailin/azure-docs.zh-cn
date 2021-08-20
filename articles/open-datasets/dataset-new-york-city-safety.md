---
title: 纽约市安全数据
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用纽约市安全数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d8efbd7469ed92b4c323ed3d94315ec8f0f4dc5c
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038525"
---
# <a name="new-york-city-safety-data"></a>纽约市安全数据

从 2010 年至今，纽约市的所有 311 服务请求。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]


## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 每天更新，截至 2019 年总共包括约 1200 万行 (500 MB)。

此数据集包含从 2010 年至今累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="additional-information"></a>其他信息

此数据集来源于纽约市政府，有关详细信息，请参阅[纽约市网站](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9)。 请参阅[此数据集的条款](https://www1.nyc.gov/home/terms-of-use.page)。

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| address | string | 1,536,593 | 655 EAST 230 STREET 78-15 PARSONS BOULEVARD | 提交者提供的事件地址门牌号。 |
| category | string | 446 | 噪声 - 住宅 暖气/热水 | 这是标识事件或条件主题（投诉类型）的层次结构的第一级别。 它可能具有相应子类别（描述符），也可能是独立的。 |
| dataSubtype | 字符串 | 1 | 311_All | “311_All” |
| dataType | 字符串 | 1 | 安全 | “安全” |
| dateTime | timestamp | 17,332,609 | 2013-01-24 00:00:00 2015-01-08 00:00:00 | 日期服务请求已创建。 |
| latitude | Double | 1,513,691 | 40.89187241649303 40.72195913199264 | 基于地理位置的事件位置纬度。 |
| longitude | Double | 1,513,713 | -73.86016845296459 -73.80969682426189 | 基于地理位置的事件位置经度。 |
| 状态 | 字符串 | 13 | 待关闭 | 服务请求的状态已提交。 |
| 子类别 | string | 1,716 | 大声的音乐/聚会 整个大楼 | 这与类别（投诉类型）相关联，并提供有关事件或状况的更多详细信息。 其值取决于投诉类型，并且在服务请求中并不总是必需的。 |

## <a name="preview"></a>预览

| dataType | dataSubtype | dateTime | category | 子类别 | 状态 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全 | 311_All | 4/25/2021 2:05:05 AM | 噪音 - 街道/人行道 | 大声的音乐/聚会 | 正在进行 | 2766 BATH AVENUE | 40.5906129741766 | -73.9847949011337 | null |  |
| 安全 | 311_All | 4/25/2021 2:04:33 AM | 噪声 - 商业 | 大声的音乐/聚会 | 正在进行 | 1033 WEBSTER AVENUE | 40.8285784533256 | -73.9117746958432 | null |  |
| 安全 | 311_All | 4/25/2021 2:04:27 AM | 噪声 - 住宅 | 大声的音乐/聚会 | 正在进行 | 620 WEST 141 STREET | 40.8241726554395 | -73.9530069547366 | null |  |
| 安全 | 311_All | 4/25/2021 2:04:04 AM | 噪声 - 住宅 | 大声的音乐/聚会 | 正在进行 | 1647 64 STREET | 40.6218907202382 | -73.9931125332078 | null |  |
| 安全 | 311_All | 4/25/2021 2:04:01 AM | 噪声 - 住宅 | 大声的音乐/聚会 | 正在进行 | 30 LENOX AVENUE | 40.7991622274945 | -73.9517496365803 | null |  |
| 安全 | 311_All | 4/25/2021 2:03:40 AM | 非法停车 | 平行停车阻挡交通 | 正在进行 | 304 WEST 148 STREET | 40.8248229687124 | -73.940696262361 | null |  |
| 安全 | 311_All | 4/25/2021 2:03:31 AM | 噪音 - 街道/人行道 | 大声的音乐/聚会 | 正在进行 | ADEE AVENUE | 40.8708386263454 | -73.8382363208686 | null |  |
| 安全 | 311_All | 4/25/2021 2:03:18 AM | 噪声 - 住宅 | 大声的音乐/聚会 | 正在进行 | 340 EVERGREEN AVENUE | 40.6947512704197 | -73.9248330229197 | null |  |
| 安全 | 311_All | 4/25/2021 2:03:13 AM | 噪声 - 住宅 | 撞击/敲击 | 正在进行 | 25 REMSEN STREET | 40.6948938116483 | -73.9973494607802 | null |  |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)。

```
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco)。

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas
```

```python
# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "citydatacontainer"
folder_name = "Safety/Release/city=SanFrancisco"
```

```python
from azure.storage.blob import BlockBlobServicefrom azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

if azure_storage_account_name is None or azure_storage_sas_token is None:
    raise Exception(
        "Provide your specific name and key for your Azure Storage account--see the Prerequisites section earlier.")

print('Looking for the first parquet under the folder ' +
      folder_name + ' in container "' + container_name + '"...')
container_url = f"https://{azure_storage_account_name}.blob.core.windows.net/"
blob_service_client = BlobServiceClient(
    container_url, azure_storage_sas_token if azure_storage_sas_token else None)

container_client = blob_service_client.get_container_client(container_name)
blobs = container_client.list_blobs(folder_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
targetBlobName = ''
for blob in sorted_blobs:
    if blob.name.startswith(folder_name) and blob.name.endswith('.parquet'):
        targetBlobName = blob.name
        break

print('Target blob to download: ' + targetBlobName)
_, filename = os.path.split(targetBlobName)
blob_client = container_client.get_blob_client(targetBlobName)
with open(filename, 'wb') as local_file:
    blob_client.download_blob().download_to_stream(local_file)
```

```python
# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)
```

```python
# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

没有适用于此平台/包组合的示例。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)。

```python
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="examples"></a>示例

- 请参阅 GitHub 上的[城市安全分析](https://github.com/scottcounts/CitySafety)示例。


## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。