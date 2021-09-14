---
title: 将 Azure API 管理与 Azure 虚拟网络结合使用
description: 了解将 API 管理实例连接到 Azure 虚拟网络的方案和要求。
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 9d021eb435eb74612f7716fb6d790e6878b9d9d5
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544381"
---
# <a name="use-a-virtual-network-with-azure-api-management"></a>将虚拟网络与 Azure API 管理结合使用

借助 Azure 虚拟网络 (VNET)，你可以将 Azure 资源放置在你能够控制访问权限的非 Internet 可路由网络中。 然后，你可以使用各种 VPN 技术将 VNET 连接到本地网络。 若要了解有关 Azure VNET 的详细信息，请先阅读 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)中的相关信息。

本文介绍了 API 管理实例的 VNET 连接选项、要求和注意事项。 你可以使用 Azure 门户、Azure CLI、Azure 资源管理器模板或其他部署工具。 可以使用[网络安全组][NetworkSecurityGroups]控制其中部署了 API 管理的子网的入站和出站流量。

有关部署步骤和网络配置的详细信息，请参阅：

* [使用 Azure API 管理连接到外部虚拟网络](./api-management-using-with-vnet.md)。
* [使用 Azure API 管理连接到内部虚拟网络](./api-management-using-with-internal-vnet.md)。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="access-options"></a>访问选项

默认情况下，必须可从 Internet 访问 API 管理实例。 使用虚拟网络，你可以将开发人员门户、API 网关和其他 API 管理终结点配置为可从 Internet（外部模式）或仅在 VNET（内部模式）中访问。 

* **外部** - 可以通过外部负载均衡器从公共 Internet 访问 API 管理终结点。 网关可以访问 VNET 中的资源。

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-external.png" alt-text="连接到外部 VNET":::

    在外部模式下使用 API 管理访问部署在虚拟网络中的后端服务。

* **内部** - 只能通过内部负载均衡器从 VNET 内部访问 API 管理终结点。 网关可以访问 VNET 中的资源。

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-internal.png" alt-text="连接到内部 VNET":::

    在内部模式中使用 API 管理可以：

    * 通过使用 Azure VPN 连接或 Azure ExpressRoute，使专用数据中心内托管的 API 可被第三方安全访问。
    * 通过公共网关公开基于云的 API 和本地 API，以便启用混合云方案。
    * 使用单一网关终结点管理托管在多个地理位置的 API。


## <a name="network-resource-requirements"></a>网络资源要求

以下是 API 管理的虚拟网络资源要求。 某些要求因托管 API 管理实例的[计算平台](compute-infrastructure.md)的版本（`stv2` 或 `stv1`）而异。

### <a name="stv2"></a>[stv2](#tab/stv2)

* 需要 Azure 资源管理器虚拟网络。
* 除了指定虚拟网络和子网，还必须提供标准 SKU [公共 IPv4 地址](../virtual-network/public-ip-addresses.md#standard)。
* 用于连接到 API 管理实例的子网可能包含其他 Azure 资源类型。
* API 管理服务、虚拟网络和子网以及公共 IP 地址资源必须位于同一区域和订阅中。
* 对于多区域 API 管理部署，请分别为每个位置配置虚拟网络资源。

### <a name="stv1"></a>[stv1](#tab/stv1)

* 需要 Azure 资源管理器虚拟网络。
* 用于连接 API 管理实例的子网必须专用于 API 管理。 它不能包含其他 Azure 资源类型。
* API 管理服务、虚拟网络和子网资源必须位于同一区域和订阅中。
* 对于多区域 API 管理部署，请分别为每个位置配置虚拟网络资源。

---

## <a name="subnet-size"></a>子网大小

可在其中部署 API 管理的子网的最小大小为 /29，其中提供 3 个可用的 IP 地址。 因此，每增加一个 API 管理缩放单元，即需要增加两个 IP 地址。 最小大小要求基于以下注意事项：

* Azure 会保留每个子网中无法使用的一些 IP 地址。 为遵从协议，保留子网的第一个和最后一个 IP 地址。 另外三个地址将用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)。

