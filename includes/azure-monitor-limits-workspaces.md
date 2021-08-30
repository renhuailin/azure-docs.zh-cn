---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: b8ada581eabe0a2fedda08d0fbb1fa9585da0cca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801602"
---
**数据收集量和保留期** 

| 层 | 每日限制 | 数据保留 | 注释 |
|:---|:---|:---|:---|
| 当前每 GB 定价层<br>（已于 2018 年 4 月推出） | 无限制 | 30 - 730 天 | 如果数据保留期超过 31 天，则需要收取额外的费用。 详细了解 Azure Monitor 定价。 |
| 旧的免费层<br>（已于 2016 年 4 月推出） | 500 MB | 7 天 | 当工作区达到 500 MB 的每日限制时，数据引入会停止，并在第二天开始时恢复。 日期基于 UTC。 请注意，由 Azure 安全中心收集的数据不包含在这一 500 MB 的每日限制内，并在超出此限制后继续进行收集。  |
| 旧的独立（按 GB）层<br>（已于 2016 年 4 月推出） | 无限制 | 30 至 730 天 | 如果数据保留期超过 31 天，则需要收取额外的费用。 详细了解 Azure Monitor 定价。 |
| 旧的按节点 (OMS) 定价层<br>（已于 2016 年 4 月推出） | 无限制 | 30 至 730 天 | 如果数据保留期超过 31 天，则需要收取额外的费用。 详细了解 Azure Monitor 定价。 |
| 旧的标准层 | 无限制 | 30 天  | 不能调整保留期 |
| 旧的高级层 | 无限制 | 365 天  | 不能调整保留期 |

**每个订阅的工作区数。**

| 定价层    | 工作区限制 | 注释
|:---|:---|:---|
| 免费层  | 10 | 此限制不能提高。 |
| 其他所有层 | 无限制 | 你会受到资源组中的资源数以及每个订阅的资源组数的限制。 |

**Azure 门户**

| Category | 限制 | 注释 |
|:---|:---|:---|
| 日志查询返回的最大记录数 | 30,000 | 在查询中使用查询作用域、时间范围和筛选器来减少结果。 |


**数据收集器 API**

| Category | 限制 | 注释 |
|:---|:---|:---|
| 单个发布内容的最大大小 | 30 MB | 将较大的卷拆分为多个发布内容。 |
| 字段值的最大大小  | 32 KB | 超过 32 KB 的字段会被截断。 |

**查询 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 单个查询中返回的最大记录数 | 500,000 | |
| 返回的数据的最大大小 | 约 104 MB（约 100 MiB）| |
| 最长查询运行时间 | 10 分钟 | 有关详细信息，请参阅[超时](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)。  |
| 最大请求速率 | 每个 Azure AD 用户或客户端 IP 地址每 30 秒 200 个请求 | 有关详细信息，请参阅[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)。 |

**Azure Monitor 日志连接器**

| Category | 限制 | 注释 |
|:---|:---|:---|
| 最大数据大小 | ~16.7 MB (~16 MiB) | 连接器基础结构规定该限制设置为低于查询 API 限制 |
| 最大记录数 | 500,000 | |
| 最大查询超时 | 110 秒 | |
| 图表 | | 日志页面中的可视化效果与连接器使用不同的图表库，某些功能当前不可在连接器中使用。 |

**常规工作区限制**

| Category | 限制 | 注释 |
|:---|:---|:---|
| 表中的最大列数         | 500 | |
| 列名称的最大字符数 | 500 | |

**<a name="data-ingestion-volume-rate">数据引入速率</a>**

Azure Monitor 是一种大规模数据服务，每月为成千上万的客户发送数 TB 的数据，并且此数据仍在不断增长。 引入量速率限制旨在使 Azure Monitor 客户与多租户环境中突然出现的引入高峰相隔绝。 为工作区定义的默认的引入量速率阈值为 500 M（压缩量），大约相应于未压缩时的每分钟 6 GB 的速率 - 根据日志长度及其压缩率，不同数据类型的实际大小可能不同。 通过[诊断设置](../articles/azure-monitor/essentials/diagnostic-settings.md)，卷速率限制适用于从 Azure 资源引入的数据。 达到容量限制时，重试机制会在 30 分钟内 4 次尝试引入数据，如果操作失败则将其删除。 它不适用于从[代理](../articles/azure-monitor/agents/agents-overview.md)或[数据收集器 API](../articles/azure-monitor/logs/data-collector-api.md) 引入的数据。

如果将数据发送至工作区时采用的引入量速率高于工作区中配置的阈值的 80%，则当继续超过阈值时，会每 6 小时向你工作区中的“操作”表发送一个事件。 如果引入量速率超过阈值，则当继续超过阈值时，某些数据会被放弃，并且每 6 小时向你工作区中的“操作”表发送一个事件。 如果引入量速率继续超过阈值，或者预计很快会达到阈值，你可打开支持请求，请求调高阈值。 

请参阅 [在 Azure Monitor 中监视 Log Analytics 工作区的运行状况](../articles/azure-monitor/logs/monitor-workspace.md)，以创建在达到任何引入限制时主动通知的警报规则。

>[!NOTE]
>根据 Log Analytics 的使用时长，你可能有权使用旧的定价层。 详细了解 [Log Analytics 的旧定价层](../articles/azure-monitor/logs/manage-cost-storage.md#legacy-pricing-tiers)。
