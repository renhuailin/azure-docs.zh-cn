---
title: 监视 Azure Monitor 中 Log Analytics 工作区的运行状况
description: 介绍如何使用“操作”表中的数据监视 Log Analytics 工作区的运行状况。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 4f127245ea36a7183603f5115739d317282ea686
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113009517"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>监视 Azure Monitor 中 Log Analytics 工作区的运行状况
若要维持 Azure Monitor 中 Log Analytics 工作区的性能和可用性，你需要能够主动检测出现的任何问题。 本文介绍如何使用[操作](/azure/azure-monitor/reference/tables/operation)表中的数据监视 Log Analytics 工作区的运行状况。 此表存在于每个 Log Analytics 工作区中，并且包含工作区中发生的错误和警告。 建议针对“警告”和“错误”级别的问题创建警报。

## <a name="_logoperation-function"></a>_LogOperation 函数

Azure Monitor 日志会将有关任何问题的详细信息发送到出现相应问题的工作区中的[操作](/azure/azure-monitor/reference/tables/operation)表。 _LogOperation 系统函数以“操作”表为基础，并提供一组简化的信息以供分析和报警 。

## <a name="columns"></a>列

_LogOperation 函数返回下表中的列。

| 列 | 说明 |
|:---|:---|
| TimeGenerated | 事件发生的时间（UTC 形式）。 |
| 类别  | 操作类别组。 可用于筛选操作类型，以及帮助创建更精确的系统审核和警报。 有关类别的列表，请参阅下面的部分。 |
| 操作  | 操作类型的说明。 该操作可以指示已达到某个 Log Analytics 限制、后端进程相关问题或任何其他服务消息。 |
| 级别 | 问题的严重级别：<br>- 信息：无需特别关注。<br>- 警告：进程未按预期完成，需要引起注意。<br>- 错误：进程失败，需要注意。 
| 详情 | 操作的详细说明，包括特定的错误消息。 |
| _ResourceId | 与操作相关的 Azure 资源的资源 ID。  |
| Computer | 计算机名（如果操作与 Azure Monitor 代理相关）。 |
| CorrelationId | 用于对连续的相关操作进行分组。 |


## <a name="categories"></a>类别

下表介绍了 _LogOperation 函数的类别。 

| 类别 | 说明 |
|:---|:---|
| 引流           | 作为数据引入过程的一部分的操作。 |
| 代理               | 指示代理安装问题。 |
| 数据收集     | 与数据收集过程相关的操作。 |
| 解决方案目标  | ConfigurationScope 类型的操作已处理。 |
| 评估解决方案 | 评估过程已执行。 |


