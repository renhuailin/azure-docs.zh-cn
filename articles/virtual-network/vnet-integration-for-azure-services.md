---
title: 用于网络隔离的 Azure 服务的虚拟网络集成
titlesuffix: Azure Virtual Network
description: 本文介绍了如何通过各种方法将 Azure 服务集成到虚拟网络，以便安全地访问 Azure 服务。
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
ms.openlocfilehash: 8630451fe4ff8b3468b5c31168a417a72e8769f3
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386275"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>将 Azure 服务与虚拟网络集成以实现网络隔离

使用 Azure 服务的虚拟网络 (VNet) 集成，你可以仅允许通过你的虚拟网络基础结构来访问服务。 VNet 基础结构还包括对等互连的虚拟网络和本地网络。

VNet 集成可以为 Azure 服务提供网络隔离的优点，可通过以下一种或多种方法实现：
- [将服务的专用实例部署到虚拟网络中](virtual-network-for-azure-services.md)。 随后即可在虚拟网络内以及从本地网络私密访问这些服务。
- 使用[专用终结点](../private-link/private-endpoint-overview.md)，它可以通过私密且安全的方式将你连接到由 [Azure 专用链接](../private-link/private-link-overview.md)提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入虚拟网络中。
- 通过[服务终结点](virtual-network-service-endpoints-overview.md)将虚拟网络扩展到服务，使用公共终结点访问服务。 服务终结点可使服务资源在虚拟网络中得到保护。
- 使用[服务标记](service-tags-overview.md)来允许或拒绝 Azure 资源进出公共 IP 终结点的流量。

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>将专用 Azure 服务部署到虚拟网络

在虚拟网络中部署专用 Azure 服务时，可通过专用 IP 地址与服务资源进行私密通信。

