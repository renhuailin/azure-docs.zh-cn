---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 649c5805c600b6282be6d05fefb59cecaf249f4f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92526149"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP 是否在所有 Azure VPN 网关 SKU 上受支持？
除了基本 SKU，其他所有 Azure VPN 网关 SKU 都支持 BGP。

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>能否将 BGP 用于 Azure 策略 VPN 网关？
否，只有基于路由的 VPN 网关支持 BGP。

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>可使用哪种 ASN（自治系统编号）？
可以将自己的公共 ASN 或专用 ASN 同时用于本地网络和 Azure 虚拟网络。 不能使用 Azure 或 IANA 保留的范围。

Azure 或 IANA 保留的 ASN 如下所示：
* 由 Azure 保留的 ASN：
  * 公用 ASN：8074、8075、12076
  * 专用 ASN：65515、65517、65518、65519、65520
* [由 IANA 保留](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)的 ASN：
   * 23456、64496-64511、65535-65551 和 429496729

连接到 Azure VPN 网关时，不能为本地 VPN 设备指定这些 ASN。

### <a name="can-i-use-32-bit-4-byte-asns"></a>是否可以使用 32 位（4 字节）ASN？
可以，VPN 网关现在支持 32 位（4 字节）ASN。 若要使用 ASN 以十进制格式进行配置，请使用 PowerShell、Azure CLI 或 Azure SDK。

### <a name="what-private-asns-can-i-use"></a>可以使用哪些专用 ASN？
可用的专用 ASN 范围包括：

* 64512-65514 和 65521-65534

IANA 或 Azure 不会保留和使用这些 ASN，因此可将其分配给 Azure VPN 网关。

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>VPN 网关将哪个地址用于 BGP 对等节点 IP？

默认情况下，VPN 网关为主备 VPN 网关分配 GatewaySubnet 范围中的一个 IP 地址，或者为双活 VPN 网关分配两个 IP 地址。 在你创建 VPN 网关时，系统会自动分配这些地址。 可以通过使用 PowerShell 或通过在 Azure 门户中查找来获取分配的实际 BGP IP 地址。 在 PowerShell 中，使用 Get-AzVirtualNetworkGateway，并查找 bgpPeeringAddress 属性 。 在 Azure 门户的“网关配置”页面上的“配置 BGP ASN”属性下查看 。

如果你的本地 VPN 路由器使用 APIPA IP 地址 (169.254.x.x) 作为 BGP IP 地址，则你必须在 Azure VPN 网关上再指定一个 Azure APIPA BGP IP 地址 。 Azure VPN 网关会选择将 APIPA 地址用于在本地网络网关中指定的本地 APIPA BGP 对等节点中，或者对非 APIPA 本地 BGP 对等节点使用专用 IP 地址。 有关详细信息，请参阅[配置 BGP](../articles/vpn-gateway/bgp-howto.md)。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN 设备上的 BGP 对等节点 IP 地址的要求是什么？
本地 BGP 对等节点地址不得与 VPN 设备的公共 IP 地址相同，也不得来自 VPN 网关的虚拟网络地址空间。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是一个分配给设备上环回接口的地址（常规 IP 地址或 APIPA 地址）。 如果设备对 BGP 使用 APIPA 地址，你必须在 Azure VPN 网关上指定 APIPA BGP IP 地址，如[配置 BGP](../articles/vpn-gateway/bgp-howto.md) 中所述。 在表示该位置的相应本地网关中指定此地址。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>使用 BGP 时应将什么指定为本地网关的地址前缀？

> [!IMPORTANT]
>
>这是之前记录的要求中的一项更改。 如果你使用 BGP 进行连接，则将相应的本地网络网关资源的“地址空间”字段留空。 Azure VPN 网关将在内部添加一个通过 IPsec 隧道传往本地 BGP 对等节点 IP 的主机路由。 请不要在“地址空间”字段添加 /32 路由。 该路由是冗余的；如果你使用 APIPA 地址作为本地 VPN 设备 BGP IP，则无法将它添加到此字段。 如果在“地址空间”字段中添加任何其他前缀，则除了通过 BGP 了解到的路由外，这些前缀将作为静态路由添加到 Azure VPN 网关上。
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>能否将同一个 ASN 同时用于本地 VPN 网络和 Azure 虚拟网络？
否，必须在本地网络和 Azure 虚拟网络之间分配不同 ASN（如果要使用 BGP 将它们连接在一起）。 无论是否为跨界连接启用了 BGP，都会为 Azure VPN 网关分配默认 ASN（即 65515）。 可以通过在创建 VPN 网关时分配不同 ASN，或者在创建网关后更改 ASN 来覆盖此默认值。 需要将本地 ASN 分配给相应的 Azure 本地网关。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN 网关将播发给我哪些地址前缀？
这些网关会将以下路由播发到本地 BGP 设备：

