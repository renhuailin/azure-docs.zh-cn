---
title: 控制 Azure 澳大利亚入口流量
description: 用于控制 Azure 澳大利亚入口流量以满足澳大利亚政府对安全 Internet 网关要求的指南
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 3e2fcfb91a0cb514a26608a29cb8cb09722b7f32
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "117028960"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>控制 Azure 澳大利亚入口流量

保护 ICT 系统的核心元素是控制网络流量。 流量应仅限系统运行所需，以减少遭入侵的可能性。

本指南将详细介绍入站（入口）网络流量在 Azure 中的工作原理，以及对已连接到 Internet 的系统实现网络安全控制的建议。

网络控制符合澳大利亚网络安全中心 (ACSC)“使用者指南”的要求和 ACSC“信息安全手册”(ISM) 的目标。

## <a name="requirements"></a>要求

ISM 中定义了英联邦系统的整体安全要求。 为协助英联邦实体实现网络安全，ACSC 发布了 [ACSC 防护：实现网络分段和隔离](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)，以及，为协助保护云环境中的系统安全，ACSC 发布了[适用于租户的云计算安全](https://www.cyber.gov.au/publications/cloud-computing-security-for-tenants)。

这些指南概述了实现网络安全和控制流量的背景，并涵盖了用于网络设计和配置的实用建议。

在服务信任门户的“澳大利亚”页中，Microsoft 的 [适用于 Microsoft Azure 租户的云计算安全](https://aka.ms/au-irap)指南重点介绍了特定 Microsoft 技术，这些技术可用于满足 ACSC 发布文档中提出的建议。

以下由 ACSC 在发布文档中确定的关键要求，对于控制 Azure 中的入口流量十分重要：

|说明|源|
|---|---|
|**实现网络分段和隔离，例如，针对多层体系结构，使用基于主机的防火墙和 CSP 网络访问控制将入站和出站 VM 网络连接限制为仅限所需的端口/协议。**| _适用于租户的云计算_|
|在租户（包括租户的远程用户）和云服务之间 **实现足够高的带宽、低延迟、可靠的网络连接**，以满足租户的可用性要求  | _适用于租户的云计算_|
|**不仅仅在网络层应用技术**。 每台主机和网络都应尽可能在可实际管理的最低级别进行分段和隔离。 在大多数情况下，从数据链路层一直到应用程序层都会应用分段和隔离；但是，在敏感环境下，物理隔离可能适用。 应补充部署基于主机和网络范围的措施并将其纳入集中监视。 使用防火墙或安全设备作为唯一安全措施是不够的。 |_ACSC 防护：实现网络分段和隔离_|
|**使用最小特权和需知原则**。 如果主机、服务或网络无需与另一台主机、服务或网络通信，则应禁止通信。 如果主机、服务或网络仅需使用特定端口或协议与其他主机、服务或网络通信，则应禁用任何其他端口或协议。 在整个网络中采用这些原则将进一步最小化用户特权，并显著提高环境的整体安全态势。 |_ACSC 防护：实现网络分段和隔离_|
|**根据主机和网络对业务运营的敏感性或严重程度，将主机和网络分隔**。 根据某些主机或网络的不同安全分类、安全域或可用性/完整性要求，可以使用不同的硬件或平台来实现分隔。 具体而言，分隔管理网络，并考虑物理隔离敏感环境的带外管理网络。 |_ACSC 防护：实现网络分段和隔离_|
|**标识、身份验证和授权所有实体对所有其他实体的访问权限**。 应限制所有用户、主机和服务的访问权限，仅限访问完成其指定职责或功能所需的其他用户、主机和服务。 应禁用跳过或降低标识、身份验证和授权服务强度级别的所有旧的或本地服务，并密切监视其使用情况。 |_ACSC 防护：实现网络分段和隔离_|
|**实现网络流量允许列表，而非拒绝列表**。 仅允许已知良好的网络流量的访问（即已经过标识、身份验证和授权），而不是拒绝已知不良网络流量的访问（例如，阻止特定地址或服务）。 使用已接受的发送方列表会形成一个优于阻止列表的安全策略，并显著提高组织检测和评估潜在网络入侵的能力。 |_ACSC 防护：实现网络分段和隔离_|
|

本文对 Azure 中使用基础结构即服务 (IaaS) 和平台即服务 (PaaS) 部署的系统如何满足这些要求提供了信息和建议。 你还应阅读[控制 Azure 澳大利亚的出口流量](gateway-egress-traffic.md)一文，充分了解如何控制 Azure 中的网络流量。

## <a name="architecture"></a>体系结构

如果要参与设计或实现网络安全和入口流量控制，必须首先了解入口网络流量在 Azure 内跨 IaaS 和 PaaS 的工作原理。 本部分将概述网络流量到达 Azure 托管资源可能途径的入口点以及可用于限制和控制该流量的安全控制措施。 本指南接下来将详细讨论其中的每个组件。

### <a name="architecture-components"></a>体系结构组件

此处显示的体系结构示意图描述了网络流量连接到 Azure 托管服务可能使用的路径。 根据为入口流量提供的功能，这些组件分为 Azure、IaaS 入口、PaaS 入口和安全控制措施。

![体系结构](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure 组件

|组件 | 说明|
|---|---|
|**DDoS 保护** | 分布式拒绝服务 (DDoS) 攻击试图耗尽应用程序的资源，使合法用户无法使用应用程序。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。 Azure 通过 Azure 平台自动提供 DDoS 防护，并提供额外的缓解功能，可针对特定应用程序启用以实现更精细的控制。|
| **流量管理器** | Azure 流量管理器是一种基于域名系统 (DNS) 的流量负载均衡器，可以在 Azure 区域内以最佳方式向服务分配流量，同时提供高可用性和响应度。 流量管理器根据流量路由方法和终结点的运行状况，使用 DNS 将客户端请求定向到最合适的终结点。|
| **ExpressRoute** | ExpressRoute 是用于使用 Microsoft 云服务的专用网络连接。 它通过连接提供商预配，相比典型的 Internet 连接，它提供更高的可靠性、更快的速度、更低的延迟，以及更高的安全性。 ExpressRoute 线路表示通过连接提供商在本地基础结构与 Microsoft 云服务之间建立的逻辑连接。|
| **ExpressRoute 专用对等互连** | ExpressRoute 专用对等互连是本地环境与专用 Azure 虚拟网络之间的连接。 通过专用对等互连，可以访问在虚拟网络中部署的 Azure 服务（例如虚拟机）。 通过专用对等互连访问的资源和虚拟网络是对组织核心网络的扩展。 专用对等互连使用专用 IP 地址在本地网络和 Azure 虚拟网络之间提供双向连接。|
| **ExpressRoute Microsoft 对等互连** | ExpressRoute Microsoft 对等互连是本地环境与 Microsoft 和 Azure 公共服务之间的连接。 这包括连接到 Microsoft 365、Dynamics 365 和 Azure PaaS 服务。 对等互连是通过组织或连接提供商拥有的公共 IP 地址建立的。 默认情况下，不能通过 ExpressRoute Microsoft 对等互连访问任何服务，组织必须选择加入所需的服务。 然后，此过程将建立与 Internet 上提供的相同终结点的连接。|
|

### <a name="iaas-ingress-components"></a>IaaS 入口组件

|组件 | 说明|
|---|---|
|**网络接口** | 网络接口是 Azure 中存在的资源。 它附加到虚拟机，并具有从与其关联的子网分配的一个不可通过 Internet 路由的专用 IP 地址。 此 IP 地址通过 Azure 资源管理器以动态或静态方式进行分配。|
|**子网** | 子网是在 VNet 中创建的 IP 地址范围。 可以在 VNet 中创建多个子网，以便进行网络分段。|
| **虚拟网络 (VNet)** | VNet 是 Azure 中的基础资源，提供用于部署资源和启用通信的平台和边界。 VNet 位于 Azure 区域内，用于定义 VNet 集成资源（例如虚拟机）的 IP 地址空间和路由边界。|
| **VNet 对等互连** | VNet 对等互连是一种 Azure 配置选项，可实现两个 VNet 之间的直接通信，而无需使用虚拟网络网关。 建立对等互连后，两个 VNet 可以直接通信，而其他配置可控制虚拟网络网关和其他传输选项的使用。|
| **公共 IP** | 公共 IP 是一种资源，它保留指定区域由 Microsoft 所有的、可通过 Internet 路由的其中一个公共 IP 地址，以供虚拟网络使用。 它可以与特定网络接口相关联，从而可以从 Internet、ExpressRoute 和 PaaS 系统访问资源。|
| **ExpressRoute 网关** | ExpressRoute 网关是虚拟网络中的一个对象，它通过 ExpressRoute 线路上的专用对等互连，提供从虚拟网络到本地网络的连接和路由。|
| **VPN 网关** | VPN 网关是虚拟网络中的一个对象，它提供从虚拟网络到外部网络的加密隧道。 加密隧道可以是用于与本地环境、其他虚拟网络或云环境进行双向通信的站点到站点连接，也可以是用于与单一终结点进行通信的点到站点连接。|
| **PaaS VNet 集成** | 许多 PaaS 功能可以部署到虚拟网络中或与虚拟网络集成。 某些 PaaS 功能可以与 VNet 完全集成，并且仅通过专用 IP 地址访问。 Azure 负载均衡器和 Azure 应用程序网关等其他功能可以在虚拟网络中具有一个带公共 IP 地址的外部接口和一个带专用 IP 地址的内部接口。 在此实例中，流量可以通过 PaaS 功能流入虚拟网络。|
|

### <a name="paas-ingress-components"></a>PaaS 入口组件

|组件 | 说明|
|---|---|
|**主机名** | Azure PaaS 功能由创建资源时分配的唯一公共主机名标识。 随后在公共 DNS 域中注册此主机名，在其中会将其解析为公共 IP 地址。|
|**公共 IP** | 除非将 Azure PaaS 功能部署在 VNet 集成配置中，否则该功能可通过 Internet 可路由的公共 IP 地址访问。 此地址可以专用于特定资源（例如公共负载均衡器），也可以与为多个实例提供共享入口点的特定功能（例如存储 或 SQL）相关联。 此公共 IP 地址可通过 Internet、ExpressRoute 访问，也可以通过 Azure 主干网络的 IaaS 公共 IP 地址访问。|
|**服务终结点** | 服务终结点提供从虚拟网络到特定 PaaS 功能的直接专用连接。 服务终结点仅适用于部分 PaaS 功能，可为 VNet 中访问 PaaS 的资源提供更高的性能和安全性。|
|

### <a name="security-controls"></a>安全控件

|组件 | 说明|
|---|---|
|**网络安全组 (NSG)** | NSG 控制流入和流出 Azure 虚拟网络资源的流量。 NSG 对允许或拒绝的流量流应用规则，这些流量包括 Azure 内部的流量，以及在 Azure 与外部网络（例如本地或 Internet）之间传送的流量。 NSG 应用于虚拟网络中的子网，或应用于单个网络接口。|
|**PaaS 防火墙** | 许多 PaaS 功能（例如存储 和 SQL）都具有内置防火墙，用于控制流入到特定资源的网络流量。 可创建规则以允许或拒绝来自特定 IP 地址和/或虚拟网络的连接。|
|**PaaS 身份验证和访问控制** | 作为分层安全方法的一部分，PaaS 功能提供多种机制对用户进行身份验证并控制特权和访问权限。|
|**Azure Policy** | Azure Policy 是 Azure 提供的一种服务，用于创建、分配和管理策略。 这些策略使用规则来控制可部署的资源类型以及这些资源的配置。 策略可用于强制实施合规性，如果资源不符合要求，则阻止其部署，也可用于监视以报告合规性状态。|
|

## <a name="general-guidance"></a>一般指南

若要在 Azure 中设计和构建安全解决方案，必须了解和控制网络流量，以便只进行已标识和授权的通信。 本指南和接下来几节有关特定组件的指南旨在介绍可用的工具和服务，以便在 Azure 工作负载中应用 _ACSC 防护：实现网络分段和隔离_ 中概述的原则。 其中将详细介绍当无法应用与本地环境中可能应用的相同的传统物理和网络控制措施时，如何创建用于保护资源的虚拟体系结构。

### <a name="specific-focus-areas"></a>特定重点区域

* 限制虚拟网络的入口点数
* 限制公共 IP 地址数量
* 考虑将 Microsoft 虚拟数据中心 (VDC) 文档中所讨论的中心和分支网络设计用于虚拟网络。
* 利用具有内置安全功能的产品建立 Internet 入站连接（例如应用程序网关、API 网关、网络虚拟设备）
* 限制 PaaS 功能的通信流，使之仅为系统功能所需
* 在 VNet 集成配置中部署 PaaS，以增强隔离和控制
* 将系统配置为使用符合 ACSC“使用者指南”和 ISM 的加密机制
* 除传统的网络控制以外，还使用基于标识的防护，例如身份验证和 Azure 基于角色的访问控制
* 实现 ExpressRoute 以与本地网络建立连接
* 实现 VPN 以管理与外部网络的流量和集成
* 利用 Azure Policy 限制区域和资源，使之仅为系统功能所需
* 利用 Azure Policy 为可访问 Internet 的资源强制实施基线安全配置

### <a name="additional-resources"></a>其他资源

|资源 | 链接|
|---|---|
|澳大利亚法规和策略合规性文档，包括“使用者指南”|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 虚拟数据中心|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC 网络分段|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|适用于租户的 ACSC 云安全| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC 信息安全手册|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>组件指南

本部分提供与 Azure 中部署的系统的入口流量相关的各个组件的进一步指导。 各部分都将介绍特定组件的用途，并提供协助完成设计和构建活动的文档和配置指南的链接。

## <a name="azure"></a>Azure

与 Azure 内资源的所有通信都通过 Microsoft 维护的网络基础结构进行，该基础结构提供连接和安全功能。 Microsoft 会自动提供一系列防护措施来保护 Azure 平台和网络基础结构，其他功能作为 Azure 服务提供，用于控制网络流量和建立网络分段和隔离。

### <a name="ddos-protection"></a>DDoS 保护

可通过 Internet 访问的资源容易受到 DDoS 攻击。 为了防范这些攻击，Azure 提供“基本”和“标准”级别的 DDoS 防护。

“基本”级别作为 Azure 平台的一部分自动启用，包括始终启用的流量监视和常见网络级别攻击的实时缓解，从而提供与 Microsoft 联机服务相同的防御措施。 整个 Azure 全球网络的规模可用于跨区域分散和缓解攻击流量。 为 IPv4 和 IPv6 Azure 公共 IP 地址提供防护

“标准”级别在专门针对 Azure 虚拟网络资源优化的“基本”服务层级上提供其他风险缓解功能。 通过专用流量监视和机器学习算法调整保护策略。 为 IPv4 Azure 公共 IP 地址提供保护。

|资源|链接|
|---|---|
|Azure DDOS 防护概述|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](../ddos-protection/ddos-protection-overview.md)|
|Azure DDoS 最佳做法|[https://docs.microsoft.com/azure/ddos-protection/fundamental-best-practices](../ddos-protection/fundamental-best-practices.md)|
|管理 DDoS 防护|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)|
|

### <a name="traffic-manager"></a>流量管理器

流量管理器通过控制用于接收连接的应用程序终结点来管理入口流量。 若要防止系统或应用程序因网络安全攻击而丧失可用性，或要在系统遭入侵后恢复服务，可以使用流量管理器将流量重定向到正常运行的可用应用程序实例。

|资源|链接|
|---|---|
|流量管理器概述 | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](../traffic-manager/traffic-manager-overview.md)|
|使用 Azure DNS 和流量管理器进行灾难恢复指南 | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](../networking/disaster-recovery-dns-traffic-manager.md)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute 可用于建立从本地环境到 Azure 托管系统的专用路径。 此连接可以提供更高的可靠性和有保障的性能，并增强网络通信的隐私性。 ExpressRoute 允许英联邦实体控制来自本地环境的入站流量，并定义特定于组织的专用地址，以用于入站防火墙规则和访问控制列表。