### <a name="ingestion"></a>引流
引入操作是在数据引入过程中出现的问题，包括有关达到 Azure Log Analytics 工作区限制的通知。 此类别中的错误情况可能意味着数据丢失，因此监视这些情况很重要。 下表提供了有关这些操作的详细信息。 请参阅 [Azure Monitor 服务限制](../service-limits.md#log-analytics-workspaces)，了解 Log Analytics 工作区的服务限制。

 
#### <a name="operation-data-collection-stopped"></a>操作：数据收集已停止  
“由于达到每日免费数据限制，数据收集已停止。 Ingestion status = OverQuota”

在过去 7 天内，日志收集已达到每日设置的限制。 在将工作区设置为“免费层”，或为此工作区配置了每日收集限制时，即已设定限制。
请注意，达到设定的限制后，数据收集将在当天自动停止，并仅在下一个收集日恢复。 
 
建议的操作： 
*   检查 _LogOperation 表中的收集已停止和收集已恢复事件。</br>
`_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Data collection"`
*   针对“数据收集已停止”操作事件[创建警报](./manage-cost-storage.md#alert-when-daily-cap-is-reached)，此警报将使你在达到收集限制时收到通知。
*   达到每日收集限制后收集的数据将丢失，请使用“工作区见解”边栏选项卡查看每个源的使用率。 或者，你可以决定（[管理每日最大数据量](./manage-cost-storage.md#manage-your-maximum-daily-data-volume) \ [将定价层更改为](./manage-cost-storage.md#changing-pricing-tier)适合自身收集率情况的层）。 
* 数据收集率按天计算，并会在第二天开始时重置，你还可以通过针对“数据收集已恢复”操作事件[创建警报](./manage-cost-storage.md#alert-when-daily-cap-is-reached)来监视收集恢复事件。

#### <a name="operation-ingestion-rate"></a>操作：引入速率
“数据引入量速率超过了工作区中的阈值：每分钟 {0:0.00} MB，并且数据已被删除。” 

建议的操作：
*   检查 _LogOperation 表中的引入速率事件 `_LogOperation | where TimeGenerated >= ago(7d) | where Category == "Ingestion" | where Operation has "Ingestion rate"` 
  注意：在持续超过阈值的情况下，每 6 小时在工作区中操作一次表。 
*   针对“数据收集已停止”操作事件[创建警报](./manage-cost-storage.md#alert-when-daily-cap-is-reached)，此警报将使你在达到限制时收到通知。
*   引入速率达到 100% 时收集的数据将被删除并丢失。 

“工作区见解”边栏选项卡，查看使用情况并尝试减少使用。</br>
有关详细信息： </br>
[Azure Monitor 服务限制](../service-limits.md#data-ingestion-volume-rate) </br>
[管理 Azure Monitor 日志的使用情况和成本](./manage-cost-storage.md#alert-when-daily-cap-is-reached)  

 
#### <a name="operation-maximum-table-column-count"></a>操作：最大表列计数
“由于字段 \<**new fields count**\> 的数量超过了每种数据类型 \<**current field count limit**\> 个自定义字段的限制，因此删除了 \<**table name**\> 类型的数据。” 

建议的操作：对于自定义表，你可以转到[分析查询中的数据](./parse-text.md)。

#### <a name="operation-field-content-validation"></a>操作：字段内容验证
“以下字段的 \<**table name**\> 类型的值 \<**field name**\> 已被剪裁为允许的最大大小，即 \<**field size limit**\> 字节。 请相应地调整输入。” 

如果字段大于限制大小，则由 Azure 日志处理，该字段将被剪裁到允许的字段限制。 不建议发送大于允许限制的字段，因为这会导致数据丢失。 

建议的操作：检查受影响数据类型的源：
*   如果数据是通过 HTTP 数据收集器 API 发送的，你将需要更改代码/脚本以在引入数据之前拆分数据。
*   对于由 Log Analytics 代理收集的自定义日志，请更改应用程序/工具的日志记录设置。
*   对于任何其他数据类型，请创建支持案例。 
</br>阅读更多内容：[Azure Monitor 服务限制](../service-limits.md#data-ingestion-volume-rate) 

### <a name="data-collection"></a>数据收集
#### <a name="operation-azure-activity-log-collection"></a>操作：Azure 活动日志收集
“无法访问订阅。 请确保 \<**subscription id**\> 订阅位于 \<**tenant id**\> Azure Active Directory 租户中。 如果订阅转移到另一个租户，则不会影响服务，但租户的信息可能需要一小时才能传播。 '"

说明：在某些情况下（例如将订阅移动到其他租户），Azure 活动日志可能会停止流入工作区。 在这些情况下，我们需要按照本文中所述的过程重新连接订阅。

建议的操作： 
* 如果警告消息中提到的订阅不再存在，请导航到“Azure 活动日志”边栏选项卡的“工作区数据源”下，选择相关订阅，最后选择“断开连接”按钮。
* 如果不再有权访问警告消息中提到的订阅：
  * 按照步骤 1 断开订阅连接。 
  * 若要继续从此订阅收集日志，请联系订阅所有者修复权限，重新启用活动日志收集。
* [创建诊断设置](../essentials/activity-log.md#send-to-log-analytics-workspace)，以便将活动日志发送到 Log Analytics 工作区。 

### <a name="agent"></a>Agent
#### <a name="operation-linux-agent"></a>操作：Linux 代理
“来自 OMS 设置的两个连续配置应用程序失败”

门户上的配置设置已更改。

建议的操作：如果代理检索新的配置设置时出现问题，则会引发此问题。 要缓解此问题，需要重新安装代理。 检查 _LogOperation 表中的代理事件。</br>

 `_LogOperation | where TimeGenerated >= ago(6h) | where Category == "Agent" | where Operation == "Linux Agent"  | distinct _ResourceId`

该列表将列出代理配置错误的资源 ID。
要缓解此问题，需要重新安装列出的代理。

   

## <a name="alert-rules"></a>警报规则
当在 Log Analytics 工作区中检测到问题时，使用 Azure Monitor 中的[日志查询警报](../alerts/alerts-log-query.md)主动获得通知。 使用一种策略，让你能够及时响应问题，同时最大限度地降低成本。 订阅将按 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)中所列对每个警报规则收费。

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