* 你的虚拟网络地址前缀。
* 已连接到 Azure VPN 网关的每个本地网关的地址前缀。
* 从连接到 Azure VPN 网关的其他 BGP 对等会话获知的路由，不包括默认路由或与任何虚拟网络前缀重叠的路由。

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>我可以向 Azure VPN 网关发布多少个前缀？
Azure VPN 网关最多支持 4000 个前缀。 如果前缀数目超过此限制，将丢弃 BGP 会话。

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>能否将默认路由 (0.0.0.0/0) 播发给 Azure VPN 网关？
是的。 请注意，这会强制所有虚拟网络出口流量流向你的本地站点。 它还可阻止虚拟网络 VM 直接从 internet 接收公共通信，例如从 internet 到 VM 的 RDP 或 SSH。

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>能否播发与虚拟网络前缀完全相同的前缀？

不能，Azure 将阻止播发与任一虚拟网络地址前缀相同的前缀或对其进行筛选。 但是，可播发属于虚拟网络内所拥有内容超集的前缀。 

例如，如果虚拟网络使用了地址空间 10.0.0.0/16，则可以播发 10.0.0.0/8。 但无法播发 10.0.0.0/16 或 10.0.0.0/24。

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>能否对虚拟网络之间的连接使用 BGP？
可以，BGP 既可用于跨界连接，也可用于虚拟网络之间的连接。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>能否将 BGP 连接与非 BGP 连接混合用于 Azure VPN 网关？
能，可以将 BGP 连接和非 BGP 连接混合用于同一 Azure VPN 网关。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN 网关是否支持 BGP 传输路由？
是，支持 BGP 传输路由，但例外是 Azure VPN 网关不会将默认路由播发到其他 BGP 对等节点。 若要启用跨多个 Azure VPN 网关的传输路由，必须在虚拟网络之间的所有中间连接上启用 BGP。 有关详细信息，请参阅[关于 BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)。

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>在 Azure VPN 网关和我的本地网络之间能否有多个隧道？
能，可以在 Azure VPN 网关和本地网络之间建立多个站点到站点 (S2S) VPN 隧道。 请注意，所有这些隧道都将计入 Azure VPN 网关的隧道总数，且你必须在这两个隧道上都启用 BGP。

例如，如果在 Azure VPN 网关与一个本地网络之间有两个冗余隧道，则它们将占用 Azure VPN 网关的总配额中的 2 个隧道。

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>在两个使用 BGP 的 Azure 虚拟网络之间能否有多个隧道？
是，但必须至少有一个虚拟网络网关采用主动-主动配置。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>能否在 Azure ExpressRoute 和 S2S VPN 共存配置中对 S2S VPN 使用 BGP？
是的。 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>应为 BGP 对等会话添加到本地 VPN 设备什么内容？
在 VPN 设备上添加 Azure BGP 对等节点 IP 地址的主机路由。 此路由指向 IPsec S2S VPN 隧道。 例如，如果 Azure VPN 对等节点 IP 为“10.12.255.30”，则在 VPN 设备上添加“10.12.255.30”的主机路由（包含匹配的 IPsec 隧道接口的下一跃点接口）。

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>虚拟网络网关是否支持将 BFD 用于使用 BGP 的 S2S 连接？
不能。 双向转发检测 (BFD) 是一种协议，与使用标准 BGP keepalive 相比，将 BFD 与 BGP 结合使用可更快地检测相邻故障时间。 BFD 使用亚秒级计时器，它专门在 LAN 环境中使用，但不跨公共 Internet 或广域网连接进行使用。

对于通过公共 Internet 进行的连接，某些数据包延迟（甚至被删除）的情况是不常见的，因此引入这些主动计时器有可能使性能更不稳定。 这种不稳定可能导致路由遭到 BGP 抑制。 替换方法是，可配置本地设备，使其具有 keepalive 时间间隔比默认的 60 秒低的计时器，并具有保持时间为 180 秒的计时器。 这可缩短收敛时间。
