---
title: 监视 Azure Monitor 中 Log Analytics 工作区的运行状况
description: 介绍如何使用“操作”表中的数据监视 Log Analytics 工作区的运行状况。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538839"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>监视 Azure Monitor 中 Log Analytics 工作区的运行状况
若要维持 Azure Monitor 中 Log Analytics 工作区的性能和可用性，你需要能够主动检测出现的任何问题。 本文介绍如何使用[操作](/azure/azure-monitor/reference/tables/operation)表中的数据监视 Log Analytics 工作区的运行状况。 此表存在于每个 Log Analytics 工作区中，并且包含工作区中发生的错误和警告。 你应定期查看这些数据，并创建警报，以便在工作区中发生任何重要事件时主动收到通知。

## <a name="_logoperation-function"></a>_LogOperation 函数

Azure Monitor 日志会将有关任何问题的详细信息发送到出现相应问题的工作区中的[操作](/azure/azure-monitor/reference/tables/operation)表。 _LogOperation 系统函数以“操作”表为基础，并提供一组简化的信息以供分析和报警 。

## <a name="columns"></a>列

_LogOperation 函数返回下表中的列。

| 列 | 说明 |
|:---|:---|
| TimeGenerated | 事件发生的时间（UTC 形式）。 |
| 类别  | 操作类别组。 可用于筛选操作类型，以及帮助创建更精确的系统审核和警报。 有关类别的列表，请参阅下面的部分。 |
| 操作  | 操作类型的说明。 可以表示某一 Log Analytics 限制、操作类型或进程的一部分。 |
| 级别 | 问题的严重级别：<br>- 信息：无需特别关注。<br>- 警告：进程未按预期完成，需要引起注意。<br>- 错误：进程失败，需要紧急关注。 
| 详细信息 | 操作的详细说明，包括特定的错误消息（如果存在）。 |
| _ResourceId | 与操作相关的 Azure 资源的资源 ID。  |
| Computer | 计算机名（如果操作与 Azure Monitor 代理相关）。 |
| CorrelationId | 用于对连续的相关操作进行分组。 |


## <a name="categories"></a>类别

下表介绍了 _LogOperation 函数的类别。 

| 类别 | 说明 |
|:---|:---|
| 引流           | 作为数据引入过程的一部分的操作。 有关详细信息，请参阅下文。 |
| 代理               | 指示代理安装问题。 |
| 数据收集     | 与数据收集过程相关的操作。 |
| 解决方案目标  | ConfigurationScope 类型的操作已处理。 |
| 评估解决方案 | 评估过程已执行。 |


### <a name="ingestion"></a>引流
引入操作是在数据引入过程中出现的问题，包括有关达到 Azure Log Analytics 工作区限制的通知。 此类别中的错误情况可能意味着数据丢失，因此监视这些情况尤为重要。 下表提供了有关这些操作的详细信息。 请参阅 [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces)，了解 Log Analytics 工作区的服务限制。


| 操作 | 级别 | 详细信息 | 相关文章 |
|:---|:---|:---|:---|
| 自定义日志 | 错误   | 已达到自定义字段列限制。 | [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces) |
| 自定义日志 | 错误   | 自定义日志引入失败。 | |
| 元数据 | 错误 | 检测到配置错误。 | |
| 数据收集 | 错误   | 数据已删除，因为请求的创建时间早于设置的天数。 | [使用 Azure Monitor 日志管理使用情况和成本](./manage-cost-storage.md#alert-when-daily-cap-reached)
| 数据收集 | 信息    | 检测到收集计算机配置。| |
| 数据收集 | 信息    | 数据收集由于新的一天而开始。 | [使用 Azure Monitor 日志管理使用情况和成本](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| 数据收集 | 警告 | 由于达到每日限制，数据收集已停止。| [使用 Azure Monitor 日志管理使用情况和成本](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| 数据处理 | 错误   | JSON 格式无效。 | [使用 HTTP 数据收集器 API（公共预览版）将日志数据发送到 Azure Monitor](../logs/data-collector-api.md#request-body) | 
| 数据处理 | 警告 | 值已剪裁为允许的最大大小。 | [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces) |
| 数据处理 | 警告 | 由于达到大小限制，字段值已被剪裁。 | [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces) | 
| 引入速率 | 信息 | 引入速率限制接近 70%。 | [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces) |
| 引入速率 | 警告 | 引入速率限制接近极限。 | [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces) |
| 引入速率 | 错误   | 已达到速率限制。 | [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces) |
| 存储 | 错误   | 由于使用的凭据无效，因此无法访问存储帐户。  |



   

## <a name="alert-rules"></a>警报规则
当在 Log Analytics 工作区中检测到问题时，使用 Azure Monitor 中的[日志查询警报](../alerts/alerts-log-query.md)主动获得通知。 你应使用这样一种策略，即让你能够及时响应问题，同时最大限度地降低成本。 按每项预警规则对订阅收费，费用具体取决于评估这些规则的频率。

一项建议的策略是根据问题级别从两项预警规则开始。 针对错误使用较短的频率（例如每 5 分钟一次），而针对警告使用较长的频率（例如 24 小时）。 由于错误指示潜在的数据丢失，因此你需要快速响应这些错误，最大限度地减少任何损失。 警告通常指示无需立即关注的问题，因此你可以每天查看它们。

使用[使用 Azure Monitor 创建、查看和管理日志警报](../alerts/alerts-log.md)中的过程创建日志预警规则。 以下部分介绍了每项规则的详细信息。


| 查询 | 阈值 | 周期 | 频率 |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

这些预警规则将对出现错误或警告的所有操作做出相同的响应。 随着你对生成警报的操作越来越熟悉，你可能想要对特定操作做出不同响应。 例如，针对特定操作，你可能想要向不同的人发送通知。 

若要为特定操作创建预警规则，请使用包含“类别”和“操作”列的查询 。 

以下示例在引入量速率达到限制的 80% 时创建警告性警报。

- 目标：选择你的 Log Analytics 工作区
- 条件：
  - 信号名称：自定义日志搜索
  - 搜索查询：`_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - 依据：结果数
  - 条件：大于
  - 阈值：0
  - 时间段：5（分钟）
  - 频率：5（分钟）
- 警报规则名称：达到每日数据限制
- 严重性：警告（严重性 1）


以下示例在数据收集达到每日限制时创建警告性警报。 

- 目标：选择你的 Log Analytics 工作区
- 条件：
  - 信号名称：自定义日志搜索
  - 搜索查询：`_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - 依据：结果数
  - 条件：大于
  - 阈值：0
  - 时间段：5（分钟）
  - 频率：5（分钟）
- 警报规则名称：达到每日数据限制
- 严重性：警告（严重性 1）



## <a name="next-steps"></a>后续步骤

- 详细了解[日志警报](../alerts/alerts-log.md)。
- 收集工作区的[查询审核数据](./query-audit.md)。
