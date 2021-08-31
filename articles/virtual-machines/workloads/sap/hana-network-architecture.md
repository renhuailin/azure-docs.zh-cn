---
title: Azure 上的 SAP HANA（大型实例）的网络体系结构 | Microsoft Docs
description: 了解用于部署 Azure SAP HANA（大型实例）的网络体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5add931f71dfdb5034e614b3d6c3ddc8703293a2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461565"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

本文将介绍用于部署 Azure SAP HANA 大型实例的网络体系结构（也称为 BareMetal 基础结构）。 

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure SAP HANA（大型实例）部署具有更大的 SAP 布局。 它们可能包含多个 SAP 解决方案，其中具有不同大小的数据库、CPU 资源消耗和内存使用情况。 

很可能不是所有的 IT 系统都已经在 Azure 中。 你的 SAP 布局也可能是混合的。 数据库管理系统 (DBMS) 和 SAP 应用程序可以混合使用 NetWeaver、S/4HANA 和 SAP HANA。 SAP 应用程序甚至可能使用另一个 DBMS。

Azure 提供不同的服务，利用这些服务，可以在 Azure 中运行 DBMS、NetWeaver 和 S/4HANA 系统。 Azure 提供网络技术，可让 Azure 充当本地软件部署的虚拟数据中心。 Azure 网络功能包括： 

- Azure 虚拟网络，它连接到与本地网络资产连接的 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路。
- ExpressRoute 线路，它使用 [1 Gbps 或更高](https://azure.microsoft.com/pricing/details/expressroute/)的最小带宽将本地环境连接到 Azure。 利用这条线路，在本地系统与虚拟机 (VM) 上运行的系统之间传输数据时就可以有充足的带宽。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- 在虚拟网络中设置了可相互通信的 Azure 中的所有 SAP 系统。
- 本地承载的 Active Directory 和 DNS 通过 ExpressRoute 从本地扩展到 Azure 中。 它们还可以完全在 Azure 中运行。

在将 HANA 大型实例集成到 Azure 数据中心网络结构时，也使用了 Azure ExpressRoute 技术。


> [!NOTE] 
> 一个 Azure 订阅只能链接到特定 Azure 区域内 HANA 大型实例缩放单元中的一个租户。 反之，一个 HANA 大型实例缩放单元租户只能链接到一个 Azure 订阅。 此要求与 Azure 中的任何其他计费对象一致。

如果 Azure SAP HANA（大型实例）部署在多个 Azure 区域中，则会在 HANA 大型实例缩放单元中部署单独的租户。 可以在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure SAP HANA（大型实例）只支持 Azure 资源管理器部署方法。

## <a name="extra-virtual-network-information"></a>额外的虚拟网络信息

若要将虚拟网络连接到 ExpressRoute，则必须创建 Azure ExpressRoute 网关。 有关详细信息，请参阅[关于 ExpressRoute 的 ExpressRoute 网关](../../../expressroute/expressroute-about-virtual-network-gateways.md)。 

Azure ExpressRoute 网关用于与 Azure 外部基础结构或 Azure 大型实例标记相连的 ExpressRoute。 可以将 Azure ExpressRoute 网关连接到最多四条 ExpressRoute 线路，但这些连接只能是来自不同的 Microsoft 企业边缘路由器 (MSEE)。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)。 

> [!NOTE] 
> 使用 ExpressRoute 连接时，可以通过 ExpressRoute 网关实现的最大吞吐量为 10 Gbps。 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 若要利用 ExpressRoute 网关的完整带宽，请使用多个流，或者在某一个文件的并行流中复制不同的文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 这部分是你的（客户的）域，并且通过 ExpressRoute 连接到 Azure。 这条 ExpressRoute 线路的全部费用由你支付。 带宽应足够大，要能够处理本地资产与所连接的 Azure 区域之间的网络流量。 请参阅下图右下方。
- Azure 网络服务（如前所述）和虚拟网络都需要添加 ExpressRoute 网关。 对于这部分，需要创建相应的设计，以满足应用程序、安全性和符合性要求。 假如有要从中选择的虚拟网络和 Azure 网关 SKU 的数量，考虑是否要使用 HANA 大型实例。 请参阅下图右上方。
- HANA 大型实例通过 ExpressRoute 连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 在 HANA 大型实例中部署了资产并将 ExpressRoute 线路连接到了虚拟网络后，你只需提供一些 IP 地址范围。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)。 对于 Azure 数据中心网络结构与 HANA 大型实例单元之间的连接，费用不会增加。
- HANA 大型实例标记内的网络，且对你来说通常是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image1-architecture.png)

即使使用 Hana 大型实例，以下两项要求仍然有效：
- 本地资产必须通过 ExpressRoute 连接到 Azure。
- 需要一个或多个运行 VM 的虚拟网络。 这些 VM 承载应用程序层，该层连接到 HANA 大型实例中承载的 HANA 实例。

