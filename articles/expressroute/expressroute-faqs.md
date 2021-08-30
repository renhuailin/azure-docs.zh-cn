---
title: 常见问题解答 - Azure ExpressRoute | Microsoft Docs
description: ExpressRoute 常见问题包含有关支持的 Azure 服务、费用、数据和连接、SLA、提供程序和位置、带宽的信息和其他技术详细信息。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: 108c7c30ad84c83e895cade79bc8a6ad30029199
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272257"
---
# <a name="expressroute-faq"></a>ExpressRoute 常见问题

## <a name="what-is-expressroute"></a>什么是 ExpressRoute？

ExpressRoute 是一项 Azure 服务，允许在 Microsoft 数据中心与本地环境或共同租用设施中的基础结构之间创建专用连接。 ExpressRoute 连接不通过公共 Internet，与通过 Internet 的典型连接相比，提供更高的安全性、可靠性、速度和更低的延迟。

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>使用 ExpressRoute 和专用网络连接的好处是什么？

ExpressRoute 连接不经过公共 Internet。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的安全性、可靠性、速度和较低的一致延迟。 在某些情况下，使用 ExpressRoute 连接在本地设备和 Azure 之间传输数据可以产生显著的成本效益。

### <a name="where-is-the-service-available"></a>哪里提供该服务？

请参阅此页面以了解服务位置和可用性：[ExpressRoute 合作伙伴和位置](expressroute-locations.md)。

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>我如果未与 ExpressRoute 运营商合作伙伴之一建立合作伙伴关系，则如何使用 ExpressRoute 连接到 Microsoft？

可以将区域运营商和地区以太网连接选择为支持的 Exchange 提供商位置之一。 然后，你可以在提供商位置与 Microsoft 实现对接。 查看 [ExpressRoute 合作伙伴和位置](expressroute-locations.md)的最后一部分，以确定服务提供商是否处在任何 Exchange 位置中。 然后，你可以通过服务提供商订购一条 ExpressRoute 线路以连接到 Azure。

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute 的费用是多少？

