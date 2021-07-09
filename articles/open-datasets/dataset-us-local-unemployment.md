---
title: 美国各地区失业统计信息
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用美国当地失业统计数字数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 13af967282d1f9be8f289612936ab7d33a9fce11
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038538"
---
# <a name="us-local-area-unemployment-statistics"></a>美国各地区失业统计信息

地区失业统计 (LAUS) 计划为美国人口普查地区及分区、州、县、大都市区和许多城市提供月度和年度就业、失业和劳动力数据。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

[原始数据集位置](https://download.bls.gov/pub/time.series/la/)提供了[自述文件](https://download.bls.gov/pub/time.series/la/la.txt)，其中包含介绍此数据集详细信息的文件。

此数据集来源于[美国劳工统计局 (BLS)](https://www.bls.gov/) 发布的[地区失业统计数据](https://www.bls.gov/lau/)。 要了解与使用此数据集相关的条款和条件，请查看[链接与版权信息](https://www.bls.gov/bls/linksite.htm)以及[重要网站声明](https://www.bls.gov/bls/website-policies.htm)。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [美国全国工作时数及收入](dataset-us-national-employment-earnings.md)
- [美国各州工作时数及收入](dataset-us-state-employment-earnings.md)
- [美国劳动力统计信息](dataset-us-labor-force.md)

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| area_code | 字符串 | 8,290 | ST3400000000000 RD8200000000000 | 标识地理区域的代码。 请参阅 https://download.bls.gov/pub/time.series/la/la.area。 |
| area_text | 字符串 | 8,238 | 哥伦比亚特区 俄勒冈 | 地理区域的名称。 请参阅https://download.bls.gov/pub/time.series/la/la.area |
| area_type_code | 字符串 | 14 | F G | 定义区域类型的唯一代码。 请参阅https://download.bls.gov/pub/time.series/la/la.area_type |
| areatype_text | 字符串 | 14 | 县和同等规模城市及人口超过 25,000 的城镇 | 区域类型的名称。 |
| footnote_codes | 字符串 | 5 | nan P |  |
| measure_code | 字符串 | 4 | 5 4 | 标识被度量元素的代码。 03：失业率，04：失业人数，05：就业人数，06：劳动力总数。 请参阅 https://download.bls.gov/pub/time.series/la/la.measure。 |
| measure_text | 字符串 | 4 | 就业 失业 | 被度量元素的名称。 请参阅https://download.bls.gov/pub/time.series/la/la.measure |
| period | 字符串 | 13 | M07 M05 | 标识时间段，通常为月。 请参阅https://download.bls.gov/pub/time.series/la/la.period |
| 季节性 | 字符串 | 2 | U S |  |
| series_id | 字符串 | 33,476 | LASST160000000000006 LASST200000000000006 | 标识系列的代码。 有关完整系列列表，请参阅 https://download.bls.gov/pub/time.series/la/la.series 。 |
| series_title | 字符串 | 33,268 | 就业：宾夕法尼亚州费城县/市，劳动力：弗吉尼亚州马纳萨斯市 | 标识系列的标题。 有关完整系列列表，请参阅 https://download.bls.gov/pub/time.series/la/la.series 。 |
| srd_code | 字符串 | 53 | 48 23 | 州、区域或部门代码。 |
| srd_text | 字符串 | 53 | 德克萨斯州缅因州 |  |
| 值 | FLOAT | 600,099 | 4.0 5.0 | 特定度量单位的值。 |
| year | int | 44 | 2009 2008 |  |

## <a name="preview"></a>预览

| area_code | area_type_code | srd_code | measure_code | series_id | year | period | 值 | footnote_codes | 季节性 | series_title | measure_text | srd_text | areatype_text | area_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M01 | 4.7 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M02 | 4.7 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M03 | 4.2 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M04 | 3.6 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M05 | 3.6 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M06 | 3.6 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M07 | 3.6 | nan | U | 失业率：纽约州大型联合统计区 (U) 叙拉古-奥本市 | 失业率 | 纽约 | 联合区域 | 纽约州大型联合统计区 (U) 叙拉古-奥本市 |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_pandas_dataframe()
```

```python
usLaborLAUS_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics)** 。

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
container_name = "laborstatisticscontainer"
folder_name = "laus/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_spark_dataframe()
```

```python
display(usLaborLAUS_df.limit(5))
```

<!-- nbend -->

 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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

没有适用于此平台/包组合的示例。

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。