Azure 中 SAP 部署的差别如下：

- 你的租户的 HANA 大型实例通过另一条 ExpressRoute 线路连接到你的虚拟网络中。 本地到 Azure 虚拟网络的 ExpressRoute 线路不与 Azure 虚拟网络和 HANA 大型实例之间的线路共享相同的路由器。 它们的负载条件保持分开。
- SAP 应用程序层和 HANA 大型实例之间的工作负载配置文件具有不同的性质。 SAP HANA 会生成许多小请求和突发，例如，数据传输（结果集）到应用程序层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- Azure ExpressRoute 网关至少有两个 ExpressRoute 连接。 一条线路从本地连接，另一条从 HANA 大型实例连接。 此配置只为另外两条来自不同 MSEE 的线路留出空间，以便连接到 ExpressRoute 网关。 此限制与 ExpressRoute FastPath 的使用无关。 所有连接的线路会共享用于 ExpressRoute 网关传入数据的最大带宽。

使用 HANA 大型实例缩放单元的修订版 3，VM 与 HANA 大型实例单元之间的网络延迟可能会比典型的 VM 到 VM 网络往返延迟要高。 根据 Azure 区域不同，值有可能会超过 0.7 毫秒的往返延迟时间，这个时间在 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中被归类为低于平均值。 若要测量 Azure VM 和 HANA 大型实例之间的网络往返延迟时间，根据 Azure 区域和工具不同，该延迟时间可能最高会达到 2 毫秒。 不过，客户还是会成功地在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。 名为“ExpressRoute FastPath”的新功能可以显著减少 HANA 大型实例与 Azure 中应用程序层 VM 之间的网络延迟（见下文）。 

HANA 大型实例缩放单元的修订版 4 改善了在 HANA 大型实例缩放单元附近部署的 Azure VM 之间的网络延迟。 如果配置了 Azure ExpressRoute FastPath，延迟会符合或好于 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中记录的平均值分类（见下文）。 

若要在修订版 4 的 HANA 大型实例单元附近部署 Azure VM，则需要应用 [Azure 邻近放置组](../../co-location.md)。 邻近放置组可用于在与修订版 4 托管的 HANA 大型实例相同的 Azure 数据中心中查找 SAP 应用程序层。 有关详细信息，请参阅[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)。

若要在 VM 与 HANA 大型实例之间提供确定性的网络延迟，使用 ExpressRoute 网关 SKU 是必不可少的。 与本地和 VM 之间的流量模式不同，VM 和 HANA 大型实例之间的流量模式可能会出现请求和数据量较小但高突发的情况。 为了应对这种突发，我们强烈建议使用 UltraPerformance 网关 SKU。 对于 HANA 大型实例类型 II SKU，必须使用 UltraPerformance 网关 SKU 作为 ExpressRoute 网关。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例类型 II SKU，只支持使用 UltraPerformance 网关 SKU 作为 ExpressRoute 网关。 在使用 ExpressRoute FastPath 时会有例外（见下文）。

### <a name="expressroute-fastpath"></a>ExpressRoute FastPath
在 2019 年 5 月，我们发布了 ExpressRoute FastPath。 FastPath 降低了 HANA 大型实例和承载 SAP 应用程序 VM 的 Azure 虚拟网络之间的延迟。 使用 FastPath，VM 与 HANA 大型实例之间的数据流不会通过 ExpressRoute 网关进行路由。 Azure 虚拟网络的子网中分配的 VM 会直接与专用企业边缘路由器进行通信。 

> [!IMPORTANT] 
> ExpressRoute FastPath 要求运行 SAP 应用程序 VM 的子网位于连接到 HANA 大型实例的同一个 Azure 虚拟网络中。 VM 如果位于与连接到 HANA 大型实例单元的 Azure 虚拟网络对等互连的 Azure 虚拟网络中，则不会受益于 ExpressRoute FastPath。 因此，在典型的中心和分支虚拟网络设计中，ExpressRoute 线路连接到某个中心虚拟网络，而包含 SAP 应用程序层（分支）的虚拟网络之间对等互连，这样，ExpressRoute FastPath 提供的优化将不起作用。 ExpressRoute FastPath 目前也不支持用户定义的路由规则 (UDR)。 有关详细信息，请参阅 [ExpressRoute 虚拟网络网关和 FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md)。 


若要详细了解如何配置 ExpressRoute FastPath，请参阅[将虚拟网络连接到 HANA 大型实例](./hana-connect-vnet-express-route.md)。    

> [!NOTE]
> 若要使用 ExpressRoute FastPath，则需要 UltraPerformance ExpressRoute 网关。


## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 ExpressRoute 线路连接到 MSEE 中。 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md)。 在路由建立后，它连接到 Azure 主干网。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 HANA 大型实例标记通过专用企业边缘路由器进行连接，以最大程度降低 Azure IaaS 中的 VM 与 HANA 大型实例缩放单元之间的网络延迟。

托管 SAP 应用程序实例的 VM 的 ExpressRoute 网关连接到一个与本地连接的 ExpressRoute 线路。 同一虚拟网络连接到一个单独的企业边缘路由器。 该边缘路由器专用于连接到大型实例缩放单元。 同样，利用 FastPath，从 HANA 大型实例到 SAP 应用程序层 VM 的数据流不会通过 ExpressRoute 网关进行路由。 此配置减少了网络往返延迟。

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设吞吐量为 2 Gbps 或 10 Gbps 的 ExpressRoute 网关带宽不产生瓶颈。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署多个连接到 SAP HANA（大型实例）的 SAP 系统或大型 SAP 系统，ExpressRoute 网关的吞吐量可能会成为瓶颈。 在这种情况下，请将应用层拆分为多个虚拟网络。 如果要在不同的 Azure 虚拟网络中隔离生产系统和非生产系统，也可以拆分应用程序层。 

在以下情形时，可以创建连接到 HANA 大型实例的特殊虚拟网络：

- 直接从 HANA 大型实例中的 HANA 实例备份到承载 NFS 共享的 Azure 中的 VM。
- 将大型备份或其他文件从 HANA 大型实例复制到 Azure 中托管的磁盘空间。

使用单独的虚拟网络来托管 VM，这些 VM 为在 HANA 大型实例与 Azure 之间大量传输的数据管理存储。 这种安排避免了在为运行 SAP 应用程序层的 VM 提供服务的 ExpressRoute 网关上将大型文件或数据从 HANA 大型实例传输到 Azure。 

如需可展开的网络体系结构，请采取以下措施：

- 为一个更大的 SAP 应用程序层使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

    下图显示了针对 Azure SAP HANA（大型实例）的更加可展开的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

根据要在不同虚拟网络（承载不同 SAP 系统的 VM）之间应用的规则和限制，应该对等互连这些虚拟网络。 有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](../../../virtual-network/virtual-network-peering-overview.md)。


## <a name="routing-in-azure"></a>Azure 中的路由

在默认部署下，对于 Azure SAP HANA（大型实例），有三个重要的网络路由注意事项：

