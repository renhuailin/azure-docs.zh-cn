---
title: 监视 Azure VPN 网关
description: 了解如何使用 Azure Monitor 查看 VPN 网关指标。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/23/2021
ms.author: alzam
ms.openlocfilehash: a55c4ac84f218b4fe657f02cac9f10a4e9dc4a68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583930"
---
# <a name="monitoring-vpn-gateway"></a>监视 VPN 网关

可以使用 Azure Monitor 监视 Azure VPN 网关。 本文讨论了可通过门户获得的指标。 指标是能够为近实时方案提供支持的轻型数据，因此，它们特别适合用于警报和快速检测问题。


|**指标**   | **单位** | **粒度** | **说明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 字节/秒  | 5 分钟| 网关上所有站点到站点连接的组合带宽平均利用率。     |
|**P2SBandwidth**| 字节/秒  | 1 分钟  | 网关上所有点到站点连接的组合带宽平均利用率。    |
|**P2SConnectionCount**| 计数  | 1 分钟  | 网关上点到站点连接的计数。   |
|**TunnelAverageBandwidth** | 字节/秒    | 5 分钟  | 在网关上创建的隧道的带宽平均利用率。 |
|**TunnelEgressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传出流量。   |
|**TunnelEgressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传出数据包的计数。   |
|**TunnelEgressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传出数据包的计数。 |
|**TunnelIngressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传入流量。   |
|**TunnelIngressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传入数据包的计数。   |
|**TunnelIngressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传入数据包的计数。 |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>以下步骤可帮助你查找和查看指标：

1. 导航到门户中的虚拟网络网关资源
2. 选择“概述”，以查看隧道总流入量和流出量指标。

   ![用于创建警报规则的选项](./media/monitor-vpn-gateway/overview.png "查看")

3. 若要查看上面列出的任何其他指标， 请单击虚拟网络网关资源下的“指标”部分，然后从下拉列表中选择指标。

   ![资源列表中的“选择”按钮和 VPN 网关](./media/monitor-vpn-gateway/metrics.png "选择")

## <a name="next-steps"></a>后续步骤

若要针对隧道指标配置警报，请参阅[针对 VPN 网关指标设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。

若要基于隧道资源日志配置警报，请参阅[基于 VPN 网关资源日志设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
