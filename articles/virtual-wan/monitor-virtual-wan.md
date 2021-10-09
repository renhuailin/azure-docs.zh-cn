---
title: 监视 Azure 虚拟 WAN
description: 通过 Azure Monitor 了解 Azure 虚拟 WAN 日志和指标。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: d1ac031b79372987561651044e81da2e3d2d2779
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636467"
---
# <a name="monitoring-virtual-wan"></a>监视虚拟 WAN

你可以使用 Azure Monitor 监视 Azure 虚拟 WAN。 虚拟 WAN 是一个网络服务，其中整合了多种网络、安全和路由功能，提供单一操作界面。 虚拟 WAN VPN 网关、ExpressRoute 网关和 Azure 防火墙都具有通过 Azure Monitor 提供的日志记录和指标。 

本文讨论了可通过门户获得的指标和诊断。 指标是能够为近实时方案提供支持的轻型数据，因此，它们特别适合用于警报和快速检测问题。

### <a name="monitoring-secured-hub-azure-firewall"></a>监视受保护的中心（Azure 防火墙） 

如果已选择使用 Azure 防火墙保护虚拟中心，则可访问以下链接了解相关的日志和指标：[Azure 防火墙日志和指标](../firewall/logs-and-metrics.md)。
可以使用 Azure 防火墙日志和指标监视受保护的中心。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。
对于保护并转换为安全中心的每个 Azure 虚拟 WAN，都会在该中心所在的资源组中创建一个显式防火墙资源对象。 

:::image type="content" source="./media/monitor-virtual-wan/firewall-resources-portal.png" alt-text="屏幕截图显示 vWAN 中心资源组中的防火墙资源。":::

诊断和日志记录配置必须通过访问“诊断设置”选项卡完成：

:::image type="content" source="./media/monitor-virtual-wan/firewall-diagnostic-settings.png" alt-text="屏幕截图显示防火墙诊断设置。":::


## <a name="metrics"></a>指标

Azure Monitor 中的指标是数字值，用于描述系统某些方面在特定时间的情况。 指标每分钟收集一次，可用于警报，因为可对其频繁采样。 可以使用相对简单的逻辑快速激发警报。

### <a name="site-to-site-vpn-gateways"></a>站点到站点 VPN 网关

Azure 站点到站点 VPN 网关提供以下指标：

#### <a name="tunnel-packet-drop-metrics"></a>隧道数据包丢弃指标
| 指标 | 说明|
| --- | --- |
| **隧道传出数据包丢弃计数** | 隧道丢弃的传出数据包计数。|
| **隧道传入数据包丢弃计数** | 隧道丢弃的传入数据包计数。|
| **隧道 NAT 数据包丢包数** | 丢包类型和 NAT 规则列出的隧道丢弃 NAT 处理数据包数|
| **隧道流出 TS 不匹配数据包丢弃** | 因隧道的流量选择器不匹配而导致的传出数据包丢弃计数。|
| **隧道流入 TS 不匹配数据包丢弃** | 因隧道的流量选择器不匹配而导致的传入数据包丢弃计数。|

#### <a name="ipsec-metrics"></a>IPSEC 指标
| 指标 | 描述|
| --- | --- |
| 隧道 MMSA 计数 | 创建或删除的 MMSA 数。|
| 隧道 QMSA 计数 | 创建或删除的 IPSEC QMSA 数。|

#### <a name="routing-metrics"></a>路由指标
| 指标 | 描述|
| --- | --- |
| **BGP 对等方状态** | 每个对等机和每个实例的 BGP 连接状态。|
| **播发的 BGP 路由数** | 每个对等机和每个实例播发的路由数。|
| **获知的 BGP 路由数** | 每个对等机和每个实例学习的路由数。|
| VNET 地址前缀计数 | 网关使用/播发的 VNET 地址前缀数。|

可以通过选择“应用拆分”并选择首选值来查看每个对等机和实例的指标。 

#### <a name="traffic-flow-metrics"></a>流量流指标
| 指标 | 说明|
| --- | --- |
| 网关带宽 | 网关站点到站点的平均聚合带宽（字节/秒）。|
| **隧道带宽** | 隧道带宽平均值（字节/秒）。|
| **隧道流出字节** | 隧道的传出字节数。 |
| **隧道流出数据包** | 隧道的传出数据包计数。 |
| **隧道流入字节** | 隧道的传入字节数。|
| **隧道流入数据包** | 隧道的传入数据包计数。|
| **隧道峰值 PPS** | 过去一分钟内每个链接连接每秒的数据包数。|
| **隧道流计数** | 每个链接连接创建非重复流的数量。|

### <a name="point-to-site-vpn-gateways"></a>点到站点 VPN 网关

Azure 点到站点 VPN 网关提供以下指标：

| 指标 | 说明|
| --- | --- |
| **网关 P2S 带宽** | 网关点到站点的平均聚合带宽（字节/秒）。 |
| **P2S 连接计数** |网关的点到站点连接计数。 网关的点到站点连接计数。 为确保在 Azure Monitor 中查看准确的指标，请选择“Sum”作为“P2S 连接计数”的“聚合类型” 。 如果还按“实例”拆分，也可以选择“Max”。 |
| 用户 VPN 路由计数 | VPN 网关上配置的用户 VPN 路由数。 此指标可以分解为“静态”路由和”动态“路由。 

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute 网关

Azure ExpressRoute 网关提供以下指标：

