---
title: 控制 Azure 澳大利亚出口流量
description: 用于控制 Azure 出口流量以满足澳大利亚政府对安全 Internet 网关要求的关键元素
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yvettep
ms.openlocfilehash: e61c7688e24d3e44ee1a68105c7a0952c4e4f521
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "117028968"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>控制 Azure 澳大利亚出口流量

保护 ICT 系统的基本组件是控制网络流量。 将通信限制为系统正常运行所需的流量可降低被入侵的可能性。 洞察和掌控与你的应用程序和服务通信的外部系统有助于检测受感染的系统以及入侵者曾经尝试或已经得手的数据泄露攻击。 本文介绍了出站（出口）网络流量在 Azure 中的工作方式，并提供了一些针对连接 Internet 系统实施网络安全控制方面的建议，以确保符合澳大利亚网络安全中心（简称 ACSC）使用者指南和 ACSC 信息安全手册（简称 ISM）的意图。

## <a name="requirements"></a>要求

ISM 中定义了澳大利亚联邦系统的整体安全要求。 为协助澳大利亚联邦实体实现网络安全，ACSC 发布了 _ACSC 防护：实现网络分段和隔离_，以及，为协助保护云环境中的系统安全，ACSC 发布了 _适用于租户的云计算安全_。

ACSC 文档概述了实现网络安全和控制流量的背景，并提供了网络设计和配置的实用建议。

ACSC 文档中已标识以下用于控制 Azure 中出口流量的关键要求。

