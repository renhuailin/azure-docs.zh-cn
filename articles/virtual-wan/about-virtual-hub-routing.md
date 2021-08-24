---
title: 关于虚拟中心路由
titleSuffix: Azure Virtual WAN
description: 了解虚拟 WAN 虚拟中心路由。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 154680d5f62140b95e7ada3a37678ee3be1c5b24
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015491"
---
# <a name="about-virtual-hub-routing"></a>关于虚拟中心路由

虚拟中心的路由功能由路由器提供，该路由器使用边界网关协议 (BGP) 管理网关之间的所有路由。 虚拟中心可以包含多个网关，例如站点到站点 VPN 网关、ExpressRoute 网关、点到站点网关、Azure 防火墙。 该路由器还提供与虚拟中心连接的虚拟网络之间的传输连接，最高可支持 50 Gbps 的总吞吐量。 这些路由功能适用于标准虚拟 WAN 客户。

若要配置路由，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。

## <a name="routing-concepts"></a><a name="concepts"></a>路由概念

以下部分介绍了虚拟中心路由的关键概念。

### <a name="hub-route-table"></a><a name="hub-route"></a>中心路由表

虚拟中心路由表可以包含一个或多个路由。 路由包括其名称、标签、目标类型、目标前缀列表和要路由的数据包的下一跃点信息。 连接通常有一个与路由表关联的或传播到路由表的路由配置。

### <a name="hub-routing-intent-and-policies"></a><a name= "hub-route"></a> 中心路由意向和策略
>[!NOTE]  
> 中心路由策略目前为托管预览版。 
>  
>若要获取对此预览版的访问权限，请向 previewinterhub@microsoft.com 发送邮件并提供你要配置路由策略的虚拟 WAN ID、订阅 ID 和 Azure 区域。 通常你会在 24-48 小时内收到确认功能已启用的响应。 
>
> 若要详细了解如何配置路由意向和策略，请查看[此文档](how-to-routing-policies.md)。


使用 Azure 防火墙管理器为公共和专用流量设置策略的客户现在可以使用路由意向和路由策略以简单得多的方式设置其网络。

路由意向和路由策略用于指定虚拟 WAN 中心如何转发 Internet 绑定流量和专用（点到站点、站点到站点、ExpressRoute、虚拟 WAN 中心和虚拟网络中的网络虚拟设备）流量。 有两种类型的路由策略：Internet 流量路由策略和专用流量路由策略。 每个虚拟 WAN 中心最多可以有一个 Internet 流量路由策略和一个专用流量路由策略，每个策略都有一个“下一个跃点”资源。 

虽然专用流量同时包含分支和虚拟网络地址前缀，但路由策略将它们视为路由意向概念中的一个实体。


* Internet 流量路由策略：在虚拟 WAN 中心配置 Internet 流量路由策略时，到该虚拟 WAN 中心的所有分支（用户 VPN（点到站点 VPN）、站点到站点 VPN 和 ExpressRoute）连接以及虚拟网络连接都会将 Internet 绑定流量转发到在路由策略中指定的 Azure 防火墙资源或第三方安全提供程序。
 

* 专用流量路由策略：在虚拟 WAN 中心配置专用流量路由策略时，进出虚拟 WAN 中心的所有分支和虚拟网络流量（包括中心间流量）都会转发到已在专用流量路由策略中指定的“下一个跃点”Azure 防火墙资源。 

若要详细了解如何配置路由意向和策略，请查看[此文档](how-to-routing-policies.md)。

### <a name="connections"></a><a name="connection"></a>连接

连接是具有路由配置的资源管理器资源。 这四种连接类型为：

* **VPN 连接**：将 VPN 站点连接到虚拟中心 VPN 网关。
* **ExpressRoute 连接**：将 ExpressRoute 线路连接到虚拟中心 ExpressRoute 网关。
* **P2S 配置连接**：将用户 VPN（点到站点）配置连接到虚拟中心用户 VPN（点到站点）网关。
* **中心虚拟网络连接**：将虚拟网络连接到虚拟中心。

可以在安装期间为虚拟网络连接设置路由配置。 默认情况下，所有连接都关联并传播到默认路由表。

