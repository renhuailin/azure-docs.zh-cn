---
title: 美国生产者价格指数行业
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的美国生产者价格指数行业数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: cd0662fc4990e6a2baf0a29ba2e96cec50509a05
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038535"
---
# <a name="us-consumer-price-index-industry"></a>美国消费者价格指数行业

生产价格指数 (PPI) 是国内生产者产品出售价格随时间变化平均值的测量值。 PPI 中包含的价格来自相关产品和服务的首次商业交易。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

生产价格指数修订当前序列指数反映了根据北美行业分类体系 (NAICS) 整理的生产者净产出的价格变动。 电脑数据集与一系列基于 NAICS 的经济时序（包括生产率、产量、就业、工资和收入）兼容。

PPI 涉及范围包括美国经济中产品生产部门的所有行业的产出，包括采矿业、制造业、农业、渔业和林业，以及天然气、电力、建筑和可与生产部门相竞争的产品行业，例如废物和废料处理行业。 此外，截至 2011 年 1 月，PPI 计划涵盖了服务业产出的四分之三以上，发布了以下行业部门中精选行业的数据：批发和零售贸易；运输和仓储；信息；金融和保险；房地产中介、租赁和出租；专业、科学和技术服务；行政、支持和废物管理服务；医疗保健和社会援助；以及住宿。

[原始数据集位置](https://download.bls.gov/pub/time.series/wp/)提供了[自述文件](https://download.bls.gov/pub/time.series/wp/wp.txt)，其中包含介绍此数据集详细信息的文件。 [常见问题解答](https://www.bls.gov/ppi/ppifaq.htm)中提供了其他信息。

此数据集来源于[美国劳工统计局 (BLS)](https://www.bls.gov/) 发布的[生产者价格指数数据](https://www.bls.gov/ppi/)。 要了解与使用此数据集相关的条款和条件，请查看[链接与版权信息](https://www.bls.gov/bls/linksite.htm)以及[重要网站声明](https://www.bls.gov/bls/website-policies.htm)。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [美国消费者价格指数](dataset-us-consumer-price-index.md)
- [美国生产者价格指数 - 商品](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| footnote_codes | 字符串 | 3 | nan P | 标识数据系列的脚注。 大多数值都为 Null。 请参阅 https://download.bls.gov/pub/time.series/pc/pc.footnote。 |
| industry_code | 字符串 | 1,064 | 221122 325412 | 行业的 NAICS 代码。 有关代码和名称，请参阅 https://download.bls.gov/pub/time.series/pc/pc.industry 。 |
| industry_name | 字符串 | 842 | 电力分配 药物制剂生产 | 对应于行业代码的名称。 有关代码和名称，请参阅 https://download.bls.gov/pub/time.series/pc/pc.industry 。 |
| period | 字符串 | 13 | M06 M07 | 标识观测数据的周期。 有关完整列表，请参阅 https://download.bls.gov/pub/time.series/pc/pc.period 。 |
| product_code | 字符串 | 4,822 | 324121P 316--- | 标识数据观察所引用产品的代码。 有关行业代码、产品代码和产品名称的映射，请参阅 https://download.bls.gov/pub/time.series/pc/pc.product 。 |
| product_name | 字符串 | 3,313 | 主要产品 次要产品 | 数据观测所引用的产品名称。 有关行业代码、产品代码和产品名称的映射，请参阅 https://download.bls.gov/pub/time.series/pc/pc.product 。 |
| 季节性 | 字符串 | 1 | U | 标识数据是否经过季节性调整的代码。 S = 季节性调整；U = 未经调整 |
| series_id | 字符串 | 4,822 | PCU332323332323M PCU333415333415C | 标识特定系列的代码。 时序是指在一致的时间间隔内在较长时间内观察到的一组数据。 有关时序详细信息（如代码、名称、开始年份和结束年份等），请参阅 https://download.bls.gov/pub/time.series/pc/pc.series 。 |
| series_title | 字符串 | 4,588 | 电力分配的 PPI 行业数据 - 西部南部中部，未进行季节性调整 电力分配的 PPI 行业数据 - 太平洋地区，未进行季节性调整 |  |
| 值 | FLOAT | 7,658 | 100.0 100.4000015258789 | 商品的价格指数。 |
| year | int | 22 | 2015 2017 | 标识观测年份。 |

## <a name="preview"></a>预览

| product_code | industry_code | series_id | year | period | 值 | footnote_codes | 季节性 | series_title | industry_name | product_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 2123240 | 212324 | PCU2123242123240 | 1998 | M01 | 117 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M02 | 116.9 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M03 | 116.3 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M04 | 116 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M05 | 116.2 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M06 | 116.3 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M07 | 116.6 | nan | U | 高岭土和球粘土开采的 PPI 行业数据 - 高岭土和球粘土，未进行季节性调整 | 高岭土和球粘土开采 | 高岭土和球粘土 |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry)** 。

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
folder_name = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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