|资源 | 链接|
|---|---|
|ExpressRoute 概述 | [https://docs.microsoft.com/azure/expressroute/](../expressroute/index.yml)|
|ExpressRoute 连接模型 | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](../expressroute/expressroute-connectivity-models.md)|
|

### <a name="expressroute-private-peering"></a>ExpressRoute 专用对等互连

专用对等互连提供了一种机制，即仅使用专用 IP 地址便可将本地环境扩展到 Azure。 这样英联邦实体就能够将 Azure 虚拟网络和地址范围与现有本地系统和服务集成。 专用对等互连可保证仅与组织授权的虚拟网络进行 ExpressRoute 通信。 如果使用专用对等互连，则根据 ACSC“使用者指南”的要求，英联邦实体必须实现网络虚拟设备 (NVA) 而非 Azure VPN 网关来建立与本地网络的安全 VPN 通信。

|资源 | 链接|
|---|---|
|ExpressRoute 专用对等互连概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|ExpressRoute 专用对等互连操作指南 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](../expressroute/expressroute-howto-routing-portal-resource-manager.md#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute Microsoft 对等互连

Microsoft 对等互连可提供与 Microsoft 公共服务的高速、低延迟连接，而无需遍历 Internet。 这使得连接具有更高的可靠性、性能和隐私性。 通过使用路由筛选器，英联邦实体可以将通信限制为仅限其所需的 Azure 区域，但该区域包括其他组织托管的服务，可能需要在本地环境和 Microsoft 之间进行额外的筛选或检查。

英联邦实体可以使用通过对等互连关系建立的专用公共 IP 地址来唯一标识本地环境，以便在 PaaS 功能内的防火墙和访问控制列表中使用。

或者，英联邦实体可以将 ExpressRoute Microsoft 对等互连用作基础网络，以便通过 Azure VPN 网关建立 VPN 连接。 在此模型中，内部本地网络通过 ExpressRoute 连接到 Azure 公共服务的通信并非活动通信，但与专用虚拟网络已建立安全连接，且符合 ACSC“使用者指南”的要求。

|资源 | 链接|
|---|---|
|ExpressRoute Microsoft 对等互连概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|ExpressRoute Microsoft 对等互连操作指南 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](../expressroute/expressroute-howto-routing-portal-resource-manager.md#msft)|
|

## <a name="iaas-ingress"></a>IaaS 入口

本部分提供用于控制 IaaS 组件入口流量的组件指导。 IaaS 包含虚拟机和其他可在 Azure 虚拟网络中部署和管理的计算资源。 要使流量到达使用 IaaS 部署的系统，必须具有虚拟网络的入口点，可以通过公共 IP 地址、虚拟网络网关或虚拟网络对等互连关系来建立此入口点。

### <a name="network-interface"></a>Linux

网络接口是所有流量流入虚拟机的入口点。 网络接口支持配置 IP 寻址，且可用于应用 NSG 或通过网络虚拟设备路由流量。 虚拟机的网络接口应进行适当规划和配置，以与总体网络分段和隔离目标保持一致。

|资源 | 链接|
|---|---|
|创建、更改或删除网络接口 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md)|
|网络接口 IP 寻址 | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/private-ip-addresses.md)|
|