说明|源
--------------- |------------------
实现网络分段和隔离，例如，使用多层体系结构，使用基于主机的防火墙和网络访问控制将入站和出站网络连接限制为仅限所需的端口和协议。| [适用于租户的云计算](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
在租户（包括租户的远程用户）和云服务之间 **实现足够高的带宽、低延迟、可靠的网络连接**，以满足租户的可用性要求  | [ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**不仅仅在网络层应用技术**。 每台主机和网络都应尽可能在可实际管理的最低级别进行分段和隔离。 在大多数情况下，从数据链路层一直到应用程序层都会应用分段和隔离；但是，在敏感环境下，物理隔离可能适用。 应补充部署基于主机和网络范围的措施并将其纳入集中监视。 仅实施防火墙或安全设备作为唯一安全措施是不够的。 |[ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**使用最小特权和需知原则**。 如果主机、服务或网络无需与另一台主机、服务或网络通信，则应禁止通信。 如果主机、服务或网络只需要与特定端口或协议上的另一个主机、服务或网络通信，则应将限制为这些端口和协议。 在整个网络中采用这些原则将进一步最小化用户特权，并显著提高环境的整体安全态势。 |[ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**根据主机和网络对业务运营的敏感性或严重程度，将主机和网络分隔**。 根据某些主机或网络的不同安全分类、安全域或可用性/完整性要求，这可能需要使用不同的硬件或平台。 具体而言，分隔管理网络，并考虑物理隔离敏感环境的带外管理网络。 |[ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**标识、身份验证和授权所有实体对所有其他实体的访问权限**。 应限制所有用户、主机和服务的访问权限，仅限访问执行其指定职责或功能所需的其他用户、主机和服务。 应尽可能禁用跳过或降低标识、身份验证和授权服务强度级别的所有旧的或本地服务，并密切监视其使用情况。 |[ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**实现网络流量允许列表，而非拒绝列表**。 仅允许已知良好的网络流量的访问（已经过标识、经过身份验证和获得授权的流量），而不是拒绝已知不良网络流量的访问（例如，阻止特定地址或服务）。 允许列表会导致高级安全策略拒绝列表，并显著提高检测和评估潜在网络入侵的能力。 |[ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
“定义允许网站的允许列表并阻止所有未列出的网站”有效地消除了攻击者使用的最常见的数据传送和渗漏技术之一。 如果用户有合法要求要访问大量网站，或者网站列表变化很快，则应考虑此类实施的成本。 相比依赖拒绝列表或者没有任何限制，即使是相对宽松允许列表也能提高安全性，同时还降低了实施成本。 以下是一个宽松允许列表的示例：允许整个澳大利亚子域（即"*.au"），或者允许 Alexa 站点排名中前 1000 名的站点（在筛选动态域名系统 (DDNS) 域和其他不当域之后）。| [澳大利亚政府信息安全手册 (ISM)](https://www.cyber.gov.au/ism)
|

本文提供有关如何控制离开 Azure 环境的网络流量的信息和建议。 本文涵盖了使用基础结构即服务 (IaaS) 和平台即服务 (PaaS) 在 Azure 中部署的系统。

[网关入口流量](gateway-ingress-traffic.md)一文介绍了进入 Azure 环境的网络流量，是本文是关于全面覆盖网络控制一文的配套内容。

## <a name="architecture"></a>体系结构

为了适当地控制出口流量，在设计和实施网络安全时，必须首先了解出口网络流量在横跨 IaaS 和 PaaS 的 Azure 中的运行方式。 本部分概述了如何处理 Azure 中托管的资源生成的出站流量，以及可用于限制和控制该流量的安全控件。

### <a name="architecture-components"></a>体系结构组件

此处显示的体系结构关系图描绘了网络流量在离开部署到虚拟网络中某个子网中的系统时，可能采用的路径。 虚拟网络中的流量在子网级别进行管理和控制，路由和安全规则适用于子网中包含的资源。 与出口流量相关的组件分为系统、有效路由、下一跃点类型、安全控件和 PaaS 出口。

![体系结构](media/egress-traffic.png)

### <a name="systems"></a>系统

系统属于 Azure 资源，也是在虚拟网络中的 IP 子网中生成出站流量的相关组件。

| 组件 | 说明 |
| --- | ---|
|虚拟网络 (VNet) | VNet 是 Azure 中的基础资源，提供用于部署资源和启用通信的平台和边界。 VNet 位于 Azure 区域内，用于定义 VNet 集成资源（例如虚拟机）的 IP 地址空间和路由边界。|
|子网 | 子网是在 VNet 中创建的 IP 地址范围。 可以在一个 VNet 中创建多个子网，以便进行网络分段。|
|网络接口| 网络接口是 Azure 中的资源。 网络接口附加到虚拟机，并被分配了一个专用 IP 地址，无法从与之关联的子网中通过 Internet 路由到这个 IP 地址。 此 IP 地址通过 Azure 资源管理器以动态或静态方式分配的。|
|公共 IP| 公共 IP 是一种资源，它保留指定区域由 Microsoft 所有的、可通过 Internet 路由的其中一个公共 IP 地址，以供虚拟网络使用。 公共 IP 可以与特定的网络接口或 PaaS 资源相关联，使资源能够与 Internet、ExpressRoute 和其他 PaaS 系统通信。|
|

### <a name="routes"></a>路由

出口流量采用的路径取决于该资源的有效路由，这是一组路由的集合，通过组合从所有可能的来源学习的路由以及应用 Azure 路由逻辑来确定。

| 组件 | 说明 |
|--- | ---|
|系统路由| Azure 自动创建系统路由，并将路由分配到虚拟网络中的每个子网。 无法创建或删除系统路由，但可以使用自定义路由覆盖某些路由。 当使用特定的 Azure 功能时，Azure 为每个子网创建默认系统路由，并向特定子网或每个子网添加其他可选默认路由。|
|服务终结点| 服务终结点提供从子网到特定 PaaS 功能的直接专用出口连接。 服务终结点仅适用于部分 PaaS 功能，可为 VNet 中访问 PaaS 的资源提供更高的性能和安全性。|
|路由表| 路由表是 Azure 中的一种资源，可创建该资源以指定由用户定义的路由 (UDR)，后者可以补充或替代系统路由或者通过边界网关协议学习到的路由。 每个 UDR 指定一个网络、一个网络掩码和一个下一跃点。 一个路由表可以关联到一个子网，同一路由表可以关联到多个子网，但一个子网最多只能有一个路由表。|
|边界网关协议 (BGP)| BGP 是一种自治系统间的路由协议。 自治系统是一个网络或接受共同管理的一组网络，并具有常见路由策略。 BGP 用于在自治系统之间交换路由信息。 BGP 可以通过虚拟网络网关集成到虚拟网络中。|
|

### <a name="next-hop-types-defined"></a>定义的下一跃点类型

Azure 中的每个路由包括网络范围、关联的子网掩码和下一跃点，用于确定如何处理流量。

下一跃点类型 | 说明
---- | ----
**虚拟网络** | 在虚拟网络的地址空间中的地址范围之间路由流量。 Azure 使用地址前缀创建路由，该前缀对应的每个地址范围是在虚拟网络的地址空间中定义的。 如果虚拟网络地址空间定义了多个地址范围，Azure 会为每个地址范围创建单个路由。 Azure 使用为每个地址范围创建的路由在 VNet 中子网之间自动路由流量。
**VNet 对等互连** | 在两个虚拟网络之间创建虚拟网络对等互连时，会针对每个虚拟网络的每个地址范围，将路由添加到对等互连到的虚拟网络。 流量在对等互连的虚拟网络之间的路由方式与在虚拟网络中的子网之间的路由方式相同。
**虚拟网络网关** | 向虚拟网络添加虚拟网关时，会添加一个或多个将“虚拟网关”列为下一跃点类型的路由。 包括的路由是在本地网络网关资源中配置的路由，以及通过 BGP 学习的任何路由。
**虚拟设备** | 虚拟设备通常运行防火墙等网络应用程序。 虚拟设备可以对流量进行额外的处理，例如筛选、检查或地址转换。 每个具有虚拟设备跃点类型的路由还必须指定下一跃点 IP 地址。
**VirtualNetworkServiceEndpoint** | 启用服务终结点时，特定服务的公共 IP 地址将被作为路由添加到下一个跃点为 VirtualNetworkServiceEndpoint 的子网。 服务终结点是针对虚拟网络中的单个子网上的单个服务启用的。 Azure 服务的公共 IP 地址定期更改。 当地址更改时，Azure 自动管理路由表中的地址。
**Internet** | 下一跃点为 Internet 的流量将退出虚拟网络，并自动转换为公共 IP 地址（来自关联 Azure 区域中提供的动态池，或者使用为该资源配置的公共 IP 地址）。 如果目标地址是用于某个 Azure 服务的，则会将流量通过 Azure 的主干网络直接路由到该服务，而不是将流量路由到 Internet。 Azure 服务之间的流量不跨越 Internet，不管虚拟网络存在于哪个 Azure 区域，也不管 Azure 服务的实例部署在哪个 Azure 区域。
**无** | 下一跃点为“none”的流量将被删除。 Azure 将为保留的地址前缀创建系统默认路由，并以“none”作为下一跃点类型。 也可使用路由表添加下一跃点为“none”的路由，以防止将流量路由到特定网络。
|

### <a name="security-controls"></a>安全控件

控制 | 说明
----- | -----
**网络安全组 (NSG)** | NSG 控制流入和流出 Azure 虚拟网络资源的流量。 NSG 对允许或拒绝的流量流应用规则，这些流量包括 Azure 内部的流量，以及在 Azure 与外部网络（例如本地或 Internet）之间传送的流量。 NSG 应用于虚拟网络中的子网，或应用于单个网络接口。
**Azure 防火墙** | Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 配置 Azure 防火墙时，可以使用基于 IP 地址、协议和端口的传统网络筛选规则，也支持基于完全限定域名 (FQDN)、服务标签和内置威胁情报进行筛选。
**网络虚拟设备 (NVA)** | 网络虚拟设备是指能够为 Azure 提供网络、安全性和其他功能的虚拟机媒体。 NVA 支持虚拟网络和部署中的 VM 形式的网络功能和服务。 NVA 可用于满足特定要求、与管理和操作工具集成，或与现有产品保持一致。 Azure 支持广泛的第三方网络虚拟设备，包括 Web 应用程序防火墙 (WAF)、防火墙、网关/路由器、应用程序传送控制器 (ADC) 和 WAN 优化器。
**服务终结点策略（预览版）** | 使用虚拟网络服务终结点策略可以通过服务终结点筛选发往 Azure 服务的虚拟网络流量，以便仅允许特定的 Azure 服务资源。 终结点策略针对发往 Azure 服务的虚拟网络流量提供精细的访问控制。
**Azure Policy** | Azure Policy 是 Azure 提供的一种服务，用于创建、分配和管理策略。 这些策略使用规则来控制可部署的资源类型以及这些资源的配置。 策略可用于强制实施合规性，如果资源不符合要求，则阻止其部署，也可用于监视以报告合规性状态。
|

### <a name="paas-egress"></a>PaaS 出口

大多数 PaaS 资源不会生成出口流量，而是响应入站请求（例如应用程序网关、存储、SQL 数据库等）或中继来自其他资源的数据（例如服务总线和 Azure 中继）。 PaaS 资源（如应用服务到存储或 SQL 数据库）之间的网络通信流是由 Azure 来管控，并通过标识和其他资源配置控制措施（并非网络分段或隔离）加以保护。

部署到虚拟网络中的 PaaS 资源接收专用 IP 地址，并像虚拟网络中的其他资源一样，受到所有路由控制规则和 NSG 限制。 虚拟网络中不存在的 PaaS 资源将利用在资源的所有实例之间共享的 IP 地址池，这些 IP 地址或者是通过 Microsoft 文档发布，也可通过 Azure 资源管理器确定。

## <a name="general-guidance"></a>一般指南

若要在 Azure 中设计和构建安全解决方案，必须了解和控制网络流量，以便只进行已标识和授权的通信。 本指南和接下来几节有关特定组件的指南旨在介绍可用的工具和服务，以便在 Azure 工作负载中应用 [ACSC 防护：实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)中概述的原则。 其中将详细介绍当无法应用与本地环境中可能应用的相同的传统物理和网络控制措施时，如何创建用于保护资源的虚拟体系结构。

### <a name="guidance"></a>指南

* 限制虚拟网络出口点的数量
* 覆盖不需要与 Internet 进行直接出站通信的所有子网的系统默认路由
* 设计和实现完整的网络体系结构，以标识和控制 Azure 资源的所有入口点和出口点
* 考虑将 Microsoft 虚拟数据中心 (VDC) 文档中所讨论的中心和分支网络设计用于虚拟网络
* 利用内置安全功能的产品实现与 Internet 的出站连接（例如Azure 防火墙、网络虚拟设备或 Web 代理）
* 使用标识控制（例如 Azure 基于角色的访问控制、条件访问和多重身份验证 (MFA)）来限制网络配置特权
* 实现锁以防止修改或删除网络配置的关键元素
* 在 VNet 集成配置中部署 PaaS，以增强隔离和控制
* 实现 ExpressRoute 以与本地网络建立连接
* 实施 VPN 以与外部网络集成
* 利用 Azure Policy 限制区域和资源，使之仅为系统功能所需
* 利用 Azure Policy，针对资源强制实施基线安全配置
* 利用网络观察程序和 Azure Monitor 来记录、审核和洞察 Azure 中的网络流量

### <a name="resources"></a>资源

项目 | 链接
-----------| ---------
_澳大利亚法规和策略合规性文档，包括“使用者指南”_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure 虚拟数据中心_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC 网络分段_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_适用于租户的 ACSC 云安全_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC 信息安全手册_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>组件指南

本部分提供与 Azure 中部署的系统的出口流量相关的各个组件的进一步指导。 各部分都将介绍特定组件的用途，并提供协助完成设计和构建活动的文档和配置指南的链接。

### <a name="systems-security"></a>系统安全性

与 Azure 内资源的所有通信都通过 Microsoft 维护的网络基础结构进行，该基础结构提供连接和安全功能。 Microsoft 会自动提供一系列防护措施来保护 Azure 平台和网络基础结构，其他功能作为 Azure 服务提供，用于控制网络流量和建立网络分段和隔离。

### <a name="virtual-network-vnet"></a>虚拟网络 (VNet)

虚拟网络是 Azure 网络的基本构建基块之一。 虚拟网络定义可用于各种系统的 IP 地址空间和路由边界。 虚拟网络分为多个子网，虚拟网络内的所有子网在相互之间均具有直接网络路由。 通过使用虚拟网络网关（ExpressRoute 或 VPN），虚拟网络中的系统可以与本地和外部环境集成，而通过 Azure 提供的网络地址转换 (NAT) 和公共 IP 地址分配，系统可以连接到 Internet 或其他 Azure 地区和服务。 了解虚拟网络和关联的配置参数和路由对于了解和控制出口网络流量至关重要。

由于虚拟网络构成了 Azure 中的基地址空间和路由边界，因此可以将虚拟网络用作网络分段和隔离的主要构建基块。

| 资源 | 链接 |
| --- | --- |
| *虚拟网络概述* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md) |
| *规划虚拟网络操作指南*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md) |
| *创建虚拟网络快速入门* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)
|

### <a name="subnet"></a>子网

子网是在 Azure 中进行网络分段和隔离的关键组件。 可以使用子网来实现系统之间的分隔。 子网是虚拟网络中应用了 NSG、路由表和服务终结点的资源。 子网可以用作防火墙规则和访问控制列表的源和目标地址。

应计划虚拟网络中的子网，以满足工作负荷和系统的要求。 设计或实现子网的相关人员应参考 ACSC 准则中的网络分段内容，确定如何将系统分组到一个子网中。

|资源|链接|
|---|---|
|*添加、更改或删除虚拟网络子网* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)
|

### <a name="network-interface"></a>Linux

网络接口是来自虚拟机的所有出口流量的源。 网络接口支持配置 IP 寻址，且可用于应用 NSG 或通过 NVA 路由流量。 虚拟机的网络接口应进行适当规划和配置，以与总体网络分段和隔离目标保持一致。

|资源|链接|
|---|---|
|*创建、更改或删除网络接口* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md) |
|*网络接口 IP 寻址*               | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/private-ip-addresses.md)
|

### <a name="vnet-integrated-paas"></a>VNet 集成 PaaS

PaaS 可以增强功能、提高可用性以及减少管理开销，但必须为其提供相应保护。 若要提高控制、强制网络分段或为应用程序和服务提供安全出口点，可以将许多 PaaS 功能与虚拟网络集成。

将 PaaS 用作系统或应用程序体系结构的集成部分时，Microsoft 提供了多种机制来将 PaaS 部署到虚拟网络中。 部署方法有助于限制访问，同时仍提供与内部系统和应用程序的连接和集成。 示例包括应用服务环境、SQL 托管实例等。

将 PaaS 部署到已实施路由和 NSG 控制的虚拟网络中时，必须了解资源的特定通信要求，包括 Microsoft 服务中的管理访问权限，以及在从这些服务中答复传入请求时通信流量将使用的路径。

| 资源  | 链接  |
| --- | --- |
| *Azure 服务的虚拟网络集成* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md) |
| *将应用与 Azure 虚拟网络集成操作指南* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/web-sites-integrate-with-vnet.md)
|

