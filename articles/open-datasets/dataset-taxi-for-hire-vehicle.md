---
title: 纽约出租车和豪华运营汽车数据集
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的纽约市出租车和豪车运营汽车数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2875ddfa1bf94121bea9038b576035042accbcfe
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038518"
---
# <a name="nyc-taxi--limousine-commission---for-hire-vehicle-fhv-trip-records"></a>纽约市出租车和豪华轿车委员会 - 绿色运营车辆 (FHV) 行程记录

营运车辆（“FHV”）的行程记录包含捕获调度基地许可证号，搭车日期、时间和出租车区域位置 ID 的字段（形成的文件如下）。 这些记录由基地所提交的 FHV 行程记录生成。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 截至 2018 年，大约有 5 亿行 (5 GB)。

此数据集包括从 2009 年到 2018 年累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="additional-information"></a>其他信息

纽约出租车和豪华轿车委员会 (TLC)：

数据是由 Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP) 授权的技术提供商收集并提供给纽约出租车和豪华轿车委员会 (TLC)。 行程数据不是由 TLC 创建的，因此 TLC 不对这些数据的准确性做任何声明。

查看[原始数据集位置](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)和[原始使用条款](https://www1.nyc.gov/home/terms-of-use.page)。

## <a name="columns"></a>列

| 名称            | 数据类型 | 唯一      | 值（示例）                         | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------|-----------|-------------|-----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dispatchBaseNum | string    | 1,144       | B02510 B02764                           | 调度行程的基地的 TLC 基本许可证号                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| doLocationId    | 字符串    | 267         | 265 132                                 | 行程结束时所处的 TLC 出租车区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| dropOffDateTime | timestamp | 57,110,352  | 2017-07-31 23:59:00 2017-10-15 00:44:34 | 行程的落客日期和时间。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| pickupDateTime  | timestamp | 111,270,396 | 2016-08-16 00:00:00 2016-08-17 00:00:00 | 行程接送的日期和时间。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| puLocationId    | 字符串    | 266         | 79 161                                  | 行程开始的 TLC 出租车区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| puMonth         | int       | 12          | 1 12                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| puYear          | int       | 5           | 2018 2017                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| srFlag          | string    | 44          | 1 2                                     | 指示该行程是否属于 High Volume FHV 公司（例如，Uber Pool、Lyft Line）提供的共享乘车链的一部分。 对于共享行程，值为 1。 对于非共享行程，该值为 NULL。 注意：对于大多数 High Volume FHV 公司，只有在行程中被请求并与其他共享乘车请求匹配的共享乘车才会被标记。 但是，Lyft（基地许可证编号 B02510 + B02844）也标记请求共享乘车，但另一个乘客未成功匹配以共享该行程的乘车。因此，来自这两个基地的 SR_Flag=1 的行程记录可能指示共享行程链中的第一个行程或请求共享乘车但从未匹配的行程。 用户应该能推测出对 Lyft 成功完成的共享行程的过度计数。 |

## <a name="preview"></a>预览

| dispatchBaseNum | pickupDateTime        | dropOffDateTime      | puLocationId | doLocationId | srFlag | puYear | puMonth |
|-----------------|-----------------------|----------------------|--------------|--------------|--------|--------|---------|
| B03157          | 6/30/2019 11:59:57 PM | 7/1/2019 12:07:21 AM | 264          | Null         | Null   | 2019   | 6       |
| B01667          | 6/30/2019 11:59:56 PM | 7/1/2019 12:28:06 AM | 264          | Null         | Null   | 2019   | 6       |
| B02849          | 6/30/2019 11:59:55 PM | 7/1/2019 12:14:10 AM | 264          | Null         | Null   | 2019   | 6       |
| B02249          | 6/30/2019 11:59:53 PM | 7/1/2019 12:15:53 AM | 264          | Null         | Null   | 2019   | 6       |
| B00887          | 6/30/2019 11:59:48 PM | 7/1/2019 12:29:29 AM | 264          | Null         | Null   | 2019   | 6       |
| B01626          | 6/30/2019 11:59:45 PM | 7/1/2019 12:18:20 AM | 264          | Null         | Null   | 2019   | 6       |
| B01259          | 6/30/2019 11:59:44 PM | 7/1/2019 12:03:15 AM | 264          | Null         | Null   | 2019   | 6       |
| B01145          | 6/30/2019 11:59:43 PM | 7/1/2019 12:11:15 AM | 264          | Null         | Null   | 2019   | 6       |
| B00887          | 6/30/2019 11:59:42 PM | 7/1/2019 12:34:21 AM | 264          | Null         | Null   | 2019   | 6       |
| B00821          | 6/30/2019 11:59:40 PM | 7/1/2019 12:02:57 AM | 264          | Null         | Null   | 2019   | 6       |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_pandas_dataframe()

nyc_tlc_df.info()
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas

# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "nyctlc"
folder_name = "fhv"

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

# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)

# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

没有适用于此平台/包组合的示例。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "fhv"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "fhv"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。