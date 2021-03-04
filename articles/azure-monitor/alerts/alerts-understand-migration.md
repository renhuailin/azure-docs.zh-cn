---
title: 了解 Azure Monitor 警报的迁移
description: 了解警报迁移的工作方式，并解决问题。
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 88b75c46898e1a74b33051d8996af96201f639c7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037704"
---
# <a name="understand-migration-options-to-newer-alerts"></a>了解迁移选项到新警报

公有云用户会 [停](./monitoring-classic-retirement.md) 用经典警报，但在 **31 年 5 2021 月31日** 之前仍处于有限使用状态。 Azure 政府云和 Azure 中国世纪互联的经典警报将于 **2024 年2月29日** 停用。

本文介绍了如何使用 "手动迁移" 和 "自主迁移" 工具来迁移剩余的警报规则。 还介绍了一些常见问题的解决方案。

> [!IMPORTANT]
> 活动日志警报（包括服务运行状况警报）和日志警报不受迁移的影响。 迁移仅适用于[此处](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)所述的经典警报规则。

> [!NOTE]
> 如果经典警报规则无效（即它们处于 [弃用的指标](#classic-alert-rules-on-deprecated-metrics) 或已删除的资源），则它们将不会迁移，在服务停用后将无法使用。

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>将经典警报手动迁移到更新的警报

对于手动迁移其剩余警报感兴趣的客户，可以使用以下部分来执行此操作。 它还包括已停用的指标，因此无法直接迁移。

### <a name="guest-metrics-on-virtual-machines"></a>虚拟机上的来宾指标

在对来宾指标创建新的指标警报之前，必须将来宾指标发送到 Azure Monitor 日志存储区。 按照以下说明创建警报：

- [启用来宾指标收集到 log analytics](../agents/agent-data-sources.md)
- [在 Azure Monitor 中创建日志警报](./alerts-log.md)

有更多选项可用于收集来宾指标并向其发出警报， [了解详细信息](../agents/agents-overview.md)。

### <a name="storage-and-classic-storage-account-metrics"></a>存储和经典存储帐户指标

可以迁移针对存储帐户的所有经典警报，但针对以下指标的警报除外：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

必须基于[旧的和新的存储指标之间的映射](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics)迁移针对 Percent 指标的经典警报规则。 需要相应地修改阈值，因为提供的新指标是绝对指标。

AnonymousThrottlingError、SASThrottlingError 和 ThrottlingError 上的经典警报规则必须拆分为两个新警报，因为没有提供相同功能的组合指标。 需要相应地调整阈值。

### <a name="cosmos-db-metrics"></a>Cosmos DB 指标

可以迁移 Cosmos DB 指标的所有经典警报，但这些指标的警报除外：

- 每秒平均请求数
- 一致性级别
- Http 2xx
- Http 3xx
- 每分钟使用的最大 RUPM
- 最大每秒 ru 数
- 其他请求费用 Mongo
- Mongo 其他请求速率
- 观察到的读取延迟
- 观察到的写入延迟
- 服务可用性
- 存储容量

每秒平均请求数，一致性级别，每分钟使用的最大 RUPM 数，最大每秒的每秒，观察到的读取延迟，观察到的写入延迟，存储容量目前在 [新系统](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts)中不可用。

不会迁移 Http 2xx、Http 3xx 和服务可用性等请求度量值的警报，因为在经典指标和新指标之间对请求进行计数的方式不同。 需要手动重新创建这些指标的警报，并调整阈值。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>针对已弃用指标的经典警报规则

下面是有关以前支持但最终不推荐使用的指标的经典警报规则。 只有少量的客户可能针对此类指标应用无效的经典警报规则。 由于这些警报规则无效，因此不会迁移它们。

| 资源类型| 已弃用的指标 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent、compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent、compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、storage_limit、storage_used、throttling、dtu_consumption_percent、storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived、httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何创建等效的新警报规则和操作组

迁移工具可将经典警报规则转换为等效的新警报规则和操作组。 对于大多数经典警报规则，等效的新警报规则将应用于包含 `windowSize` 和 `aggregationType` 等相同属性的相同指标。 但是，某些经典警报规则应用于新系统中包含不同但等效的指标的指标。 除非以下部分另有说明，否则，以下原则适用于经典警报的迁移：

- **Frequency**：定义经典或新警报规则检查条件的频率。 `frequency`经典警报规则中的不能由用户进行配置，并且对于所有资源类型始终是5分钟。 等效规则的频率也设置为5分钟。
- "**聚合类型**"：定义度量值在相关窗口中的聚合方式。 对于大多数指标而言，使用经典警报和新警报时，`aggregationType` 也是相同的。 在某些情况下，由于经典警报和新警报的指标不同，将使用为指标定义的等效 `aggregationType` 或 `primary Aggregation Type`。
- **单元**：在其上创建警报的指标的属性。 某些等效指标具有不同的单位。 可根据需要相应地调整阈值。 例如，如果原始度量值的单位为秒，但等效的新指标以毫秒为单位，则原始阈值将乘以1000，以确保相同的行为。
- **窗口大小**：定义汇总度量值数据以与阈值进行比较的窗口。 对于 `windowSize` 5 分钟、15分钟、30分钟、1小时、3小时、6小时、12小时、1天的标准值，不会对等效的新警报规则进行任何更改。 对于其他值，将使用最近的 `windowSize` 。 对于大多数客户而言，此更改不会有任何影响。 少量的客户可能需要调整阈值才能获得完全相同的行为。

以下部分详细介绍了新系统中包含不同但等效的指标的指标。 不会列出任何对于经典和新警报规则均相同的指标。 可在[此处](../essentials/metrics-supported.md)找到新系统中支持的指标列表。

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>StorageAccounts/ClassicStorage/storageAccounts

对于 blob、表、文件和队列等存储帐户服务，以下度量值映射到等效的指标，如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 包含维度 "ResponseType"="AuthorizationError" 和 "Authentication"="Anonymous" 的事务指标| |
| AnonymousClientOtherError | 包含维度 "ResponseType"="ClientOtherError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousClientTimeOutError| 包含维度 "ResponseType"="ClientTimeOutError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousNetworkError | 包含维度 "ResponseType"="NetworkError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousServerOtherError | 包含维度 "ResponseType"="ServerOtherError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousServerTimeOutError | 包含维度 "ResponseType"="ServerTimeOutError" 和 "Authentication"="Anonymous" 的事务指标 | |
| AnonymousSuccess | 包含维度 "ResponseType"="Success" 和 "Authentication"="Anonymous" 的事务指标 | |
| AuthorizationError | 包含维度 "ResponseType"="AuthorizationError" 的事务指标 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 容量 | BlobCapacity | 使用 `aggregationType` 值“average”而不是“average”。 指标仅适用于 Blob 服务 |
| ClientOtherError | 包含维度 "ResponseType"="ClientOtherError" 的事务指标  | |
| ClientTimeoutError | 包含维度 "ResponseType"="ClientTimeOutError" 的事务指标 | |
| ContainerCount | ContainerCount | 使用 `aggregationType` 值“average”而不是“average”。 指标仅适用于 Blob 服务 |
| NetworkError | 包含维度 "ResponseType"="NetworkError" 的事务指标 | |
| ObjectCount | BlobCount| 使用 `aggregationType` 值“average”而不是“average”。 指标仅适用于 Blob 服务 |
| SASAuthorizationError | 包含维度 "ResponseType"="AuthorizationError" 和 "Authentication"="SAS" 的事务指标 | |
| SASClientOtherError | 包含维度 "ResponseType"="ClientOtherError" 和 "Authentication"="SAS" 的事务指标 | |
| SASClientTimeOutError | 包含维度 "ResponseType"="ClientTimeOutError" 和 "Authentication"="SAS" 的事务指标 | |
| SASNetworkError | 包含维度 "ResponseType"="NetworkError" 和 "Authentication"="SAS" 的事务指标 | |
| SASServerOtherError | 包含维度 "ResponseType"="ServerOtherError" 和 "Authentication"="SAS" 的事务指标 | |
| SASServerTimeOutError | 包含维度 "ResponseType"="ServerTimeOutError" 和 "Authentication"="SAS" 的事务指标 | |
| SASSuccess | 包含维度 "ResponseType"="Success" 和 "Authentication"="SAS" 的事务指标 | |
| ServerOtherError | 包含维度 "ResponseType"="ServerOtherError" 的事务指标 | |
| ServerTimeOutError | 包含维度 "ResponseType"="ServerTimeOutError" 的事务指标  | |
| 成功 | 包含维度 "ResponseType"="Success" 的事务指标 | |
| TotalBillableRequests| 事务 | |
| TotalEgress | 流出量 | |
| TotalIngress | 流入量 | |
| TotalRequests | 事务 | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

对于 Cosmos DB，等效指标如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| 数据大小 | DataUsage| |
| 文档计数 | DocumentCount||
| 索引大小 | IndexUsage||
| 服务不可用 | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| 限制的请求数 | 维度为 "StatusCode" 的 TotalRequests = "429"| "Average" 聚合类型更正为 "Count"|
| 内部服务器错误数 | 维度 "StatusCode" = "500"} 的 TotalRequests| "Average" 聚合类型更正为 "Count"|
| Http 401 | 维度为 "StatusCode" 的 TotalRequests = "401"| "Average" 聚合类型更正为 "Count"|
| Http 400 | 维度为 "StatusCode" 的 TotalRequests = "400"| "Average" 聚合类型更正为 "Count"|
| 请求总数 | TotalRequests| "Max" 聚合类型已更正为 "Count"|
| Mongo Count 请求费用| MongoRequestCharge 维度 "CommandName" = "count"||
| Mongo 计数请求速率 | MongoRequestsCount 维度 "CommandName" = "count"||
| Mongo 删除请求费用 | MongoRequestCharge 维度 "CommandName" = "delete"||
| Mongo 删除请求速率 | MongoRequestsCount 维度 "CommandName" = "delete"||
| Mongo 插入请求费用 | MongoRequestCharge 维度 "CommandName" = "insert"||
| Mongo 插入请求速率 | MongoRequestsCount 维度 "CommandName" = "insert"||
| Mongo 查询请求费用 | MongoRequestCharge 维度 "CommandName" = "find"||
| Mongo 查询请求速率 | MongoRequestsCount 维度 "CommandName" = "find"||
| Mongo 更新请求费用 | MongoRequestCharge 维度 "CommandName" = "update"||
| Mongo 插入失败的请求 | 带有维度 "CommandName" = "insert" 和 "Status" = "failed" 的 MongoRequestCount| "Average" 聚合类型更正为 "Count"|
| Mongo 查询失败的请求数 | 含维度 "CommandName" = "query" 和 "Status" = "failed" 的 MongoRequestCount| "Average" 聚合类型更正为 "Count"|
| Mongo Count 失败请求数 | 带有维度 "CommandName" = "count" 和 "Status" = "failed" 的 MongoRequestCount| "Average" 聚合类型更正为 "Count"|
| Mongo 更新失败请求数 | 含维度 "CommandName" = "update" 和 "Status" = "failed" 的 MongoRequestCount| "Average" 聚合类型更正为 "Count"|
| Mongo 其他失败的请求 | 带有维度 "CommandName" = "other" 和 "Status" = "failed" 的 MongoRequestCount| "Average" 聚合类型更正为 "Count"|
| Mongo 删除失败的请求 | 含维度 "CommandName" = "delete" 和 "Status" = "failed" 的 MongoRequestCount| "Average" 聚合类型更正为 "Count"|