### <a name="public-ip"></a>公共 IP

在虚拟网络外部进行通信时使用公共 IP 地址。 这包括 PaaS 资源以及下一跃点为 Internet 的任何路由。 澳大利亚联邦实体应仔细规划公共 IP 地址的分配，并仅将其分配给真正需要它的资源。 按照一般设计实践，公共 IP 地址应分配给虚拟网络（例如 Azure 防火墙、VPN 网关或网络虚拟设备）的受控出口点。

|资源|链接|
|---|---|
|*公共 IP 地址概述*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/public-ip-addresses.md#public-ip-addresses) |
|*创建、更改或删除公共 IP 地址* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/virtual-network-public-ip-address.md)
|

## <a name="effective-routes"></a>有效路由

有效路由是通过系统路由、服务终结点、路由表以及 BGP 和 Azure 路由逻辑的组合而决定的结果集合。 当出站流量是从子网发送时，Azure 使用最长前缀匹配算法，根据目标 IP 地址选择路由。 如果多个路由包含同一地址前缀，Azure 根据以下优先级选择路由类型：

1. 用户定义的路由
2. BGP 路由
3. 系统路由

因此务必要注意，即使 BGP 路由更具体，与虚拟网络、虚拟网络对等互连或虚拟网络服务终结点相关的流量的系统路由也仍是首选路由。