![将专用 Azure 服务部署到虚拟网络](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

将专用 Azure 服务部署到虚拟网络可提供以下功能：
- 虚拟网络内的资源可以通过专用 IP 地址彼此进行私密通信。 例如，在虚拟网络中，在虚拟机上运行的 HDInsight 与 SQL Server 之间可直接传输数据。
- 本地资源可通过站点到站点 VPN（VPN 网关）或 ExpressRoute 使用专用 IP 地址访问虚拟网络中的资源。
- 虚拟网络可使用专用 IP 地址进行对等互连，实现虚拟网络中资源之间的彼此通信。
- 虚拟网络中的服务实例通常由 Azure 服务完全托管。 此管理包括监视资源的运行状况和根据负载进行缩放。
- 服务实例部署在虚拟网络的子网中。 根据服务提供的指南，必须通过网络安全组对子网开放入站和出站网络访问。
- 某些服务会对它们部署在其中的子网施加限制。 这些限制会限制策略、路由或同一子网内组合 VM 和服务资源的应用。 检查每个服务的特定限制，因为它们可能会随时间而改变。 此类服务的示例包括：Azure NetApp 文件、专用 HSM、Azure 容器实例、应用服务。
- （可选）服务可能需要一个委派子网作为显式标识符，用于表示子网可承载特定服务。 服务可以通过委托获得显式权限，可以在委托的子网中创建服务专属资源。
- 如需 REST API 响应的示例，请参阅包含委托子网的虚拟网络。 可以通过可用委托 API 获得一个内容广泛的列表，其中包含的服务使用委托子网模型。

有关可部署到虚拟网络中的服务的列表，请参阅[将专用 Azure 服务部署到虚拟网络](virtual-network-for-azure-services.md)。

## <a name="private-link-and-private-endpoints"></a>专用链接和专用终结点

专用终结点允许流量安全地从虚拟网络流入到 Azure 资源。 无需公共 IP 地址即可建立此专用链接。 专用终结点是虚拟网络中的 Azure 服务的特殊网络接口。 为资源创建专用终结点时，它会在虚拟网络上的客户端与你的 Azure 资源之间提供安全连接。 从虚拟网络的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与 Azure 服务之间的连接为专用链接。

在关系图中，右侧显示 Azure SQL 数据库作为目标 PaaS 服务。 目标可以是[支持专用终结点的任何服务](../private-link/availability.md)。 多个客户有多个逻辑 SQL Server 实例，这些实例都可以通过公共 IP 地址访问。

在本例中，逻辑 SQL Server 的一个实例通过专用终结点公开。 该终结点使 SQL Server 可以通过客户端虚拟网络中的专用 IP 地址访问。 由于 DNS 配置更改，客户端应用程序现在将其流量直接发送到该专用终结点。 目标服务将看到源自 VNet 的专用 IP 地址的流量。 

专用链接由绿色箭头表示。 目标资源的公共 IP 地址仍然可以与专用终结点同时存在。 客户端应用程序不再使用公共 IP。 防火墙现在可以禁止对该公共 IP 地址的任何访问，使其只能通过专用终结点访问。 从 VNet 到没有专用终结点的 SQL Server 的连接仍将源自公共 IP 地址。 此流由蓝色箭头表示。

![专用终结点](./media/network-isolation/architecture-private-endpoints.png)

客户端应用程序通常使用 DNS 主机名来访问目标服务。 无需对应用程序进行更改。 [必须配置 VNet 中的 DNS 解析](../private-link/private-endpoint-dns.md)，以将同一主机名解析为目标资源的专用 IP 地址，而不是原始公共 IP 地址。 通过客户端和目标服务之间的专用路径，客户端不依赖于公共 IP 地址。 目标服务可以关闭公共访问。

通过公开各个实例可以[防止数据盗窃](../private-link/private-endpoint-overview.md#network-security-of-private-endpoints)。 恶意行动者无法从数据库收集信息并将其上传到另一个公共数据库或存储帐户。 可以阻止访问所有 PaaS 服务的公共IP 地址。 仍可允许通过专用终结点访问 PaaS 实例。

若要详细了解专用链接和支持的 Azure 服务列表，请参阅[什么是专用链接？](../private-link/private-link-overview.md)。

## <a name="service-endpoints"></a>服务终结点

服务终结点通过 Azure 主干网络提供与 Azure 服务的安全直接连接。 使用终结点可以保护 Azure 资源，只允许在虚拟网络中对其进行访问。 借助服务终结点，VNet 中的专用 IP 地址无需出站公共 IP 即可访问 Azure 服务。

如果没有服务终结点，仅限制对 VNet 的访问可能会很有挑战性。 源 IP 地址可以更改，也可以与其他客户共享。 例如，具有共享出站 IP 地址的 PaaS 服务。 使用服务终结点时，目标服务看到的源 IP 地址将成为 VNet 中的专用 IP 地址。 通过这种入口流量更改，可以轻松识别来源，并将其用于配置适当的防火墙规则。 例如，仅允许来自该 VNet 中特定子网的流量。

使用服务终结点时，Azure 服务的 DNS 条目会保持不变，继续解析为分配给 Azure 服务的公共 IP 地址。

在下图中，右侧是同一目标 PaaS 服务。 左侧有一个包含两个子网的客户 VNet：子网 A，其服务终结点指向 `Microsoft.Sql`，子网 B，未定义服务终结点。 

当子网 B 中的资源尝试访问任何 SQL Server 时，它将使用公共 IP 地址进行出站通信。 此流量由蓝色箭头表示。 SQL Server 防火墙必须使用该公共 IP 地址来允许或阻止网络流量。 

当子网 A 中的资源尝试访问数据库服务器时，它将被视为 VNet 中的专用 IP 地址。 此流量由绿色箭头表示。 现在 SQL Server 防火墙可以专门允许或阻止子网 A。不需要了解源服务的公共 IP 地址。

![服务终结点](./media/network-isolation/architecture-service-endpoints.png)

服务终结点适用于目标服务的所有实例。 例如，Azure 客户的所有 SQL Server 实例，而不只是客户的实例。

有关详细信息，请参阅[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)

## <a name="service-tags"></a>服务标记

服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 使用服务标记，可定义[网络安全组](./network-security-groups-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)上的网络访问控制。 可以允许或拒绝服务的流量。 若要允许或拒绝流量，请指定规则的源或目标字段中的服务标记。 

![使用服务标记允许或拒绝流量](./media/network-isolation/service-tags.png)

在访问具有公共终结点的 Azure 服务时，实现网络隔离并保护你的 Azure 资源不受 Internet 的影响。 创建入站/出站网络安全组规则，以拒绝传入和传出 Internet 的流量，并允许传入/传出 AzureCloud 的流量 。 有关更多服务标记，请参阅特定 Azure 服务的[可用服务标记](service-tags-overview.md#available-service-tags)。

有关服务标记和支持它们的 Azure 服务的详细信息，请参阅[服务标记概述](service-tags-overview.md)

## <a name="compare-private-endpoints-and-service-endpoints"></a>比较专用终结点和服务终结点

>[!NOTE]
> Microsoft 建议使用 Azure 专用链接。 专用链接在从本地私密访问 PaaS、内置数据泄露保护和将服务映射到自己网络中的专用 IP 方面提供更好的功能。 有关详细信息，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)

值得指出的是，服务终结点和专用终结点都有共同的特征，而不应只考虑它们之间的差异。

这两种功能都用于对目标服务上的防火墙进行更精细的控制。 例如，限制对 SQL Server 数据库或存储帐户的访问。 但两者的操作不同，这在前面的部分中有更详细的介绍。

这两种方法都克服了[源网络地址转换 (SNAT) 端口耗尽](../load-balancer/load-balancer-outbound-connections.md#scenarios)的问题。 当通过网络虚拟设备 (NVA) 或具有 SNAT 端口限制的服务经隧道传输流量时，可能会发现耗尽的情况。 使用服务终结点或专用终结点时，流量会通过优化路径直接传输到目标服务。 这两种方法都有利于带宽密集型应用程序，因为降低了延迟和成本。

在这两种情况下，仍可以确保进入目标服务的流量通过网络防火墙或 NVA。 对于这两种方法，此过程是不同的。 使用服务终结点时，应在防火墙子网（而不是在部署源服务的子网）上配置服务终结点。 使用专用终结点时，在源子网上为专用终结点的 IP 地址添加一个用户定义路由 (UDR)。 而不是在专用终结点的子网中进行此操作。

请参阅下表来比较和了解差别。

| 注意事项                                                                                                                                    | 服务终结点                                                                                                           | 专用终结点                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 应用配置的级别的服务范围                                                                                                   | 整个服务（例如所有客户的所有 SQL Server 或存储帐户 ）                                      | 单个实例（例如你拥有的特定 SQL Server 实例或存储帐户）                                                                    |
|内置数据泄露保护 - 数据可被恶意内部人员从受保护的 PaaS 资源移动/复制到其他不受保护的 PaaS 资源| 否 | 是|
|从本地以私密方式访问 PaaS 资源| 否| 是|
|服务访问所需的 NSG 配置| 是（使用服务标记）| 否|
| 无需使用任何公共 IP 地址即可访问服务                                                                                       | 否                                                                                                                          | 是                                                                                                                                                               |
| Azure 到 Azure 的流量保留在 Azure 主干网络上                                                                                       | 是                                                                                                                         | 是                                                                                                                                                               |
| 服务可以禁用其公共 IP 地址                                                                                                        | 否                                                                                                                          | 是                                                                                                                                                               |
| 可以轻松地限制来自 Azure 虚拟网络的流量                                                                             | 是（允许从特定子网访问和/或使用 NSG）                                                                   | 否*                                                                                                                                                               |
| 可以轻松地限制来自本地 (VPN/ExpressRoute) 的流量                                                                           | N/A**                                                                                                                       | 否*                                                                                                                                                               |
| 需要 DNS 更改                                                                                                                             | 否                                                                                                                          | 是（请参阅 [DNS 配置](../private-link/private-endpoint-dns.md)）                                                                 |
| 影响解决方案的成本                                                                                                                | 否                                                                                                                          | 是（请参阅[专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)）                                                                       |
| 影响解决方案的[复合 SLA](/azure/architecture/framework/resiliency/business-metrics#composite-slas) | 否                                                                                                                          | 是（专用链接服务本身具有 [99.99% SLA](https://azure.microsoft.com/support/legal/sla/private-link/)）                                                 |

*网络视距进入专用终结点的任何内容都将具有网络级别的访问权限。 此访问权限无法由专用终结点上的 NSG 控制。

**无法从本地网络访问在虚拟网络中保护的 Azure 服务资源。 要允许来自本地的流量，请允许来自本地或 ExpressRoute 的公共（通常为 NAT）IP 地址。 可通过 Azure 服务资源的 IP 防火墙配置添加这些 IP 地址。 有关详细信息，请参阅 [VNet 常见问题解答](virtual-networks-faq.md#can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints)。

## <a name="next-steps"></a>后续步骤

- 了解如何[将应用与 Azure 网络集成](../app-service/web-sites-integrate-with-vnet.md)。
- 了解如何[使用服务标记限制对资源的访问](tutorial-restrict-network-access-to-resources.md)。
- 了解如何[使用 Azure 专用链接以私密方式连接到 Azure Cosmos 帐户](../private-link/tutorial-private-endpoint-cosmosdb-portal.md)。
