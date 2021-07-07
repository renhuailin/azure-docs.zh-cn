---
title: COVID-19 数据湖
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用 COVID-19 数据湖。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: e43ea946511efaa313204ca26bf7e05f1b3f923a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038642"
---
# <a name="covid-19-data-lake"></a>COVID-19 数据湖

COVID-19 数据湖包含各种来源的 COVID-19 相关数据集。 它涵盖了测试和患者结果跟踪数据、社交距离政策、医院容纳能力和流动性，等等。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

新冠肺炎数据湖托管在美国东部区域的 Azure Data Lake Storage 存储中。 对于每个数据集，均提供 csv、json、json-lines 和 parquet 格式的修改版本， 还引入了原始数据。

在没有 ISO 3166 细分代码的位置添加了这些代码来简化联接。 列名称用小写字母重新设置格式且带下划线分隔符。 数据集每日进行更新，同时还提供已修改文件和原始文件的历史副本。

## <a name="datasets"></a>数据集

| 数据集                                                                 | 说明                                                                                                                                                                                                                                             |
|--------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [必应 COVID-19 数据](dataset-bing-covid-19.md)                                                       | 必应新冠肺炎数据包含来自各地区的已确诊病例、死亡病例和治愈病例，该数据每日更新。                                                                                                                                                      |
| [COVID 跟踪项目](dataset-covid-tracking.md)                                                | 新冠肺炎跟踪项目数据集提供了美国各州和各区域有关测试、确诊病例、住院数和患者结果的最新数字。                                                                                     |
| [欧洲疾病预防与控制中心 (ECDC) Covid-19 病例](dataset-ecdc-covid-cases.md) | 来自欧洲疾病预防控制中心 (ECDC) 的关于全球新冠肺炎病例地理分布情况的最新可用公共数据。 每行/每个条目包含各国家/地区每天报告的新增病例数。 |
| [牛津大学 COVID-19 政府响应追踪系统](dataset-oxford-covid-government-response-tracker.md)                              | 牛津大学新冠肺炎疫情政府响应追踪系统 (OxCGRT) 数据集包含有关各国政府应对新冠肺炎的措施以及这些措施的执行时间的系统信息。                                                                                              |

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。