---
title: 牛津 COVID-19 政府响应追踪系统
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用牛津大学 COVID-19 政府响应追踪系统数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a39c8915027cddeb168f5bb0c63f915492ece398
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444607"
---
# <a name="oxford-covid-19-government-response-tracker"></a>牛津 COVID-19 政府响应追踪系统

[牛津大学新 Covid-19 政府响应追踪系统 (OxCGRT) 数据集](https://github.com/OxCGRT/covid-policy-tracker/)包含了关于各国家/地区政府应对新冠肺炎的措施以及这些措施的执行时间的系统化信息。

这些信息可以帮助决策者和公民以一致的方式理解政府的应对措施，协助抗击疫情。 OxCGRT 系统地收集各国政府应对疫情的多种不同常用政策的相关信息，按照一个反映政府行动严厉程度的评分记录这些政策，并将这些分数聚合为一组政策指数。


[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>数据集

修改后的数据集版本现提供 CSV、JSON、JSON-Lines 和 Parquet 格式，该数据集每日更新：
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

所有修改后的版本都添加了 iso_country 代码和加载时间，还使用了带下划线分隔符、采用小写字母格式的列名称。

原始数据： https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/latest/CovidPolicyTracker.csv

已修改的数据和原始数据的历史版本： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/

## <a name="data-volume"></a>数据量

截至 2020 年 6 月 8 日，它们包含 27,919 行（CSV 4.9 MB、JSON 20.9 MB、JSONL 20.8 MB、Parquet 133.0 KB）。

## <a name="data-source"></a>数据源

此数据来自 Thomas Hale、Sam Webster、Anna Petherick、Toby Phillips 和 Beatriz Kira。 （2020 年）。 牛津大学新冠肺炎疫情政府响应追踪系统。 [布拉瓦尼克政治学院](https://www.bsg.ox.ac.uk/)。 原始数据每天从[最新的 OxCGRT csv 文件](https://github.com/OxCGRT/covid-policy-tracker/blob/master/data/OxCGRT_latest.csv)中引入。 有关此数据集（包括其收集方式）的详细信息，请参阅[政府跟踪器响应站点](https://www.bsg.ox.ac.uk/research/research-projects/covid-19-government-response-tracker)。

## <a name="data-quality"></a>数据质量
OxCGRT 不保证数据的准确性和时效性。 有关详细信息，请参阅[数据质量声明](https://github.com/OxCGRT/covid-policy-tracker#data-quality)。

## <a name="license-and-use-rights-attribution"></a>许可和使用权归属

此数据根据[知识共享署名 4.0 协议国际版](https://github.com/OxCGRT/covid-policy-tracker/blob/master/LICENSE.txt)获得许可。

来源：Thomas Hale、Sam Webster、Anna Petherick、Toby Phillips 和 Beatriz Kira。 （2020 年）。 牛津大学新冠肺炎疫情政府响应追踪系统。 布拉瓦尼克政治学院。

## <a name="contact"></a>联系人

如有关于 COVID-19 数据湖中此数据集或其他数据集的任何问题或反馈，请联系 askcovid19dl@microsoft.com。

## <a name="columns"></a>列

| 名称                                  | 数据类型 | 唯一 | 值（示例）            | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------|-----------|--------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| c1_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志。 0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c1_school_closing                     | Double    | 5      | 3.0 2.0                    | 记录是否关闭中小学与大学。 0 - 不采取措施；1 - 建议关闭；2 - 要求关闭（仅限某些级别或类别的学校，如仅限高中或仅限公立学校）；3 - 要求关闭所有级别的学校；空白 - 无数据                                                                                                                                                                                                                                                                                                 |
| c2_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志。 0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| c2_workplace_closing                  | Double    | 5      | 2.0 1.0                    | 记录是否关闭工作场所。 0 - 不采取措施；1 - 建议关闭工作场所（或建议在家办公）；2 - 要求某些部门或类别的员工暂停工作（或在家办公）；3 - 除必要工作场所（如超市、医院）外，所有工作场所均须关闭（或在家办公）；空白 - 无数据                                                                                                                                                                                                                          |
| c3_cancel_public_events               | Double    | 4      | 2.0 1.0                    | 记录是否取消公共活动。 0 - 不采取措施；1 - 建议取消；2 - 要求取消；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                    |
| c3_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志。 0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c4_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志：0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c4_restrictions_on_gatherings         | Double    | 6      | 4.0 3.0                    | 记录对私人聚会的限制。 0 - 不限制；1 - 限制大型聚会（1000 人以上）；2 - 限制 101-1000 人之间的聚会；3 - 限制 11-100 人之间的聚会；4 - 限制 10 人或以下的聚会；空白 - 无数据                                                                                                                                                                                                                 |
| c5_close_public_transport             | Double    | 4      | 1.0 2.0                    | 记录是否停运公共交通。0 - 不采取措施；1 - 建议停运（或大幅减少提供的数量/路线/交通工具）；2 - 要求停运（或禁止大多数公民使用）；空白 - 无数据                                                                                                                                                                                                                                                                                                          |
| c5_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志：0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志：0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_stay_at_home_requirements          | Double    | 5      | 1.0 2.0                    | 记录“就地避难”或者居家隔离的命令。0 - 不采取措施；1 - 不建议外出；2 - 要求除日常锻炼、超市购物和“必要”出行之外，不得外出；3 - 要求在极少例外的情况下（如允许每周外出一次，或每次外出仅限一人，等等）不得外出；空白 - 无数据                                                                                                                                                |
| c7_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志：0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c7_restrictions_on_internal_movement  | Double    | 4      | 2.0 1.0                    | 记录对城市/地区之间内部流动的限制：0 - 不采取措施；1 - 建议不要在不同地区/城市之间旅行；2 - 限制地区内部流动；空白 - 无数据                                                                                                                                                                                                                                                                                                                                      |
| c8_international_travel_controls      | Double    | 6      | 3.0 4.0                    | 记录对国际旅行的限制。 注意：此处记录的政策仅针对外国旅客，而非本国公民。0 - 无限制；1 - 对入境人员进行筛查；2 - 对来自部分或所有地区的入境人员进行隔离；3 - 禁止来自某些地区的人员入境；4 - 禁止所有地区的人员入境或全面关闭边境；空白 - 无数据                                                                                                                                                                                                                           |
| confirmedcases                        | smallint  | 18,238 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| confirmeddeaths                       | smallint  | 14,906 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countrycode                           | string    | 186    | USA BRA                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countryname                           | string    | 186    | United States Brazil       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| date                                  | date      | 478    | 2020-08-25 2021-03-30      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e1_flag                               | boolean   | 3      | True                       | 二进制标志，表示部门范围。0 - 仅限正式部门员工；1 - 包含非正式部门员工；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                          |
| e1_income_support                     | Double    | 4      | 1.0 2.0                    | 记录政府是否向失业或无法工作的人提供现金资助。 注意：仅在与工资/薪酬明确相关的情况下才包括对公司的补偿。0 - 无收入支持；1 - 政府补偿低于工资损失的 50%（或者如果是固定金额，则低于工资中位数的 50%）；2 - 政府补偿等于或高于工资损失的 50%（或者如果是固定金额，则高于工资中位数的 50%）；空白 - 无数据                                                                        |
| e2_debt/contract_relief               | Double    | 4      | 1.0 2.0                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e3_fiscal_measures                    | Double    | 819    | -0.01 3.0                  | 宣布的经济刺激支出。注意：仅记录在先前宣布的支出基础上追加的金额。记录的财政刺激金额以美元计价，包括 E4、H4 或 H5 之外的任何支出或减税。0 - 当天无支出；空白 - 无数据                                                                                                                                                                                                                                                               |
| e4_international_support              | Double    | 113    | -0.02 5000000.0            | 宣布向其他国家/地区提供与 Covid-19 相关的援助。注意：仅记录在先前宣布的支出基础上追加的金额。记录的金额以美元计价。0 - 当天无支出；空白 - 无数据                                                                                                                                                                                                                                                                                                                 |
| h1_flag                               | boolean   | 3      | True                       | 地理范围的二进制标志：0 - 定向；1 - 通用；空白 - 无数据                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| h1_public_information_campaigns       | Double    | 4      | 2.0 1.0                    | 记录是否存在公众宣传计划。0 - 无 Covid-19 公众宣传计划；1 - 政府官员敦促对 Covid-19 保持警惕；2 - 联合公众宣传计划（如借助传统媒体和社交媒体）；空白 - 无数据                                                                                                                                                                                                                                                                                         |
| h2_testing_policy                     | Double    | 5      | 2.0 1.0                    | 记录有关哪些人员应接受检测的政府政策。注意：此项记录的是针对当前感染（PCR 检测）的检测政策，而非免疫检测（抗体检测）的政策。0 - 无检测政策；1 - 仅对 (a) 有症状且 (b) 符合特定标准（如重要岗位工作人员、住院患者、与已知病例接触、从海外归来）的人员进行检测；2 - 对任何出现 Covid-19 症状的人员进行检测；3 - 开放公众检测（向无症状人群提供“免下车”检测）；空白 - 无数据 |
| h3_contact_tracing                    | Double    | 4      | 2.0 1.0                    | 记录在确诊后追踪接触者的政府政策。注意：我们正在寻找相关政策，希望能够识别所有可能接触新冠肺炎患者的人群；自愿安装的蓝牙应用不太可能实现此目标。0 - 不追踪接触者；1 - 在有限的情况下追踪接触者，并非针对所有病例都进行追踪；2 - 全面追踪接触者，针对所有确诊病例进行追踪                                                                                                                                                          |
| h4_emergency_investment_in_healthcare | Double    | 462    | 35.0 562.0                 | 宣布的医疗保健系统方面（例如医院，口罩等）的短期支出。注意：仅记录在先前宣布的支出基础上追加的金额。记录的金额以美元计价。0 - 当天无新支出；空白 - 无数据                                                                                                                                                                                                                                                                                                         |
| h5_investment_in_vaccines             | Double    | 133    | 1.0 191.0                  | 宣布的新冠肺炎疫苗研发公共支出。注意：仅记录之前宣布的支出以外的金额；以美元记录货币价值；0 - 当天无新支出；空白 - 无数据                                                                                                                                                                                                                                                                                                                            |
| iso_country                           | string    | 186    | US BR                      | ISO 3166 国家或地区代码                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| load_date                             | timestamp | 1      | 2021-04-26 00:06:25.157000 | 日期和时间数据是从外部源加载的                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| stringencyindex                       | Double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| stringencyindexfordisplay             | Double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="preview"></a>预览

| countryname | countrycode | date       | c1_school_closing | c2_workplace_closing | c3_cancel_public_events | c4_restrictions_on_gatherings | c5_close_public_transport | c6_stay_at_home_requirements | c7_restrictions_on_internal_movement | c8_international_travel_controls | e1_income_support | e2_debt/contract_relief | e3_fiscal_measures | e4_international_support | h1_public_information_campaigns | h2_testing_policy | h3_contact_tracing | h4_emergency_investment_in_healthcare | h5_investment_in_vaccines | m1_wildcard | stringencyindex | stringencyindexfordisplay | iso_country | load_date             |
|-------------|-------------|------------|-------------------|----------------------|-------------------------|-------------------------------|---------------------------|------------------------------|--------------------------------------|----------------------------------|-------------------|-------------------------|--------------------|--------------------------|---------------------------------|-------------------|--------------------|---------------------------------------|---------------------------|-------------|-----------------|---------------------------|-------------|-----------------------|
| 阿鲁巴       | ABW         | 2020-01-01 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-02 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-03 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-04 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-05 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-06 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-07 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-08 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-09 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 阿鲁巴       | ABW         | 2020-01-10 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | Null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker)。

## <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-oxford-covid-19-government-response-tracker-oxcgrt-dataset"></a>此笔记本记录了用于访问牛津大学 Covid-19 政府响应追踪系统 (OxCGRT) 数据集的 URL 和示例代码

Azure Blob 存储上托管的不同文件格式的 URL：

CSV： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv 

JSON： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json

JSONL： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl 

Parquet： https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

使用 Pandas 中的内置功能从 http URL 下载数据集文件。 Pandas 具有各种文件格式的读取器：

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


首先将数据集文件加载到 Pandas 数据帧，并查看一些示例行

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet")
df.head(10)
```

检查各字段的数据类型，并验证更新的列是否采用日期/时间格式

```python
df.dtypes
```

此数据集包含多个国家/地区的数据。 验证数据适用于哪些国家/地区。

首先，我们将查看每个国家/地区的最新数据：

```python
df.groupby('countryname').first().filter(['confirmedcases ', 'confirmeddeaths','h5_investment_in_vaccines',
    'c6_stay_at_home_requirements','h4_emergency_investment_in_healthcare','c4_restrictions_on_gatherings', 'load_date'])
```

接下来，我们将执行一些聚合，以确保相关列（如 `confirmedcases` 和 `confirmeddeaths` ）与最新数据相符。 你应会看到上表中最新日期的阳性病例数和死亡人数与 `confirmedcases` 和 `confirmeddeaths` 的聚合结果匹配。


```python
df.groupby('countryname').agg({'countryname': 'count','confirmedcases': 'sum','confirmeddeaths': 'sum',
                               'h5_investment_in_vaccines': 'count', 'c6_stay_at_home_requirements':'sum'})
```

我们可以对一些国家/地区执行一些基本的可视化效果

```python
import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countryname', 'confirmedcases', 
'confirmeddeaths']].groupby(['countryname']).max().sort_values(by='confirmedcases', 
                                           ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')
```

```python
df_US = df.groupby(df.date).agg({'confirmedcases': 'sum','confirmeddeaths':'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="confirmedcases",grid=True)
df_US.plot(kind='line',x='date',y="confirmeddeaths",grid=True)

```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

没有适用于此平台/包组合的示例。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker)。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker)。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
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
