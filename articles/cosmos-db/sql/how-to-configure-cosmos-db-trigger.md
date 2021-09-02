---
title: 适用于 Cosmos DB 的 Azure Functions 触发器的高级配置
description: 了解如何配置适用于 Cosmos DB 的 Azure Functions 触发器使用的日志记录和连接策略
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 44e738cc50feb66fc8806236be388b6baa18a17f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113502"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>如何配置适用于 Cosmos DB 的 Azure Functions 触发器的日志记录和连接
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文介绍使用适用于 Cosmos DB 的 Azure Functions 触发器时可设置的高级配置选项。

## <a name="enabling-trigger-specific-logs"></a>启用触发器特定日志

适用于 Cosmos DB 的 Azure Functions 触发器在内部使用[更改源处理器库](change-feed-processor.md)，该库会生成一组运行状况日志，而这些日志又可用于监视内部操作以[进行故障排除](./troubleshoot-changefeed-functions.md)。

运行状况日志描述了在负载均衡方案或初始化期间尝试执行操作时，适用于 Cosmos DB 的 Azure Functions 触发器的行为。

### <a name="enabling-logging"></a>启用日志记录

若要在使用适用于 Cosmos DB 的 Azure Functions 触发器时启用日志记录，请在 Azure Functions 项目或 Azure Functions 应用中找到 `host.json` 文件，并[配置所需的日志记录级别](../../azure-functions/functions-monitoring.md#log-levels-and-categories)。 按以下示例所示为 `Host.Triggers.CosmosDB` 启用跟踪：

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

使用更新的配置部署 Azure 函数后，将在跟踪中看到适用于 Cosmos DB 的 Azure Functions 触发器日志。 可以在类别 `Host.Triggers.CosmosDB` 下查看所配置的日志记录提供程序中的日志。

### <a name="query-the-logs"></a>查询日志

在 [Azure Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) 中运行以下查询，以查询适用于 Cosmos DB 的 Azure Functions 触发器生成的日志：

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>配置连接策略

有两种连接模式 - Direct（直接）模式和 Gateway（网关）模式。 若要详细了解这些连接模式，请参阅[连接模式](sql-sdk-connection-modes.md)一文。 默认情况下，**网关** 用于在适用于 Cosmos DB 的 Azure Functions 触发器中建立所有连接。 但是，对于性能驱动的方案，它可能不是最佳选项。

### <a name="changing-the-connection-mode-and-protocol"></a>更改连接模式和协议

有两个密钥配置设置可用于配置客户端连接策略 – **连接模式** 和 **连接协议**。 可以更改 Cosmos DB 的 Azure Functions 触发器和所有 [Azure Cosmos DB 绑定](../../azure-functions/functions-bindings-cosmosdb-v2-output.md)使用的默认连接模式与协议。 若要更改默认设置，需要在 Azure Functions 项目或 Azure Functions 应用中找到 `host.json` 文件，并添加以下[附加设置](../../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)：

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

其中，`connectionMode` 必须包含所需的连接模式（Direct 或 Gateway），`protocol` 必须包含所需的连接协议（Tcp 或 Https）。 

如果 Azure Functions 项目正在使用 Azure Functions V1 运行时（配置名称略有不同），则应使用 `documentDB` 而不是 `cosmosDB`：

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> 在消耗计划中托管函数应用时，每个实例可以维护的套接字连接数量都有限制。 使用 Direct/TCP 模式时，根据设计会创建更多的连接，这可能会达到[消耗计划限制](../../azure-functions/manage-connections.md#connection-limit)，在这种情况下，可使用 Gateway 模式或者改为在[高级计划](../../azure-functions/functions-premium-plan.md)或[专用（应用服务）计划](../../azure-functions/dedicated-plan.md)中托管函数应用。

## <a name="next-steps"></a>后续步骤

* [Azure Functions 中的连接限制](../../azure-functions/manage-connections.md#connection-limit)
* 在 Azure Functions 应用程序中[启用监视](../../azure-functions/functions-monitoring.md)。
* 了解使用适用于 Cosmos DB 的 Azure Functions 触发器时如何[诊断和排查常见问题](./troubleshoot-changefeed-functions.md)。