在 Azure 中设计或实施路由拓扑的相关人员应了解 Azure 如何路由流量，并制定一种体系结构，以用于平衡系统的必要功能和所需的安全和可见性。 应慎重规划环境，避免路由行为出现过多异常和需要干预的情况，因为这会增加复杂性，并可能会导致故障排除和错误查找更困难、更耗时。

| 资源 | 链接  |
| --- | --- |
| *查看有效路由* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>系统路由

对于[系统路由](../virtual-network/virtual-networks-udr-overview.md#system-routes)，设计或实施虚拟网络的相关人员应了解默认的系统路由，以及可用于补充或替代这些系统路由的选项。

### <a name="service-endpoints"></a>服务终结点

在子网上启用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)能够通过与相关 PaaS 资源的直接通信路径。 通过将可用的通信路径限制为仅限该服务，便可提高性能和安全性。 使用服务终结点会引入潜在的数据外泄路径，这是因为默认配置允许访问 PaaS 服务的所有实例，而非应用程序或系统所需的特定实例。

澳大利亚联邦实体应评估提供 PaaS 资源的直接访问权限的相关风险，包括滥用路径的可能性和后果。

要降低与服务终结点相关的潜在风险，可以尽可能地实施服务终结点策略，或者考虑在 Azure 防火墙或 NVA 子网上启用服务终结点，并从能够应用其他筛选、监视或检查的特定子网路由流量。