### <a name="subnet"></a>子网

子网是在 Azure 中进行网络分段和隔离的关键组件。 可以按相似方式使用子网来实现系统之间的分隔。 可将 NSG 应用于子网，以限制入口通信流，使之仅为系统功能所需。 子网可用作防火墙规则和访问控制列表的源地址和目标地址，并可针对服务终结点进行配置，以建立与 PaaS 功能的连接。

|资源 | 链接|
|---|---|
|添加、更改或删除虚拟网络子网 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)|
|

### <a name="virtual-network-vnet"></a>虚拟网络 (VNet)

VNet 是 Azure 网络的基本构建基块之一。 虚拟网络定义可用于各种系统的 IP 地址空间和路由边界。 虚拟网络分为多个子网，虚拟网络内的所有子网在相互之间均具有直接网络路由。 通过使用虚拟网络网关（ExpressRoute 或 VPN），本地和外部环境可以访问虚拟网络内的系统。 了解虚拟网络和关联的配置参数和路由对于了解和控制入口网络流量至关重要。

|资源 | 链接|
|---|---|
|虚拟网络概述 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md)|
|规划虚拟网络操作指南 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|
创建虚拟网络快速入门 | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)|
|

### <a name="vnet-peering"></a>VNet 对等互连

VNet 对等互连用于在两个虚拟网络之间提供直接通信路径。 建立对等互连后，一个虚拟网络中的主机就可以通过高速路由路径直接连接到另一个虚拟网络中的主机。 NSG 仍可正常应用于流量，并且可以使用高级配置参数来定义是否允许通过虚拟网络网关或从其他外部系统建立通信。

