---
title: 纽约市出租车和豪车绿色数据集
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放式数据集中使用纽约市出租车和豪车绿色数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 058969168b09c1eb394b6fc7a9f06c401c7f9763
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038544"
---
# <a name="nyc-taxi--limousine-commission---green-taxi-trip-records"></a>纽约市出租车和豪华轿车委员会 - 绿色出租车行程记录

绿色的出租车行程记录包括捕获以下信息的字段：上车和下车日期/时间、上车和下车位置、行程距离、逐条记录的车费、费率类型、付款类型和司机报告的乘客数。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>数量和保留期

此数据集以 Parquet 格式存储。 截至 2018 年，总共约有 8000 万行 (2 GB)。

此数据集包括从 2009 年到 2018 年累积的历史记录。 可使用我们的 SDK 中的参数设置来提取特定时间范围内的数据。

## <a name="storage-location"></a>存储位置

此数据集存储在美国东部 Azure 区域。 建议将计算资源分配到美国东部地区，以实现相关性。

## <a name="additional-information"></a>其他信息

纽约出租车和豪华轿车委员会 (TLC)：

数据是由 Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP) 授权的技术提供商收集并提供给纽约出租车和豪华轿车委员会 (TLC)。 行程数据不是由 TLC 创建的，因此 TLC 不对这些数据的准确性做任何声明。