|资源|链接|
|---|---|
|*教程：使用 Azure 门户通过虚拟网络服务终结点限制对 PaaS 资源的网络访问* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

### <a name="route-tables"></a>路由表

路由表提供管理员配置的机制，用于控制 Azure 中的网络流量。 可以利用路由表将流量转发到 Azure 防火墙或 NVA，直接连接到外部资源，或者替代 Azure 系统路由。 利用路由表，还可以避免将通过虚拟网络网关学习到的网络提供给子网中的资源使用，只需要禁用虚拟网络网关路由传播即可。

|资源|链接|
|---|---|
|*路由概念 - 自定义路由* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes)|
|*教程：路由网络流量* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](../virtual-network/tutorial-create-route-table-portal.md)|
|

### <a name="border-gateway-protocol-bgp"></a>边界网关协议 (BGP)

虚拟网络网关可以利用 BGP 与本地或其他外部网络动态交换路由信息。 如果 BGP 是通过基于 ExpressRoute 专用对等互连的 ExpressRoute 虚拟网络网关配置的，或者是在 Azure VPN 网关上启用了 BGP，则 BGP 适用于虚拟网络。

在 Azure 中设计和实施虚拟网络和虚拟网络网关的相关人员应该花些时间来了解 Azure 中 BGP 的行为以及可用的配置选项。

