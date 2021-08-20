---
title: 美国劳动力统计信息
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用美国劳动力统计信息数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d5e244ee760dbf274a4fc8efcf5320d6ed6ac303
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038517"
---
# <a name="us-labor-force-statistics"></a>美国劳动力统计信息

美国劳动力统计信息 劳动力、劳动力参与率，以及按年龄、性别、种族和人种划分的非机构平民人口 。

此数据集来源于[美国劳工统计局 (BLS)](https://www.bls.gov/) 发布的[当前就业统计数据 - CES（国家）数据](https://www.bls.gov/ces/)。 要了解与使用此数据集相关的条款和条件，请查看[链接与版权信息](https://www.bls.gov/bls/linksite.htm)以及[重要网站声明](https://www.bls.gov/bls/website-policies.htm)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="related-datasets"></a>相关数据集

- [美国全国工作时数及收入](dataset-us-national-employment-earnings.md)
- [美国各州工作时数及收入](dataset-us-state-employment-earnings.md)
- [美国各地区失业统计信息](dataset-us-local-unemployment.md)

## <a name="columns"></a>列

| 名称 | 数据类型 | 唯一 | 值（示例） |
|-|-|-|-|
| absn_code | int | 4 | 3 4 |
| activity_code | int | 7 | 8 3 |
| ages_code | int | 35 | 10 17 |
| born_code | int | 3 | 1 2 |
| cert_code | int | 5 | 4 3 |
| chld_code | int | 6 | 2 5 |
| class_code | int | 14 | 2 1 |
| disa_code | int | 3 | 2 1 |
| duration_code | int | 11 | 18 6 |
| education_code | int | 9 | 40 19 |
| entr_code | int | 3 | 1 2 |
| expr_code | int | 3 | 1 2 |
| footnote_codes | 字符串 | 7 | nan 4.0 |
| hheader_code | int | 2 | 1 |
| hour_code | int | 13 | 1 16 |
| indy_code | int | 323 | 368 169 |
| jdes_code | int | 3 | 1 2 |
| lfst_code | int | 33 | 20 30 |
| look_code | int | 7 | 1 6 |
| mari_code | int | 5 | 2 1 |
| mjhs_code | int | 6 | 1 5 |
| occupation_code | int | 566 | 8999 4999 |
| orig_code | int | 14 | 1 2 |
| pcts_code | int | 23 | 5 8 |
| period | 字符串 | 18 | M07 M06 |
| periodicity_code | 字符串 | 3 | M Q |
| race_code | int | 14 | 1 3 |
| rjnw_code | int | 9 | 1 3 |
| rnlf_code | int | 11 | 63 64 |
| rwns_code | int | 17 | 10 1 |
| 季节性 | 字符串 | 2 | U S |
| seek_code | int | 2 | 1 |
| series_id | 字符串 | 45,478 | LNU01300000 LNU02034560 |
| series_title | 字符串 | 34,264 | （调整前）就业水平 - 农业及相关行业（调整前）平民劳动力水平 |
| sexs_code | int | 3 | 1 2 |
| tdat_code | int | 6 | 1 4 |
| 值 | FLOAT | 121,742 | 3.0 4.0 |
| vets_code | int | 8 | 25 1 |
| wkst_code | int | 7 | 1 4 |
| year | int | 80 | 2018 2017 |

## <a name="preview"></a>预览

| series_id | year | period | 值 | footnote_codes | lfst_code | periodicity_code | series_title | absn_code | activity_code | ages_code | cert_code | class_code | duration_code | education_code | entr_code | expr_code | hheader_code | hour_code | indy_code | jdes_code | look_code | mari_code | mjhs_code | occupation_code | orig_code | pcts_code | race_code | rjnw_code | rnlf_code | rwns_code | seek_code | sexs_code | tdat_code | vets_code | wkst_code | born_code | chld_code | disa_code | 季节性 |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| LNS11000031Q | 1972 | Q01 | 4300 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1972 | Q02 | 4370 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1972 | Q03 | 4397 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1972 | Q04 | 4381 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q01 | 4408 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q02 | 4445 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q03 | 4477 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q04 | 4523 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1974 | Q01 | 4574 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1974 | Q02 | 4538 | nan | 10 | Q | （季节性）平民劳动力水平 - 20 岁 及以上，黑人或非裔美国人男性 | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-labor-force-statistics -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-labor-force-statistics)。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLFS

labor = UsLaborLFS()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-labor-force-statistics -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-labor-force-statistics)。

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
folder_name = "lfs/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-labor-force-statistics -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-labor-force-statistics)。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLFS

labor = UsLaborLFS()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-labor-force-statistics -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-labor-force-statistics)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "lfs/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-labor-force-statistics -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-labor-force-statistics)。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "lfs/"
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