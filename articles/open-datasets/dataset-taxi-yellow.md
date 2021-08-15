---
title: 纽约市出租车和豪车黄色数据集
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的纽约市出租车和豪车黄色数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 5bf2a3b363c7d8a1cd0b10b1c958c4cfbb567deb
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038681"
---
# <a name="nyc-taxi--limousine-commission---yellow-taxi-trip-records"></a>纽约市出租车和豪车委员会 - 黄色出租车行程记录

黄色的出租车行程记录包括捕获以下信息的字段：上车和下车日期/时间、上车和下车位置、行程距离、逐条记录的车费、费率类型、付款类型和司机报告的乘客数。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 截至 2018 年，总共约有 15 亿行 (50 GB)。

此数据集包括从 2009 年到 2018 年累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="additional-information"></a>其他信息

纽约出租车和豪华轿车委员会 (TLC)：

数据是由 Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP) 授权的技术提供商收集并提供给纽约出租车和豪华轿车委员会 (TLC)。 行程数据不是由 TLC 创建的，因此 TLC 不对这些数据的准确性做任何声明。

查看[原始数据集位置](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)和[原始使用条款](https://www1.nyc.gov/home/terms-of-use.page)。

## <a name="columns"></a>列
| 名称                 | 数据类型 | 唯一      | 值（示例）                         | 说明                                                                                                                                                                                                                                            |
|----------------------|-----------|-------------|-----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | 字符串    | 265         | 161 236                                 | 未使用出租车计价器的 TLC 出租车区域。                                                                                                                                                                                                   |
| endLat               | Double    | 961,994     | 41.366138 40.75                         |                                                                                                                                                                                                                                                        |
| endLon               | Double    | 1,144,935   | -73.137393 -73.9824                     |                                                                                                                                                                                                                                                        |
| extra                | Double    | 877         | 0.5 1.0                                 | 其他杂费和附加费。 目前，这仅包括 0.50 美元和 1 美元的高峰时段费和跨夜费用。                                                                                                                                   |
| fareAmount           | Double    | 18,935      | 6.5 4.5                                 | 计价器计算的时间和距离费用。                                                                                                                                                                                                    |
| improvementSurcharge | 字符串    | 60          | 0.3 0                                   | 已针对行程的起步价征收 0.30 美元的改进附加费。 自 2015 年起开始征收改进附加费。                                                                                                                                     |
| mtaTax               | Double    | 360         | 0.5 -0.5                                | 根据使用的计量费率自动触发的 0.50 美元 MTA 税。                                                                                                                                                                        |
| passengerCount       | int       | 64          | 1 2                                     | 车辆中的乘客人数。 这是驾驶员输入的值。                                                                                                                                                                               |
| paymentType          | string    | 6,282       | CSH CRD                                 | 表示乘客如何支付行程费用的数字代码。 1 = 信用卡；2 = 现金；3 = 免费；4 = 争议；5 = 未知；6 = 失效行程。                                                                                                          |
| puLocationId         | 字符串    | 266         | 237 161                                 | 使用了出租车计价器的 TLC 出租车区域。                                                                                                                                                                                                      |
| puMonth              | int       | 12          | 3 5                                     |                                                                                                                                                                                                                                                        |
| puYear               | int       | 29          | 2012 2011                               |                                                                                                                                                                                                                                                        |
| rateCodeId           | int       | 56          | 1 2                                     | 行程结束时实行的最终费率代码。 1 = 标准费率；2 = JFK；3 = Newark；4 = Nassau 或 Westchester；5 = 议价票价；6 = 拼车。                                                                                                |
| startLat             | Double    | 833,016     | 41.366138 40.7741                       |                                                                                                                                                                                                                                                        |
| startLon             | Double    | 957,428     | -73.137393 -73.9821                     |                                                                                                                                                                                                                                                        |
| storeAndFwdFlag      | 字符串    | 8           | N 0                                     | 此标志指示是否先将行程记录保存在车辆内存中，然后再发送到供应商（也称为“存储并转发”），因为车辆未连接到服务器。 Y =“存储并转发”行程；N = 非“存储并转发”行程。 |
| tipAmount            | Double    | 12,121      | 1.0 2.0                                 | 此字段自动填充信用卡小费。 不包括现金小费。                                                                                                                                                                |
| tollsAmount          | Double    | 6,634       | 5.33 4.8                                | 行程中支付的所有通行费总额。                                                                                                                                                                                                                |
| totalAmount          | Double    | 39,707      | 7.0 7.8                                 | 向乘客收取的总金额。 不含现金小费。                                                                                                                                                                                    |
| tpepDropoffDateTime  | timestamp | 290,185,010 | 2010-11-07 01:29:00 2013-11-03 01:22:00 | 未使用计价器的日期和时间。                                                                                                                                                                                                       |
| tpepPickupDateTime   | timestamp | 289,948,585 | 2010-11-07 01:00:00 2009-11-01 01:05:00 | 采用了计价器的日期和时间。                                                                                                                                                                                                          |
| tripDistance         | Double    | 14,003      | 1.0 0.9                                 | 出租车计价器报告的所经过的行程距离（以英里为单位）。                                                                                                                                                                                          |
| vendorID             | string    | 7           | VTS CMT                                 | 指示提供记录的 TPEP 提供商的代码。 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                   |
| vendorID             | int       | 2           | 2 1                                     | 指示提供记录的 LPEP 提供商的代码。 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                   |

## <a name="preview"></a>预览

| vendorID | tpepPickupDateTime    | tpepDropoffDateTime   | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeId | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | puYear | puMonth |
|----------|-----------------------|-----------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|--------|---------|
| 2        | 2088/1/24，凌晨 0:25:39 | 2088/1/24，早上 7:28:25  | 1              | 4.05         | 24           | 162          | 1          | N               | 2           | 14.5       | 0     | 0.5    | 0.3                  | 0         | 0           | 15.3        | 2088   | 1       |
| 2        | 2088/1/24，凌晨 0:15:42 | 2088/1/24，凌晨 0:19:46 | 1              | 0.63         | 41           | 166          | 1          | N               | 2           | 4.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 5.3         | 2088   | 1       |
| 2        | 2084/11/4，中午 12:32:24 | 2084/11/4，中午 12:47:41 | 1              | 1.34         | 238          | 236          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 2084/11/4，中午 12:25:53 | 2084/11/4，中午 12:29:00 | 1              | 0.32         | 238          | 238          | 1          | N               | 2           | 4          | 0     | 0.5    | 0.3                  | 0         | 0           | 4.8         | 2084   | 11      |
| 2        | 2084/11/4，中午 12:08:33 | 2084/11/4，中午 12:22:24 | 1              | 1.85         | 236          | 238          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 2084/11/4，上午 11:41:35 | 2084/11/4，上午 11:59:41 | 1              | 1.65         | 68           | 237          | 1          | N               | 2           | 12.5       | 0     | 0.5    | 0.3                  | 0         | 0           | 13.3        | 2084   | 11      |
| 2        | 2084/11/4，上午 11:27:28 | 2084/11/4，上午 11:39:52 | 1              | 1.07         | 170          | 68           | 1          | N               | 2           | 9          | 0     | 0.5    | 0.3                  | 0         | 0           | 9.8         | 2084   | 11      |
| 2        | 2084/11/4，上午 11:19:06 | 2084/11/4，上午 11:26:44 | 1              | 1.3          | 107          | 170          | 1          | N               | 2           | 7.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 8.3         | 2084   | 11      |
| 2        | 2084/11/4，上午 11:02:59 | 2084/11/4，上午 11:15:51 | 1              | 1.85         | 113          | 137          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 2084/11/4，上午 10:46:05 | 2084/11/4，上午 10:50:09 | 1              | 0.62         | 231          | 231          | 1          | N               | 2           | 4.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 5.3         | 2084   | 11      |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
folder_name = "yellow"

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
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
blob_relative_path = "yellow"
blob_sas_token = r"

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
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
blob_relative_path = "yellow"
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