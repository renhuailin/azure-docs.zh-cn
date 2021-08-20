---
title: 波士顿安全数据
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用“波士顿安全数据”。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1c9a4917f1b5d2b719a247d111b62897f8063bc9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038653"
---
# <a name="boston-safety-data"></a>波士顿安全数据

向波士顿市报告了 311 次呼叫。

请参阅此链接以详细了解 [BOS:311](https://www.cityofboston.gov/311/)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 每天更新一次，截至 2019 年总共包含约 10 万行 (10 MB) 记录。

此数据集包含从 2011 年至今累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="additional-information"></a>其他信息

此数据集来自波士顿市政府。 有关详细信息，请参见[波士顿数据集站点](https://data.boston.gov/dataset/311-service-requests)。 有关数据集许可，请参阅[开放数据共享公共领域贡献和许可 (ODC PDDL)](http://opendefinition.org/licenses/odc-pddl/)。

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| address | string | 140,612 | \" \" 1 City Hall Plz Boston MA 02108 | 位置。 |
| category | string | 54 | Street Cleaning Sanitation | 服务请求原因。 |
| dataSubtype | 字符串 | 1 | 311_All | “311_All” |
| dataType | 字符串 | 1 | 安全 | “安全” |
| dateTime | timestamp | 1,529,075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | 服务请求的公开日期和时间。 |
| latitude | Double | 1,622 | 42.3594 42.3603 | 这是纬度值。 纬线平行于赤道。 |
| longitude | Double | 1,806 | -71.0587 -71.0583 | 这是经度值。 经度线垂直于纬度线，并且都穿过两个极点。 |
| source | string | 7 | Constituent Call Citizens Connect App | 案件的原始来源。 |
| 状态 | 字符串 | 2 | Closed Open | 事件状态。 |
| 子类别 | string | 209 | Parking Enforcement Requests for Street Cleaning | 服务请求类型。 |

## <a name="preview"></a>预览

| ataType | dataSubtype | dateTime | category | 子类别 | 状态 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全 | 311_All | 4/27/2021 11:45:49 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 打开 | 51 Gardner St Allston MA 02134 | 42.3535 | -71.1285 | Citizens Connect App |  |
| 安全 | 311_All | 4/27/2021 11:43:43 PM | Sanitation | Missed Trash/Recycling/Yard Waste/Bulk Item | 打开 | 4 Putnam Pl Roxbury MA 02119 | 42.3298 | -71.0883 | 自助服务 |  |
| 安全 | 311_All | 4/27/2021 11:37:19 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 打开 | 36 Raven St Dorchester MA 02125 | 42.3177 | -71.0546 | Citizens Connect App |  |
| 安全 | 311_All | 4/27/2021 11:30:00 PM | Sanitation | Missed Trash/Recycling/Yard Waste/Bulk Item | 打开 | 58 Bicknell St Dorchester MA 02121 | 42.2984 | -71.0834 | Constituent Call |  |
| 安全 | 311_All | 4/27/2021 11:10:20 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 打开 | 2000 Commonwealth Ave Brighton MA 02135 | 42.3394 | -71.1585 | Citizens Connect App |  |
| 安全 | 311_All | 4/27/2021 11:06:00 PM | Noise Disturbance | Loud Parties/Music/People | 打开 | INTERSECTION of Lewis St & North St Boston MA | 42.3594 | -71.0587 | Constituent Call |  |
| 安全 | 311_All | 4/27/2021 11:05:00 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 打开 | 1 Nassau St Boston MA 02111 | 42.3486 | -71.0629 | Constituent Call |  |
| 安全 | 311_All | 4/27/2021 11:00:55 PM | Code Enforcement | Poor Conditions of Property | 打开 | 17 Mercer St South Boston MA 02127 | 42.3332 | -71.0492 | Citizens Connect App |  |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston)。

```
# This is a package in preview.
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston)。

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
folder_name = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston)。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston)。

```python
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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