- Azure SAP HANA（大型实例）只能由 Azure VM 通过专用 ExpressRoute 连接进行访问，而不能直接从本地访问。 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 可传递的路由限制是由于 SAP HANA 大型实例当前使用的 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。 有关例外情况，请参阅下一部分：[直接路由到 HANA 大型实例](#direct-routing-to-hana-large-instances)。

- 如果已在两个不同的 Azure 区域部署了 HANA 大型实例单位以进行灾难恢复，则会像过去一样应用相同的临时性路由限制。 换言之，一个区域（如美国西部）中的 HANA 大型实例的 IP 地址没有路由到另一个区域（如美国东部）部署的 HANA 大型实例。 此限制与跨区域使用 Azure 网络对等互连或者交叉连接那些将 HANA 大型实例连接到虚拟网络的 ExpressRoute 线路无关。 如需图形呈现形式，请参阅[在多个区域使用 HANA 大型实例单元](#use-hana-large-instance-units-in-multiple-regions)部分中的插图。 此限制源于已部署的体系结构，禁止直接使用 HANA 系统复制进行灾难恢复。 如需了解最近的更改，同样，请参阅[在多个区域中使用 HANA 大型实例单元](#use-hana-large-instance-units-in-multiple-regions)。 

- Azure SAP HANA 大型实例具有从服务器 IP 池地址范围中分配的 IP 地址，该地址范围是在请求 HANA 大型实例部署时提交的。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)。 此 IP 地址可以通过 Azure 订阅以及将 Azure 虚拟网络连接到 HANA 大型实例的线路进行访问。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 它不再像此解决方案的前面部署中的情况那样通过网络地址转换 (NAT) 来分配。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由到 HANA 大型实例

在默认情况下，可传递的路由不适用于以下情况：

- 在 HANA 大型实例单元与本地部署之间路由。

- 在不同区域中部署的 HANA 大型实例单元之间。

有三种方法可以在这些情况下启用可传递路由：

- 来回路由数据的反向代理。 例如，部署在连接到 HANA 大型实例和本地的 Azure 虚拟网络中的 F5 BIG-IP、NGINX（带流量管理器）作为虚拟防火墙/流量路由解决方案。
- 在 Linux VM 中使用 IPTables 规则在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。 运行 IPTables 的 VM 必须部署在连接到 HANA 大型实例和本地的 Azure 虚拟网络中。 VM 的大小必须设置为让 VM 的网络吞吐量满足预期的网络流量。 有关 VM 网络带宽的详细信息，请参阅 [Azure 中 Linux 虚拟机的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
- [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)是另一种实现本地与 HANA 大型实例单元之间直接流量的解决方案。 

这些解决方案的所有流量都将通过 Azure 虚拟网络进行路由。 因此，这些流量也可能会受到所使用的软设备或受到 Azure 网络安全组的限制。 这样，本地的特定 IP 地址或 IP 地址范围可能会被阻止或被显式允许访问 HANA 大型实例。 

> [!NOTE]  
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 所用组件的供应商或集成商必须提供支持。 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft 引入了名为 [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) 的新功能。 在以下两种情况下，Global Reach 可用于 HANA 大型实例：

- 实现从本地到部署在不同区域中的 HANA 大型实例单元的直接访问。
- 实现部署在不同区域中的 HANA 大型实例单元之间的直接通信。


##### <a name="direct-access-from-on-premises"></a>从本地直接访问
在提供 Global Reach 的 Azure 区域中，可以请求为 ExpressRoute 线路启用 Global Reach。 此线路会将本地网络连接到与 HANA 大型实例连接的 Azure 虚拟网络。 ExpressRoute 线路的本地端会有成本。 有关详细信息，请参阅 [Global Reach 附加产品](https://azure.microsoft.com/pricing/details/expressroute/)的定价。 无需为将 HANA 大型实例连接到 Azure 的线路支付额外费用。 

> [!IMPORTANT]  
> 在使用 Global Reach 启用 HANA 大型实例单元和本地资产之间的直接访问时，不会通过 Azure 虚拟网络来路由网络数据和控制流。 而是会直接在 Microsoft 企业交换路由器之间路由网络数据和控制流。 所以，部署在 Azure 虚拟网络中的任何 NSG 或 ASG 规则，或者任何类型的防火墙、NVA 或代理，都不会受到影响。 如果使用 ExpressRoute Global Reach 实现从本地到 HANA 大型实例单元的直接访问，则需要在本地端的防火墙中定义访问 HANA 大型实例单元的限制和权限。 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>连接不同 Azure 区域中的 HANA 大型实例
同样，ExpressRoute Global Reach 可用于连接两个在不同区域中部署的 HANA 大型实例租户。 这种隔离是 HANA 大型实例租户用于在这两个区域中连接到 Azure 的 ExpressRoute 线路。 连接在不同区域中部署的两个 HANA 大型实例租户时，不会产生额外费用。 

> [!IMPORTANT]  
> HANA 大型实例租户之间的网络流量的数据流和控制流不会通过 Azure 网络进行路由。 所以，无法使用 Azure 功能或网络虚拟设备 (NVA) 来强制实施 HANA 大型实例租户之间的通信限制。 

若要详细了解如何启用 ExpressRoute Global Reach，请参阅[将虚拟网络连接到 HANA 大型实例](./hana-connect-vnet-express-route.md)。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例和 VM 之间传输的数据没有加密。 但是，可以单纯是为了在 HANA DBMS 端和基于 JDBC/ODBC 的应用程序之间进行交换而启用流量加密。 有关详细信息，请参阅 [SAP HANA 和 JDBC/ODBC 客户端之间的安全通信](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

为了进行灾难恢复，需要在多个 Azure 区域中有 HANA 大型实例单元。 如果只使用 Azure [全局 VNet 对等互连](../../../virtual-network/virtual-network-peering-overview.md)，在默认情况下，可传递的路由在不同区域中的 HANA 大型实例租户之间不会起作用。 但是，Global Reach 实现了不同区域中的 HANA 大型实例单元之间的通信。 这种使用 ExpressRoute Global Reach 的方案可以实现：

 - 在不增加代理或防火墙的情况下进行 HANA 系统复制。
 - 在不同区域中的 HANA 大型实例单元之间复制备份，以执行系统复制或系统刷新。


![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

前图显示了两个区域中的虚拟网络如何连接到两条 ExpressRoute 线路。 这些线路用于连接到两个 Azure 区域（灰色线条）中的 Azure SAP HANA（大型实例）。 使用两个交叉连接的原因是为了防止任何一侧出现 MSEE 的故障。 两个 Azure 区域中的两个虚拟网络之间的通信流应通过两个不同区域中的两个虚拟网络（蓝色虚线）的[全球对等互连](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure)进行处理。 粗红线描述的是 ExpressRoute Global Reach 连接。 利用此连接，不同区域中租户的 HANA 大型实例单元相互之间可以进行通信。 

> [!IMPORTANT] 
> 如果使用了多条 ExpressRoute 线路，请使用 AS Path 追加和本地首选 BGP 设置，以确保正确路由流量。

## <a name="next-steps"></a>后续步骤

了解 SAP HANA（大型实例）的存储体系结构。

> [!div class="nextstepaction"]
> [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)