| 指标 | 说明|
| --- | --- |
| **BitsInPerSecond** | 每秒流入 Azure 的位数。|
| **BitsOutPerSecond** | 每秒流出 Azure 的位数。 |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>查看网关指标

以下步骤可帮助你查找和查看指标：

1. 在门户中，导航到具有网关的虚拟中心。

2. 选择“VPN (站点到站点)”以查找站点到站点网关，选择“ExpressRoute”以查找 ExpressRoute 网关，或选择“用户 VPN (点到站点)”以查找点到站点网关。

3. 选择“指标”。

   :::image type="content" source="./media/monitor-virtual-wan/view-metrics.png" alt-text="屏幕截图显示了一个站点到站点 VPN 窗格，其中选择了 Azure Monitor 中的“视图”。":::

4. 在“指标”页上，你可以查看感兴趣的指标。

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="屏幕截图显示了“指标”页面，其中突出显示了类别。":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>诊断日志

### <a name="site-to-site-vpn-gateways"></a>站点到站点 VPN 网关

Azure 站点到站点 VPN 网关提供以下诊断：

| 指标 | 说明|
| --- | --- |
| **网关诊断日志** | 特定于网关的诊断，例如运行状况、配置、服务更新以及其他诊断。|
| **隧道诊断日志** | 这是与 IPsec 隧道相关的日志，例如站点到站点 IPsec 隧道的连接和断开连接事件、协商的 SA、断开连接原因以及其他诊断。|
| **路由诊断日志** | 这是与静态路由、BGP、路由更新以及其他诊断的事件相关的日志。 |
| **IKE 诊断日志** | 用于 IPsec 连接的特定于 IKE 的诊断。 |

### <a name="point-to-site-vpn-gateways"></a>点到站点 VPN 网关

Azure 点到站点 VPN 网关提供以下诊断：

| 指标 | 说明|
| --- | --- |
| **网关诊断日志** | 特定于网关的诊断，例如运行状况、配置、服务更新以及其他诊断。 |
| **IKE 诊断日志** | 用于 IPsec 连接的特定于 IKE 的诊断。|
| **P2S 诊断日志** | 这是用户 VPN（点到站点）P2S 配置和客户端事件。 它们包括客户端连接/断开连接、VPN 客户端地址分配以及其他诊断。|

### <a name="express-route-gateways"></a>Express Route 网关

不支持 Azure 虚拟 WAN 中 Express Route 网关的诊断日志。

### <a name="view-diagnostic-logs-configuration"></a><a name="diagnostic-steps"></a>查看诊断日志配置

以下步骤可帮助你创建、编辑和查看诊断设置：

1. 在门户中，导航到虚拟 WAN 资源，然后在“连接”组中选择“中心”。 

   :::image type="content" source="./media/monitor-virtual-wan/select-hub.png" alt-text="显示 vWAN 门户中的中心选择的屏幕截图。":::

2. 在左侧的“连接”组下，选择要检查诊断的网关：

   :::image type="content" source="./media/monitor-virtual-wan/select-hub-gateway.png" alt-text="显示中心的“连接”部分的屏幕截图。":::

3. 在页面右侧，单击“在 Azure Monitor 中查看”链接到“日志”，然后选择一个选项。 你可以选择发送到 Log Analytics、流式传输到事件中心，或者直接存档到某个存储帐户。

   :::image type="content" source="./media/monitor-virtual-wan/view-hub-gateway-logs.png" alt-text="日志的 Azure Monitor 中的选择视图的屏幕截图。":::

4. 在此页中，可以创建新的诊断设置（“+ 添加诊断设置”）或编辑现有诊断设置（“编辑设置”）。 可以选择将诊断日志发送到 Log Analytics（如下面的示例所示）、流式传输到事件中心、发送到第 3 方解决方案或存档到存储帐户。

    :::image type="content" source="./media/monitor-virtual-wan/select-gateway-settings.png" alt-text="选择诊断日志设置的屏幕截图。":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 示例查询

如果选择将诊断数据发送到 Log Analytics 工作区，则可以使用类似 SQL 的查询（如下面的示例）来检查数据。 有关详细信息，请参阅 [Log Analytics 查询语言](/services-hub/health/log_analytics_query_language)。

以下示例包含一个查询，用于获取站点到站点路由诊断。

`AzureDiagnostics | where Category == "RouteDiagnosticLog"`

根据本文前面部分中所报告的表，根据需要替换 = = 之后的以下值。

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

若要执行查询，必须打开配置为接收诊断日志的 Log Analytics 资源，然后在窗格左侧的“常规”选项卡下选择“日志”：

:::image type="content" source="./media/monitor-virtual-wan/log-analytics-query-samples.png" alt-text="Log Analytics 查询示例。":::

有关 Azure VPN 网关（站点到站点和点到站点）的其他 Log Analytics 查询示例，可以访问页面[使用诊断日志对 Azure VPN 网关进行故障排除](../vpn-gateway/troubleshoot-vpn-with-azure-diagnostics.md)。 对于 Azure 防火墙，提供了一个[工作簿](../firewall/firewall-workbook.md)以简化日志分析。 使用其图形界面，无需手动编写任何 Log Analytics 查询即可调查诊断数据。 

## <a name="activity-logs"></a><a name="activity-logs"></a>活动日志

默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。 可以使用 Azure 活动日志（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作。 

## <a name="next-steps"></a>后续步骤

* 若要了解如何监视 Azure 防火墙日志和指标，请参阅[教程：监视 Azure 防火墙日志](../firewall/firewall-diagnostics.md)。
* 若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](../azure-monitor/essentials/data-platform-metrics.md)。
