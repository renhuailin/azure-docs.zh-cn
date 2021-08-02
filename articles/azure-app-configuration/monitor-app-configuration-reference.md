---
title: 监视 Azure 应用程序配置数据参考
description: 监视应用程序配置时所需的重要参考资料
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: ec6515403574af29c47b03b55eb3cbc42ecd4a7a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968347"
---
# <a name="monitoring-app-configuration-data-reference"></a>监视应用程序配置数据参考

监视应用程序配置收集的数据，可参考本文。 请参阅[监视应用程序配置](monitor-app-configuration.md)，逐步了解如何收集和分析应用程序配置的监视数据。

## <a name="metrics"></a>指标 
资源提供程序和类型：[应用程序配置平台指标](../azure-monitor/essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores)

| 指标 | 计价单位 | 说明 |
|-------|-----| ----- |
| Http 传入请求计数   | 计数 | 传入 Http 请求总数 |
|Http 传入请求持续时间 | 毫秒 | Http 请求的服务器端持续时间 |
| 受限的 Http 请求计数 | 计数 |    受限请求是返回 429 状态代码（请求过多）的 Http 请求 |

有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。


## <a name="metric-dimensions"></a>指标维度
应用程序配置具有以下与指标关联的维度。

| 标准名称 | 维度说明 |
|-------|-----|
| Http 传入请求计数 | Http 请求总计数。 支持的维度是每个请求的 HttpStatusCode 或 AuthenticationScheme。 可以通过 AAD 或 HMAC 身份验证筛选 AuthenticationScheme。   |
| Http 传入请求持续时间 | 每个请求的服务器端持续时间。 支持的维度是使用每个请求的 HttpStatusCode 或 AuthenticationScheme。 可以通过 AAD 或 HMAC 身份验证筛选 AuthenticationScheme。 |
| 受限的 Http 请求计数 | 此指标没有任何维度 |

 有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

## <a name="resource-logs"></a>资源日志
本部分列出了为应用程序配置收集的资源日志的类别类型。 

| 资源日志类型 | 更多信息|
|-------|-----|
| HttpRequest | [应用程序配置资源日志类别信息](../azure-monitor/essentials/resource-logs-categories.md) |

有关更多信息，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。
 
## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

本节介绍与应用程序配置相关并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志 Kusto 表。

|资源类型 | 说明 |
|-------|-----|
| [AACHttpRequest](/azure/azure-monitor/reference/tables/aachttprequest) | 发送到所选应用程序配置资源的每个 Http 请求的条目。 |
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) | Azure 活动日志中的条目，可用于深入了解 Azure 中发生的任何订阅级别或管理组级别事件。 |

有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

### <a name="diagnostics-tables"></a>诊断表

应用程序配置使用 [AACHttpRequest 表](/azure/azure-monitor/reference/tables/aachttprequest)存储资源日志信息。

Http 请求

|属性 | 类型 | 说明 |
|-------|-----| ----- |
|Category   |string |事件的日志类别，始终为 HttpRequest。 
|ClientIPAddress |  字符串| 发出请求的客户端的 IP 地址。
|ClientRequestId|   字符串| 客户端提供的请求 ID。
|CorrelationId| 字符串| 关联日志的 GUID。
|DurationMs|    int |操作持续时间，以毫秒为单位。
|Method 字符串| HTTP| Http 请求方法（get 或 post）
|RequestId| 字符串| 服务器生成的唯一请求 ID。
|RequestLength| int |HTTP 请求的长度（以字节为单位）。
|RequestURI|    字符串| 请求的 URI，可以包含密钥和标签名称。 
|_ResourceId|   字符串| 与记录关联的资源的唯一标识符
|ResponseLength|    int|    HTTP 响应的长度（以字节为单位）。
|SourceSystem| 字符串|  
|StatusCode|    int |请求的 HTTP 状态代码。
|TenantId|  字符串  |请求的 WorkspaceId。 
|TimeGenerated| datetime|   生成请求日志时的时间戳 (UTC)
|类型   |字符串|    表的名称
|UserAgent| 字符串| 客户端提供的用户代理。


## <a name="see-also"></a>另请参阅

* 有关监视 Azure 应用程序配置的说明，请参阅[监视 Azure 应用程序配置](monitor-app-configuration.md)。
* 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。