### <a name="association"></a><a name="association"></a>关联

每个连接都与一个路由表相关联。 将连接与路由表相关联，使你可以将流量发送到路由表中指定为路由的目标。 连接的路由配置将显示关联的路由表。  可以将多个连接关联到相同的路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接都关联到同一（默认）路由表。

默认情况下，所有连接都与虚拟中心中的“默认路由表”相关联。 每个虚拟中心都有自己的默认路由表，可以对其进行编辑以添加静态路由。 如果前缀相同，静态添加的路由优先于动态获取的路由。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="关联"lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

### <a name="propagation"></a><a name="propagation"></a>传播

连接将路由动态传播到路由表。 在 VPN 连接、ExpressRoute 连接或 P2S 配置连接中，使用 BGP 将路由从虚拟中心传播到本地路由器。 路由可以传播到一个或多个路由表。

可为每个虚拟中心使用“无路由表”。 传播到“无路由表”意味着不需要从连接传播路由。 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="传播":::

### <a name="labels"></a><a name="labels"></a>标签

标签提供一种对路由表进行逻辑分组的机制。 该机制在将路由从连接传播到多个路由表的过程中尤其有用。 例如，默认路由表具有一个名为“默认”的内置标签。 当用户将连接路由传播到“默认”标签时，它会自动应用于虚拟 WAN 中每个中心的所有默认路由表。

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>在虚拟网络连接中配置静态路由

配置静态路由提供了一种机制来引导流量通过下一跃点 IP，这可以是与虚拟中心连接的分支 VNet 中预配的网络虚拟设备 (NVA) 的 IP。 静态路由由路由名称、目标前缀列表和下一跃点 IP 组成。

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>预先存在的路由的路由表

路由表现在具有关联和传播功能。 预先存在的路由表是不具有这些功能的路由表。 如果中心路由中有预先存在的路由，并且你希望使用新功能，请考虑以下事项：

* **在虚拟中心中具有预先存在的路由的标准虚拟 WAN 客户**：

   如果你在 Azure 门户中的中心的“路由”部分具有预先存在的路由，则需要首先删除它们，再尝试在 Azure 门户中的中心的“路由表”部分创建新的路由表。

* **在虚拟中心中具有预先存在的路由的基本虚拟 WAN 客户**：

   如果你在 Azure 门户中的中心的“路由”部分具有预先存在的路由，则需要首先删除它们，再将虚拟 WAN 从基本版升级到标准版。 请参阅[将虚拟 WAN 从基本版升级到标准版](upgrade-virtual-wan.md)。

## <a name="hub-reset"></a><a name="reset"></a>中心重置

虚拟中心“重置”仅可在 Azure 门户中使用。 通过重置功能，可将任何失败的资源（例如路由表、中心路由器或虚拟中心资源本身）恢复为其正确预配状态。 在联系 Microsoft 寻求支持之前，请考虑重置中心。 此操作不会重置虚拟中心内的任何网关。

## <a name="additional-considerations"></a><a name="considerations"></a>其他注意事项

配置虚拟 WAN 路由时，请考虑以下事项：

* 所有分支连接（点到站点、站点到站点和 ExpressRoute）都需要与默认路由表关联。 这样，所有分支都将具有相同的前缀。
* 所有分支连接都需要将它们的路由传播到同一组路由表。 例如，如果你决定分支应传播到默认路由表，则此配置应在所有分支间保持一致。 因此，与默认路由表关联的所有连接都可以访问所有分支。
* 当前不支持分支到分支通过 Azure 防火墙。
* 在多个区域使用 Azure 防火墙时，所有辐射虚拟网络都必须关联到同一路由表。 例如，不可能出现让其中一部分 VNet 通过 Azure 防火墙，而同一虚拟中心的其他 VNet 绕过 Azure 防火墙的情况。
* 每个 VNet 连接都可配置一个下一跃点 IP。
* 与 0.0.0.0/0 路由有关的所有信息均限制在本地中心的路由表中。 此路由不会跨中心传播。
## <a name="next-steps"></a>后续步骤

* 若要配置路由，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。
* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
