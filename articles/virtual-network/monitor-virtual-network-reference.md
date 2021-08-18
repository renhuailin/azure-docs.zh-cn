---
title: 有关监视 Azure 虚拟网络数据的参考
description: 监视 Azure 虚拟网络时所需的重要参考资料
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 2494c0c0cdb2b4a7b06b4cf246f70a46deb8a3a4
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109856"
---
# <a name="monitoring-azure-virtual-network-data-reference"></a>有关监视 Azure 虚拟网络数据的参考

有关收集和分析 Azure 虚拟网络监视数据的详细信息，请参阅[监视 Azure 虚拟网络](monitor-virtual-network.md)。

## <a name="metrics"></a>指标

本部分列出了为 Azure 虚拟网络自动收集的所有平台指标。  

| 指标类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 虚拟网络 | [Microsoft.Network/virtualNetworks](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkvirtualnetworks) |
| Linux | [Microsoft.Network/networkInterfaces](../azure-monitor/essentials/metrics-supported.md#microsoftnetworknetworkinterfaces) |
| 公共 IP 地址 | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) |
| NAT 网关 | [Microsoft.Network/natGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)

有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

Azure 虚拟网络具有以下与其指标关联的维度。

### <a name="dimensions-for-nat-gateway"></a>NAT 网关的维度

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| 方向（传出 - 传入） | 流量的流动方向。 支持的值为“传入”和“传出”。 |
| **协议** | 传输协议的类型。 支持的值为“TCP”和“UDP”。 |

## <a name="resource-logs"></a>资源日志

本部分列出了可为 Azure 虚拟网络中使用的资源收集的资源日志类型。 

有关参考，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。

|资源日志类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 网络安全组 | [Microsoft.Network/networksecuritygroups](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworknetworksecuritygroups) |
| 公共 IP 地址 | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkpublicipaddresses) |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

本节介绍与 Azure 虚拟网络相关的并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志 Kusto 表。 

|资源类型 | 说明 |
|-------|-----|
| 虚拟网络 | [Microsoft.Network/virtualNetworks](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-networks) |
| Linux | [Microsoft.Network/networkInterface](/azure/azure-monitor/reference/tables/tables-resourcetype#network-interfaces) |
| 公共 IP 地址 | [Microsoft.Network/publicIP](/azure/azure-monitor/reference/tables/tables-resourcetype#public-ip-addresses) |

### <a name="diagnostics-tables"></a>诊断表

**虚拟网络**

Azure 虚拟网络没有诊断日志。

## <a name="activity-log"></a>活动日志

下表列出了可能在活动日志中创建的与 Azure 虚拟网络相关的操作。

| 操作 | 说明 |
|:---|:---|
| 所有管理操作 | 所有管理操作，包括创建、更新和删除 Azure 虚拟网络。 |
| 创建或更新虚拟网络 | 已创建或更新虚拟网络。 |
| 删除虚拟网络 | 已删除虚拟网络。|

有关活动日志条目架构的详细信息，请参阅[活动日志架构](../azure-monitor/essentials/activity-log-schema.md)。

## <a name="see-also"></a>另请参阅

- 有关监视 Azure 虚拟网络的介绍，请参阅[监视 Azure 虚拟网络](monitor-virtual-network.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
