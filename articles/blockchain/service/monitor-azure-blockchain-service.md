---
title: 监视 Azure 区块链服务 (ABS)
description: 通过 Azure Monitor 监视 Azure 区块链服务
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594918"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>通过 Azure Monitor 监视 Azure 区块链服务  

当客户在 Azure 区块链服务 (ABS) 上运行生产级区块链方案时，监视资源的可用性、性能和操作就变得至关重要。 本文介绍 Azure 区块链服务生成的监视数据，以及如何使用 Azure Monitor 的各种功能和集成来对其进行分析和发出警报，以管理生产级别环境。  

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure 区块链服务使用 Azure Monitor 创建监视数据，Azure Monitor 是 Azure 中的一个全堆栈监视服务，提供用于监视 Azure 资源的一整套功能。 有关 Azure Monitor 的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/essentials/monitor-azure-resource.md)。
 

本文的以下各部分介绍了从 Azure 区块链服务中收集的特定数据，并提供了有关使用 Azure 工具配置数据收集和分析此数据的示例。

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>监视从 Azure 区块链服务中收集的数据  

Azure 区块链服务收集与其他 Azure 资源类型相同的监视数据，如[监视 Azure 资源中的数据](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述。 请参阅[监视 Azure 区块链服务数据引用](#monitor-azure-blockchain-service-data-reference)来获取有关 Azure 区块链服务创建的日志和指标的详细参考。

Azure 门户中每个 Azure 区块链服务成员资源的概述页面都包含事务的简要视图（包括已解决的请求和已处理的块）。 创建 Azure 区块链服务成员资源后，系统会立即自动收集其中部分数据并进行分析；同时，你可以使用其他配置启用其他数据收集。

## <a name="diagnostic-settings"></a>诊断设置  

平台指标和活动日志是自动收集的，但必须创建一个诊断设置才能收集资源日志，或在 Azure Monitor 外部转发这些日志。 有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)。

创建诊断设置时，请指定要收集的日志类别。 下面列出了 Azure 区块链服务的类别。

**区块链代理日志**：如果要监视 NGNIX 代理日志，请选择类别。 所有客户事务详细信息都可用于审核和调试目的。  

**区块链应用程序日志**：选择类别以获取托管服务托管的区块链应用程序的日志。 例如，对于 ABS-Quorum 成员，这些日志将是来自 Quorum 本身的日志。  

**指标请求**：选择此选项可将 Azure Cosmos DB 中的指标数据收集到诊断设置中的目标，此数据在 Azure 指标中自动收集。 同时收集指标数据和资源日志可将这两种类型的数据一起分析，并在 Azure Monitor 外部发送指标数据。

## <a name="analyze-metric-data"></a>分析指标数据  

可以使用指标资源管理器分析 Azure 区块链服务的指标，导航到“ABS 资源”边栏选项卡中“监视”部分下的“指标”选项卡。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门指南](../../azure-monitor/essentials/metrics-getting-started.md)。 Azure 区块链服务的完整指标位于“Azure 区块链服务标准指标”命名空间中。

添加筛选器或拆分指标时，可以使用“节点”维度，它基本上提供每个事务节点的指标和 ABS 成员的验证器节点。

## <a name="analyze-log-data"></a>分析日志数据

你可以在“日志搜索”栏中输入下面这些查询，以帮助监视 Azure 区块链服务。 这些查询使用[新语言](../../azure-monitor/logs/log-query-overview.md)。

若要查询区块链应用程序日志中的错误情况，请使用以下查询：

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

若要查询区块链代理日志中的错误情况，请使用以下查询  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
可以使用 Azure 日志中提供的时间筛选器来筛选特定时间范围内的查询。

## <a name="monitor-azure-blockchain-service-data-reference"></a>监视 Azure 区块链服务数据引用  

本文提供了对所收集日志和指标数据的引用，用于分析 Azure 区块链服务的性能和可用性。  

### <a name="resource-logs"></a>资源日志

所有资源日志都共享一个顶级公共架构，该架构具有几种特定于区块链服务的独特属性。 可以参阅[顶级资源日志架构](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)一文，下面涵盖了 Azure 区块链服务特定属性的详细信息  

下表列出了在 Azure Monitor 日志或 Azure 存储中收集的 Azure 区块链代理日志的属性。  


| 属性名称  | 说明 |
|:---|:---|
| time | 操作发生时的日期和时间 (UTC)。 |
| resourceID  | 为其启用了日志的 Azure 区块链服务资源。  |
| category  |对于 Azure 区块链服务，可能的值为“Proxylogs”和“Applicationlogs” 。 |
| operationName  | 此事件表示的操作的名称。   |
| 日志级别  | 默认情况下，Azure 区块链服务启用“信息”日志级别。   |
| NodeLocation  | 在其中部署区块链成员的 Azure 区域。  |
| BlockchainNodeName  | 在其上执行操作的 Azure 区块链服务成员的节点名称。   |
| EthMethod  | Quorum 中由基础区块链协议调用的方法可以是 eth_sendTransactions、eth_getBlockByNumber 等。  |
| Agent  | 代表用户进行操作的用户代理（如 web 浏览器 Mozilla、Microsoft Edge 等）。值的示例包括“Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77”  |
| 代码   | HTTP 错误代码。 通常，4XX 和 5XX 是错误情况。  |
| NodeHost  | 节点的 DNS 名称。   |
| RequestMethodName | 调用 HTTP 方法时，此处的可能值为用于创建成员的 PUT、用于获取现有成员详细信息的 GET、用于删除成员的 DELETE，以及用于更新成员的 PATCH。   |
| BlockchainMemberName  | 用户提供的 Azure 区块链服务成员名称。  |
| 联盟 | 用户提供的联盟名称。   |
| Remote  | 发出请求的客户端的 IP。  |
| RequestSize  | 请求的大小（以字节为单位）。  |
| RequestTime  | 请求持续时间（以毫秒为单位）。|




