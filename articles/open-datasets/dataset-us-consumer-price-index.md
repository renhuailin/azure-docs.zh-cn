---
title: 美国消费者价格指数
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用美国消费者价格指数数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fffe6b1521f3f9ab4973a64f03ef52e4a9019658
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038540"
---
# <a name="us-consumer-price-index"></a>美国消费者价格指数

消费者价格指数 (CPI) 是衡量城市消费者为一系列市场消费商品和服务所支付的价格随时间变化的平均值。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

[原始数据集位置](https://download.bls.gov/pub/time.series/cu/)提供了[自述文件](https://download.bls.gov/pub/time.series/cu/cu.txt)，其中包含介绍此数据集详细信息的文件。

此数据集来源于[美国劳工统计局 (BLS)](https://www.bls.gov/) 发布的[消费者价格指数数据](https://www.bls.gov/cpi/)。 有关条款和条件，请查看[链接和版权信息](https://www.bls.gov/bls/linksite.htm)和[重要网站和声明](https://www.bls.gov/bls/website-policies.htm)。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 为实现相关性，建议将计算资源置于美国东部。

## <a name="related-datasets"></a>相关数据集

- [美国生产者价格指数 - 行业](dataset-us-producer-price-index-industry.md)
- [美国生产者价格指数 - 商品](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| area_code | 字符串 | 70 | 0000 0300 | 用于标识特定地理区域的唯一代码。 有关完整的区域代码，请参阅 http://download.bls.gov/pub/time.series/cu/cu.area |
| area_name | 字符串 | 69 | 美国城市平均值（南方） | 特定地理区域的名称。 有关所有区域名称和代码，请参阅 https://download.bls.gov/pub/time.series/cu/cu.area 。 |
| footnote_codes | 字符串 | 3 | nan U | 标识数据系列的脚注。 大多数值都为 Null。 |
| item_code | 字符串 | 515 | SA0E SAF11 | 确定数据观测相关的项目。 有关所有项目名称和代码，请参阅 https://download.bls.gov/pub/time.series/cu/cu.item 。 |
| item_name | 字符串 | 515 | 家庭能量食品 | 商品全称。 有关项目名称和代码，请参阅 https://download.bls.gov/pub/time.series/cu/cu.txt 。 |
| period | 字符串 | 16 | S01 S02 | 标识观测数据的周期。 格式：M01-M13 或 S01-S03（M = 月，M13 = 年度平均，S =半年）。 例如：M06 = 6 月。 有关周期名称和代码，请参阅 https://download.bls.gov/pub/time.series/cu/cu.period 。 |
| periodicity_code | 字符串 | 3 | R S | 数据观察的频率。 S = 半年；R = 定期。 |
| 季节性 | 字符串 | 1,043 | U S | 标识数据是否经过季节性调整的代码。 S = 季节性调整；U = 未经调整。 |
| series_id | 字符串 | 16,683 | CWURS400SA0E CWUR0100SA0E | 标识特定系列的代码。 时序是指在较长时间内以一致的时间间隔（即，月、季度、半年、年）观测到的一组数据。 BLS 时序数据通常以月为时间间隔生成，其数据范围从特定地理区域的特定使用者项目（其价格按月收集）到特定行业的工人类别（其就业率按月记录）等等。 有关详细信息，请参阅 https://download.bls.gov/pub/time.series/cu/cu.txt |
| series_title | 字符串 | 8,336 | 美国城市酒精饮料平均值、所有城市消费者、洛杉矶-长滩-阿纳海姆的未按季节性调整运输、加利福尼亚州、所有城市消费者、未进行季节性调整 | 相应 series_id 的系列名称。 有关时序 ID 和名称，请参阅 https://download.bls.gov/pub/time.series/cu/cu.series 。 |
| 值 | FLOAT | 310,603 | 100.0 101.0999984741211 | 商品的价格指数。 |
| year | int | 25 | 2018 2017 | 标识观测年份。 |

## <a name="preview"></a>预览

| area_code | item_code | series_id | year | period | 值 | footnote_codes | 季节性 | periodicity_code | series_title | item_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | 加利福尼亚圣地亚哥卡尔斯巴德的电力、所有城市消费者、未根据季节调整 | 电能 | 加利福尼亚州圣迭戈卡尔斯巴德 |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | 加利福尼亚圣地亚哥卡尔斯巴德的电力、所有城市消费者、未根据季节调整 | 电能 | 加利福尼亚州圣迭戈卡尔斯巴德 |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | 加利福尼亚圣地亚哥卡尔斯巴德的电力、所有城市消费者、未根据季节调整 | 电能 | 加利福尼亚州圣迭戈卡尔斯巴德 |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | 加利福尼亚圣地亚哥卡尔斯巴德的电力、所有城市消费者、未根据季节调整 | 电能 | 加利福尼亚州圣迭戈卡尔斯巴德 |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | 加利福尼亚圣地亚哥卡尔斯巴德的电力、所有城市消费者、未根据季节调整 | 电能 | 加利福尼亚州圣迭戈卡尔斯巴德 |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | 加利福尼亚圣地亚哥卡尔斯巴德的电力、所有城市消费者、未根据季节调整 | 电能 | 加利福尼亚州圣迭戈卡尔斯巴德 |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_pandas_dataframe()
```

```python
usLaborCPI_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index)** 。

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
folder_name = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_spark_dataframe()
```

```python
display(usLaborCPI_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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