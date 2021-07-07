---
title: 美国全国工作时数及收入
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放式数据集中使用美国全国工作时数及收入数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: ffbd29a3aa19b999c9202a0cea36cafee6a4c5f4
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038537"
---
# <a name="us-national-employment-hours-and-earnings"></a>美国全国工作时数及收入

当前就业统计 (CES) 计划对美国非农就业、工时和工人收入进行了详细的行业估计。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

[原始数据集位置](https://download.bls.gov/pub/time.series/ce/)中提供了[自述文件](https://download.bls.gov/pub/time.series/ce/ce.txt)，其中包含有关此数据集的详细信息。

此数据集来源于[美国劳工统计局 (BLS)](https://www.bls.gov/) 发布的[当前就业统计数据 - CES（国家）数据](https://www.bls.gov/ces/)。 要了解与使用此数据集相关的条款和条件，请查看[链接与版权信息](https://www.bls.gov/bls/linksite.htm)以及[重要网站声明](https://www.bls.gov/bls/website-policies.htm)。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [美国各州工作时数及收入](dataset-us-state-employment-earnings.md)
- [美国各地区失业统计信息](dataset-us-local-unemployment.md)
- [美国劳动力统计信息](dataset-us-labor-force.md)

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） | 说明 |
|-|-|-|-|-|
| data_type_code | 字符串 | 37 | 1 10 | 请参见https://download.bls.gov/pub/time.series/ce/ce.datatype |
| data_type_text | 字符串 | 37 | 所有员工，数千位员工，上千 | 请参见https://download.bls.gov/pub/time.series/ce/ce.datatype |
| footnote_codes | 字符串 | 2 | nan P |  |
| industry_code | 字符串 | 902 | 30000000 32000000 | 包含的不同行业。 请参阅https://download.bls.gov/pub/time.series/ce/ce.industry |
| industry_name | 字符串 | 895 | 非耐用品 耐用品 | 包含的不同行业。 请参阅https://download.bls.gov/pub/time.series/ce/ce.industry |
| period | 字符串 | 13 | M03 M06 | 请参见https://download.bls.gov/pub/time.series/ce/ce.period |
| 季节性 | 字符串 | 2 | U S |  |
| series_id | 字符串 | 26,021 | CEU3100000008 CEU9091912001 | 数据集中可用的不同数据系列类型。 请参阅https://download.bls.gov/pub/time.series/ce/ce.series |
| series_title | 字符串 | 25,685 | 所有员工，数千，耐用品，非季节性调整，所有员工，数千，非耐用品，非季节性调整 | 数据集可用数据系列类型的标题。 请参阅https://download.bls.gov/pub/time.series/ce/ce.series |
| supersector_code | 字符串 | 22 | 31 60 | 粗略的行业或部门分类。 请参见https://download.bls.gov/pub/time.series/ce/ce.supersector |
| supersector_name | 字符串 | 22 | 耐用品，专业和业务服务 | 粗略的行业或部门分类。 请参见https://download.bls.gov/pub/time.series/ce/ce.supersector |
| 值 | FLOAT | 572,372 | 38.5 38.400001525878906 |  |
| year | int | 81 | 2017 2012 |  |

## <a name="preview"></a>预览

| data_type_code | industry_code | supersector_code | series_id | year | period | 值 | footnote_codes | 季节性 | series_title | supersector_name | industry_name | data_type_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M04 | 52 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M05 | 65 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M06 | 74 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M07 | 103 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M08 | 108 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M09 | 152 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M10 | 307 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M11 | 248 | nan | S | 所有员工，3 个月的平均变动，季节性调整，数千，私有总数，季节性调整 | 私有总计 | 私有总计 | 所有员工，3 个月的平均变动，季节性调整，数千 |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_pandas_dataframe()
```

```python
usLaborEHENational_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national)** 。

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
folder_name = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_spark_dataframe()
```

```python
display(usLaborEHENational_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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