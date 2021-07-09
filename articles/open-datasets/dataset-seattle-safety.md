---
title: 西雅图安全数据
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用西雅图安全数据。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: c865e09a6d4591b1f80ddd55c2a260b6e06f154a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038546"
---
# <a name="seattle-safety-data"></a>西雅图安全数据

西雅图消防部门 911 调遣。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 它每日更新，在 2019 年包含大约 800,000 行 (20 MB)。

此数据集包含从 2010 年至今累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 为确保相关性，建议将计算资源置于美国东部。

## <a name="additional-information"></a>其他信息

此数据集来自西雅图市政府。 有关详细信息，请参阅[西雅图城市网站](http://web5.seattle.gov/MNM/incidentresponse.aspx)。 请参阅[许可和属性，获取与使用此数据集相关的条款](https://creativecommons.org/publicdomain/zero/1.0/legalcode)。 如果对数据源有任何疑问，请发送电子邮件至 open.data@seattle.gov。

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| address | 字符串 | 196,965 | 517 3rd Av 318 2nd Av Et S | 事件位置。 |
| category | 字符串 | 232 | 帮助响应医疗响应 | 响应类型。 |
| dataSubtype | 字符串 | 1 | 911_Fire | “911_Fire” |
| dataType | 字符串 | 1 | 安全 | “安全” |
| dateTime | timestamp | 1,533,401 | 2020 年 11 月 4 日 06:49:00 2019 年 6 月 19 日 13:49:00 | 呼叫的日期和时间。 |
| latitude | Double | 94,332 | 47.602172 47.600194 | 这是纬度值。 纬线平行于赤道。 |
| longitude | Double | 79,492 | -122.330863 -122.330541 | 这是经度值。 经度线垂直于纬度线，并且都穿过两个极点。 |

## <a name="preview"></a>预览

| dataType | dataSubtype | dateTime | category | 子类别 | 状态 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全 | 911_Fire | 2021 年 4 月 28 日凌晨 5:22:00 | 垃圾着火 | null | null | 200 University St | 47.607299 | -122.337087 | null |  |
| 安全 | 911_Fire | 2021 年 4 月 28 日凌晨 5:15:00 | 会审事件 | null | null | 6th Ave / Olive Way | 47.61313 | -122.336282 | null |  |
| 安全 | 911_Fire | 2021 年  4 月 28 日凌晨 5:12:00 | 帮助响应 | null | null | 4th Ave S / Seattle Blvd S | 47.596486 | -122.329046 | null |  |
| 安全 | 911_Fire | 2021 年 4 月 28 日凌晨 5:09:00 | 垃圾着火 | null | null | 3rd Ave / University St | 47.607763 | -122.335976 | null |  |
| 安全 | 911_Fire | 2021 年 4 月 28 日凌晨 4:57:00 | 低灵敏度响应 | null | null | 533 3rd Ave W | 47.623717 | -122.360635 | null |  |
| 安全 | 911_Fire | 2021 年 4 月 28 日凌晨 4:57:00 | 转向 AMR | null | null | 4638 S Austin St | 47.534702 | -122.274812 | null |  |
| 安全 | 911_Fire | 2021 年 4 月 28 日凌晨 4:55:00 | 会审事件 | null | null | 8th Ave N / Harrison St | 47.622051 | -122.341066 | null |  |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_seattle -->

> [!TIP]
> _DownloadNotebook 的目录？serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_seattle_。 **[在 GitHub 中打开](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_seattle)** 。

```
# This is a package in preview.
from azureml.opendatasets import SeattleSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SeattleSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_seattle -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_seattle)** 。

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
folder_name = "Safety/Release/city=Seattle"
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

# <a name="sql"></a>[SQL](#tab/sql)

没有适用于此平台/包组合的示例。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_seattle -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_seattle)** 。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import SeattleSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SeattleSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_seattle -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_seattle)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Seattle"
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

# <a name="sql"></a>[SQL](#tab/sql)

没有适用于此平台/包组合的示例。

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_seattle -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_seattle)** 。

```python
# This is a package in preview.
from azureml.opendatasets import SeattleSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SeattleSafety(start_date=start_date, end_date=end_date)
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_seattle -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_seattle)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Seattle"
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

# <a name="sql"></a>[SQL](#tab/sql)

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK             'https://azureopendatastorage.blob.core.windows.net/citydatacontainer/Safety/Release/city=Seattle/*.parquet',
        FORMAT         = 'parquet'
    ) AS [r];
```

---

## <a name="examples"></a>示例

- 请参阅 GitHub 上的[城市安全分析](https://github.com/scottcounts/CitySafety)示例。


## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。