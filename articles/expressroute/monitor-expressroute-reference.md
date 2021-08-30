---
title: 监视 ExpressRoute 数据参考
description: 监视 ExpressRoute 时所需的重要参考资料
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: cd5a4ef0196843069d50f333089f8bffca956acc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289805"
---
# <a name="monitoring-expressroute-data-reference"></a>监视 ExpressRoute 数据参考

本文提供了对所收集日志和指标数据的引用，用于分析 ExpressRoute 的性能和可用性。
有关收集和分析 ExpressRoute 监视数据的详细信息，请参阅[监视 ExpressRoute](monitor-expressroute.md)。

## <a name="metrics"></a>指标

本部分列出了为 ExpressRoute 自动收集的所有平台指标。 有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。

| 指标类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| ExpressRoute 线路 | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
| ExpressRoute 线路对等互连 | [Microsoft.Network/expressRouteCircuits/peerings](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuitspeerings) |
| ExpressRoute 网关 | [Microsoft.Network/expressRouteGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutegateways) |
| ExpressRoute Direct | [Microsoft.Network/expressRoutePorts](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |

>[!NOTE]
> 只有在至少建立了一个 Global Reach 连接的情况下，才能使用 *GlobalGlobalReachBitsInPerSecond* 和 *GlobalGlobalReachBitsOutPerSecond*。
>

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

ExpressRoute 具有以下与指标关联的维度。

### <a name="dimension-for-expressroute-circuit"></a>ExpressRoute 线路的维度

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **PeeringType** | 配置的对等互连的类型。 支持的值为 Microsoft 和专用对等互连。 |
| **对等互连** | 支持的值为 Primary 和 Secondary。 |
| PeeredCircuitSkey | 使用 Global Reach 连接的远程 ExpressRoute 线路服务密钥。 |

### <a name="dimension-for-expressroute-gateway"></a>ExpressRoute 网关的维度

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **roleInstance** | 网关实例。 每个 ExpressRoute 网关由多个实例组成，支持的值为 GatewayTenantWork_IN_X（其中 X 的最小值为 0，最大值为网关实例数 - 1）。 |

### <a name="dimension-for-express-direct"></a>ExpressRoute Direct 的维度

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **链接**。 | 物理链接。 每个 ExpressRoute Direct 端口对由两个物理链接组成以实现冗余，支持的值为 link1 和 link2。 |

## <a name="resource-logs"></a>资源日志

本部分列出了可为 ExpressRoute 收集的资源日志类型。 

|资源日志类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| ExpressRoute 线路 | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkexpressroutecircuits) |

有关参考，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

Azure ExpressRoute 使用 Azure Monitor 日志中的 Kusto 表。 可以使用 Log Analytics 查询这些表。 有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

## <a name="activity-log"></a>活动日志

下表列出了可能在活动日志中创建的与 ExpressRoute 相关的操作。

| 操作 | 说明 |
|:---|:---|
| 所有管理操作 | 所有管理操作，包括创建、更新和删除 ExpressRoute 线路。 |
| 创建或更新 ExpressRoute 线路 | 已创建或已更新 ExpressRoute 线路。 |
| 删除 ExpressRoute 线路 | 已删除 ExpressRoute 线路。|

有关活动日志条目架构的详细信息，请参阅[活动日志架构](../azure-monitor/essentials/activity-log-schema.md)。

## <a name="schemas"></a>架构

有关顶级诊断日志架构的详细说明，请参阅 [Azure 诊断日志支持的服务、架构和类别](../azure-monitor/essentials/resource-logs-schema.md)。

通过 Log Analytics 查看任何指标时，输出将包含以下列：

|**列**|类型|**说明**|
| --- | --- | --- |
|TimeGrain|字符串|PT1M（每分钟推送一次指标值）|
|Count|real|通常等于 2（每个 MSEE 每分钟推送一个指标值）|
|最低配置|real|两个 MSEE 推送的两个指标值中的最小值|
|最大值|real|两个 MSEE 推送的两个指标值中的最大值|
|平均值|real|等于 (最小值 + 最大值)/2|
|总计|real|来自两个 MSEE 的两个指标值的总和（所查询指标的需关注的主要值）|

## <a name="see-also"></a>另请参阅

- 有关如何监视 Azure ExpressRoute 的说明，请参阅[监视 Azure ExpressRoute](monitor-expressroute.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。