---
title: 用于网络隔离的 Azure 服务的虚拟网络集成
titlesuffix: Azure Virtual Network
description: 本文介绍将 Azure 服务集成到虚拟网络的不同方法，使你能够安全地访问 Azure 服务。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: a25d6d0d1990682287b5962a7bd93a0c525db133
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882358"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>将 Azure 服务与虚拟网络集成以实现网络隔离

虚拟网络 (VNet) 使用 Azure 服务进行集成，你可以将对服务的访问仅锁定到你的虚拟网络基础结构。 VNet 基础结构还包括对等互连的虚拟网络和本地网络。

VNet 集成为 Azure 服务提供网络隔离的优点，可通过以下一种或多种方法实现：
- 在[虚拟网络中部署服务的专用实例](virtual-network-for-azure-services.md)。 随后即可在虚拟网络内以及从本地网络私密访问这些服务。
- 使用 [私有终结点](../private-link/private-endpoint-overview.md) ，将你私下并安全地连接到由 [Azure 专用链接](../private-link/private-link-overview.md)提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址，从而将该服务有效地引入到虚拟网络。
- 通过 [服务终结点](virtual-network-service-endpoints-overview.md)将虚拟网络扩展到服务，使用公共终结点来访问服务。 服务终结点可使服务资源在虚拟网络中得到保护。
- 使用 [服务标记](service-tags-overview.md) 允许或拒绝进出公共 IP 终结点的 Azure 资源的流量。

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>将专用 Azure 服务部署到虚拟网络

在虚拟网络中部署专用 Azure 服务时，可通过专用 IP 地址与服务资源进行私密通信。

![将专用 Azure 服务部署到虚拟网络](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

将专用 Azure 服务部署到虚拟网络提供以下功能：
- 虚拟网络内的资源可以通过专用 IP 地址彼此进行私密通信。 例如，在虚拟网络中，在虚拟机上运行的 HDInsight 与 SQL Server 之间可直接传输数据。
- 本地资源可通过站点到站点 VPN（VPN 网关）或 ExpressRoute 使用专用 IP 地址访问虚拟网络中的资源。
- 虚拟网络可使用专用 IP 地址进行对等互连，实现虚拟网络中资源之间的彼此通信。
- 虚拟网络中的服务实例通常由 Azure 服务完全托管。 这包括监视资源的运行状况和负载扩展。
- 服务实例部署在虚拟网络的子网中。 根据服务提供的指南，必须通过网络安全组对子网开放入站和出站网络访问。
- 某些服务还会对它们能够部署到其中的子网施加限制，限制策略、路由的应用，或者要求将 VM 和服务资源组合到同一子网中。 检查每个服务的特定限制，因为它们可能会随时间而改变。 此类服务的示例包括：Azure NetApp 文件、专用 HSM、Azure 容器实例、应用服务。
- （可选）服务可能需要一个委派子网作为显式标识符，用于表示子网可承载特定服务。 服务可以通过委托获得显式权限，可以在委托的子网中创建服务专属资源。
- 如需 REST API 响应的示例，请参阅包含委托子网的虚拟网络。 可以通过可用委托 API 获得一个内容广泛的列表，其中包含的服务使用委托子网模型。

有关可部署到虚拟网络中的服务的列表，请参阅将 [专用 Azure 服务部署到虚拟](virtual-network-for-azure-services.md)网络。

## <a name="private-link-and-private-endpoints"></a>专用链接和专用终结点

你可以使用专用终结点将事件直接从虚拟网络直接发送到 Azure 资源，而无需通过公共 internet。 专用终结点是虚拟网络中的 Azure 服务的特殊网络接口。 为 Azure 资源创建专用终结点时，它会在虚拟网络上的客户端与 Azure 资源之间提供安全连接。 从虚拟网络的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点和 Azure 服务之间的连接使用安全的专用链接。

下面的示例演示了事件网格资源专用终结点的私有访问，该终结点在虚拟网络和事件网格资源的客户端之间提供安全连接。

![使用专用终结点对 SQL DB 资源进行专用访问](./media/network-isolation/architecture-diagram.png)

有关私有链接和支持的 Azure 服务列表的详细信息，请参阅 [什么是私有链接？](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>服务终结点
VNet 服务终结点通过 Azure 主干网络上的优化路由，提供与 Azure 服务的安全直接连接。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 服务终结点使 VNet 中的专用 IP 地址能够到达 Azure 服务的终结点，且无需在 VNet 中使用公共 IP 地址。

![在虚拟网络中保护 Azure 服务](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

有关详细信息，请参阅 [虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>服务标记

服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 使用服务标记，可以定义 [网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 或 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上的网络访问控制。 通过在规则的相应源字段或目标字段中指定服务标记名（例如，AzureEventGrid），可以允许或拒绝相应服务的流量。

![使用服务标记允许或拒绝流量](./media/network-isolation/service-tags.png)

可使用服务标记来实现网络隔离，保护 Azure 资源免受常规 Internet 侵害，同时访问具有公共终结点的 Azure 服务。 可创建入站/出站网络安全组规则，以拒绝进出 Internet 的流量并允许进出 AzureCloud 或特定 Azure 服务的其他[可用服务标记](service-tags-overview.md#available-service-tags)的流量 。

有关支持服务标记和 Azure 服务的详细信息，请参阅 [服务标记概述](service-tags-overview.md)

## <a name="next-steps"></a>后续步骤

- 了解如何将 [应用与 Azure 网络集成](../app-service/web-sites-integrate-with-vnet.md)。
- 了解如何 [使用服务标记来限制对资源的访问](tutorial-restrict-network-access-to-resources.md)。
- 了解如何 [使用 Azure Private Link 将专用连接到 Azure Cosmos 帐户](../private-link/create-private-endpoint-cosmosdb-portal.md)。