### <a name="how-equivalent-action-groups-are-created"></a>如何创建等效的操作组

经典警报规则具有与警报规则本身关联的电子邮件、webhook、逻辑应用和 runbook 操作。 新警报规则使用可跨多个警报规则重复使用的操作组。 无论使用该操作的警报规则有多少，迁移工具都会为相同操作创建单个操作组。 迁移工具创建的操作组使用命名格式“Migrated_AG*”。

> [!NOTE]
> 经典警报在用于通知经典管理员角色时，根据经典管理员的区域设置发送本地化电子邮件。 新警报电子邮件是通过操作组发送的，并且仅以英语表示。

## <a name="rollout-phases"></a>推出阶段

迁移工具将分阶段推出到使用经典警报规则的客户。 当订阅准备好使用该工具迁移时，订阅所有者会收到一封电子邮件。

> [!NOTE]
> 由于该工具是分阶段推出的，在早期阶段，你可能会发现某些订阅尚未做好迁移的准备。

目前，大部分订阅已标记为准备好迁移。 只有针对以下资源类型应用经典警报的订阅仍未做好迁移的准备。

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

在订阅级别拥有“监视参与者”内置角色的任何用户都可以触发迁移。 拥有自定义角色和以下权限的用户也可以触发迁移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 除了获取上述权限以外，还应额外地将订阅注册到 Microsoft.AlertsManagement 资源提供程序。 只有这样，才能成功迁移 Application Insights 中的“失败异常”警报。 