有关定价信息，请查看[定价详细信息](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>如果购买具有给定带宽的 ExpressRoute 线路，系统是否会为入口和出口流量分别分配此带宽？

是的，ExpressRoute 线路带宽为双工。 例如，如果你购买了 200 mbps ExpressRoute 线路，那么你将获得 200 Mbps 的入口流量和 200 Mbps 的出口流量。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>如果我购买具有给定带宽的 ExpressRoute 线路，我从网络服务提供商处购买的专用连接必须具有相同速度吗？

不是。 可以从服务提供商处购买任何速度的专用连接。 但是，与 Azure 的连接速度限制为购买的 ExpressRoute 线路带宽。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>如果购买具有给定带宽的 ExpressRoute 线路，是否能够使用比所购买的带宽更多的带宽？

是的，使用 ExpressRoute 线路的辅助连接上提供的带宽时，你最多可以使用所购带宽限制的两倍。 线路的内置冗余是使用到两个Microsoft 企业边缘路由器 (MSEE) 的主连接和辅助连接配置的，每个连接都使用所购买的带宽。 如有必要，通过辅助连接提供的带宽可用于额外的流量。 但是，由于辅助连接是为了实现冗余，因此它得不到保障，不应在一段持续的时间内将它用于额外的流量。 若要详细了解如何使用这两个连接来传输流量，请参阅[使用 AS PATH 追加](./expressroute-optimize-routing.md#solution-use-as-path-prepending)。

如果计划仅使用主连接来传输流量，则连接的带宽是固定的，试图超额订阅它会导致数据包丢失情况增加。 如果流量流经 ExpressRoute 网关，则网关 SKU 的带宽是固定的，不可提升。 有关每个网关 SKU 的带宽，请参阅[关于 ExpressRoute 虚拟网络网关](expressroute-about-virtual-network-gateways.md#aggthroughput)。

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>能否同时与虚拟网络和其他 Azure 服务使用同一专用网络连接？

是的。 设置 ExpressRoute 线路后，可以同时访问虚拟网络中的服务和其他 Azure 服务。 通过专用对等路径可连接到虚拟网络，通过 Microsoft 对等路径可连接到其他服务。

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>如何在 ExpressRoute 专用对等互连上播发 VNet？

ExpressRoute 网关会播发 Azure VNet 的地址空间，无法在子网级别包含/排除。 它始终为播发的 VNet 地址空间。 此外，如果使用 VNet 对等互连并且对等互连 VNet 启用了“使用远程网关”，则还会播发对等互连 VNet 的地址空间。

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>在 ExpressRoute 专用对等互连上，可以将多少个前缀从 VNet 播发到本地？

在单个 ExpressRoute 连接上进行播发时，或使用网关传输通过 VNet 对等互连进行播发时，最多可播发 1000 个 IPv4 前缀。 例如，如果在连接到 ExpressRoute 线路的单个 VNet 上有 999 个地址空间，则所有这些 999 个前缀都会播发到本地。 或者，如果启用了 VNet 以允许通过 1 个地址空间进行网关传输，并使用“允许远程网关”选项启用了 500 个分支 Vnet，则使用网关部署的 VNet 会将 501 个前缀播放到本地。

如果使用的是双堆栈线路，则在单个 ExpressRoute 连接上（或使用网关传输通过 VNet 对等互连时），最多可播发 100 个 IPv6 前缀。 这是对上述限制的补充。

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>如果超过 ExpressRoute 连接上的前缀限制，会发生什么情况？

ExpressRoute 线路与网关（以及使用网关传输的对等互连 VNet，如果适用）之间的连接将会中断。 当不再超过前缀限制时，它会重新建立。  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>是否可以筛选来自本地网络的路由？

筛选/包含路由的唯一方法是在本地边缘路由器上进行。 用户定义的路由可以添加到 VNet 中以影响特定路由，但这是静态的，不是 BGP 播发的一部分。

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute 是否提供服务级别协议 (SLA)？

有关信息，请参阅 [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) 页。

## <a name="supported-services"></a>支持的服务

ExpressRoute 对各种服务类型支持[三个路由域](expressroute-circuit-peerings.md)：专用对等互连、Microsoft 对等互连和公共对等互连（已弃用）。

### <a name="private-peering"></a>专用对等互连

支持：

* 虚拟网络，包括所有虚拟机和云服务

### <a name="microsoft-peering"></a>Microsoft 对等互连

如果对 Azure Microsoft 对等互连启用了 ExpressRoute 线路，则可以通过该线路访问 Azure 中使用的[公共 IP 地址范围](../virtual-network/public-ip-addresses.md#public-ip-addresses)。 Azure Microsoft 对等互连将提供对当前在 Azure 上托管的服务的访问权限（具有地理限制，具体取决于线路的 SKU）。 若要验证特定服务的可用性，可以查看该服务的文档，以了解是否为该服务发布了保留范围。 然后，查找目标服务的 IP 范围，并与 [Azure IP 范围和服务标记 – 公有云 XML 文件](https://www.microsoft.com/download/details.aspx?id=56519)中列出的范围进行比较。 或者，可以为相关服务开具支持票证以进行说明。

支持：

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI - 通过 Azure 区域社区提供，请参阅[此处](/power-bi/service-admin-where-is-my-tenant-located)以了解如何查找 Power BI 租户的区域。
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/)（Azure 全球服务社区）
* 适用于 IaaS（虚拟机、虚拟网络网关、负载均衡器等）的 Azure 公共 IP 地址  
* 还支持大多数其他 Azure 服务。 请直接对要使用的服务进行确认来验证是否支持。

不支持：

* CDN
* Azure Front Door
* [Windows 虚拟桌面](https://azure.microsoft.com/services/virtual-desktop/)
* 多重身份验证服务器（旧版）
* 流量管理器
* 逻辑应用

### <a name="public-peering"></a>公共对等互连

在新的 ExpressRoute 线路上已禁用公共对等互连。 Azure 服务当前在 Microsoft 对等互连上可用。 如果你的线路是在弃用公共对等互连之前创建，则可以根据所需服务选择使用 Microsoft 对等互连或公共对等互连。

有关公共对等互连的详细信息和配置步骤，请参阅 [ExpressRoute 公共对等互连](about-public-peering.md)。

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>在配置 Microsoft 对等互连时，为什么会看到“播发的公用前缀”状态为“需要验证”？

Microsoft 会验证指定的“播发的公用前缀”和“对等 ASN”（或“客户 ASN”）是否已在 Internet 路由注册表中分配给你。 如果要从其他实体获取公用前缀，并且没有在路由注册表中记录分配，则自动验证不会完成，需要手动验证。 如果自动验证失败，则你会看到消息“需要验证”。

如果看到消息“需要验证”，请收集相关文档，它们显示公用前缀已由在路由注册表中作为前缀所有者列出的实体分配给你的组织，然后通过开具支持票证来提交这些文档以进行手动验证，如下所示。

![屏幕截图，显示与“公共前缀的所有权证明”相对应的“新建支持请求”（支持票证）。](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>ExpressRoute 是否支持 Dynamics 365？

Dynamics 365 和 Common Data Service (CDS) 环境托管在 Azure 上，因此客户可以受益于针对 Azure 资源的底层 ExpressRoute 支持。 如果路由器筛选器包含在其中托管 Dynamics 365/CDS 环境的 Azure 区域，则可以连接到其服务终结点。

> [!NOTE]
> 如果在同一[地缘政治区域](./expressroute-locations-providers.md#expressroute-locations)内部署了 ExpressRoute 线路，则通过 Azure ExpressRoute 进行 Dynamics 365 连接不需要 [ExpressRoute 高级版](#expressroute-premium)。

## <a name="data-and-connections"></a>数据和连接

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>对于使用 ExpressRoute 可以传输的数据量是否有限制？

我们不对数据传输量设置限制。 有关带宽价格的信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute 支持的连接速度是多少？

支持的带宽如下：

50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps

### <a name="which-service-providers-are-available"></a>可以选择哪些服务提供商？

有关服务提供商和位置的列表，请参阅 [ExpressRoute 合作伙伴和位置](expressroute-locations.md)。

## <a name="technical-details"></a>技术详细信息

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>将本地位置连接到 Azure 有哪些技术要求？

有关要求，请参阅 [ExpressRoute 先决条件页](expressroute-prerequisites.md)。

### <a name="are-connections-to-expressroute-redundant"></a>与 ExpressRoute 的连接是冗余的吗？

是的。 每条 ExpressRoute 线路都配置了一对冗余的交叉连接，以便提供高可用性。

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>如果我的某个 ExpressRoute 链路出现故障，我会失去连接吗？

如果其中一个交叉连接出现故障，不会失去连接。 可使用冗余连接，以支持网络负载和提供 ExpressRoute 线路的高可用性。 另外，还可以在不同对等位置创建一条线路以获得线路级恢复能力。

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>如何在专用对等互连上实现冗余？

来自不同对等互连位置的多条 ExpressRoute 线路或来自同一对等互连位置的最多四个连接可以连接到同一虚拟网络，以此在单条线路变得不可用的情况下提供高可用性。 你随后可以为其中一个本地连接[分配更高的权重](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection)，使特定线路成为首选。 强烈建议客户至少设置两个 ExpressRoute 线路，以避免单一故障点。 

请参阅[此处](./designing-for-high-availability-with-expressroute.md)以了解如何进行高可用性设计，并参阅[此处](./designing-for-disaster-recovery-with-expressroute-privatepeering.md)以了解如何进行灾难恢复设计。  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>如何在 Microsoft 对等互连上实现冗余？

强烈建议客户在使用 Microsoft 对等互连访问 Azure 公共服务（如 Azure 存储或 Azure SQL）时以及将 Microsoft 对等互连用于 Microsoft 365 时在不同的对等互连位置使用多个线路，以避免单一故障点。 客户可以在两个线路上播放相同的前缀，并使用 [AS PATH 附加](./expressroute-optimize-routing.md#solution-use-as-path-prepending)或播放不同的前缀来确定来自本地的路径。

请参阅[此处](./designing-for-high-availability-with-expressroute.md)以了解如何进行高可用性设计。

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>如何确保连接到 ExpressRoute 的虚拟网络上的高可用性？

可以通过将同一对等互连位置中最多四条 ExpressRoute 线路连接到你的虚拟网络，或者将不同对等互连位置（例如，新加坡、新加坡 2）中的最多 16 个 ExpressRoute 线路连接到你的虚拟网络，来实现高可用性。 如果一条 ExpressRoute 线路出现故障，连接会故障转移到另一条 ExpressRoute 线路。 默认情况下，将基于等成本多路径路由 (ECMP) 对离开虚拟网络的流量进行路由。 可以使用连接权重来使一条线路优先于另一条线路。 有关详细信息，请参阅[优化 ExpressRoute 路由](expressroute-optimize-routing.md)。

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>如何确保 Microsoft 对等互连或公共对等互连上以 Azure 公共服务（如 Azure 存储和 Azure SQL）为目标的流量首选采用 ExpressRoute 路径？

必须在路由器上实现“本地首选项”属性，以确保从本地到 Azure 的路径始终首选采用 ExpressRoute 线路。

请参阅[此处](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings)有关 BGP 路径选择和常见路由器配置的其他详细信息。 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>如果我不在云交换中共置，而我的服务提供商提供点到点连接，我需要在本地网络与 Microsoft 之间订购两个物理连接吗？

如果服务提供商可以通过物理连接建立两条以太网虚拟线路，则只需要一个物理连接。 物理连接（例如光纤）的终点在第 1 层 (L1) 设备上（请参阅下图）。 两条以太网虚拟线路使用不同的 VLAN ID 进行标记，一个供主要线路使用，一个供辅助线路使用。 这些 VLAN ID 位于外部 802.1Q 以太网标头中。 内部 802.1Q 以太网标头（不显示）会映射到特定的 [ExpressRoute 路由域](expressroute-circuit-peerings.md)。

![示意图，突出显示了第 1 层 (L1) 的主要和次要虚拟线路，这些线路构成客户站点上的交换机与 ExpressRoute 位置之间的物理连接。](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>能否使用 ExpressRoute 将我的一个 VLAN 扩展到 Azure？

不是。 不支持将第 2 层连接扩展到 Azure。

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>能否在我的订阅中有多条 ExpressRoute 线路？

是的。 可以在订阅中有多条 ExpressRoute 线路。 默认限制设置为 10。 如果需要增大限制，请联系 Microsoft 支持部门。

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>能否使用不同服务提供商的 ExpressRoute 线路？

是的。 可以使用许多服务提供商的 ExpressRoute 线路。 每条 ExpressRoute 线路只与一个服务提供商相关联。 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>我在同一城市内看到两个 ExpressRoute 对等互连位置（例如，新加坡和新加坡 2）。 我应选择哪个对等位置来创建我的 ExpressRoute 线路？
如果服务提供商在两个站点均提供 ExpressRoute，则可以与提供商协作，选择任意一个站点来设置 ExpressRoute。 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>在相同市内是否可以有多条 ExpressRoute 线路？ 是否可以将这些线路链接到同一虚拟网络？

是的。 可以具有多条包含相同或不同服务提供商的 ExpressRoute 线路。 如果城区内有多个 ExpressRoute 对等位置，并且线路创建在了不同的对等位置，则可以将这些线路链接到同一虚拟网络。 如果线路是在同一对等互连位置创建的，你最多可以将四条线路链接到同一虚拟网络。

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>如何将我的虚拟网络连接到 ExpressRoute 线路？

基本步骤如下：

* 建立一条 ExpressRoute 线路并让服务提供商启用它。
* 你或者提供商必须配置 BGP 对等互连。
* 将虚拟网络连接到 ExpressRoute 线路。

有关详细信息，请参阅 [ExpressRoute 线路预配工作流和线路状态](expressroute-workflows.md)。

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>我的 ExpressRoute 线路是否存在连接界限？

是的。 [ExpressRoute 合作伙伴和位置](expressroute-locations.md)一文概述了 ExpressRoute 线路的连接界限。 一条 ExpressRoute 线路的连接范围限制为单个地缘政治区域。 可以通过启用 ExpressRoute 高级功能，将连接扩展为跨地缘政治区域。

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>能否将多个虚拟网络链接到一条 ExpressRoute 线路？

是的。 在标准 ExpressRoute 线路上最多可以有 10 个虚拟网络连接，在[高级 ExpressRoute 线路](#expressroute-premium)上最多可以有 100 个。 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>我有多个包含虚拟网络的 Azure 订阅。 能否将不同订阅中的虚拟网络连接到单个 ExpressRoute 线路？

是的。 可以在同一订阅中最多链接 10 个虚拟网络作为线路，或在不同的订阅中使用单一 ExpressRoute 线路。 可以通过启用 ExpressRoute 高级功能来提高此限制。 请注意，专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者；所有虚拟网络共享相同的带宽。

有关详细信息，请参阅[在多个订阅之间共享 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)。

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>我有多个关联到不同 Azure Active Directory 租户或企业协议合约的 Azure 订阅。 是否可以将位于单独租户和合约中的虚拟网络连接到不在同一租户或合约中的单个 ExpressRoute 线路？

是的。 ExpressRoute 授权可以跨订阅、租户和合约边界，不需要进行额外配置。 请注意，专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者；所有虚拟网络共享相同的带宽。

有关详细信息，请参阅[在多个订阅之间共享 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)。

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>连接到同一线路的虚拟网络相互隔离吗？

否。 从路由角度看，连接到同一 ExpressRoute 线路的所有虚拟网络都属于同一路由域，不是相互隔离的。 如果需要路由隔离，则需要创建单独的 ExpressRoute 线路。

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>能否将一个虚拟网络连接到多条 ExpressRoute 线路？

是的。 可将一个虚拟网络最多链接到处于相同位置的 4 个 ExpressRoute 线路，或处于不同对等互连位置的 16 个 ExpressRoute 线路。 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>能否从连接到 ExpressRoute 线路的虚拟网络访问 Internet？

是的。 如果尚未通过 BGP 会话公布默认路由 (0.0.0.0/0) 或 Internet 路由前缀，可以从连接到 ExpressRoute 线路的虚拟网络连接到 Internet。

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>能否阻止与连接到 ExpressRoute 线路的虚拟网络建立 Internet 连接？

是的。 可以播发默认路由 (0.0.0.0/0) 以阻止与虚拟网络内部署的虚拟机建立所有 Internet 连接，并通过 ExpressRoute 线路路由出所有流量。

> [!NOTE]
> 如果因为某个原因（例如，服务中断或配置错误）从播发的路由中撤消了已播发路由 0.0.0.0/0，则 Azure 会向已连接虚拟网络上的资源提供[系统路由](../virtual-network/virtual-networks-udr-overview.md#system-routes)，以便与 Internet 建立连接。  若要确保阻止发往 Internet 的出口流量，建议使用针对 Internet 流量的出站拒绝规则将网络安全组置于所有子网上。

请注意，如果播发默认路由，我们会强制将传送到通过 Microsoft 对等互连提供的服务（如 Azure 存储和 SQL DB）的流量传回本地。 必须将路由器配置为通过 Microsoft 对等互连路径或通过 Internet 将流量传回 Azure。 如果已启用了该服务的一个服务终结点，则不会强制将发送到该服务的流量传回本地。 流量将保持在 Azure 主干网络中。 若要详细了解服务终结点，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>连接到同一 ExpressRoute 线路的虚拟网络能否互相对话？

是的。 连接到同一 ExpressRoute 线路的虚拟网络中部署的虚拟机可以彼此通信。 建议设置[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)以促进此通信。

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>是否可以结合使用 ExpressRoute 来设置一个站点到站点 VPN 连接，以便连接到虚拟网络？

是的。 ExpressRoute 可与站点到站点 VPN 共存。 请参阅[配置 ExpressRoute 和站点到站点并存连接](expressroute-howto-coexist-resource-manager.md)。

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>如何在站点到站点 VPN 连接和 ExpressRoute 之间启用路由？

如果要在连接到 ExpressRoute 的分支与连接到站点到站点 VPN 连接的分支之间启用路由，则需要设置 [Azure 路由服务器](../route-server/expressroute-vpn-support.md)。

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>为什么有一个公共 IP 地址与虚拟网络上的 ExpressRoute 网关相关联？

此公共 IP 地址仅用于内部管理，不会给虚拟网络带来安全隐患。

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>对于我可以公布的路由数有限制吗？

是的。 对于专用对等互连，我们最多接受 4000 个路由前缀；对于 Microsoft 对等互连，接受 200 个。 如果启用 ExpressRoute 高级功能，可以将专用对等互连的此限制提高为 10,000 个路由。

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>对于我可以通过 BGP 会话公布的 IP 范围有限制吗？

对于 Microsoft 对等 BGP 会话中，不接受私有前缀 (RFC1918)。 我们在 Microsoft 和专用对等互连上接受任何前缀大小（最多 /32）。

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>如果超过 BGP 限制，会发生什么情况？

BGP 会话会被删除。 当前缀计数低于限制后，将重置这些会话。

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP 保持时间是多少？ 是否可以调整它？

保持时间为 180。 Keep-Alive（保持活动）消息每隔 60 秒发送一次。 这些是 Microsoft 端的固定设置，不能更改。 可以配置不同的计时器，BGP 会话参数会相应地协商。

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>是否可以更改 ExpressRoute 线路的带宽？

是的，可以尝试在 Azure 门户中或者使用 PowerShell.mpt 来增加 ExpressRoute 线路的带宽。 如果在创建线路的物理端口上有容量可用，则更改会成功。 

如果更改失败，这意味着当前端口上没有剩余足够的容量，需要创建具有更高带宽的新 ExpressRoute 线路；或者意味着在该位置没有额外的容量，在这种情况下将无法增加带宽。 

还必须跟进连接服务提供商，确保他们更新其网络中的限制以支持带宽增加。 不过，无法减小 ExpressRoute 线路的带宽。 必须创建具有更低带宽的新 ExpressRoute 线路并删除旧线路。

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>如何更改 ExpressRoute 线路的带宽？

可使用 Azure 门户、REST API、PowerShell 或 Azure CLI 更新 ExpressRoute 线路的带宽。

### <a name="i-received-a-notification-about-maintenance-on-my-expressroute-circuit-what-is-the-technical-impact-of-this-maintenance"></a>我收到有关 ExpressRoute 线路维护的通知。 此次维护对技术有何影响？

如果你在[主动-主动模式](./designing-for-high-availability-with-expressroute.md#active-active-connections)下操作线路，则在维护期间受到的影响极小，甚至没有影响。 我们对线路的主要连接和辅助连接分别执行维护。 计划性维护通常在对等互连位置所在时区的工作时间以外执行，你不能选择维护时间。

### <a name="i-received-a-notification-about-a-software-upgrade-or-maintenance-on-my-expressroute-gateway-what-is-the-technical-impact-of-this-maintenance"></a>我收到有关 ExpressRoute 网关的软件升级或维护的通知。 此次维护对技术有何影响？

在网关的升级或维护期间，你应会受到极小的影响，甚至没有影响。 ExpressRoute 网关由多个实例组成，在升级期间，一次脱机一个实例。 尽管这可能会导致网关暂时对虚拟网络支持较低的网络吞吐量，但网关本身不会遇到任何停机。


## <a name="expressroute-premium"></a>ExpressRoute 高级版

### <a name="what-is-expressroute-premium"></a>什么是 ExpressRoute 高级版？

ExpressRoute 高级版是以下功能的集合：

* 对于专用对等互连，将路由表限制从 4000 个路由提升为 10,000 个路由。
* 增加了可在 ExpressRoute 线路上启用的 VNet 和 ExpressRoute Global Reach 连接的数量（默认为 10）。 有关详细信息，请参阅 [ExpressRoute 限制](#limits)表。
* 连接到 Microsoft 365
* 通过 Microsoft 核心网络建立全局连接。 现在，可将一个地缘政治区域中 VNet 链接到另一个区域中的 ExpressRoute 线路。<br>
    **示例：**

    *  可将欧洲西部创建的 VNet 链接到硅谷创建的 ExpressRoute 线路。 
    *  在 Microsoft 对等互连中，会播发来自其他地缘政治区域的前缀，以便可以进行连接，例如，通过硅谷的线路连接到欧洲西部地区的 SQL Azure。


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>如果我启用了 ExpressRoute 高级版，可以在 ExpressRoute 线路上启用多少 Vnet 和 ExpressRoute Global Reach 连接？

下表显示了 ExpressRoute 限制和每条 ExpressRoute 线路的 VNet 和 ExpressRoute Global Reach 连接数：

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>如何启用 ExpressRoute 高级版？

在启用相应的功能后，可启用 ExpressRoute 高级功能，并可通过更新线路状态关闭高级功能。 可以在创建线路时启用 ExpressRoute 高级版，或者调用 REST API/PowerShell cmdlet。

### <a name="how-do-i-disable-expressroute-premium"></a>如何禁用 ExpressRoute 高级版？

可以通过调用 REST API 或 PowerShell cmdlet 来禁用 ExpressRoute 高级版。 必须确保已调整连接需求以满足默认限制，然后再禁用 ExpressRoute 高级版。 如果使用规模超出了默认限制，禁用 ExpressRoute 高级版的请求会失败。

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>我是否可以从高级功能集选择所需的功能？

不是。 无法选择功能。 如果启用 ExpressRoute 高级版，我们会启用所有功能。

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute 高级版的费用是多少？

有关费用，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>除了支付 ExpressRoute 高级版费用以外，是否还要支付标准版 ExpressRoute 的费用？

是的。 ExpressRoute 高级版的费用是在 ExpressRoute 线路费用以及连接提供商所收费用的基础之上收取的。

## <a name="expressroute-local"></a>ExpressRoute Local

### <a name="what-is-expressroute-local"></a>什么是 ExpressRoute Local？

ExpressRoute Local 是 ExpressRoute 线路除标准 SKU 和高级 SKU 之外的一个 SKU。 Local 的一个重要功能是，ExpressRoute 对等互连位置上的 Local 线路使你只能访问同一城市中或附近的一个或两个 Azure 区域。 与此相反，标准线路使你可以访问一个地缘政治区域中的所有 Azure 区域，而高级线路使你可以访问全球所有 Azure 区域。 具体来说，采用本地 SKU 时，只能从 ExpressRoute 线路的相应本地区域通告路由（通过 Microsoft 和私有对等互连）。 无法接收与定义的本地区域不同的其他区域的路由。

### <a name="what-are-the-benefits-of-expressroute-local"></a>ExpressRoute Local 的优点是什么？

你需要为标准或高级 ExpressRoute 线路支付流出量数据传输费用，但无需为 ExpressRoute Local 线路单独支付流出量数据传输费用。 换句话说，ExpressRoute Local 的价格包括数据传输费用。 如果有大量的数据要传输，则 ExpressRoute Local 是更经济的解决方案，你可通过专用连接将数据引入接近所需 Azure 区域的 ExpressRoute 对等互连位置。 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>ExpressRoute Local 提供以及未提供哪些功能？

与标准 ExpressRoute 线路相比，Local 线路具有一组相同的功能，不同之处在于：
* 如上所述的 Azure 区域访问范围
* ExpressRoute Global Reach 在 Local 上不可用

ExpressRoute Local 还会对资源施加与标准相同的限制（例如，每个线路的 VNet 数）。 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>ExpressRoute Local 在哪些位置可用，以及各个对等互连位置映射到哪些 Azure 区域？

ExpressRoute Local 在位于一个或两个 Azure 区域附近的对等互连位置上可用。 在州或省/市/自治区或是国家/地区中没有 Azure 区域的对等位置上不提供该功能。 请参阅[“位置”页](expressroute-locations-providers.md)上的确切映射。  

## <a name="expressroute-for-microsoft-365"></a>适用于 Microsoft 365 的 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>如何创建 ExpressRoute 线路，以便连接到 Microsoft 365 服务？

1. 请查看 [ExpressRoute 先决条件页](expressroute-prerequisites.md)，以确保满足要求。
2. 为确保满足连接需求，请查看 [ExpressRoute 合作伙伴和位置](expressroute-locations.md)一文中的服务提供商和位置列表。
3. 请查看[针对 Microsoft 365 的网络规划和性能优化](/microsoft-365/enterprise/network-planning-and-performance)，规划容量要求。
4. 遵循工作流中列出的步骤来设置连接：[ExpressRoute 线路预配工作流和线路状态](expressroute-workflows.md)。

> [!IMPORTANT]
> 确保在配置与 Microsoft 365 服务的连接时已启用 ExpressRoute 高级版加载项。
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>我的现有 ExpressRoute 线路是否支持连接到 Microsoft 365 服务？

是。 可以将现有 ExpressRoute 线路配置为支持连接到 Microsoft 365 服务。 确保容量足以连接到 Microsoft 365 服务，并确保已启用高级版加载项。 [针对 Microsoft 365 的网络规划和性能优化](/microsoft-365/enterprise/network-planning-and-performance)可帮助规划连接需求。 另外，请参阅[创建和修改 ExpressRoute 线路](expressroute-howto-circuit-classic.md)。

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>通过 ExpressRoute 连接可以访问哪些 Microsoft 365 服务？

如需可以通过 ExpressRoute 使用的服务的最新列表，请参阅 [Microsoft 365 URL 和 IP 地址范围](/microsoft-365/enterprise/urls-and-ip-address-ranges)页。

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>用于 Microsoft 365 服务的 ExpressRoute 的费用是多少？

Microsoft 365 服务要求启用高级版加载项。 有关费用，请参阅[定价详细信息页](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>哪些区域支持适用于 Microsoft 365 的 ExpressRoute？

有关信息，请参阅 [ExpressRoute 合作伙伴和位置](expressroute-locations.md)。

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>是否即使为组织配置了 ExpressRoute，也可以通过 Internet 访问 Microsoft 365？

是。 即使为网络配置了 ExpressRoute，也可以通过 Internet 访问 Microsoft 365 服务终结点。 如果你所在位置的网络配置为通过 ExpressRoute 连接到 Microsoft 365 服务，请咨询组织的网络团队。

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>如何在 Azure ExpressRoute 上规划 Microsoft 365 网络流量的高可用性？
请参阅有关[使用 Azure ExpressRoute 实现高可用性和故障转移](/microsoft-365/enterprise/network-planning-with-expressroute)的建议

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>是否可以通过 Azure 美国政府版 ExpressRoute 线路访问 Office 365 美国政府社区 (GCC) 服务？

是的。 可以通过 Azure 美国政府版 ExpressRoute 访问 Office 365 GCC 服务终结点。 但是，首先需要在 Azure 门户上开具支持票证，向 Microsoft 提供要播发的前缀。 解决支持票证后，将建立到 Office 365 GCC 服务的连接。 

## <a name="route-filters-for-microsoft-peering"></a>用于 Microsoft 对等互连的路由筛选器

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>首次启用 Microsoft 对等互连时，会看到哪些路由？

不会看到任何路由。 必须将路由筛选器附加到线路才能启动前缀播发。 有关说明，请参阅[配置用于 Microsoft 对等互连的路由筛选器](how-to-routefilter-powershell.md)。

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>我已启用 Microsoft 对等互连，目前正在尝试选择 Exchange Online，但有错误指出我无权执行此操作。

使用路由筛选器时，任何客户都可以启用 Microsoft 对等互连。 但是，若要使用 Microsoft 365 服务，仍需获得 Microsoft 365 的授权。

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>我在 2017 年 8 月 1 日前已启用了 Microsoft 对等互连，如何利用路由筛选器？

现有线路将继续为 Microsoft 365 播发前缀。 如果希望添加基于同一 Microsoft 对等互连的 Azure 公共前缀播发，可以创建一个路由筛选器，选择需要播发的服务（包括你需要的 Microsoft 365 服务），并将筛选器附加到你的 Microsoft 对等互连。 有关说明，请参阅[配置用于 Microsoft 对等互连的路由筛选器](how-to-routefilter-powershell.md)。

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>我在一个位置建立了 Microsoft 对等互连，目前我正尝试在另一个位置启用它，但未看到任何前缀。

* 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。

* 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。 默认情况下，不会显示任何前缀。

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>隐私

### <a name="does-the-expressroute-service-store-customer-data"></a>ExpressRoute 服务是否存储客户数据？

不是。
