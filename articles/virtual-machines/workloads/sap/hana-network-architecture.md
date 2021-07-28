---
title: Azure 上的 SAP HANA（大型实例）的网络体系结构 | Microsoft Docs
description: 有关如何部署 Azure 上的 SAP HANA（大型实例）的网络体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b9baf6dc372b9ce5d85a935502cdaf710e8d0b43
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108749134"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA（大型实例）网络体系结构

Azure 网络服务的体系结构是在 HANA 大型实例上成功部署 SAP 应用程序的关键组件。 通常，Azure 上的 SAP HANA（大型实例）部署具有较大的 SAP 布局和多种不同的 SAP 解决方案，其中具有可变的数据库大小、CPU 资源消耗和内存利用。 很可能不是所有的 IT 系统都已经在 Azure 中。 从 DBMS 和 SAP 应用程序的角度来看，你的 SAP 布局通常也是混合的，即混合使用 NetWeaver、S/4HANA、SAP HANA 和另一个 DBMS。 Azure 提供不同的服务，可让你在 Azure 中运行不同的 DBMS、NetWeaver 和 S/4HANA 系统。 Azure 还提供网络技术，可让 Azure 充当本地软件部署的虚拟数据中心

除非完整的 IT 系统托管在 Azure 中， 否则 Azure 网络功能用于将本地环境与 Azure 资产连接，让 Azure 看起来就像是你的虚拟数据中心。 使用的 Azure 网络功能如下： 

- Azure 虚拟网络连接到与本地网络资产连接的 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路。
- 将本地环境连接到 Azure 的 ExpressRoute 线路的最小带宽应为 [1 Gbps 或更高](https://azure.microsoft.com/pricing/details/expressroute/)。 规定这个最小带宽是为了确保在本地系统与在 VM 上运行的系统之间传输数据。 此外，还能提供足够的带宽从本地最终用户到 Azure 系统的连接。
- Azure 中的所有 SAP 系统都创建在虚拟网络中，以便彼此通信。
- 本地托管的 Active Directory 和 DNS 通过 ExpressRoute 从本地扩展到 Azure 中，或在 Azure 中完全运行。

对于将 HANA 大型实例集成到 Azure 数据中心网络结构的具体案例，也使用了 Azure ExpressRoute 技术


> [!NOTE] 
> 一个 Azure 订阅只能链接到特定 Azure 区域内 HANA 大型实例缩放单元中的一个租户。 反之，一个 HANA 大型实例缩放单元租户只能链接到一个 Azure 订阅。 此要求与 Azure 中的任何其他计费对象一致。

将 Azure 上的 SAP HANA（大型实例）部署在多个不同的 Azure 区域中会导致在大型实例缩放单元中部署单独的租户。 可在同一 Azure 订阅下运行这两个实例，只要这些实例属于同一 SAP 布局即可。 

> [!IMPORTANT] 
> Azure SAP HANA（大型实例）只支持 Azure 资源管理器部署方法。

 

## <a name="additional-virtual-network-information"></a>其他虚拟网络信息

若要将虚拟网络连接到 ExpressRoute，则必须创建 Azure ExpressRoute 网关。 有关详细信息，请参阅[关于 ExpressRoute 的 ExpressRoute 网关](../../../expressroute/expressroute-about-virtual-network-gateways.md)。 

Azure ExpressRoute 网关用于与 Azure 外部基础结构或 Azure 大型实例标记相连的 ExpressRoute。 最多可以将 Azure ExpressRoute 网关连接到四个不同的 ExpressRoute 线路，只要这些连接来自不同的 Microsoft 企业边缘路由器。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)。 

> [!NOTE] 
> 使用 ExpressRoute 连接时，可以通过 ExpressRoute 网关实现的最大吞吐量为 10 Gbps。 在位于虚拟网络中的 VM 与本地系统之间复制文件（使用单一复制流）不会获得各种网关 SKU 的完整吞吐量。 若要利用 ExpressRoute 网关的完整带宽，应使用多个流。 或者，必须采用单一文件的并行流复制各个文件。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 大型实例的网络体系结构
HANA 大型实例的网络体系结构可以分为四个不同的部分：

