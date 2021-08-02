---
title: 使用诊断日志排查 Azure VPN 网关问题
description: 了解如何使用诊断日志排查 Azure VPN 网关问题。
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: cc4165a2636169c34b14795a8c2298a0f9917916
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652128"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>使用诊断日志对 Azure VPN 网关进行故障排除

本文将帮助你了解可用于 VPN 网关诊断的不同日志，并学习如何使用它们来有效地排查 VPN 网关问题。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Azure 中提供以下日志：

|名称* | 说明* |
|---        | ---               |
|**GatewayDiagnosticLog** | 包含网关配置事件、主要更改和维护事件的诊断日志。 |
|**TunnelDiagnosticLog** | 包含隧道状态更改事件。 在适用的情况下，隧道连接/断开连接事件将汇总状态更改的原因。 |
|**RouteDiagnosticLog** | 记录网关上静态路由和 BGP 事件发生的更改。 |
|**IKEDiagnosticLog** | 记录网关上的 IKE 控制消息和事件。 |
|**P2SDiagnosticLog** | 记录网关上的点到站点控制消息和事件。 |

请注意，这些表中有几列可用。 在本文中，我们将仅介绍最相关的列，便于你更轻松地使用日志。

## <a name="set-up-logging"></a><a name="setup"></a>设置日志记录

