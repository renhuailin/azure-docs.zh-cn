---
title: 了解 Azure Monitor 警报的迁移
description: 了解警报迁移的工作原理并排查问题。
ms.topic: conceptual
ms.date: 09/06/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 2167e0ea05206bc9c991353d6518090773934635
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543648"
---
# <a name="understand-migration-options-to-newer-alerts"></a>了解迁移到更新警报的迁移选项

经典警报已对公有云用户[停用](./monitoring-classic-retirement.md)。 适用于 Azure 政府云和 Azure 中国世纪互联的经典警报将于 2024 年 2 月 29 日停用。

本文介绍了手动迁移和自愿性迁移工具的工作原理，该工具将用来迁移剩余的警报规则。 此外还介绍了一些常见问题的解决方法。

> [!IMPORTANT]
> 活动日志警报（包括服务运行状况警报）和日志警报不受迁移的影响。 迁移仅适用于[此处](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)所述的经典警报规则。

> [!NOTE]
> 如果经典警报规则无效，即，它们应用于[已弃用的指标](#classic-alert-rules-on-deprecated-metrics)或已删除的资源，则它们不会进行迁移，在服务停用后将不可用。

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>将经典警报手动迁移到更新的警报

有兴趣手动迁移其剩余警报的客户可能已经根据以下部分的说明这样做了。 它还包括已停用的因此无法直接迁移的指标。

### <a name="guest-metrics-on-virtual-machines"></a>虚拟机上的来宾指标

在针对来宾指标创建新的指标警报之前，必须先将来宾指标发送到 Azure Monitor 日志存储。 请按照这些说明创建警报：

- [启用将来宾指标收集到 Log Analytics](../agents/agent-data-sources.md)
- [在 Azure Monitor 中创建日志警报](./alerts-log.md)

还有其他一些选项可用于收集来宾指标并对其发出警报。[了解详细信息](../agents/agents-overview.md)。

### <a name="storage-and-classic-storage-account-metrics"></a>存储帐户和经典存储帐户指标

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

必须将针对 AnonymousThrottlingError、SASThrottlingError 和 ThrottlingError 的经典警报规则拆分为两个新警报，因为没有任何合并的指标可提供相同的功能。 需要相应地调整阈值。

### <a name="cosmos-db-metrics"></a>Cosmos DB 指标

除了针对以下指标的警报之外，可以迁移针对 Cosmos DB 指标的所有经典警报：

- 每秒平均请求数
- 一致性级别
- Http 2xx
- Http 3xx
- 每分钟消耗的最大 RUPM
- 每秒最大 RU 数
- Mongo 的其他请求费用
- Mongo 的其他请求速率
- 观察到的读取延迟
- 观察到的写入延迟
- 服务可用性
- 存储容量

每秒平均请求数、一致性级别、每分钟消耗的最大 RUPM、每秒最大 RU 数、观察到的读取延迟、观察到的写入延迟和存储容量目前在[新系统](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts)中不可用。

不会迁移针对 Http 2xx、Http 3xx 和服务可用性等请求指标的警报，因为对请求进行计数的方式在经典指标和新指标之间是不同的。 需要调整阈值，手动重新创建针对这些指标的警报。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>针对已弃用指标的经典警报规则

以下针对指标的经典警报规则在以前受支持，但最终将会弃用。 只有少量的客户可能针对此类指标应用无效的经典警报规则。 由于这些警报规则无效，因此不会迁移它们。

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

- **频率**：定义经典警报规则或新警报规则检查条件的频率。 经典警报规则中的 `frequency` 不可由用户配置，对于所有资源类型始终为 5 分钟。 等效规则的频率也设置为 5 分钟。
- **聚合类型**：定义如何在感兴趣的时间范围内聚合指标。 对于大多数指标而言，使用经典警报和新警报时，`aggregationType` 也是相同的。 在某些情况下，由于经典警报和新警报的指标不同，将使用为指标定义的等效 `aggregationType` 或 `primary Aggregation Type`。
- **单位**：针对其创建警报的指标的属性。 某些等效指标具有不同的单位。 可根据需要相应地调整阈值。 例如，如果原始指标以秒为单位，但等效的新指标以毫秒为单位，则原始阈值将乘以 1000，以确保相同的行为。
- **时间范围**：定义一个时间范围，指标数据将在该范围内进行聚合，以便与阈值进行比较。 对于 5 分钟、15 分钟、30 分钟、1 小时、3 小时、6 小时、12 小时、1 天等标准 `windowSize` 值，等效的新警报规则未有任何变化。 对于其他值，将使用最接近的 `windowSize`。 大多数客户不会受到此项更改的影响。 少量的客户可能需要调整阈值才能获得完全相同的行为。

以下部分详细介绍了新系统中包含不同但等效的指标的指标。 对于经典和新警报规则保持不变的任何指标未列出。 可在[此处](../essentials/metrics-supported.md)找到新系统中支持的指标列表。

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts 和 Microsoft.ClassicStorage/storageAccounts

对于 Blob、表、文件和队列等存储帐户服务，以下指标将映射到等效的指标，如下所示：

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

对于 Cosmos DB，等效的指标如下所示：

| 经典警报中的指标 | 新警报中的等效指标 | 注释|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| 数据大小 | DataUsage| |
| 文档计数 | DocumentCount||
| 索引大小 | IndexUsage||
| 服务不可用 | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| 限制的请求数 | 包含维度 "StatusCode" = "429" 的 TotalRequests| “Average”聚合类型已更正为“Count”|
| 内部服务器错误数 | 包含维度 "StatusCode" = "500"} 的 TotalRequests| “Average”聚合类型已更正为“Count”|
| Http 401 | 包含维度 "StatusCode" = "401" 的 TotalRequests| “Average”聚合类型已更正为“Count”|
| Http 400 | 包含维度 "StatusCode" = "400" 的 TotalRequests| “Average”聚合类型已更正为“Count”|
| 请求总数 | TotalRequests| “Max”聚合类型已更正为“Count”|
| Mongo Count 请求费用| 维度 "CommandName" = "count" 的 MongoRequestCharge||
| Mongo Count 请求速率 | 维度 "CommandName" = "count" 的 MongoRequestsCount||
| Mongo Delete 请求费用 | 维度 "CommandName" = "delete" 的 MongoRequestCharge||
| Mongo 删除请求速率 | 维度 "CommandName" = "delete" 的 MongoRequestsCount||
| Mongo Insert 请求费用 | 维度 "CommandName" = "insert" 的 MongoRequestCharge||
| Mongo 插入请求速率 | 维度 "CommandName" = "insert" 的 MongoRequestsCount||
| Mongo Query 请求费用 | 维度 "CommandName" = "find" 的 MongoRequestCharge||
| Mongo 查询请求速率 | 维度 "CommandName" = "find" 的 MongoRequestsCount||
| Mongo Update 请求费用 | 维度 "CommandName" = "update" 的 MongoRequestCharge||
| Mongo Insert 失败请求数 | 包含维度 "CommandName" = "insert" 和 "Status" = "failed" 的 MongoRequestCount| “Average”聚合类型已更正为“Count”|
| Mongo Query 失败请求数 | 包含维度 "CommandName" = "query" 和 "Status" = "failed" 的 MongoRequestCount| “Average”聚合类型已更正为“Count”|
| Mongo Count 失败请求数 | 包含维度 "CommandName" = "count" 和 "Status" = "failed" 的 MongoRequestCount| “Average”聚合类型已更正为“Count”|
| Mongo Update 失败请求数 | 包含维度 "CommandName" = "update" 和 "Status" = "failed" 的 MongoRequestCount| “Average”聚合类型已更正为“Count”|
| Mongo 的其他失败请求数 | 包含维度 "CommandName" = "other" 和 "Status" = "failed" 的 MongoRequestCount| “Average”聚合类型已更正为“Count”|
| Mongo Delete 失败请求数 | 包含维度 "CommandName" = "delete" 和 "Status" = "failed" 的 MongoRequestCount| “Average”聚合类型已更正为“Count”|