- 本地网络和到 Azure 的 ExpressRoute 连接。 此部分是客户域，通过 ExpressRoute 连接到 Azure。 此 ExpressRoute 线路的全部费用由你作为客户支付。 带宽应足够大，以处理本地资产与所连接的 Azure 区域之间的网络流量。 请参阅下图右下方。
- Azure 网络服务（如前所述）和虚拟网络都需要添加 ExpressRoute 网关。 此部件是需要根据应用程序要求以及安全性和符合性要求找到合适设计的区域。 使用 HANA 大型实例是另一个考虑点，需要考虑到虚拟网络数以及可供选择的 Azure 网关 SKU。 请参阅下图右上方。
- 通过 ExpressRoute 技术将 HANA 大型实例连接到 Azure 中。 此部件已部署，由 Microsoft 处理。 你只需提供一些 IP 地址范围，在将资产部署到 HANA 大型实例中后，再将 ExpressRoute 线路连接到虚拟网络。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)。 对于 Azure 数据中心网络结构与 HANA 大型实例单位之间的连接，你作为客户无需支付额外费用。
- HANA 大型实例标记内的网络，且对你来说通常是透明的。

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的虚拟网络](./media/hana-overview-architecture/image1-architecture.png)

使用 HANA 大型实例这一事实并不会改变将本地资产通过 ExpressRoute 连接到 Azure 这一要求， 也不会改变使用一个或多个虚拟网络这一要求。虚拟网络运行的 Azure VM 托管应用层，该层连接到托管在 HANA 大型实例单位中的 HANA 实例。 

Azure 中 SAP 部署的差别如下：

- 客户租户的 HANA 大型实例单位通过另一 ExpressRoute 线路连接到虚拟网络中。 为了隔离负载条件，本地与 Azure 虚拟网络之间的 ExpressRoute 线路不会和 Azure 虚拟网络与 HANA 大型实例之间的线路共享相同的路由器。
- 在 SAP 应用层和 HANA 大型实例之间的工作负荷配置文件在本质上不同于包含多个小型请求的迸发型数据传输（结果集），后者是从 SAP HANA 传输到应用层中。
- 与数据在本地和 Azure 之间交换的典型方案相比，SAP 应用程序体系结构对网络延迟更为敏感。
- Azure ExpressRoute 网关至少有两个 ExpressRoute 连接。 一个线路从本地连接，另一个从 HANA 大型实例连接。 这样剩下的空间只够另外两条线路从不同的 MSE 连接到 ExpressRoute 网关上。 此限制与 ExpressRoute FastPath 的使用无关。 所有连接的线路会共享用于 ExpressRoute 网关传入数据的最大带宽。