## <a name="common-problems-and-remedies"></a>常见问题和补救措施

[触发迁移](alerts-using-migration-tool.md)后，提供的电子邮件地址会收到电子邮件，其中告知迁移已完成，或者需要你采取任何措施。 本部分描述了一些常见问题及其处理方法。

### <a name="validation-failed"></a>验证失败

由于订阅中的经典警报规则发生了一些最新更改，因此无法迁移订阅。 此问题是暂时性的。 几天后当迁移状态恢复为“准备好迁移”时，可以重新开始迁移。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>范围锁阻止我们迁移规则

在迁移过程中，将会创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，范围锁可防止我们创建或删除资源。 根据范围锁，无法迁移部分或全部规则。 若要解决此问题，可以删除 [迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中列出的订阅、资源组或资源的作用域锁定，并再次触发迁移。 无法禁用作用域锁定，必须在迁移过程中将其删除。 [详细了解如何管理范围锁](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>具有 "拒绝" 影响的策略阻止我们迁移你的规则

在迁移过程中，将会创建新的指标警报和新的操作组，然后删除经典警报规则。 但是， [Azure 策略](../../governance/policy/index.yml) 分配可能会阻止我们创建资源。 根据策略分配，无法迁移部分或全部规则。 [迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中列出了阻止进程的策略分配。 通过以下方法之一解决此问题：

- 从策略分配中排除在迁移过程中的订阅、资源组或单个资源。 [了解有关管理策略排除范围的详细信息](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)。
- 将策略分配上的 "强制模式" 设置为 " **已禁用** "。 [了解有关策略分配的 enforcementMode 属性的详细信息](../../governance/policy/concepts/assignment-structure.md#enforcement-mode)。
- 将订阅、资源组或单个资源上的 Azure 策略例外 (预览) 设置为策略分配。 [了解有关 Azure 策略免除结构的详细信息](../../governance/policy/concepts/exemption-structure.md)。
- 例如，删除或更改 "已禁用"、"审核"、"追加" 或 "修改" 的效果 (可以解决与缺少的标记) 相关的问题。 [了解有关管理策略效果的详细信息](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [迁移准备](alerts-prepare-migration.md)