### <a name="how-equivalent-action-groups-are-created"></a>如何创建等效的操作组

经典警报规则中的电子邮件、Webhook、逻辑应用和 Runbook 操作已绑定到警报规则本身。 新警报规则使用可在多个警报规则之间重用的操作组。 不管有多少警报规则正在使用操作，迁移工具都会为相同的操作创建一个操作组。 迁移工具创建的操作组使用命名格式“Migrated_AG*”。

> [!NOTE]
> 用于通知经典管理员角色时，经典警报根据经典管理员的区域设置发送本地化电子邮件。 新警报电子邮件通过操作组发送，并且仅采用英语。

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

由于最近对订阅中的经典警报规则做了某些更改，无法迁移该订阅。 此问题是暂时性的。 几天后当迁移状态恢复为“准备好迁移”时，可以重新开始迁移。

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>范围锁阻止我们迁移你的规则

在迁移过程中，将会创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，范围锁可能会阻止我们创建或删除资源。 部分或所有规则可能无法迁移，具体取决于范围锁。 若要解决此问题，可以删除[迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中列出的订阅、资源组或资源的范围锁，并且再次触发迁移。 在迁移过程中，不能禁用范围锁，必须将其删除。 [详细了解如何管理范围锁](../../azure-resource-manager/management/lock-resources.md#portal)。

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>具有“拒绝”效果的策略阻止我们迁移你的规则

在迁移过程中，将会创建新的指标警报和新的操作组，然后删除经典警报规则。 但是，[Azure Policy](../../governance/policy/index.yml) 分配可能会阻止我们创建资源。 部分或所有规则可能无法迁移，具体取决于策略分配。 [迁移工具](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)中会列出阻止此过程的策略分配。 请通过以下方法之一解决此问题：

- 在迁移过程中从策略分配中排除订阅、资源组或单个资源。 [详细了解如何管理策略排除范围](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)。
- 对于该策略分配，将“强制模式”设置为“禁用”。 [详细了解策略分配的 enforcementMode 属性](../../governance/policy/concepts/assignment-structure.md#enforcement-mode)。
- 在策略分配的订阅、资源组或单个资源上设置 Azure Policy 豁免（预览版）。 [详细了解 Azure Policy 豁免结构](../../governance/policy/concepts/exemption-structure.md)。
- 删除效果或将效果更改为“disabled”、“audit”、“append”或“modify”（这可以解决许多问题，例如与标记缺失相关的问题）。 [详细了解如何管理策略效果](../../governance/policy/concepts/definition-structure.md#policy-rule)。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [迁移准备](alerts-prepare-migration.md)