使用 HANA 大型实例戳修订版 3，VM 与 HANA 大型实例单元之间的网络延迟可能比典型的 VM 到 VM 网络往返延迟要高。 测量到的值可能超过 0.7 毫秒的往返延迟，具体取决于 Azure 区域。而在 [SAP 说明 #1100926 - 常见问题解答：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中，0.7 毫秒被归类为低于平均值。 依赖于 Azure 区域和工具来测量 Azure VM 和 HANA 大型实例单元之间的网络往返延迟，所测量的延迟可以达到或大约 2 毫秒。 尽管如此，客户在 SAP HANA 大型实例上部署基于 SAP HANA 的生产型 SAP 应用程序很成功。 请确保在 Azure HANA 大型实例中对自己的业务流程进行彻底的测试。 名为 ExpressRoute FastPath 的新功能可以大大减少 HANA 大型实例与 Azure 中应用程序层 VM 之间的网络延迟（见下文）。 

使用 HANA 大型实例戳修订版 4，如果已配置 Azure ExpressRoute FastPath，则邻近 HANA 大型实例戳部署的 Azure VM 之间的网络延迟满足或优于平均分类，如 [SAP 说明 #1100926 - FAQ：网络性能](https://launchpad.support.sap.com/#/notes/1100926/E)中所述（见下文）。 若要邻近修订版 4 的 HANA 大型实例单元部署 Azure VM，则需要利用 [Azure 邻近放置组](../../co-location.md)。 [为 SAP 应用程序提供最佳网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)中介绍了如何使用邻近放置组在与修订版 4 托管 HANA 大型实例单元相同的 Azure 数据中心内定位 SAP 应用程序层。

若要在 VM 与 HANA 大型实例之间实现确定性的网络延迟，必须选择 ExpressRoute 网关 SKU。 不同于本地与 VM 之间的流量模式，VM 与 HANA 大型实例之间的流量模式可能是这样的：一开始流量很小，但随着要传输的请求和数据量的增多，可能会出现流量突然增高的迸发现象。 为了应对这种迸发现象，我们强烈建议使用 UltraPerformance 网关 SKU。 对于 HANA 大型实例类型 II SKU，必须使用 UltraPerformance 网关 SKU 作为 ExpressRoute 网关。

> [!IMPORTANT] 
> 假定所有的网络流量都位于 SAP 应用层与数据库层之间，则仅支持使用虚拟网络的 HighPerformance 或 UltraPerformance 网关 SKU 来连接到 Azure 上的 SAP HANA（大型实例）。 对于 HANA 大型实例类型 II SKU，只支持使用 UltraPerformance 网关 SKU 作为 ExpressRoute 网关。 使用 ExpressRoute FastPath 时会有例外（见下文）

### <a name="expressroute-fastpath"></a>ExpressRoute FastPath
为了降低延迟，ExpressRoute FastPath 于 2019 年 5 月引入并发布，用于实现 HANA 大型实例与托管 SAP 应用程序 VM 的 Azure 虚拟网络之间的特定连接。 与到目前为止推出的解决方案的主要区别是，VM 与 HANA 大型实例之间的数据流不再通过 ExpressRoute 网关进行路由。 Azure 虚拟网络子网中分配的 VM 会直接与专用企业边缘路由器进行通信。 

> [!IMPORTANT] 
> ExpressRoute FastPath 功能要求运行 SAP 应用程序 VM 的子网位于与 HANA 大型实例连接的同一 Azure 虚拟网络中。 位于与直接连接 HANA 大型实例单位的 Azure 虚拟网络进行了对等互连的 Azure 虚拟网络中的 VM 无法从 ExpressRoute FastPath 受益。 因此，典型中心辐射型虚拟网络设计（其中 ExpressRoute 线路连接到中心虚拟网络，包含 SAP 应用程序层的虚拟网络（辐射型）被对等互连）无法利用 ExpressRoute FastPath 的优化。 另外，ExpressRoute FastPath 暂不支持用户定义的路由规则 (UDR)。 有关详细信息，请参阅 [ExpressRoute 虚拟网络网关和 FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md)。 


如需更详细地了解如何配置 ExpressRoute FastPath，请参阅[将虚拟网络连接到 HANA 大型实例](./hana-connect-vnet-express-route.md)文档。    

> [!NOTE]
> 必须有 UltraPerformance ExpressRoute 网关，ExpressRoute FastPath 才能正常运行


## <a name="single-sap-system"></a>单个 SAP 系统

上面所示的本地基础结构通过 ExpressRoute 连接到 Azure。 ExpressRoute 线路连接到 Microsoft 企业边缘路由器 (MSEE)。 有关详细信息，请参阅 [ExpressRoute 技术概述](../../../expressroute/expressroute-introduction.md)。 在路由建立后，它连接到 Azure 主干网。

> [!NOTE] 
> 若要在 Azure 中运行 SAP 布局，请连接到距离 SAP 布局中的 Azure 区域最近的企业边缘路由器。 HANA 大型实例标记通过专用企业边缘路由器设备进行连接，以最大程度降低 Azure IaaS 中的 VM 与 HANA 大型实例标记之间的网络延迟。

托管 SAP 应用程序实例的 VM 的 ExpressRoute 网关连接到一个与本地连接的 ExpressRoute 线路。 同一虚拟网络连接到一个专门用于连接大型实例模具的单独企业边缘路由器。 使用 ExpressRoute FastPath，从 HANA 大型实例到 SAP 应用程序层 VM 的数据流不再通过 ExpressRoute 网关进行路由，从而减少了网络往返延迟。

此系统是单个 SAP 系统的直观示例。 SAP 应用层承载在 Azure 中。 SAP HANA 数据库在 Azure 上的 SAP HANA（大型实例）上运行。 假设吞吐量为 2 Gbps 或 10 Gbps 的 ExpressRoute 网关带宽不产生瓶颈。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多个 SAP 系统或大型 SAP 系统

如果部署多个 SAP 系统或大型 SAP 系统用于连接 Azure SAP HANA（大型实例），ExpressRoute 网关的吞吐量可能会造成瓶颈。 或者，不妨在不同的 Azure 虚拟网络中隔离生产系统和非生产系统。 在这种情况下，请将应用层拆分成多个虚拟网络。 还可以针对以下案例创建用于连接到 HANA 大型实例的特殊虚拟网络：

- 为承载 NFS 共享的 Azure 中的 VM 直接从 HANA 大型实例中的 HANA 实例执行备份
- 将从 HANA 大型实例单位大的备份或其他文件复制到在 Azure 中管理的磁盘空间。

使用单独的虚拟网络来托管 VM，这些 VM 为在 HANA 大型实例与 Azure 之间大量传输的数据管理存储。 这种安排避免了将大型文件或数据从 HANA 大型实例传输到 Azure 对ExpressRoute 网关产生的影响，此网关为运行 SAP 应用程序层的 VM 提供服务。 

要获得缩放性更好的网络体系结构，请采取以下措施：

- 对于单个较大的 SAP 应用层，使用多个虚拟网络。
- 为所部署的每个 SAP 系统部署一个单独的虚拟网络，而不是将这些 SAP 系统集中放置在同一虚拟网络中的各个子网中。

  适用于 Azure 上的 SAP HANA（大型实例）的缩放性更好的网络体系结构：

![跨多个虚拟网络部署 SAP 应用层](./media/hana-overview-architecture/image4-networking-architecture.png)

根据在不同虚拟网络（托管不同 SAP 系统的 VM）之间应用的规则和限制，需要将这些虚拟网络进行对等。 有关虚拟网络对等互连的详细信息，请参阅[虚拟网络对等互连](../../../virtual-network/virtual-network-peering-overview.md)。


## <a name="routing-in-azure"></a>Azure 中的路由

在默认部署下，对于 Azure SAP HANA（大型实例），有三个重要的网络路由注意事项：

* Azure SAP HANA（大型实例）只能由 Azure VM 通过专用 ExpressRoute 连接进行访问，而不能直接从本地访问。 在 Microsoft 向你提供 HANA 大型实例单元后，不能立即从本地直接访问。 传递路由限制是用于 SAP HANA 大型实例的当前 Azure 网络体系结构造成的。 需要进行直接访问的某些管理客户端和任何应用程序（例如在本地运行的 SAP Solution Manager）都无法连接到 SAP HANA 数据库。 请查看“直接路由到 HANA 大型实例”部分来了解例外情况。

* 如果已在两个不同的 Azure 区域部署了 HANA 大型实例单位以进行灾难恢复，则会应用与之前相同的临时性路由限制。 换句话说，其中一个区域（如美国西部）的 HANA 大型实例单位的 IP 地址无法路由到部署在另一个区域（如美国东部）的 HANA 大型实例单位。 此限制与跨区域使用 Azure 网络对等互连或交叉连接将 HANA 大型实例单元连接到虚拟网络的 ExpressRoute 线路无关。 有关图形表示形式，请参阅“在多个区域使用 HANA 大型实例单位”部分中的插图。 此限制因已部署的体系结构产生，会禁止将 HANA 系统复制立即作为灾难恢复功能使用。 请查看“在多个区域中使用 HANA 大型实例单元”部分来了解最近更改。 

* Azure SAP HANA（大型实例）单位具有从服务器 IP 池地址范围中分配的 IP 地址，该地址范围是在请求 HANA 大型实例部署时提交的。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)。 此 IP 地址可以通过 Azure 订阅以及将 Azure 虚拟网络连接到 HANA 大型实例的线路进行访问。 从该服务器 IP 池地址范围中分配的 IP 地址将直接分配给硬件单元， 而不会经过 NAT 转换，在此解决方案的第一个部署中也存在这种情况。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由到 HANA 大型实例

默认情况下，可传递路由不适用于以下情况：

* 在 HANA 大型实例单元与本地部署之间路由。

* 在部署在两个不同区域中的 HANA 大型实例单元之间路由。

有三种方法可以在这些情况下启用可传递路由：

- 来回路由数据的反向代理。 例如，部署在连接到 HANA 大型实例和本地的 Azure 虚拟网络中的 F5 BIG-IP、NGINX（带流量管理器）作为虚拟防火墙/流量路由解决方案。
- 在 Linux VM 中使用 IPTables 规则在本地位置与 HANA 大型实例单元之间，或者在不同区域中的 HANA 大型实例单元之间实现路由。 运行 IPTables 的 VM 需要部署在连接到 HANA 大型实例和本地的 Azure 虚拟网络中。 VM 需要相应地调整大小，以便 VM 的网络吞吐量能够满足预期的网络流量。 有关 VM 网络带宽的详细信息，请参阅 [Azure 中 Linux 虚拟机的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
- [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)是另一种实现本地与 HANA 大型实例单元之间直接流量的解决方案。 

这些解决方案的所有流量都通过 Azure 虚拟网络进行路由，因此流量可能会受到所使用的软设备或 Azure 网络安全组的额外限制，进而本地的某些 IP 地址或 IP 地址范围可能会被阻止或显式允许访问 HANA 大型实例。 

> [!NOTE]  
> 请注意，Microsoft 不实现也不支持涉及第三方网络设备或 IPTables 的自定义解决方案。 必须由所用组件的供应商或集成者提供支持。 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft 引入了名为 [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) 的新功能。 在以下两种情况下，Global Reach 可用于 HANA 大型实例：

- 实现从本地到部署在不同区域中的 HANA 大型实例单元的直接访问
- 实现部署在不同区域中的 HANA 大型实例单元之间的直接通信


##### <a name="direct-access-from-on-premises"></a>从本地直接访问
在 Global Reach 可用的 Azure 区域中，可以请求为 ExpressRoute 线路启用 Global Reach 功能，此线路将本地网络连接到还与 HANA 大型实例单元连接的 Azure 虚拟网络。 对于 ExpressRoute 线路的本地端，存在一些成本影响。 请查看 [Global Reach 加载项](https://azure.microsoft.com/pricing/details/expressroute/)价格来了解价格。 对于将 HANA 大型实例单元连接到 Azure 的线路，没有与之相关的额外成本。 

> [!IMPORTANT]  
> 如果使用 Global Reach 实现 HANA 大型实例单元与本地资产之间的直接访问，则网络数据和控制流不会通过 Azure 虚拟网络进行路由，而是直接在 Microsoft 企业交换路由器之间进行路由。 因此，任何 NSG 或 ASG 规则或部署在 Azure 虚拟网络中的任何类型防火墙、NVA 或代理都不会受到影响。 如果使用 ExpressRoute Global Reach 实现从本地到 HANA 大型实例单元的直接访问，则需要在本地端的防火墙中定义访问 HANA 大型实例单元的限制和权限 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>连接不同 Azure 区域中的 HANA 大型实例
同样，由于 ExpressRoute Global Reach 可用于将本地连接到 HANA 大型实例单元，它也可用于连接两个部署在两个不同区域中的 HANA 大型实例租户。 这种隔离是 HANA 大型实例租户用于在这两个区域中连接到 Azure 的 ExpressRoute 线路。 连接两个不同区域中部署的两个 HANA 大型实例租户时，不会产生额外费用。 

> [!IMPORTANT]  
> 不同 HANA 大型实例租户之间的网络流量的数据流和控制流不会通过 Azure 网络进行路由。 因此，无法使用 Azure 功能或 NVA 来强制实施两个 HANA 大型实例租户之间的通信限制。 

如需更详细地了解如何启用 ExpressRoute Global Reach，请参阅[将虚拟网络连接到 HANA 大型实例](./hana-connect-vnet-express-route.md)文档。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型实例的 Internet 连接
HANA 大型实例未建立直接 Internet 连接。 这会限制某些功能，例如，直接向 OS 供应商注册 OS 映像的功能。 可能需要使用本地 SUSE Linux Enterprise Server 订阅管理工具服务器或 Red Hat Enterprise Linux 订阅管理器。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 与 HANA 大型实例之间的数据加密
在 HANA 大型实例与 VM 之间传输的数据不会加密。 但是，仅仅是用于 HANA DBMS 端和基于 JDBC/ODBC 应用程序之间的交换，可以启用加密的流量。 有关详细信息，请参阅[此 SAP 文档](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多个区域使用 HANA 大型实例单位

为了实现灾难恢复设置，需要在多个 Azure 区域中有 HANA 大型实例单元。 即使使用 Azure [全局 VNet 对等互连]，可传递路由也在两个不同区域中的 HANA 大型实例租户之间默认不起作用。 但 Global Reach 开辟了预配在两个不同区域中的 HANA 大型实例单元之间的通信路径。 ExpressRoute Global Reach 的这种使用方案便于：

 - 执行 HANA 系统复制（没有任何额外的代理或防火墙）
 - 在两个不同区域中的 HANA 大型实例单元之间复制备份，以执行系统复制或系统刷新


![连接到了不同 Azure 区域中的 Azure 大型实例模具的虚拟网络](./media/hana-overview-architecture/image8-multiple-regions.png)

上图展示了两个区域中的不同虚拟网络如何连接到两个不同的 ExpressRoute 线路，这两个线路用于连接到两个 Azure 区域（灰线）中的 Azure SAP HANA（大型实例）。 使用这两个交叉连接的原因是为了防止两侧出现 MSEE 故障。 两个 Azure 区域中的两个虚拟网络之间的通信流应通过两个不同区域中的两个虚拟网络（蓝色虚线）的[全球对等互连](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure)进行处理。 粗红线是指 ExpressRoute Global Reach 连接，此连接便于两个不同区域中的租户的 HANA 大型实例单元能够相互通信。 

> [!IMPORTANT] 
> 如果使用了多个 ExpressRoute 线路，则应使用“AS 路径前追加”和“本地首选 BGP”设置来确保正确路由流量。

后续步骤
- 请参阅 [SAP HANA（大型实例）存储体系结构](hana-storage-architecture.md)