若要了解如何使用 Azure Log Analytics 从 Azure VPN 网关设置诊断日志事件，请查看[从 VPN 网关设置诊断日志事件的警报](./vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

在 GatewayDiagnosticLog 表中审核配置更改。 你执行的更改可能几分钟后才会在日志中反映出来。

这里有一个示例查询可供参考。

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

此查询是关于 GatewayDiagnosticLog 的，它将显示多个列。

|名称* | 说明* |
|---        | ---               |
|**TimeGenerated** | 每个事件的时间戳（UTC 时区）。|
|**OperationName** |发生的事件。 它可以是 SetGatewayConfiguration、SetConnectionConfiguration、HostMaintenanceEvent、GatewayTenantPrimaryChanged、MigrateCustomerSubscription、GatewayResourceMove、ValidateGatewayConfiguration 中的任意一个。|
|**消息** | 正在发生的操作的详细信息，并列出了成功/失败结果。|

下面的示例演示了在应用新配置时记录的活动：

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="GatewayDiagnosticLog 中所示的“设置网关”操作的示例。":::


请注意，每次在 VPN 网关或本地网关上修改某个配置时，都会记录 SetGatewayConfiguration。
通过将 GatewayDiagnosticLog 表中的结果与 TunnelDiagnosticLog 表的结果交叉引用，可帮助我们确定在更改配置或进行维护时隧道连接是否失败 。 如果失败，我们就找到了可能的根本原因。

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

TunnelDiagnosticLog 表非常适合用于检查隧道的历史连接状态。

这里有一个示例查询可供参考。

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

此查询是关于 TunnelDiagnosticLog 的，它将显示多个列。


|名称* | 说明* |
|---        | ---               |
|**TimeGenerated** | 每个事件的时间戳（UTC 时区）。|
|**OperationName** | 发生的事件。 它可以是 TunnelConnected 或 TunnelDisconnected 。|
| Instance\_s | 触发事件的网关角色实例。 它可以是 GatewayTenantWorker\_IN\_0 或 GatewayTenantWorker\_IN\_1（它们是网关的两个实例的名称）。|
| **资源** | 指示 VPN 网关的名称。 |
| **ResourceGroup** | 指示网关所在的资源组。|


示例输出：

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="TunnelDiagnosticLog 中所示的隧道连接事件的示例。":::


TunnelDiagnosticLog 很适合用于排查有关 VPN 意外断开连接的历史事件问题。 它的轻型特性让你能够轻松分析几天内较大的时间范围。
只有在确定断开连接的时间戳之后，才能切换到 IKEdiagnosticLog 表的更详细分析，来更深入地了解断开连接的原因（如果这些断开连接与 IPsec 有关）。


一些故障排除技巧：
- 如果在一个网关实例上看到断开连接事件，且几秒钟后在不同的网关实例上看到连接事件，则表示正在进行网关故障转移。 这通常是由于网关实例上的维护而导致的预期行为。 若要详细了解此行为，请查看[关于 Azure VPN 网关冗余](./vpn-gateway-highlyavailable.md#activestandby)。
- 如果你有意在 Azure 端上运行网关重置，则会观察到此行为，这将导致重启活动网关实例。 若要详细了解此行为，请查看[重置 VPN 网关](./reset-gateway.md)。
- 如果在一个网关实例上看到断开连接事件，且几秒钟后在同一网关实例上看到连接事件，则可能是由于网络问题导致 DPD 超时，或者本地设备错误地发送了断开连接事件。

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

RouteDiagnosticLog 表跟踪静态修改的路由或通过 BGP 接收的路由的活动。

这里有一个示例查询可供参考。

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

此查询是关于 RouteDiagnosticLog 的，它将显示多个列。

|名称* | 说明* |
|---        | ---               |
|**TimeGenerated** | 每个事件的时间戳（UTC 时区）。|
|**OperationName** | 发生的事件。 可以是 StaticRouteUpdate、BgpRouteUpdate、BgpConnectedEvent、BgpDisconnectedEvent 中的任意一个。|
| **消息** | 正在发生的操作的详细信息。|

输出将显示有关已连接/断开连接的 BGP 对等体以及已交换路由的有用信息。

示例：


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="RouteDiagnosticLog 中所示的 BGP 路由交换活动的示例。":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

IKEDiagnosticLog 表为 IKE/IPsec 提供详细的调试记录。 在排查断开连接故障或连接 VPN 方案失败时，这非常有用。

这里有一个示例查询可供参考。

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

此查询是关于 IKEDiagnosticLog 的，它将显示多个列。


|名称* | 说明* |
|---        | ---               |
|**TimeGenerated** | 每个事件的时间戳（UTC 时区）。|
| **RemoteIP** | 本地 VPN 设备的 IP 地址。 在实际情况中，按相关本地设备的 IP 地址进行筛选将很有用（如果有多个 IP 地址）。 |
|**LocalIP** | 正在进行故障排除的 VPN 网关的 IP 地址。 在实际情况中，按相关 VPN 网关的 IP 地址进行筛选将很有用（如果订阅中有多个 IP 地址）。 |
|**事件** | 包含用于故障排除的诊断消息。 它们通常以关键字开头，指的是 Azure 网关执行的操作：\[SEND\] 表示由 Azure 网关发送的 IPSec 数据包引发的事件。  \[RECEIVED\] 表示由于从本地设备接收到的数据包而引发的事件。  \[LOCAL\] 表示 Azure 网关在本地执行的操作。 |


请注意，RemoteIP、LocalIP 和 Event 列并没有出现在 AzureDiagnostics 数据库上的原始列列表中，而是通过分析“Message”列的输出来添加到查询中，从而简化其分析。

疑难解答提示:

- 若要确定 IPSec 协商的开始，需要找到初始 SA\_INIT 消息。 此类消息可由隧道的任一端发送。 发送第一个数据包的一方在 IPsec 术语中称为“发起程序”，另一方则称为“响应方”。 第一个 SA\_INIT 消息始终是 rCookie = 0 的消息。

- 如果 IPsec 隧道未能建立，Azure 将每几秒继续重试一次。 出于此原因，在 IKEdiagnosticLog 上排查“VPN 关闭”问题非常方便，因为你无需等待特定时间就可重现问题。 而且在理论上，每次尝试时失败都始终相同，因此你可随时放大到一个失败的协商“示例”。

- SA\_INIT 包含对等机要用于此 IPSec 协商的 IPSec 参数。 官方文档   
[默认的 IPsec/IKE 参数](./vpn-gateway-about-vpn-devices.md#ipsec)列出了 Azure 网关支持的 IPsec 参数以及默认设置。


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

VPN 诊断的最后一个可用表是 P2SDiagnosticLog。 此表跟踪点到站点的活动。

这里有一个示例查询可供参考。

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

此查询是关于 P2SDiagnosticLog 的，它将显示多个列。

|名称* | 说明* |
|---        | ---               |
|**TimeGenerated** | 每个事件的时间戳（UTC 时区）。|
|**OperationName** | 发生的事件。 将为 P2SLogEvent。|
| **消息** | 正在发生的操作的详细信息。|

输出将显示网关应用的所有点到站点设置，以及现有的 IPsec 策略。

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="P2SDiagnosticLog 中所示的点到站点连接的示例。":::

此外，每当客户端通过 IKEv2 或 OpenVPN 点到站点连接时，该表都将记录数据包活动、EAP/RADIUS 对话和用户成功/失败结果。

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="P2SDiagnosticLog 中所示的 EAP 身份验证示例。":::

## <a name="next-steps"></a>后续步骤

若要基于隧道资源日志配置警报，请参阅[基于 VPN 网关资源日志设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。