|资源 | 链接|
|---|---|
|虚拟网络对等互连概述 |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md)|
|创建、更改或删除虚拟网络对等互连 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="public-ip-on-vnet"></a>VNET 中的公共 IP

公共 IP 地址用于为虚拟网络中部署的服务提供入口通信路径。 英联邦实体应仔细规划公共 IP 地址的分配，并仅将其分配给真正需要它的资源。 一般设计实践而言，公共 IP 地址应分配给具有内置安全功能的资源（例如应用程序网关或网络虚拟设备），以便提供安全受控的虚拟网络公共入口点。

|资源 | 链接|
|---|---|
|公共 IP 地址概述 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/public-ip-addresses.md#public-ip-addresses)|
|创建、更改或删除公共 IP 地址 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/virtual-network-public-ip-address.md)|
|

### <a name="expressroute-gateway"></a>ExpressRoute 网关

ExpressRoute 网关针对本地环境提供入口点，应将其部署以满足安全性、可用性、财务和性能要求。 ExpressRoute 网关提供定义的网络带宽，并在部署后产生使用成本。 虚拟网络中只能部署一个 ExpressRoute 网关，但它可以连接到多个 ExpressRoute 线路，并可由多个虚拟网络通过 VNet 对等互连使用，从而支持多个虚拟网络共享带宽和连接。 请务必小心使用 ExpressRoute 网关配置本地环境和虚拟网络之间的路由，确保使用已知的受控网络入口点进行端到端连接。 使用 ExpressRoute 网关的英联邦实体还必须部署网络虚拟设备来建立与本地环境的 VPN 连接，从而符合 ACSC“使用者指南”的要求。

