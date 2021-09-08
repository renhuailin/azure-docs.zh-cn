---
title: 排查 Azure 路由服务器问题
description: 了解如何排查 Azure 路由服务器问题。
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: b5b40f4e4dfa72eacdcf178dedbc11c969bf7315
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424665"
---
# <a name="troubleshooting-azure-route-server-issues"></a>排查 Azure 路由服务器问题

## <a name="connectivity-issues"></a>连接问题

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>为什么我的 NVA 在将默认路由 (0.0.0.0/0) 播发到 Azure 路由服务器后失去了 Internet 连接？
当 NVA 播发默认路由时，Azure 路由服务器会针对虚拟网络中的所有 VM（包括 NVA 本身）进行编程。 此默认路由将 NVA 设置为所有 Internet 绑定流量的下一个跃点。 如果 NVA 需要 Internet 连接，则需要配置[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md)来替代 NVA 中的此默认路由，并将 UDR 附加到托管 NVA 的子网（请参阅下面的示例）。 否则，NVA 主机将继续发送 Internet 绑定流量，包括由 NVA 发送回 NVA 本身的流量。

| 路由 | 下一跃点 |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>为什么我可以从我的 NVA ping 到 Azure 路由服务器上的 BGP 对等 IP，但在设置它们之间的 BGP 对等互连后，却不能再对相同的 IP 进行 ping 操作了？ BGP 对等互连为何会关闭？

在某些 NVA 中，需要为 Azure 路由服务器子网添加静态路由。 例如，如果 Azure 路由服务器在 10.0.255.0/27 中，而 NVA 在 10.0.1.0/24 中，则需要将以下路由添加到 NVA 中的路由表：

| 路由 | 下一跃点 |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 是托管 NVA（或者，更准确地说是 NIC 之一）的子网中的默认网关 IP。

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>为什么我在将 Azure 路由服务器部署到已具有 ExpressRoute 网关和/或 Azure VPN 网关的虚拟网络后，无法通过 ExpressRoute 和/或 Azure VPN 连接到本地网络？
将 Azure 路由服务器部署到虚拟网络后，需要更新网关和虚拟网络之间的控制平面。 在此更新过程中，虚拟网络中的 VM 会在一段时间内失去与本地网络的连接。 我们强烈建议你安排维护时段，以便在生产环境中部署 Azure 路由服务器。  

## <a name="control-plane-issues"></a>控制平面问题

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>为什么我的 NVA 与 Azure 路由服务器之间的 BGP 对等互连不断开启和关闭（“不稳定”）？

造成不稳定的原因可能是 BGP 计时器设置。 默认情况下，Azure 路由服务器上的“保持活动状态”计时器设置为 60 秒，“保持关闭”计时器设置为 180 秒。

### <a name="why-does-my-on-premises-network-connected-to-azure-vpn-gateway-not-receive-the-default-route-advertised-by-azure-route-server"></a>连接到 Azure VPN 网关的本地网络为何未收到 Azure 路由服务器播发的默认路由？

虽然 Azure VPN 网关可以从其 BGP 对等节点（包括 Azure 路由服务器）接收到默认路由，但它[不会向其他对等节点播发默认路由](../vpn-gateway/vpn-gateway-vpn-faq.md#what-address-prefixes-will-azure-vpn-gateways-advertise-to-me)。 

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>为什么即使 BGP 对等互连处于开启状态，我的 NVA 也无法收到来自 Azure 路由服务器的路由？

Azure 路由服务器使用的 ASN 为 65515。 请确保为 NVA 配置其他 ASN，以便在 NVA 和 Azure 路由服务器之间建立“eBGP”会话，从而实现自动传播路由。 请确保在 BGP 配置中启用“多跃点”，因为 NVA 和 Azure 路由服务器位于虚拟网络的不同子网中。

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>我的 NVA 与 Azure 路由服务器之间的 BGP 对等互连处于开启状态。 我可以看到它们之间正确交换了路由。 为什么 NVA 路由未出现在 VM 的有效路由表中？ 

* 如果你的 VM 与 NVA 和 Azure 路由服务器位于同一 VNet 中：

     Azure 路由服务器会公开托管在两个 VM 上的两个 BGP 对等 IP，这两个 VM 共同负责将路由发送到虚拟网络中运行的所有其他 VM。 每个 NVA 都必须为两个 VM 设置两个相同的 BGP 会话（例如，使用相同的 AS 编号和相同的 AS 路径，并播发一组相同的路由），以便虚拟网络中的 VM 可以从 Azure 路由服务器获取一致的路由信息。 请参阅下图。

    ![显示包含路由服务器的网络虚拟设备的示意图。](./media/faq/network-virtual-appliances.png)

    在有两个或更多 NVA 实例时，如果要将一个 NVA 实例指定为活动实例，将其他实例指定为被动实例，可以为来自不同 NVA 实例的相同路由播发不同的路径。

* 如果 VM 所在的虚拟网络与托管 NVA 和 Azure 路由服务器的虚拟网络不同。 请检查是否在两个 Vnet 之间启用了 VNet 对等互连，以及是否在 VM 的 VNet 中启用了“使用远程路由服务器”。

### <a name="why-is-the-equal-cost-multi-path-ecmp-function-of-my-expressroute-turned-off-after-i-deploy-azure-route-server-to-the-virtual-network"></a>为何在将 Azure 路由服务器部署到虚拟网络后，ExpressRoute 的等价多路径路由 (ECMP) 功能关闭了？

通过多个 ExpressRoute 连接将同一路由从本地网络播发到 Azure 时，通常 ECMP 默认处于启用状态，以便发往这些路由的流量从 Azure 返回本地。 但部署路由服务器后，ExpressRoute 与 Azure 路由服务器之间的 BGP 交换中会丢失多路径信息，因此来自 Azure 的流量只会遍历其中一个 ExpressRoute 连接。 在 Azure 路由服务器的未来版本中，将解除此限制。  

## <a name="next-steps"></a>后续步骤

了解如何[配置 Azure 路由服务器](quickstart-configure-route-server-powershell.md)