|资源|链接|
|---|---|
|*路由概念：BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools)|
|*ExpressRoute 路由要求* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](../expressroute/expressroute-routing.md)|
|*关于 Azure VPN 网关的 BGP* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](../vpn-gateway/vpn-gateway-bgp-overview.md)|
|*教程：ExpressRoute Microsoft 对等互连配置站点到站点 VPN* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](../expressroute/site-to-site-vpn-over-microsoft-peering.md)|
|

## <a name="next-hop-types"></a>下一个跃点类型

### <a name="virtual-network"></a>虚拟网络

下一个跃点为虚拟网络的路由将自动被添加为系统路由，但也可以将其添加到用户定义的路由，以便在系统路由被重写的情况下将流量定向回虚拟网络。

### <a name="vnet-peering"></a>VNet 对等互连

VNet 对等互连允许两个不同的虚拟网络之间进行通信。 必须在每个虚拟网络上启用配置 VNet 对等互连，但虚拟网络不必位于同一区域、同一订阅，也不必关联到同一个 Azure Active Directory (Azure AD) 租户。

配置 VNet 对等互连时，设计和实施 VNet 对等互连的相关人员必须了解四个关联的配置参数以及这些参数如何应用到对等方的每一方：

1. 允许虚拟网络访问：选择“启用”（默认选项）可启用两个虚拟网络之间的通信。  启用虚拟网络之间的通信可允许资源连接到任意虚拟网络，并以相同的带宽和延迟互相之间进行通信，就如同它们是连接到同一个虚拟网络一样。
2. 允许转发的流量：选中此框将允许由网络转发的流量（并非是从该虚拟网络发起的）通过对等互连流动到此虚拟网络。 此设置是实施中心辐射型网络拓扑的基础。
3. 允许网关传输：选中此框可允许对等互连虚拟网络利用附加到此虚拟网络的虚拟网络网关。 在具有虚拟网络网关资源的虚拟网络上启用“允许网关传输”，但仅当在其他虚拟网络上启用了“使用远程网关”时才适用。 
4. 使用远程网关：选中此框可允许来自此虚拟网络的流量流经附加到正与之对等互连的虚拟网络的虚拟网络网关。 在没有虚拟网络网关的虚拟网络上启用“使用远程网关”，仅当在其他虚拟网络上启用了“允许网关传输”选项时才适用。 

|资源|链接|
|---|---|
| 概念：虚拟网络对等互连                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md) |
| 创建、更改或删除虚拟网络对等互连 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="virtual-network-gateway"></a>虚拟网络网关

虚拟网络网关提供一种机制，用于将虚拟网络与外部网络（如本地环境、合作伙伴环境和其他云部署）集成。 两种类型的虚拟网络网关是“ExpressRoute”和“VPN”。

#### <a name="expressroute-gateway"></a>ExpressRoute 网关

ExpressRoute 网关提供从虚拟网络到本地环境的出口点，应将其部署以满足安全性、可用性、财务和性能要求。 ExpressRoute 网关提供定义的网络带宽，并在部署后产生使用成本。 虚拟网络中只能部署一个 ExpressRoute 网关，但它可以连接到多个 ExpressRoute 线路，并可由多个虚拟网络通过 VNet 对等互连使用，从而支持共享带宽和连接。 请务必小心使用 ExpressRoute 网关配置本地环境和虚拟网络之间的路由，确保使用已知的受控网络入口点进行端到端连接。 使用基于 ExpressRoute 专用对等互连的 ExpressRoute 网关的澳大利亚联邦实体还必须部署网络虚拟设备（简称 NVA）来建立与本地环境的 VPN 连接，从而符合 ACSC“使用者指南”的要求。

必须注意的是，ExpressRoute 网关对地址范围、社区以及通过 BGP 交换的其他特定配置项目设定了某些限制。

| 资源  | 链接  |
|---|---|
| ExpressRoute 网关概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md) |
| 配置 ExpressRoute 的虚拟网络网关 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
|

#### <a name="vpn-gateway"></a>VPN 网关

Azure VPN 网关提供从虚拟网络到外部网络出口网络点，以确保站点到站点的安全连接。 VPN 网关提供定义的网络带宽，并在部署后产生使用成本。 使用 VPN 网关的澳大利亚联邦实体应确保根据 ACSC“使用者指南”的要求配置此网关。 虚拟网络中只能部署一个 VPN 网关，但它可以配置多个隧道，并可由多个虚拟网络通过 VNet 对等互连使用，从而支持多个虚拟网络共享带宽和连接。 可以通过 Internet 或 Microsoft 对等互连的 ExpressRoute 来建立 VPN 网关。

