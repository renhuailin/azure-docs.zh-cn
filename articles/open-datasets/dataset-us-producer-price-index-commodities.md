---
title: 美国生产者价格指数 - 商品
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“美国生产者价格指数 - 商品”数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 224a5d0278237d5a7d87ee8e3c9adaedb71f193a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038674"
---
# <a name="us-producer-price-index---commodities"></a>美国生产者价格指数 - 商品

生产价格指数 (PPI) 是国内生产者产品出售价格随时间变化平均值的测量值。 PPI 中包含的价格来自相关产品和服务的首次商业交易。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

每个月发布约 10,000 个针对单个产品和产品组的 PPI。 PPI 适用于美国经济中产品生产部门的近乎所有行业的产出，包括采矿业、制造业、农业、渔业和林业，以及天然气、电力、建筑和可与生产部门相竞争的产品行业，例如废物和废料处理行业。 以 2007 年经济普查中报告的收入衡量，PPI 计划覆盖了约 72% 的服务业产出。 数据涉及以下部门中的行业：批发和零售贸易；运输和仓储；信息；金融和保险；房地产中介、租赁和出租；专业、科学和技术服务；行政、支持和废物管理服务；医疗保健和社会援助；以及住宿。

[原始数据集位置](https://download.bls.gov/pub/time.series/wp/)提供了[自述文件](https://download.bls.gov/pub/time.series/wp/wp.txt)，其中包含介绍此数据集详细信息的文件。 [常见问题解答](https://www.bls.gov/ppi/ppifaq.htm)中提供了其他信息。

此数据集来源于[美国劳工统计局 (BLS)](https://www.bls.gov/) 发布的[生产者价格指数数据](https://www.bls.gov/ppi/)。 要了解与使用此数据集相关的条款和条件，请查看[链接与版权信息](https://www.bls.gov/bls/linksite.htm)以及[重要网站声明](https://www.bls.gov/bls/website-policies.htm)。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [美国消费者价格指数](dataset-us-consumer-price-index.md)
- [美国生产者价格指数 - 行业](dataset-us-producer-price-index-industry.md)

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| footnote_codes | 字符串 | 3 | nan P | 标识数据系列的脚注。 大多数值都为 Null。 请参阅 https://download.bls.gov/pub/time.series/wp/wp.footnote。 |
| group_code | string | 56 | 02 01 | 标识指数涵盖的主要商品组的代码。 有关组代码和名称，请参阅 https://download.bls.gov/pub/time.series/wp/wp.group 。 |
| group_name | string | 56 | 加工食品和饲料；农产品 | 指数涵盖的主要商品组的名称。 有关组代码和名称，请参阅 https://download.bls.gov/pub/time.series/wp/wp.group 。 |
| item_code | 字符串 | 2,949 | 1 11 | 确定数据观测相关的项目。 有关项目代码和名称，请参阅 https://download.bls.gov/pub/time.series/wp/wp.item 。 |
| item_name | 字符串 | 3,410 | 仓库、贮藏和相关服务；乘用车租赁 | 商品全称。 有关项目代码和名称，请参阅 https://download.bls.gov/pub/time.series/wp/wp.item 。 |
| period | 字符串 | 13 | M06 M07 | 标识观测数据的周期。 有关周期值列表，请参阅 https://download.bls.gov/pub/time.series/wp/wp.period 。 |
| 季节性 | 字符串 | 2 | U S | 标识数据是否经过季节性调整的代码。 S = 季节性调整；U = 未经调整 |
| series_id | 字符串 | 5,458 | WPU101 WPU091 | 标识特定系列的代码。 时序是指在一致的时间间隔内在较长时间内观察到的一组数据。 有关时序详细信息（如代码、名称、开始年份和结束年份等），请参阅 https://download.bls.gov/pub/time.series/wp/wp.series 。 |
| series_title | 字符串 | 4,379 | 采矿服务的 PPI 商品数据，未按季节调整；金属处理服务的 PPI 商品数据，未按季节调整 | 特定系列的标题。 时序是指在一致的时间间隔内在较长时间内观察到的一组数据。 有关 ID、名称、开始年份和结束年份等时序的详细信息，请参阅 https://download.bls.gov/pub/time.series/wp/wp.series 。 |
| 值 | FLOAT | 6,788 | 100.0 99.0999984741211 | 商品的价格指数。 |
| year | int | 26 | 2018 2017 | 标识观测年份。 |

## <a name="preview"></a>预览

| item_code | group_code | series_id | year | period | 值 | footnote_codes | 季节性 | series_title | group_name | item_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 120922 | 05 | WPU05120922 | 2008 | M06 | 100 | nan | U | 燃油及相关产品和能源的 PPI 商品数据；精制烟煤地下采矿（仅限机械粉碎/筛选/分粒）的 PPI 商品数据，未按季节调整 | 燃油及相关产品和能源 | 精制烟煤地下采矿，仅限机械粉碎/筛选/分粒 |
| 120922 | 05 | WPU05120922 | 2008 | M07 | 104.6 | nan | U | 燃油及相关产品和能源的 PPI 商品数据；精制烟煤地下采矿（仅限机械粉碎/筛选/分粒）的 PPI 商品数据，未按季节调整 | 燃油及相关产品和能源 | 精制烟煤地下采矿，仅限机械粉碎/筛选/分粒 |
| 120922 | 05 | WPU05120922 | 2008 | M08 | 104.4 | nan | U | 燃油及相关产品和能源的 PPI 商品数据；精制烟煤地下采矿（仅限机械粉碎/筛选/分粒）的 PPI 商品数据，未按季节调整 | 燃油及相关产品和能源 | 精制烟煤地下采矿，仅限机械粉碎/筛选/分粒 |
| 120922 | 05 | WPU05120922 | 2008 | M09 | 98.3 | nan | U | 燃油及相关产品和能源的 PPI 商品数据；精制烟煤地下采矿（仅限机械粉碎/筛选/分粒）的 PPI 商品数据，未按季节调整 | 燃油及相关产品和能源 | 精制烟煤地下采矿，仅限机械粉碎/筛选/分粒 |
| 120922 | 05 | WPU05120922 | 2008 | M10 | 101.5 | nan | U | 燃油及相关产品和能源的 PPI 商品数据；精制烟煤地下采矿（仅限机械粉碎/筛选/分粒）的 PPI 商品数据，未按季节调整 | 燃油及相关产品和能源 | 精制烟煤地下采矿，仅限机械粉碎/筛选/分粒 |
| 120922 | 05 | WPU05120922 | 2008 | M11 | 95.2 | nan | U | 燃油及相关产品和能源的 PPI 商品数据；精制烟煤地下采矿（仅限机械粉碎/筛选/分粒）的 PPI 商品数据，未按季节调整 | 燃油及相关产品和能源 | 精制烟煤地下采矿，仅限机械粉碎/筛选/分粒 |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities)。

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
folder_name = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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