查看[原始数据集位置](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)和[原始使用条款](https://www1.nyc.gov/home/terms-of-use.page)。

## <a name="columns"></a>列

| 名称                 | 数据类型 | 唯一     | 值（示例）                         | 说明                                                                                                                                                                                                                                          |
|----------------------|-----------|------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | 字符串    | 264        | 74 42                                   | 未使用出租车计价器的 DOLocationID TLC 出租车区域。                                                                                                                                                                                    |
| dropoffLatitude      | Double    | 109,721    | 40.7743034362793 40.77431869506836      | 从 2016 年 7 月起弃用                                                                                                                                                                                                                       |
| dropoffLongitude     | Double    | 75,502     | -73.95272827148438 -73.95274353027344   | 从 2016 年 7 月起弃用                                                                                                                                                                                                                       |
| extra                | Double    | 202        | 0.5 1.0                                 | 其他杂费和附加费。 目前，这仅包括 0.50 美元和 1 美元的高峰时段费和跨夜费用。                                                                                                                                 |
| fareAmount           | Double    | 10,367     | 6.0 5.5                                 | 计价器计算的时间和距离费用。                                                                                                                                                                                                  |
| improvementSurcharge | 字符串    | 92         | 0.3 0                                   | 已针对路边招呼行程的起步价征收 0.30 美元的改进附加费。 自 2015 年起开始征收改进附加费。                                                                                                                         |
| lpepDropoffDatetime  | timestamp | 58,100,713 | 2016-05-22 00:00:00 2016-05-09 00:00:00 | 未使用计价器的日期和时间。                                                                                                                                                                                                     |
| lpepPickupDatetime   | timestamp | 58,157,349 | 2013-10-22 12:40:36 2014-08-09 15:54:25 | 采用了计价器的日期和时间。                                                                                                                                                                                                        |
| mtaTax               | Double    | 34         | 0.5 -0.5                                | 根据使用的计量费率自动触发的 0.50 美元 MTA 税。                                                                                                                                                                      |
| passengerCount       | int       | 10         | 1 2                                     | 车辆中的乘客人数。 这是驾驶员输入的值。                                                                                                                                                                             |
| paymentType          | int       | 5          | 2 1                                     | 表示乘客如何支付行程费用的数字代码。 1 = 信用卡 2 = 现金 3 = 免费 4 = 争议 5 = 未知 6 = 失效行程                                                                                                              |
| pickupLatitude       | Double    | 95,110     | 40.721351623535156 40.721336364746094   | 从 2016 年 7 月起弃用                                                                                                                                                                                                                       |
| pickupLongitude      | Double    | 55,722     | -73.84429931640625 -73.84429168701172   | 从 2016 年 7 月起弃用                                                                                                                                                                                                                       |
| puLocationId         | 字符串    | 264        | 74 41                                   | 使用了出租车计价器的 TLC 出租车区域。                                                                                                                                                                                                    |
| puMonth              | int       | 12         | 3 5                                     |                                                                                                                                                                                                                                                      |
| puYear               | int       | 14         | 2015 2016                               |                                                                                                                                                                                                                                                      |
| rateCodeID           | int       | 7          | 1 5                                     | 行程结束时实行的最终费率代码。 1= 标准费率 2= JFK 3= Newark 4= Nassau 或 Westchester 5= 议价票价 6= 拼车                                                                                                    |
| storeAndFwdFlag      | 字符串    | 2          | N Y                                     | 此标志指示是否先将行程记录保存在车辆内存中，然后再发送到供应商（也称为“存储并转发”），因为车辆未连接到服务器。 Y = 存储和转发行程 N = 不存储和转发行程 |
| tipAmount            | Double    | 6,206      | 1.0 2.0                                 | 小费金额 - 此字段自动填充信用卡小费。 不包括现金小费。                                                                                                                                                 |
| tollsAmount          | Double    | 2,150      | 5.54 5.76                               | 行程中支付的所有通行费总额。                                                                                                                                                                                                              |
| totalAmount          | Double    | 20,188     | 7.8 6.8                                 | 向乘客收取的总金额。 不含现金小费。                                                                                                                                                                                  |
| tripDistance         | Double    | 7,060      | 0.9 1.0                                 | 出租车计价器报告的所经过的行程距离（以英里为单位）。                                                                                                                                                                                        |
| tripType             | int       | 3          | 1 2                                     | 指示行程是在路边招呼的还是根据使用的计量费率自动分配的调度的代码，驾驶员可以更改此代码。 1= 接头招呼 2= 派单                                                      |
| vendorID             | int       | 2          | 2 1                                     | 指示提供记录的 LPEP 提供商的代码。 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                 |

## <a name="preview"></a>预览

| vendorID | lpepPickupDatetime     | lpepDropoffDatetime    | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeID | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | tripType | puYear | puMonth |
|----------|------------------------|------------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|----------|--------|---------|
| 2        | 6/24/2081 5:40:37 下午   | 6/24/2081 6:42:47 下午   | 1              | 16.95        | 93           | 117          | 1          | N               | 1           | 52         | 1     | 0.5    | 0.3                  | 0         | 2.16        | 55.96       | 1        | 2081   | 6       |
| 2        | 11/28/2030 12:19:29 上午 | 11/28/2030 12:25:37 上午 | 1              | 1.08         | 42           | 247          | 1          | N               | 2           | 6.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 7.3         | 1        | 2030   | 11      |
| 2        | 11/28/2030 12:14:50 上午 | 11/28/2030 12:14:54 上午 | 1              | 0.03         | 42           | 42           | 5          | N               | 2           | 5          | 0     | 0      | 0                    | 0         | 0           | 5           | 2        | 2030   | 11      |
| 2        | 11/14/2020 11:38:07 上午 | 11/14/2020 11:42:22 上午 | 1              | 0.63         | 129          | 129          | 1          | N               | 2           | 4.5        | 1     | 0.5    | 0.3                  | 0         | 0           | 6.3         | 1        | 2020   | 11      |
| 2        | 11/14/2020 9:55:36 上午  | 11/14/2020 10:04:54 上午 | 1              | 3.8          | 82           | 138          | 1          | N               | 2           | 12.5       | 1     | 0.5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2020   | 11      |
| 2        | 8/26/2019 4:18:37 下午   | 8/26/2019 4:19:35 下午   | 1              | 0            | 264          | 264          | 1          | N               | 2           | 1          | 0     | 0.5    | 0.3                  | 0         | 0           | 1.8         | 1        | 2019   | 8       |
| 2        | 7/1/2019 8:28:33 上午    | 7/1/2019 8:32:33 上午    | 1              | 0.71         | 7            | 7            | 1          | N               | 1           | 5          | 0     | 0.5    | 0.3                  | 1.74      | 0           | 7.54        | 1        | 2019   | 7       |
| 2        | 7/1/2019 12:04:53 上午   | 7/1/2019 12:21:56 上午   | 1              | 2.71         | 223          | 145          | 1          | N               | 2           | 13         | 0.5   | 0.5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2019   | 7       |
| 2        | 7/1/2019 12:04:11 上午   | 7/1/2019 12:21:15 上午   | 1              | 3.14         | 166          | 142          | 1          | N               | 2           | 14.5       | 0.5   | 0.5    | 0.3                  | 0         | 0           | 18.55       | 1        | 2019   | 7       |
| 2        | 7/1/2019 12:03:37 上午   | 7/1/2019 12:09:27 上午   | 1              | 0.78         | 74           | 74           | 1          | N               | 1           | 6          | 0.5   | 0.5    | 0.3                  | 1.46      | 0           | 8.76        | 1        | 2019   | 7       |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
folder_name = "green"

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
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
blob_relative_path = "green"
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
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))

# Display data statistic information
display(nyc_tlc_df, summary = True)
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "green"
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