---
title: 排查 Java v4 SDK 的 Azure Cosmos DB HTTP 408 或请求超时问题
description: 了解如何诊断和修复 Java v4 SDK 的 Java SDK 请求超时异常。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 718a0aa96579b3234bfa242408565246602762a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113197"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>诊断和排查 Azure Cosmos DB Java v4 SDK 请求超时异常
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

如果 SDK 在超时限制发生之前未能完成请求，则会出现 HTTP 408 错误。

## <a name="troubleshooting-steps"></a>疑难解答步骤
下面的列表包含请求超时异常的已知原因和解决方案。

### <a name="existing-issues"></a>现有问题
如果看到请求被卡住的时间更长或超时更频繁，请将 Java v4 SDK 升级到最新版本。 注意：强烈建议使用 4.7.0 及更高版本。 请查看 [Java v4 SDK 发行说明](sql-api-sdk-java-v4.md)，了解更多详细信息。

### <a name="high-cpu-utilization"></a>CPU 利用率较高
最常见的情况是 CPU 利用率较高。 为实现最佳延迟，CPU 利用率应大约为 40%。 将时间间隔设为 10 秒来监视最大 CPU 利用率（而不是平均利用率）。 对于可能会为单个查询执行多个连接的跨分区查询，更常见的情况是出现 CPU 峰值。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。

### <a name="connection-throttling"></a>连接限制
连接限制可能会因主机上的连接限制或 Azure SNAT (PAT) 端口耗尽而出现。

### <a name="connection-limit-on-a-host-machine"></a>主机上的连接限制
某些 Linux 系统（例如 Red Hat）的打开文件总数存在上限。 Linux 中的套接字以文件形式实现，因此，此上限也限制了连接总数。 运行以下命令。

```bash
ulimit -a
```

#### <a name="solution"></a>解决方案：
允许打开文件的最大数（标识为“nofile”）至少需要是 10,000 或以上。 有关详细信息，请参阅 Azure Cosmos DB Java SDK v4 [性能提示](performance-tips-java-sdk-v4-sql.md)。

### <a name="socket-or-port-availability-might-be-low"></a>套接字或端口可用性可能较低
在 Azure 中运行时，使用 Java SDK 的客户端可能会遇到 Azure SNAT (PAT) 端口耗尽的情况。

#### <a name="solution-1"></a>解决方案 1：
如果正在 Azure VM 上运行，请按照 [SNAT 端口耗尽指南](troubleshoot-java-sdk-v4-sql.md#snat)操作。

#### <a name="solution-2"></a>解决方案 2：
如果正在 Azure 应用服务上运行，请按照[连接错误故障排除指南](../../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)和[使用应用服务诊断](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)操作。

#### <a name="solution-3"></a>解决方案 3：
如果正在 Azure Functions 上运行，请验证是否遵循 [Azure Functions 建议](../../azure-functions/manage-connections.md#static-clients)，即是否为所有涉及的服务（包括 Azure Cosmos DB）维护单一实例或静态客户端。 根据函数应用托管的类型和大小查看[服务限制](../../azure-functions/functions-scale.md#service-limits)。

#### <a name="solution-4"></a>解决方案 4：
如果使用 HTTP 代理，请确保它支持 SDK `GatewayConnectionConfig` 中配置的连接数。 否则将遇到连接问题。

### <a name="create-multiple-client-instances"></a>创建多个客户端实例
创建多个客户端实例可能会导致连接争用和超时问题。

#### <a name="solution-1"></a>解决方案 1：
按照[性能提示](performance-tips-java-sdk-v4-sql.md#sdk-usage)操作，并在整个应用程序中使用单个 CosmosClient 实例。

#### <a name="solution-2"></a>解决方案 2：
如果单一实例 CosmosClient 没法包含在应用程序中，建议在 CosmosClient 中通过此 API `connectionSharingAcrossClientsEnabled(true)` 跨多个 Cosmos 客户端使用连接共享。 如果同一个 JVM 中有多个 Cosmos 客户端实例与多个 Cosmos 帐户交互，则启用此选项后可在 Cosmos 客户端的实例之间以直接模式进行连接共享（若可行）。 请注意，设置此选项时，将对其他所有客户端实例使用第一个实例化客户端的连接配置（例如套接字超时配置、空闲超时配置）。

### <a name="hot-partition-key"></a>热分区键
Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 存在热分区时，物理分区上的一个或多个逻辑分区键会消耗物理分区的所有请求单位/秒 (RU/s)。 同时，将无法使用其他物理分区上的 RU/s。 故障表现是，所消耗的 RU/s 总数将小于数据库或容器上整体预配的 RU/s，但针对热逻辑分区键仍将显示请求限制 (429s)。 使用[规范化 RU 使用量指标](../monitor-normalized-request-units.md)来查看工作负载是否遇到热分区。 

#### <a name="solution"></a>解决方案：
选择均匀分配请求量和存储的适当分区键。 了解如何[更改分区键](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)。

### <a name="high-degree-of-concurrency"></a>并发度较高
应用程序正在执行高级别的并发，这可能会导致通道上出现争用。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。

### <a name="large-requests-or-responses"></a>大型请求或响应
较大的请求或响应可能导致通道上出现队头阻塞并加剧资源争用（甚至在并发度相对较低的情况下）。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>失败率在 Azure Cosmos DB SLA 范围之内
应用程序应该能够处理暂时性故障，并在必要时重试。 任何 408 异常不会被重试，因为在创建路径时不可能知道服务是否创建了该项。 再次发送相同的项进行创建将导致冲突异常。 用户应用程序业务逻辑可能包含用于处理冲突的自定义逻辑，这会消除现有项的不确定性与来自“创建”重试的冲突。

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>失败率与 Azure Cosmos DB SLA 不符
请联系 [Azure 支持部门](https://aka.ms/azure-support)。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-java-sdk-v4-sql.md)使用 Azure Cosmos DB Java v4 SDK 时遇到的问题。
* 了解 [Java v4](performance-tips-java-sdk-v4-sql.md) 的性能准则。