* 除 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 API 管理实例还会使用以下地址：
    * 每单位 Premium SKU 两个 IP 地址，或 
    * Developer SKU，一个 IP 地址。 

* 每个实例会为外部负载均衡器保留额外的 IP 地址。 当部署到[内部 VNET](./api-management-using-with-internal-vnet.md) 时，实例需要使用相关内部负载均衡器的额外 IP 地址。

## <a name="routing"></a>路由

将 API 管理实例部署到[外部 VNET](./api-management-using-with-vnet.md#routing) 或[内部 VNET](./api-management-using-with-internal-vnet.md#routing) 时，请参阅路由指南。

详细了解 [API 管理的 IP 地址](api-management-howto-ip-addresses.md)。

## <a name="dns"></a>DNS

在外部模式下，VNET 为 API 管理终结点和其他 Azure 资源启用 [Azure 提供的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)。 它不提供本地资源的名称解析。 

在内部模式下，你必须提供自己的 DNS 解决方案，以确保 API 管理终结点和其他所需的 Azure 资源的名称解析。 建议配置 Azure [专用 DNS 区域](../dns/private-dns-overview.md)。

有关详细信息，请参阅： 
* [Azure 虚拟网络中资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)  
* [创建 Azure 专用 DNS 区域](../dns/private-dns-getstarted-portal.md)

> [!IMPORTANT]
> 如果计划对 VNET 使用自定义 DNS 解决方案，则应先进行设置，再将 API 管理服务部署到 VNET 中。 否则，每次通过运行[应用网络配置操作](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates)更改 DNS 服务器时，都需要更新 API 管理服务。

## <a name="limitations"></a>限制

某些限制因托管 API 管理实例的[计算平台](compute-infrastructure.md)的版本（`stv2` 或 `stv1`）而异。

### <a name="stv2"></a>[stv2](#tab/stv2)

* 无法在订阅之间移动包含 API 管理实例的子网。
* 对于在内部 VNET 模式下配置的多区域 API 管理部署，用户拥有路由权限，并且负责跨多个区域管理负载均衡。
* 若要将 API 从 [OpenAPI 规范](import-and-publish.md)导入 API 管理，规范 URL 必须托管在可公开访问的 Internet 地址上。

### <a name="stv1"></a>[stv1](#tab/stv1)

* 无法在订阅之间移动包含 API 管理实例的子网。
* 对于在内部 VNET 模式下配置的多区域 API 管理部署，用户拥有路由权限，并且负责跨多个区域管理负载均衡。
* 若要将 API 从 [OpenAPI 规范](import-and-publish.md)导入 API 管理，规范 URL 必须托管在可公开访问的 Internet 地址上。
* 由于平台限制，另一个区域的全局对等互连 VNET 中的资源与处于内部模式的 API 管理服务之间的连接将中断。 有关详细信息，请参阅[虚拟网络文档](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。

---

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [使用 VPN 网关将虚拟网络连接到后端](../vpn-gateway/design.md#s2smulti)
* [通过不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [虚拟网络常见问题](../virtual-network/virtual-networks-faq.md)

连接到虚拟网络：
* [使用 Azure API 管理连接到外部虚拟网络](./api-management-using-with-vnet.md)。
* [使用 Azure API 管理连接到内部虚拟网络](./api-management-using-with-internal-vnet.md)。

请查看以下主题

* [使用 Vpn 网关将虚拟网络连接到后端](../vpn-gateway/design.md#s2smulti)
* [通过不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何使用 API 检查器跟踪 Azure API 管理中的调用](api-management-howto-api-inspector.md)
* [虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)
* [服务标记](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/virtual-network-concepts/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/virtual-network-concepts/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