| 资源  | 链接 |
| --- | --- |
| VPN 网关概述| [https://docs.microsoft.com/azure/vpn-gateway](../vpn-gateway/index.yml)|
| 规划和设计 VPN 网关 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
| Azure 澳大利亚中的 Azure VPN 网关 | [Azure 澳大利亚中的 Azure VPN 网关](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>虚拟设备的下一跃点

通过虚拟设备的下一跃点，可以在应用了虚拟网络的 Azure 网络和路由拓扑外部处理网络流量。 虚拟设备可以应用安全规则、转换地址、建立 VPN、代理流量或其他一系列功能。 将通过路由表中的 UDR 应用虚拟设备的下一跃点，并且虚拟设备的下一跃点可用于将流量定向到 Azure 防火墙、单个 NVA 或 Azure 负载均衡器，从而提供跨多个 NVA 的可用性。 要使用虚拟设备进行路由，必须启用关联的网络接口进行 IP 转发。

| 资源  | 链接 |
| --- | ---|
| 路由概念：自定义路由 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes) |
| 启用或禁用 IP 转发 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](../virtual-network/virtual-network-network-interface.md#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>VirtualNetworkServiceEndpoint 的下一个跃点

只有在子网中配置服务终结点并且不能通过路由表手动配置时，才会添加下一跃点类型为 VirtualNetworkServiceEndpoint 的路由。

### <a name="next-hop-of-internet"></a>下一个跃点为 Internet

下一个跃点 Internet 用于访问使用公共 IP 地址的任何资源（包括 Internet 以及 Azure 区域的 PaaS 和 Azure 服务）。 下一个跃点 Internet 不需要默认路由 (0.0.0.0/0) 涵盖所有网络，但可用于启用到特定公共服务的路由路径。 下一个跃点 Internet 应该用于将路由添加到系统功能所需要的授权服务功能（如 Microsoft 管理地址）。

可以使用下一个跃点 Internet 添加的服务示例包括：

1. 用于 Windows 激活的密钥管理服务
2. 应用服务环境管理

|资源|链接|
|---|---|
| Azure 中的出站连接 | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](../load-balancer/load-balancer-outbound-connections.md) |
| 使用 Azure 自定义路由启用 KMS 激活 | [https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation) |
| 锁定应用服务环境  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](../app-service/environment/firewall-integration.md) |
|

### <a name="next-hop-of-none"></a>下一个跃点“无”

下一跃点“无”可用于阻止与特定网络的通信。 与控制是允许还是拒绝流量遍历可用网络路径的 NSG 不同，使用下一个跃点“无”会完全删除网络路径。 创建下一个跃点为“无”的路由时，应小心，尤其是在将此路由应用于默认路由 0.0.0.0/0 时，因为这样做可能会产生意外后果，并且可能会导致系统问题的排除变得复杂且耗时。

## <a name="security"></a>安全

通过保护 IaaS 和 PaaS 功能本身的安全以及在将与这些功能进行通信的系统上实现受控的通信路径，可以实现对这些功能的网络分段和隔离控制。

在 Azure 中设计和构建解决方案是一个创建逻辑体系结构的过程，便于了解、控制和监视整个 Azure 中的网络资源。 此逻辑体系结构是在 Azure 平台中由软件定义的，它取代了传统网络环境中实现的物理网络拓扑。

创建的逻辑体系结构必须提供可用性所需的功能，但还必须提供安全性和完整性所需的可见性和控制。

实现必要的网络分段和隔离工具，并保护和强制实施网络拓扑和这些工具的实现，才能实现此目的。

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

NSG 用于指定子网或特定网络接口允许的入站和出站流量。 配置 NSG 时，澳大利亚联邦实体应采用审批列表方法，该方法将规则配置为允许必要的流量，将默认规则配置为拒绝与特定允许语句不匹配的所有流量。 务必小心规划和配置 NSG，确保正确捕获所有必要的入站和出站流量。 这包括识别和了解虚拟网络和本地环境中使用的所有专用 IP 地址范围，以及特定 Microsoft 服务，例如 Azure 负载均衡器 和 PaaS 管理要求。 参与设计和实现 NSG 的人员还应了解如何使用服务标记和应用程序安全组，从而创建精细的、特定于服务和应用程序的安全规则。

请务必注意，NSG 的默认配置允许流向虚拟网络中的所有地址和所有公共 IP 地址的出站流量。

|资源|链接|
|---|---|
|网络安全概述 | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|创建、更改或删除网络安全组 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

### <a name="azure-firewall"></a>Azure 防火墙