|资源 | 链接|
|---|---|
|ExpressRoute 网关概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md)|
|配置 ExpressRoute 的虚拟网络网关 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)|
|

### <a name="vpn-gateway"></a>VPN 网关

Azure VPN 网关针对外部网络提供网络入口点，以实现安全的站点到站点连接或点到站点连接。 VPN 网关提供定义的网络带宽，并在部署后产生使用成本。 使用 VPN 网关的英联邦实体应确保根据 ACSC“使用者指南”的要求配置此网关。 虚拟网络中只能部署一个 VPN 网关，但它可以配置多个隧道，并可由多个虚拟网络通过 VNet 对等互连使用，从而支持多个虚拟网络共享带宽和连接。 可以通过 Internet 或 Microsoft 对等互连的 ExpressRoute 来建立 VPN 网关。

|资源 | 链接|
|---|---|
|VPN 网关概述 | [https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|规划和设计 VPN 网关 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
|适用于澳大利亚政府机构的 VPN 网关配置|[澳大利亚政府机构所需的 IPSEC 配置](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS VNet 集成

利用 PaaS 可以增强功能、提高可用性以及减少管理开销，但必须为其提供相应保护。 若要提高控制、强制网络分段或为应用程序和服务提供安全入口点，可以将许多 PaaS 功能与虚拟网络集成。

若要提供安全入口点，可以为应用程序网关等 PaaS 功能配置一个面向公众的外部接口和一个内部专用接口，以便与应用程序服务进行通信。 这样便无需为应用程序服务器配置公共 IP 地址并将其公开给外部网络。

为了将 PaaS 用作系统或应用程序体系结构的集成部分，Microsoft 提供了多种机制来将 PaaS 部署到虚拟网络中。 部署方法会限制外部网络（例如 Internet）的入站访问，同时提供与内部系统和应用程序的连接和集成。 示例包括应用服务环境、SQL 托管实例等。

|资源 | 链接|
|---|---|
|Azure 服务的虚拟网络集成 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md)|
|将应用与 Azure 虚拟网络集成操作指南 | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/web-sites-integrate-with-vnet.md)|
|

