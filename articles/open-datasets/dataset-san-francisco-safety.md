---
title: 旧金山安全数据
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“旧金山安全数据”数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4502d51054f43b7b1d876443c12f67418ba84060
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982449"
---
# <a name="san-francisco-safety-data"></a>旧金山安全数据

旧金山市消防部门呼叫服务和 311 事件。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

消防呼叫服务包括所有消防单位对呼叫的响应。 每个记录都包括呼叫号码、事件编号、地址、消防单位标识符、呼叫类型和处理情况。 其中还包括所有相关的时间间隔。 因为此数据集基于响应，并且由于大多数呼叫涉及多个单位，所以每个呼叫号码都有多个记录。 地址与街区号、交叉点或电话亭关联，而不与特定地址关联。

311 案例包括与某个地点或景物（例如，公园、街道或建筑物）相关的案例，于 2008 年 7 月 1 日之后创建。 不包括用户记录的与其自身需求相关的案例。 例如，财产或商业税务问题、停车许可请求等。 有关详细信息，请参阅[计划链接](https://support.datasf.org/help/311-case-data-faq)。
 
## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 每天更新一次，截至 2019 年约有 600 万行 (400 MB)。

此数据集包含从 2015 年至今累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [消防部门呼叫](https://data.sfgov.org/Public-Safety/Fire-Department-Calls-for-Service/nuek-vuh3)
- [311 案例](https://data.sfgov.org/City-Infrastructure/311-Cases/vw6y-z8j6)

## <a name="additional-information"></a>其他信息

此数据集来自旧金山市政府。 请参阅[此数据集的使用条款](https://datasf.org/opendata/terms-of-use/)。

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| address | string | 280,652 | Not associated with a specific address 0 Block of 6TH ST | 事件地址（注意：为保护呼叫者的隐私，地址和位置泛化为街道的中间街区、交叉点或最近的电话亭位置）。 |
| category | string | 108 | Street and Sidewalk Cleaning Potentially Life-Threatening | 311 服务请求类型或 911 火灾报警呼叫类型组的人类可读名称。 |
| dataSubtype | 字符串 | 2 | 911_Fire 311_All | “911_Fire”或“311_All”。 |
| dataType | 字符串 | 1 | 安全 | “安全” |
| dateTime | timestamp | 6,496,563 | 2020-10-19 12:28:08 2020-07-28 06:40:26 | 发出服务请求或收到火灾报警的日期和时间。 |
| latitude | Double | 1,615,369 | 37.777624238929 37.786117211838 | 位置纬度，采用 WGS84 投影。 |
| longitude | Double | 1,554,612 | -122.39998111124 -122.419854245692 | 位置经度，采用 WGS84 投影。 |
| source | string | 9 | Phone Mobile/Open311 | 服务请求接收机制或路径；通常是“手机”、“短信”、“网站”、“移动应用”、“Twitter”等，但术语可能因系统而异。 |
| 状态 | 字符串 | 3 | Closed Open | 服务请求当前状态的单字指示符。 （注意：GeoReport V2 仅允许“待处理”和“已结案”） |
| 子类别 | string | 1,270 | Medical Incident Bulky Items | 311 事件的服务请求子类型或 911 火灾报警的呼叫类型的人类可读名称。 |

## <a name="preview"></a>预览

| dataType | dataSubtype | dateTime | category | 子类别 | 状态 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全 | 911_Fire | 4/26/2021 2:56:13 AM | 不会造成人身伤害 | 医疗事故 | Null | 700 Block of GEARY ST | 37.7863607914647 | -122.415616900246 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:56:13 AM | 不会造成人身伤害 | 医疗事故 | Null | 700 Block of GEARY ST | 37.7863607914647 | -122.415616900246 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:54:03 AM | 不会造成人身伤害 | 医疗事故 | Null | 0 Block of ESSEX ST | 37.7860048266229 | -122.395077258809 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:54:03 AM | 不会造成人身伤害 | 医疗事故 | Null | 0 Block of ESSEX ST | 37.7860048266229 | -122.395077258809 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:52:17 AM | 不会造成人身伤害 | 医疗事故 | Null | 700 Block of 29TH AVE | 37.7751770865322 | -122.488604397217 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:50:28 AM | 可能会造成人身伤害 | 医疗事故 | Null | 1000 Block of GEARY ST | 37.7857350982044 | -122.420555240691 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:50:28 AM | 可能会造成人身伤害 | 医疗事故 | Null | 1000 Block of GEARY ST | 37.7857350982044 | -122.420555240691 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:33:52 AM | 不会造成人身伤害 | 医疗事故 | Null | 100 Block of BELVEDERE ST | 37.767791696654 | -122.449332294394 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:33:52 AM | 不会造成人身伤害 | 医疗事故 | Null | 100 Block of BELVEDERE ST | 37.767791696654 | -122.449332294394 | null |  |
| 安全 | 911_Fire | 4/26/2021 2:33:51 AM | 可能会造成人身伤害 | 医疗事故 | Null | 100 Block of 6TH ST | 37.7807920802756 | -122.408385745499 | null |  |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork)。

```
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork)。

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
folder_name = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork)。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork)。

```python
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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
