---
title: 欧洲疾病预防与控制中心 (ECDC) 新冠肺炎病例
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“欧洲疾病预防与控制中心 (ECDC) 新冠肺炎病例”数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 31868ee27e531e70df4c89944f0baf888527a190
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447593"
---
# <a name="european-centre-for-disease-prevention-and-control-ecdc-covid-19-cases"></a>欧洲疾病预防与控制中心 (ECDC) 新冠肺炎病例

来自欧洲疾病预防控制中心 (ECDC) 的关于全球新冠肺炎病例地理分布情况的[最新可用公共数据](https://www.ecdc.europa.eu/en/publications-data/download-todays-data-geographic-distribution-covid-19-cases-worldwide)。 每行/每个条目包含各国家/地区每天报告的新增病例数。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>数据集

修改后的数据集版本现提供 CSV、JSON、JSON-Lines 和 Parquet 格式，该数据集每日更新：
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

已修改的所有数据集都添加了 iso_country_region 代码和加载时间，还使用了带下划线分隔符、采用小写字母格式的列名称。

原始数据： https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/latest/ECDCCases.csv

已修改的数据和原始数据的历史版本： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/

## <a name="data-volume"></a>数据量
截至 2020 年 5 月 28 日，它们包含 19,876 行（CSV 1.5 MB、JSON 4.9 MB、JSONL 4.9 MB、Parquet 54.1 KB）。

## <a name="data-source"></a>数据源

原始数据每日从 [ECDC csv 文件](https://opendata.ecdc.europa.eu/covid19/casedistribution/csv)中引入。 有关此数据集（包括其来源）的详细信息，请参阅 [ECDC 数据集合页](https://www.ecdc.europa.eu/en/covid-19/data-collection)。

## <a name="data-quality"></a>数据质量
ECDC 不保证数据的准确性和时效性。 [阅读免责声明](https://www.ecdc.europa.eu/en/covid-19/data-collection)。

## <a name="license-and-use-rights-attribution"></a>许可和使用权归属

此数据根据此处的 ECDC 版权政策提供并许可使用。 对于版权属于第三方的任何文档，必须获得版权所有者的复制许可。

必须始终确认 ECDC 是此数据的原始来源。 材料的每个副本中都必须包含此类确认。

## <a name="contact"></a>联系人

如果对新冠肺炎数据湖中的此数据集或其他数据集有任何疑问或反馈，请联系 askcovid19dl@microsoft.com。

## <a name="columns"></a>列

| 名称                      | 数据类型 | 唯一 | 值（示例）            | 说明                            |
|---------------------------|-----------|--------|----------------------------|----------------------------------------|
| cases                     | smallint  | 5,515  | 1 2                        | 报告的病例数               |
| continent_exp             | string    | 6      | Europe Africa              | 大洲名称                         |
| countries_and_territories | string    | 214    | Canada Belgium             | 国家或地区名称              |
| country_territory_code    | string    | 213    | KOR ISL                    | 国家或地区的三字母代码 |
| date_rep                  | date      | 350    | 2020-12-11 2020-11-22      | 报告日期                     |
| day                       | smallint  | 31     | 14 13                      | 几月几日                           |
| deaths                    | smallint  | 1,049  | 1 2                        | 报告的死亡人数              |
| geo_id                    | string    | 214    | CA SE                      | 地域标识符                         |
| iso_country               | string    | 214    | SE US                      | ISO 3166 国家或地区代码        |
| load_date                 | timestamp | 1      | 2021-04-26 00:06:22.123000 | 数据加载到 Azure 的日期      |
| 月份                     | smallint  | 12     | 10 8                       | 月份                           |
| year                      | smallint  | 2      | 2020 2019                  | 年龄                                   |

## <a name="preview"></a>预览

| date_rep   | day | 月份 | year | cases | deaths | countries_and_territories | geo_id | country_territory_code | continent_exp | load_date             | iso_country |
|------------|-----|-------|------|-------|--------|---------------------------|--------|------------------------|---------------|-----------------------|-------------|
| 2020-12-14 | 14  | 12    | 2020 | 746   | 6      | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-13 | 13  | 12    | 2020 | 298   | 9      | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-12 | 12  | 12    | 2020 | 113   | 11     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-11 | 11  | 12    | 2020 | 63    | 10     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-10 | 10  | 12    | 2020 | 202   | 16     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-09 | 9   | 12    | 2020 | 135   | 13     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-08 | 8   | 12    | 2020 | 200   | 6      | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-07 | 7   | 12    | 2020 | 210   | 26     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-06 | 6   | 12    | 2020 | 234   | 10     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-05 | 5   | 12    | 2020 | 235   | 18     | 阿富汗               | AF     | AFG                    | 亚洲          | 4/26/2021 12:06:22 AM | AF          |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

此笔记本记录了用于访问“欧洲疾病预防与控制中心 (ECDC) 新冠肺炎病例”数据集的 URL 和示例代码。Azure Blob 存储中存放的不同数据集文件格式的 URL：¶ CSV： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv

JSON： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json

JSONL： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl

Parquet： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

使用 Pandas 中的内置功能从 http URL 下载数据集文件。 Pandas 具有各种文件格式的读取器：

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet")
df.head(10)

df.dtypes

df.groupby('countries_and_territories').first().filter(['continent_exp','cases', 'deaths','date_rep'])

df.groupby('continent_exp').agg({'countries_and_territories': 'count','cases': 'count','deaths': 'count'})

import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countries_and_territories', 'cases', 'deaths']].groupby(['countries_and_territories'
         ]).max().sort_values(by='cases',ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')

df_Worldwide=df[df['countries_and_territories']=='United_States_of_America']

df.plot(kind='line',x='date_rep',y="cases",grid=True)
df.plot(kind='line',x='date_rep',y="deaths",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="confirmed_change",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="deaths_change",grid=True)

```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

没有适用于此平台/包组合的示例。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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

## <a name="examples"></a>示例

参阅此数据集的用法示例：

- [使用 Synapse SQL 无服务器终结点分析新冠肺炎数据](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/how-azure-synapse-analytics-helps-you-analyze-covid-data-with/ba-p/1457836)
- [使用 Azure Synapse Analytics 中的 SQL 终结点对新冠肺炎数据进行线性回归分析](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/linear-regression-analysis-on-covid-data-using-sql-endpoint-in/ba-p/1468697)

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。
