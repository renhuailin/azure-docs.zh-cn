---
title: 芝加哥安全数据
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“芝加哥安全数据”数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d39c89308f0c33f98f1c5d074746a2008317472a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038646"
---
# <a name="chicago-safety-data"></a>芝加哥安全数据

芝加哥市的 311 服务请求，包括历史的卫生法规投诉、报告的坑洞和路灯问题

自 2011 年 1 月 1 日以来，所有向 311 提出的待解决的卫生法规投诉和所有已完成的请求。 芝加哥街道卫生局调查了报告的违反芝加哥卫生法规的行为，并采取了补救措施。 对于违规行为（例如垃圾箱溢出和小巷中的垃圾），居民可以提出服务请求。 311 有时会收到重复的卫生法规投诉。 被标记为重复项的请求与在先请求位于同一地理区域，并且大致在同一时间输入到 311 的客户服务请求 (CSR) 系统中。 重复投诉在状态字段中被标记为“待解决 - 重复”或“已完成 - 重复”。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

芝加哥交通部 (CDOT) 监管芝加哥 4,000 多英里主干道和住宅街道上的坑洞修补。 CDOT 通过 311 呼叫中心接收坑洞报告。 CDOT 使用地图和跟踪系统来识别坑洞位置并调度工作人员。

一次 311 呼叫可能会导致多次坑洞修补。 工作人员到达并修补一个 311 坑洞时，会填满该街区中的所有其他坑洞。 坑洞修补在 311 第一次收到坑洞报告七天内完成。 天气状况（严寒和降雨）会影响修补时间。 在天气情况良好且没有降雨的时候，工作人员可以修补数千个坑洞。 

如果对于四个地址的缓冲范围，已提出一个在先请求，则系统将该请求的状态设为“重复(待解决)”。 例如，如果存在一个用于 6535 N Western 的 CSR，并且收到了针对 6531 N Western（位于原始 CSR 的 4 个地址之内）的新请求，则系统将新请求的状态设为“重复(待解决)”。 街道修补完成后，原始请求在 CSR 中的状态显示为“已完成”，任何重复请求的状态显示为“重复(已解决)”。 已检查报告的地址但未发现坑洞或坑洞已修补时，服务请求的状态也是“已完成”。 如果发现了街道的其他问题（例如“塌陷”或“公共设施断开失败”），则转而提交到相应的部门或承包商。

自 2011 年 1 月 1 日以来，所有向 311 提出的“路灯 - 全部故障”（三盏或更多的灯发生故障）的待解决报告以及所有已完成的请求。 芝加哥交通局 (CDOT) 监管约 25 万盏路灯，这些路灯覆盖芝加哥的主干道和住宅街道。 CDOT 根据居民对路灯故障的报告进行维修和灯泡更换。 每当 CDOT 收到“全部故障”的报告时，所指派的维修电工将查看该电路中的路灯（每个电路有 8 - 16 盏灯），确保均正常工作。 如果在原始请求的 4 个日历日内提出对同一电路中路灯故障的第二个请求，则最新请求自动设置为“重复（待解决）”状态。 由于 CDOT 的电工将查看电路中的灯，验证其是否均正常工作，因此将自动查看任何“重复(待解决)”地址并进行维修。 路灯维修完成后，原始请求在 CSR 中的状态显示为“已完成”，任何重复请求的状态显示为“重复（已解决）”。 发生以下情况之一时，服务请求也会设置为“已完成”状态：已检查报告的灯但发现维修情况良好且功能正常；服务请求针对于不存在的地址；灯由承包商维护。 数据每天更新。

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 每天更新，截至 2018 年总共包括约 1 百万行 (80 MB)。

此数据集包括从 2011 年到 2018 年累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [芝加哥卫生设施](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Sanitation-Code-Complaints-Hi/me59-5fac)
- [芝加哥坑洞](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Pot-Holes-Reported-Historical/7as2-ds3y)
- [芝加哥路灯](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Street-Lights-All-Out-Histori/zuxi-7xem)

## <a name="additional-information"></a>其他信息

此数据集源自芝加哥市政府。

有关此数据集的使用条款，请参阅此处。 如果对数据源有任何疑问，请发送电子邮件至 dataportal@cityofchicago.org。

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| address | string | 140,612 | \" \" 1 City Hall Plz Boston MA 02108 | 位置。 |
| category | string | 54 | 街道清洁卫生设施 | 服务请求原因。 |
| dataSubtype | 字符串 | 1 | 311_All | “311_All” |
| dataType | 字符串 | 1 | 安全 | “安全” |
| dateTime | timestamp | 1,529,075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | 服务请求的公开日期和时间。 |
| latitude | Double | 1,622 | 42.3594 42.3603 | 这是纬度值。 纬线平行于赤道。 |
| longitude | Double | 1,806 | -71.0587 -71.0583 | 这是经度值。 经度线垂直于纬度线，并且都穿过两个极点。 |
| source | string | 7 | Constituent Call Citizens Connect App | 案件的原始来源。 |
| 状态 | 字符串 | 2 | Closed Open | 事件状态。 |
| 子类别 | string | 209 | Parking Enforcement Requests for Street Cleaning | 服务请求类型。 |

## <a name="preview"></a>预览

| dataType | dataSubtype | dateTime | category | 子类别 | 状态 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全 | 311_All | 4/25/2021 11:55:04 PM | 路灯故障投诉 | Null | 打开 | 4800 W WASHINGTON BLVD | 41.882148426 | -87.74556256 | null |  |
| 安全 | 311_All | 4/25/2021 11:54:31 PM | 311 咨询呼叫 | Null | 已完成 | 2111 W Lexington ST |  |  | null |  |
| 安全 | 311_All | 4/25/2021 11:52:11 PM | 311 咨询呼叫 | Null | 已完成 | 2111 W Lexington ST |  |  | null |  |
| 安全 | 311_All | 4/25/2021 11:49:56 PM | 311 咨询呼叫 | Null | 已完成 | 2111 W Lexington ST |  |  | null |  |
| 安全 | 311_All | 4/25/2021 11:48:53 PM | 垃圾装运车维护 | Null | 打开 | 3409 E 106TH ST | 41.702545562 | -87.540917602 | null |  |
| 安全 | 311_All | 4/25/2021 11:46:01 PM | 311 咨询呼叫 | Null | 已完成 | 2111 W Lexington ST |  |  | null |  |
| 安全 | 311_All | 4/25/2021 11:45:46 PM | 飞机噪音投诉 | Null | 已完成 | 10510 W ZEMKE RD |  |  | null |  |
| 安全 | 311_All | 4/25/2021 11:45:02 PM | 311 咨询呼叫 | Null | 已完成 | 2111 W Lexington ST |  |  | null |  |
| 安全 | 311_All | 4/25/2021 11:44:24 PM | 下水道塌陷检查请求 | Null | 打开 | 7246 W THORNDALE AVE | 41.987984339 | -87.808702917 | null |  |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago)。

```
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago)。

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
folder_name = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago)。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago)。

```python
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

```python
# Display data statistic information
display(safety, summary = True)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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