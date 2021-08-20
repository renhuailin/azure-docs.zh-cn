---
title: COVID 跟踪项目
titleSuffix: Azure Open Datasets
description: 了解如何使用 Azure 开放数据集中的“新冠肺炎跟踪项目”数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 55814a6b1b78673c20447d6129f609058d5c794f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453695"
---
# <a name="covid-tracking-project"></a>新冠肺炎跟踪项目

新冠肺炎跟踪项目数据集提供了美国各州和各区域有关测试、确诊病例、住院数和患者结果的最新数字。

有关此数据集的详细信息，请参阅[项目 GitHub 存储库](https://github.com/COVID19Tracking/covid-tracking-data)。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>数据集

修改后的数据集版本现提供 CSV、JSON、JSON-Lines 和 Parquet 格式。
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

所有修改版本都添加了 ISO 3166 细分代码和加载时间，还使用了带下划线分隔符、采用小写字母格式的列名称。

原始数据：“https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/latest/daily.json”

已修改的数据和原始数据的历史版本： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/

https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/

## <a name="data-volume"></a>数据量
所有数据集都是每日更新。 截至 2020 年 5 月 13 日，它们包含 4,100 行（CSV 574 KB、JSON 1.8 MB、JSONL 1.8 MB、Parquet 334 KB）。

## <a name="data-source"></a>数据源

此数据最初由 The Atlantic 的新冠肺炎跟踪项目发布。 原始数据是通过 [states_daily_4p_et.csv 文件](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/data/states_daily_4pm_et.csv)从新冠肺炎跟踪 GitHub 存储库引入的。 有关此数据集的详细信息（包括其在新冠肺炎跟踪项目 API 中的来源），请参阅[项目 GitHub 存储库](https://github.com/COVID19Tracking/covid-tracking-data)。

## <a name="data-quality"></a>数据质量
新冠肺炎跟踪项目会对各州的数据质量进行评分，并提供有关其数据质量评估结果的进一步信息。 有关详细信息，请参阅[新冠肺炎跟踪项目数据页](https://covidtracking.com/data)。 GitHub 存储库中的数据可能比 API 晚一个小时；必须使用 API 才能访问最新数据。

## <a name="license-and-use-rights-attribution"></a>许可和使用权归属

此数据根据 [Apache License 2.0](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/LICENSE) 的条款和条件授权。

无论何时使用此数据都必须保留所有版权、专利、商标和归属声明。

## <a name="contact"></a>联系人

如有关于新冠肺炎数据湖中此数据集或其他数据集的任何问题或反馈，请联系 askcovid19dl@microsoft.com。

## <a name="columns"></a>列

| 名称                        | 数据类型 | 唯一 | 值（示例）                                                                   | 说明                                                                                                                 |
|-----------------------------|-----------|--------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| date                        | date      | 420    | 2020-11-10 2021-01-30                                                             | 每日总计的收集日期。                                                                             |
| date_checked                | string    | 9,487  | 2020-12-01T00:00:00Z 2020-09-01T00:00:00Z                                         | 已放弃                                                                                                                  |
| death                       | smallint  | 7,327  | 2 5                                                                               | 截至目前，因新冠肺炎死亡的总人数。                                                        |
| death_increase              | smallint  | 429    | 1 2                                                                               | 已放弃                                                                                                                  |
| fips                        | smallint  | 56     | 26 55                                                                             | 国家/地区人口普查 FIPS 代码。                                                                                             |
| fips_code                   | string    | 60     | 53 25                                                                             | 国家/地区人口普查 FIPS 代码。                                                                                             |
| hash                        | 字符串    | 20,780 | 63df8cccd23a5476bab2d8111b138e4c9becd35e c606cd6990f16086b5382e12d84f6206172d493d | 该记录的哈希代码                                                                                                      |
| hospitalized                | int       | 7,641  | 89995 4                                                                           | 已放弃                                                                                                                  |
| hospitalized_cumulative     | int       | 7,641  | 89995 4                                                                           | 截至目前，因新冠肺炎到医院就诊的总人数，包括已康复或死亡的人数。 |
| hospitalized_currently      | smallint  | 3,886  | 8 13                                                                              | 当天因新冠肺炎入院的人数。                                                                      |
| hospitalized_increase       | smallint  | 615    | 1 2                                                                               | 已放弃                                                                                                                  |
| in_icu_cumulative           | smallint  | 2,295  | 990 220                                                                           | 截至目前，因新冠肺炎而入住重症监护室的总人数，包括已康复或死亡的人数。      |
| in_icu_currently            | smallint  | 1,643  | 2 8                                                                               | 当天在重症监护室接受新冠肺炎治疗的总人数。                                                                 |
| iso_country                 | 字符串    | 1      | 美国                                                                                | ISO 3166 国家或地区代码                                                                                             |
| iso_subdivision             | string    | 57     | US-UM US-WA                                                                       | ISO 3166 细分代码                                                                                                   |
| last_update_et              | timestamp | 9,487  | 2020-12-01 00:00:00 2020-09-01 00:00:00                                           | 上次数据更新时间                                                                                        |
| load_time                   | timestamp | 1      | 2021-04-26 00:06:49.883000                                                        | 数据从源加载到 Azure 的日期和时间                                                                  |
| 消极                    | int       | 10,864 | 305972 2140                                                                       | 截至目前，新冠肺炎检测结果呈阴性的总人数。                                                        |
| negative_increase           | int       | 7,328  | 6 17                                                                              | 已放弃                                                                                                                  |
| on_ventilator_cumulative    | smallint  | 677    | 411 412                                                                           | 截至目前，使用呼吸机治疗新冠肺炎的总人数，包括之后康复或死亡的人数。    |
| on_ventilator_currently     | smallint  | 837    | 4 10                                                                              | 当天使用呼吸机治疗新冠肺炎的人数。                                                               |
| 挂起                     | smallint  | 944    | 2 17                                                                              | 结果尚未确定的检测数。                                                                    |
| pos_neg                     | int       | 18,282 | 2140 2                                                                            | 已放弃                                                                                                                  |
| 积极                    | int       | 16,837 | 2 1                                                                               | 截至目前，新冠肺炎检测结果呈阳性的总人数。                                                        |
| positive_increase           | smallint  | 4,754  | 1 2                                                                               | 已放弃                                                                                                                  |
| recovered                   | int       | 8,286  | 29 19                                                                             | 截至目前，新冠肺炎患者的总治愈人数。                                                             |
| state                       | string    | 56     | MI PA                                                                             | 国家/地区双字母代码。                                                                                              |
| total                       | int       | 18,283 | 2140 2                                                                            | 已放弃                                                                                                                  |
| total_test_results          | int       | 18,648 | 2140 3                                                                            | 国家/地区提供的全部检测结果                                                                                    |
| total_test_results_increase | int       | 13,463 | 1 2                                                                               | 已放弃                                                                                                                  |

## <a name="preview"></a>预览

| date       | state | 积极 | hospitalized_currently | hospitalized_cumulative | on_ventilator_currently | data_quality_grade | last_update_et        | hash                                     | date_checked          | death | hospitalized | total   | total_test_results | pos_neg | fips | death_increase | hospitalized_increase | negative_increase | positive_increase | total_test_results_increase | fips_code | iso_subdivision | load_time             | iso_country | 消极 | in_icu_cumulative | on_ventilator_cumulative | recovered | in_icu_currently |
|------------|-------|----------|------------------------|-------------------------|-------------------------|--------------------|-----------------------|------------------------------------------|-----------------------|-------|--------------|---------|--------------------|---------|------|----------------|-----------------------|-------------------|-------------------|-----------------------------|-----------|-----------------|-----------------------|-------------|----------|-------------------|--------------------------|-----------|------------------|
| 2021-03-07 | AK    | 56886    | 33                     | 1293                    | 2                       | null               | 3/5/2021 3:59:00 AM   | dc4bccd4bb885349d7e94d6fed058e285d4be164 | 3/5/2021 3:59:00 AM   | 305   | 1293         | 56886   | 1731628            | 56886   | 2    | 0              | 0                     | 0                 | 0                 | 0                           | 2         | US-AK           | 4/26/2021 12:06:49 AM | 美国          |          |                   |                          |           |                  |
| 2021-03-07 | AL    | 499819   | 494                    | 45976                   |                         | Null               | 3/7/2021 11:00:00 AM  | 997207b430824ea40b8eb8506c19a93e07bc972e | 3/7/2021 11:00:00 AM  | 10148 | 45976        | 2431530 | 2323788            | 2431530 | 1    | -1             | 0                     | 2087              | 408               | 2347                        | 1         | US-AL           | 4/26/2021 12:06:49 AM | 美国          | 1931711  | 2676              | 1515                     | 295690    |                  |
| 2021-03-07 | AR    | 324818   | 335                    | 14926                   | 65                      | Null               | 3/7/2021 12:00:00 AM  | 50921aeefba3e30d31623aa495b47fb2ecc72fae | 3/7/2021 12:00:00 AM  | 5319  | 14926        | 2805534 | 2736442            | 2805534 | 5    | 22             | 11                    | 3267              | 165               | 3380                        | 5         | US-AR           | 4/26/2021 12:06:49 AM | 美国          | 2480716  |                   | 1533                     | 315517    | 141              |
| 2021-03-07 | AS    | 0        |                        |                         |                         | Null               | 12/1/2020 12:00:00 AM | 96d23f888c995b9a7f3b4b864de6414f45c728ff | 12/1/2020 12:00:00 AM | 0     |              | 2140    | 2140               | 2140    | 60   | 0              | 0                     | 0                 | 0                 | 0                           | 60        | US-AS           | 4/26/2021 12:06:49 AM | 美国          | 2140     |                   |                          |           |                  |
| 2021-03-07 | AZ    | 826454   | 963                    | 57907                   | 143                     | Null               | 3/7/2021 12:00:00 AM  | 0437a7a96f4471666f775e63e86923eb5cbd8cdf | 3/7/2021 12:00:00 AM  | 16328 | 57907        | 3899464 | 7908105            | 3899464 | 4    | 5              | 44                    | 13678             | 1335              | 45110                       | 4         | US-AZ           | 4/26/2021 12:06:49 AM | 美国          | 3073010  |                   |                          |           | 273              |
| 2021-03-07 | CA    | 3501394  | 4291                   |                         |                         | Null               | 3/7/2021 2:59:00 AM   | 63c5c0fd2daef2fb65150e9db486de98ed3f7b72 | 3/7/2021 2:59:00 AM   |       |              | 3501394 | 49646014           | 3501394 | 6    | 258            | 0                     | 0                 | 3816              | 133186                      | 6         | US-CA           | 4/26/2021 12:06:49 AM | 美国          |          |                   |                          |           | 1159             |
| 2021-03-07 | CO    | 436602   | 326                    | 23904                   |                         | Null               | 3/7/2021 1:59:00 AM   | 444746cda3a596f183f3fa3269c8cab68704e819 | 3/7/2021 1:59:00 AM   | 5989  | 23904        | 2636060 | 6415123            | 2636060 | 8    | 3              | 18                    | 0                 | 840               | 38163                       | 8         | US-CO           | 4/26/2021 12:06:49 AM | 美国          | 2199458  |                   |                          |           |                  |
| 2021-03-07 | CT    | 285330   | 428                    | 12257                   |                         | Null               | 3/4/2021 11:59:00 PM  | bcc0f7bc8c2bf77eec31b25f8b59d510f679d3e7 | 3/4/2021 11:59:00 PM  | 7704  | 12257        | 285330  | 6520366            | 285330  | 9    | 0              | 0                     | 0                 | 0                 | 0                           | 9         | US-CT           | 4/26/2021 12:06:49 AM | 美国          |          |                   |                          |           |                  |
| 2021-03-07 | DC    | 41419    | 150                    |                         | 16                      | Null               | 3/6/2021 12:00:00 AM  | a3aa0d623d538807fb9577ad64354f48cf728cc8 | 3/6/2021 12:00:00 AM  | 1030  |              | 41419   | 1261363            | 41419   | 11   | 0              | 0                     | 0                 | 146               | 5726                        | 11        | US-DC           | 4/26/2021 12:06:49 AM | 美国          |          |                   |                          | 29570     | 38               |
| 2021-03-07 | DE    | 88354    | 104                    |                         |                         | Null               | 3/6/2021 6:00:00 PM   | 059d870e689d5cc19c35f5eb398214d7d9856373 | 3/6/2021 6:00:00 PM   | 1473  |              | 633424  | 1431942            | 633424  | 10   | 9              | 0                     | 917               | 215               | 5867                        | 10        | US-DE           | 4/26/2021 12:06:49 AM | 美国          | 545070   |                   |                          |           | 13               |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Azure Blob 存储中存放的不同数据集文件格式的 URL：

CSV： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv

JSON： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json

JSONL： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl

Parquet： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

使用 Pandas 中的内置功能从 http URL 下载数据集文件。 Pandas 具有各种文件格式的读取器：

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet ")
df.head(10)

df.dtypes

df.groupby('state').first().filter(['date','positive', 'death'])

df.groupby(df.state).agg({'state': 'count','positive_increase': 'sum','death_increase': 'sum'})

df_NY=df[df['state'] == 'NY']
df_NY.plot(kind='line',x='date',y="positive",grid=True)
df_NY.plot(kind='line',x='date',y="positive_increase",grid=True)
df_NY.plot(kind='line',x='date',y="death",grid=True)
df_NY.plot(kind='line',x='date',y="death_increase",grid=True)

df_US=df.groupby(df.date).agg({'positive': 'sum','positive_increase': 'sum','death':'sum','death_increase': 'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="positive",grid=True)
df_US.plot(kind='line',x='date',y="positive_increase",grid=True)
df_US.plot(kind='line',x='date',y="death",grid=True)
df_US.plot(kind='line',x='date',y="death_increase",grid=True)



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
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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