下表列出了 Azure 区块链应用程序日志的属性。


| 属性名称  | 说明 |
|:---|:---|
| time | 操作发生时的日期和时间 (UTC)。 |
| resourceID  | 为其启用了日志的 Azure 区块链服务资源。|
| category  |对于 Azure 区块链服务，可能的值为“Proxylogs”和“Applicationlogs” 。  |
| operationName  | 此事件表示的操作的名称。   |
| 日志级别  | 默认情况下，Azure 区块链服务启用“信息”日志级别。   |
| NodeLocation  | 在其中部署区块链成员的 Azure 区域。  |
| BlockchainNodeName  | 在其上执行操作的 Azure 区块链服务成员的节点名称。   |
| BlockchainMessage    | 此字段将包含作为纯数据日志的区块链应用程序日志。 对于 ABS-Quorum，此字段将包括 Quorum 日志。 它包含有关信息、错误、警告的日志条目类型的信息，以及一条字符串，其中提供了有关所执行操作的详细信息。   |
| TenantID    | Azure 区块链服务的区域特定租户。 此字段的格式为 https://westlake-rp-prod.<region>.cloudapp.azure.com，其中区域指定所部署成员的 Azure 区域。       |
| SourceSystem   | 系统将填充日志，在本例中为 Azure。    |



### <a name="metrics"></a>指标

下表列出了为 Azure 区块链服务收集的平台指标。 所有指标都存储在“Azure 区块链服务标准指标”命名空间中。

有关 Azure Monitor 支持的所有指标（包括 Azure 区块链服务）的列表，请参阅 [Azure Monitor 支持的指标](../../azure-monitor/essentials/metrics-supported.md)。

### <a name="blockchain-metrics"></a>区块链指标

下表指定为 Azure 区块链服务成员资源收集的区块链指标的列表。


| 指标名称 | 计价单位  |  聚合类型| 说明   |
|---|---|---|---|
| 挂起的事务数   | Count  |  平均值 | 正在等待挖掘的事务数。   |
| 已处理的块数   | 计数  | Sum  |  每个时间间隔内处理的块数。 当前块大小为 5 秒，因此，每个节点一分钟内将处理 12 个块，5 分钟内处理 60 个块。   |
|已处理的事务数    | 计数  | Sum  | 块中处理的事务数。    |
|已排队的事务数    |  Count | 平均值  | 无法立即挖掘的事务数。 这可能是因为它们未按顺序到达，而未来的事务正在等待前一个事务到达。 也可以是两个事务，这两个事物具有只使用一次 (nonce) 的相同数字和相同 gas 值，因此无法挖掘第二个事务。   |

### <a name="connection-metrics"></a>连接指标  

下表列出了为 Azure 区块链服务成员资源收集的不同连接指标。 这些是 NGINX 代理指标。


| 指标名称 | 计价单位  |  聚合类型| 说明 |
|---|---|---|---|
| 已接受的连接数   | 计数  |  Sum | 已接受的客户端连接总数。   |
| 活动连接数  | Count  | 平均值  |  当前活动客户端连接数，包括等待中的连接数。    |
|已处理的连接数    | 计数  | Sum  | 已处理的连接总数。 通常，参数值与已接受的连接数相同，除非已达到某些资源的限制值。     |
|已处理的请求数     |  计数 | Sum  | 客户端请求的总数。  |


### <a name="performance-metrics"></a>性能指标

下表列出了为 Azure 区块链成员资源的每个节点收集的性能指标。  


| 指标名称 | 计价单位  |  聚合类型| 说明   |
|---|---|---|---|
| CPU 使用率百分比   | 百分比  |  最大值 | CPU 使用率的百分比。     |
| IO 读取字节数   | 千字节   | Sum  |  区块链成员资源的所有节点上 IO 读取字节数的总和。      |
|IO 写入字节数     | 千字节   | Sum  | 区块链成员资源的所有节点上 IO 写入字节数的总和。     |
|内存限制       |  千兆字节   | 平均值    | 每个节点上区块链进程的最大可用内存。 |
|内存用量     | 千兆字节  |  平均值 | 所有节点上平均使用的内存量。  |
| 内存使用率百分比     | 百分比   | 平均值  |  所有节点上平均使用的内存百分比。       |
|存储使用率      | 千兆字节   | 平均值  | 所有节点上平均使用的存储空间 (GB)。       |


## <a name="next-steps"></a>后续步骤

了解有关[区块链数据管理器](./data-manager.md)的详细信息，以捕获区块链数据并将其转换为 Azure 事件网格。