Azure 防火墙可用于构建中心辐射型网络拓扑并实施集中式网络安全控制。 使用 Azure 防火墙，通过实施允许列表方法来满足 ISM 对出口流量的必要要求，该方法仅对系统功能所需的 IP 地址、协议、端口和 FQDN 进行授权。 澳大利亚联邦实体应采取基于风险的方法来确定 Azure 防火墙提供的安全功能是否足以满足其要求。 对于需要更多的安全功能（超出了 Azure 防火墙提供的功能），澳大利亚联邦实体应考虑实施 NVA。

|资源|链接|
|---|---|
|*Azure 防火墙文档* | [https://docs.microsoft.com/azure/firewall](../firewall/index.yml)|
|*教程：使用 Azure PowerShell 在混合网络中部署和配置 Azure 防火墙* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](../firewall/tutorial-hybrid-ps.md)|
|

### <a name="network-virtual-appliances-nvas"></a>网络虚拟设备 (NVA)

NVA 可用于构建中心分支网络拓扑，提供增强或补充的网络功能，也可以用作 Azure 网络机制的替代项，以供用户熟悉支持和管理并与本地网络服务保持一致。 可以部署 NVA 以满足特定的安全要求，例如；在某些情况下，针对与网络流量、HTTPS 解密、内容检查、筛选和其他安全功能相关的身份感知有一定要求。 应以高可用性配置部署 NVA，参与 NVA 设计或实施的人员应查阅相应的供应商文档，了解有关在 Azure 中部署的指南。

|资源|链接|
|---|---|
|*部署具有高可用性的网络虚拟设备* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>服务终结点策略（预览版）

根据服务的可用性以及数据外泄的可能性和影响的安全风险评估来配置服务终结点策略。 对应 Azure 存储，应考虑服务终结点策略，并且根据关联的风险概况，酌情进行管理。

| 资源 | 链接  |
| --- | --- |
| *服务终结点策略概述* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](../virtual-network/virtual-network-service-endpoint-policies-overview.md) |
| *使用 Azure 门户创建、更改或删除服务终结点策略* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](../virtual-network/virtual-network-service-endpoint-policies-portal.md)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy 是强制实施和维护 Azure 环境逻辑体系结构完整性的关键组件。 可以通过 Azure 服务使用各种服务和出口网络流量路径。 澳大利亚联邦实体必须了解其环境中存在的资源和可用的网络出口点。 为了确保没有在 Azure 环境中创建未经授权的网络出口点，英联邦实体应使用 Azure Policy 来控制可部署的资源类型以及这些资源的配置。 实用示例包括：将资源限制为仅使用已授权和已审批的资源，以及要求将 NSG 添加到子网。

|资源 | 链接|
|---|---|
|*Azure Policy 概述* | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|*允许的资源类型示例策略* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)|
|*在子网上强制 NSG 示例策略*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="paas-egress-capabilities"></a>PaaS 出口功能

PaaS 功能可以增强功能和简化管理，但在解决网络分段和隔离需求方面增加了复杂性。 PaaS 功能通常配置有公共 IP 地址，可以通过 Internet 进行访问。  如果你使用的是系统和解决方案中的 PaaS 功能，应小心识别组件之间的通信流，并创建网络安全规则以仅允许该通信。 作为安全深层防御方法的一部分，应为 PaaS 功能配置加密、身份验证以及适当的访问控制和权限。  

### <a name="public-ip-for-paas"></a>PaaS 公共 IP

PaaS 功能的公共 IP 地址根据托管或部署服务的区域进行分配。 若要构建适当的网络安全规则和路由拓扑，以实现涵盖 Azure 虚拟网络、PaaS 以及 ExpressRoute 和 Internet 连接的网络分段和隔离，你需要了解公共 IP 地址分配和区域。 Azure 会从分配到每个 Azure 区域的池中分配 IP 地址。 Microsoft 使每个区域中使用的地址可供下载，且这些地址以定期、受控的方式进行更新。 每个区域中的可用服务也经常随着新服务的发布或服务的更广泛部署而发生变化。 澳大利亚联邦实体应定期查看这些资料，并可以根据需要使用自动化来维护系统。 可以通过联系 Microsoft 支持人员获取每个区域中托管的某些服务的特定 IP 地址。

| 资源 | 链接 |
| --- | --- |
| *Microsoft Azure 数据中心 IP 范围*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *每个区域的 Azure 服务*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Azure 应用服务中的入站和出站 IP 地址* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](../app-service/overview-inbound-outbound-ips.md)
|

## <a name="next-steps"></a>后续步骤

将你的整体架构和设计与已发布的 [IaaS 和 PaaS Web 应用程序的受保护蓝图](https://aka.ms/au-protected)进行比较。