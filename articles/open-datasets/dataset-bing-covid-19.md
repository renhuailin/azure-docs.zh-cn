---
title: 必应新冠肺炎
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“必应新冠肺炎”数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7d797e169a0f1fbeeceaf377e731a051112c68e3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452292"
---
# <a name="bing-covid-19"></a>必应新冠肺炎

必应新冠肺炎数据包含来自各地区的已确诊病例、死亡病例和治愈病例，该数据每日更新。
该数据反映在[必应新冠肺炎跟踪器](https://bing.com/covid)中。

必应会从多个受信任的可靠来源收集数据，包括[世界卫生组织 (WHO)](https://www.who.int/emergencies/diseases/novel-coronavirus-2019)、[疾病控制和预防中心 (CDC)](https://www.cdc.gov/coronavirus/2019-ncov/index.html)、全国和各州公共健康部门、[BNO News](https://bnonews.com/index.php/2020/04/the-latest-coronavirus-cases/)、[24/7 Wall St.](https://247wallst.com/) 和[维基百科](https://en.wikipedia.org/wiki/2019%E2%80%9320_coronavirus_pandemic)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>数据集
修改后的数据集现提供 CSV、JSON、JSON-Lines 和 Parquet 格式。

- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

已修改的所有数据集都添加了 ISO 3166 细分代码和加载时间，还使用了带下划线分隔符、采用小写字母格式的列名称。

原始数据： https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/bing_covid-19_data/latest/Bing-COVID19-Data.csv

已修改的数据和原始数据的历史版本： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/

## <a name="data-volume"></a>数据量
所有数据集都是每日更新。 截至 2020 年 5 月 11 日，它们包含 125,576 行（CSV 16.1 MB、JSON 40.0 MB、JSONL 39.6 MB、Parquet 1.1 MB）。

## <a name="license-and-use-rights-attribution"></a>许可和使用权归属
必须严格遵守[条款和条件](https://github.com/microsoft/Bing-COVID-19-Data/blob/master/LICENSE.txt)将此数据用于教育和学术目的，例如医疗研究、政府机构和学术机构。

出版物中使用或引述的数据应包含归属于“必应新冠肺炎跟踪器”这一表述，且附上指向 www.bing.com/covid 的链接。

## <a name="contact"></a>联系人
如果对新冠肺炎数据湖中的此数据集或其他数据集有任何疑问或反馈，请联系 askcovid19dl@microsoft.com。

## <a name="columns"></a>列

| 名称             | 数据类型 | 唯一    | 值（示例）                    | 说明                                                          |
|------------------|-----------|-----------|------------------------------------|----------------------------------------------------------------------|
| admin_region_1   | string    | 864       | Texas Georgia                      | country_region 中的区域                                         |
| admin_region_2   | string    | 3,143     | Washington County Jefferson County | admin_region_1 中的区域                                         |
| confirmed        | int       | 120,692   | 1 2                                | 该区域的确诊病例数                                  |
| confirmed_change | int       | 12,120    | 1 2                                | 前一天确诊病例数的变化                 |
| country_region   | string    | 237       | United States India                | 国家/地区                                                       |
| deaths           | int       | 20,616    | 1 2                                | 该区域的死亡病例数                                      |
| deaths_change    | smallint  | 1,981     | 1 2                                | 前一天死亡病例数的变化                          |
| id               | int       | 1,783,534 | 742546 69019298                    | 唯一标识符                                                    |
| iso_subdivision  | string    | 484       | US-TX US-GA                        | 两部分组成的 ISO 细分代码                                        |
| iso2             | string    | 226       | US IN                              | 双字母国家/地区代码标识符                                     |
| iso3             | string    | 226       | USA IND                            | 三字母国家/地区代码标识符                                     |
| latitude         | Double    | 5,675     | 42.28708 19.59852                  | 区域质心的纬度                               |
| load_time        | timestamp | 1         | 2021-04-26 00:06:34.719000         | 从 GitHub 上的必应源中加载文件的日期和时间 |
| longitude        | Double    | 5,693     | -2.5396 -155.5186                  | 区域质心的经度                              |
| recovered        | int       | 73,287    | 1 2                                | 该区域的治愈病例数                                       |
| recovered_change | int       | 10,441    | 1 2                                | 前一天治愈病例数的变化                 |
| 已更新          | date      | 457       | 2021-04-23 2021-04-22              | 记录的截至日期                                        |

## <a name="preview"></a>预览

| id     | 已更新    | confirmed | deaths | iso2 | iso3 | country_region | admin_region_1 | iso_subdivision | admin_region_2 | load_time             | confirmed_change | deaths_change |
|--------|------------|-----------|--------|------|------|----------------|----------------|-----------------|----------------|-----------------------|------------------|---------------|
| 338995 | 2020-01-21 | 262       | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM |                  |               |
| 338996 | 2020-01-22 | 313       | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 51               | 0             |
| 338997 | 2020-01-23 | 578       | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 265              | 0             |
| 338998 | 2020-01-24 | 841       | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 263              | 0             |
| 338999 | 2020-01-25 | 1320      | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 479              | 0             |
| 339000 | 2020-01-26 | 2014      | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 694              | 0             |
| 339001 | 2020-01-27 | 2798      | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 784              | 0             |
| 339002 | 2020-01-28 | 4593      | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 1795             | 0             |
| 339003 | 2020-01-29 | 6065      | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 1472             | 0             |
| 339004 | 2020-01-30 | 7818      | 0      | Null | Null | 全球      | Null           | Null            | Null           | 4/26/2021 12:06:34 AM | 1753             | 0             |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data)。

#### <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-bing-covid-19-dataset"></a>此笔记本记录了用于访问[必应新冠肺炎数据集](https://github.com/microsoft/Bing-COVID-19-Data)的 URL 和示例代码

使用以下 URL 获取存放在 Azure Blob 存储中的特定文件格式：

CSV： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv

JSON： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json

JSONL： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl

Parquet： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

使用 Pandas 中的内置功能从 http URL 下载数据集文件。 Pandas 具有各种文件格式的读取器：

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet")
df.head(10)
```

检查各字段的数据类型，并验证更新的列是否采用日期/时间格式

```python
df.dtypes
```

现在我们查看全球数据，并绘制一些简单图表将数据可视化

```python
df_Worldwide=df[df['country_region']=='Worldwide']
```

```python
df_Worldwide_pivot=df_Worldwide.pivot_table(df_Worldwide, index=['country_region','updated'])

df_Worldwide_pivot
```

```python
df_Worldwide.plot(kind='line',x='updated',y="confirmed",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="confirmed_change",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths_change",grid=True)
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

没有适用于此平台/包组合的示例。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data)。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
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

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data)。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
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