## <a name="paas-ingress"></a>PaaS 入口

PaaS 功能可以增强功能和简化管理，但在解决网络分段和隔离需求方面增加了复杂性。 PaaS 功能通常配置有公共 IP 地址，可以通过 Internet 进行访问。  使用 PaaS 功能构建系统时，应小心确定系统内组件之间的所有必要通信流，以及创建用于仅允许进行这些通信的网络安全规则。 作为安全深层防御方法的一部分，应为 PaaS 功能配置加密、身份验证以及适当的访问控制和权限。  

### <a name="hostname"></a>主机名

PaaS 功能由主机名唯一标识，允许在同一公共 IP 地址上托管同一服务的多个实例。 创建资源且资源位于 Microsoft 拥有的 DNS 域中时，将指定唯一主机名。 具有应用程序级别筛选功能的安全工具内可以使用授权服务的特定主机名。 还可以根据要求为某些服务配置自定义域。

|资源 | 链接|
|---|---|
|通过 PowerShell 运行 Get-AzureRMEnvironment 命令可以获取 Azure 服务使用的许多公共命名空间 | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](/powershell/module/azurerm.profile/get-azurermenvironment)|
|为 Azure 云服务配置自定义域名 | 应用服务和其他服务可以拥有自定义域 [https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](../cloud-services/cloud-services-custom-domain-name-portal.md)|
|

### <a name="public-ip-for-paas"></a>PaaS 公共 IP

PaaS 功能的公共 IP 地址根据托管或部署服务的区域进行分配。 若要构建适当的网络安全规则和路由拓扑，以实现涵盖 Azure 虚拟网络、PaaS 以及 ExpressRoute 和 Internet 连接的网络分段和隔离，你需要了解公共 IP 地址分配和区域。 Azure 会从分配到每个 Azure 区域的池中分配 IP 地址。 Microsoft 使每个区域中使用的地址可供下载，且这些地址以定期、受控的方式进行更新。 每个区域中的可用服务也经常随着新服务的发布或服务的更广泛部署而发生变化。 英联邦实体应定期查看这些资料，并可以根据需要利用自动化来维护系统。 可以通过联系 Microsoft 支持人员获取每个区域中托管的某些服务的特定 IP 地址。

|资源 | 链接|
|---|---|
|Microsoft Azure 数据中心 IP 范围 | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|每个区域的 Azure 服务 | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>服务终结点

虚拟网络服务终结点为虚拟网络中的子网提供高速专用入口网络连接，以便其使用特定的 PaaS 功能。 为实现 PaaS 功能的完整网络分段和隔离，必须将 PaaS 功能配置为仅接受来自所需虚拟网络的连接。 并非所有 PaaS 功能都支持包含服务终结点和传统基于 IP 地址的规则的防火墙规则组合，因此，应谨慎了解应用程序功能和管理所需的通信流，以便实现这些安全控制措施的同时不会影响服务的可用性。

|资源 | 链接|
|---|---|
|服务终结点概述 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](../virtual-network/virtual-network-service-endpoints-overview.md)
|教程 |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

## <a name="security"></a>安全

通过保护 IaaS 和 PaaS 功能本身的安全以及在将与这些功能进行通信的系统上实现受控的通信路径，可以实现对这些功能的网络分段和隔离控制。

在 Azure 中设计和构建解决方案是一个创建逻辑体系结构的过程，便于了解、控制和监视整个 Azure 中的网络资源。 此逻辑体系结构是在 Azure 平台中由软件定义的，它取代了传统网络环境中实现的物理网络拓扑。

创建的逻辑体系结构必须提供可用性所需的功能，但还必须提供安全性和完整性所需的可见性和控制。

实现必要的网络分段和隔离工具，并保护和强制实施网络拓扑和这些工具的实现，才能实现此目的。

本指南中提供的信息可用于帮助确定需要允许的入口流量源，以及进一步控制或约束流量的方式。

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

NSG 用于指定子网或特定网络接口允许的入站和出站流量。 配置 NSG 时，英联邦实体应采用审批列表方法，该方法将规则配置为允许必要的流量，将默认规则配置为拒绝与特定允许语句不匹配的所有流量。 务必小心规划和配置 NSG，确保正确捕获所有必要的入站和出站流量。 这包括识别和了解 Azure 虚拟网络和本地环境中使用的所有专用 IP 地址范围，以及特定 Microsoft 服务，例如 Azure 负载均衡器 和 PaaS 管理要求。 参与设计和实现网络安全组的人员还应了解如何使用服务标记和应用程序安全组，从而创建精细的、特定于服务和应用程序的安全规则。

|资源 | 链接|
|---|---|
|网络安全概述 | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)
|创建、更改或删除网络安全组 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

## <a name="paas-firewall"></a>PaaS 防火墙

PaaS 防火墙是一种可应用于某些 PaaS 服务的网络访问控制功能。 它允许配置 IP 地址筛选或特定虚拟网络筛选以限制特定 PaaS 实例的入口流量。 对于包含防火墙的 PaaS 功能，应根据应用程序要求将网络访问控制策略配置为仅允许必要的入口流量。  

|资源 | 链接|
|---|---|
|Azure SQL 数据库和 Azure Synapse Analytics IP 防火墙规则 | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](../azure-sql/database/firewall-configure.md)|
|存储网络安全 | [https://docs.microsoft.com/azure/storage/common/storage-network-security](../storage/common/storage-network-security.md)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS 身份验证和访问控制

根据 PaaS 功能及其用途，使用网络控制来限制访问可能不可行或不切实际。 作为 PaaS 分层安全模型的一部分，Azure 提供各种身份验证和访问控制机制来限制对服务的访问（即使允许网络流量）。 PaaS 功能的典型身份验证机制包括 Azure Active Directory、应用程序级别身份验证、共享密钥或访问签名。 安全标识用户后，可以使用角色来控制用户可以执行的操作。 这些工具可以用作替代方法或补充措施来限制对服务的访问。

|资源 | 链接|
|---|---|
|控制和授予对 SQL 数据库和 Azure Synapse Analytics 的数据库访问权限 | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](../azure-sql/database/logins-create-manage.md)|
|Azure 存储服务的授权 | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy 是强制实施和维护 Azure 环境逻辑体系结构完整性的关键组件。 鉴于 Azure 服务提供各种服务和入口网络流量路径，英联邦实体了解其环境中的资源以及可用网络入口点至关重要。 为了确保没有在 Azure 环境中创建未经授权的网络入口点，英联邦实体应利用 Azure Policy 来控制可部署的资源类型以及这些资源的配置。 实用示例包括：将资源限制为仅使用已授权和已审批的资源、对存储强制实施 HTTPS 加密，以及要求将 NSG 添加到子网。

|资源 | 链接|
|---|---|
|Azure Policy 概述 | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|允许的资源类型示例策略 | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)
|确保 HTTPS 存储帐户示例策略|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](../governance/policy/samples/index.md)_
|在子网上强制 NSG 示例策略| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="next-steps"></a>后续步骤

请查看[网关入口流量管理和控制](gateway-egress-traffic.md)一文，了解有关如何使用 Azure 中的网关组件管理从 Azure 环境流向其他网络的流量流的详细信息。