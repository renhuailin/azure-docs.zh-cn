---
title: 专用链接 - Azure Database for MySQL
description: 了解专用链接如何用于 Azure Database for MySQL。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8a41832fdcc53820a9f7fe6436ec608b69f84126
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652297"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Azure Database for MySQL 的专用链接

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

使用专用链接可以通过专用终结点连接到 Azure 中的各种 PaaS 服务。 Azure 专用链接实质上是将 Azure 服务引入专用虚拟网络 (VNet) 中。 就像 VNet 中的任何其他资源一样，可以使用专用 IP 地址访问 PaaS 资源。

有关支持专用链接功能的 PaaS 服务列表，请查看“专用链接”[文档](../private-link/index.yml)。 专用终结点是特定 [VNet](../virtual-network/virtual-networks-overview.md) 和子网中的专用 IP 地址。

> [!NOTE]
> 专用链接功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保数据库服务器位于其中一个定价层中。

## <a name="data-exfiltration-prevention"></a>数据渗透防护

Azure Database for MySQL 中的数据渗透是指已获授权的用户（例如数据库管理员）能够从一个系统提取数据，并将其移到组织外部的其他位置或系统。 例如，该用户将数据移到第三方拥有的存储帐户。

假设如下场景，一位用户在 Azure 虚拟机 (VM) 中运行 MySQL Workbench，该虚拟机连接到美国西部预配的 Azure Database for MySQL 服务器。 以下示例演示了如何使用网络访问控制来限制通过公共终结点对 Azure Database for MySQL 进行访问。

* 通过将“*允许 Azure 服务*”设置为“关闭”，禁止所有 Azure 服务流量通过公共终结点进入 Azure Database for MySQL。 务必通过[防火墙规则](./concepts-firewall-rules.md)或[虚拟网络服务终结点](./concepts-data-access-and-security-vnet.md)来禁止某些 IP 地址或 IP 范围访问服务器。

* 仅允许流量使用 VM 的专用 IP 地址进入 Azure Database for MySQL。 有关详细信息，请参阅有关[服务终结点](concepts-data-access-and-security-vnet.md)和 [VNet 防火墙规则](howto-manage-vnet-using-portal.md)的文章。

* 在 Azure VM 上，按如下所示使用网络安全组 (NSG) 和服务标记缩小传出连接的范围

    * 指定一个 NSG 规则以允许“*服务标记 = SQL.WestUs*”的流量 - 仅允许连接到美国西部的 Azure Database for MySQL
    * 指定一个 NSG 规则（具有较高的优先级），以拒绝“*服务标记 = SQL*”的流量 - 拒绝连接到所有区域中 Azure Database for MySQL 的更新</br></br>

完成此设置后，Azure VM 只能连接到“美国西部”区域的 Azure Database for MySQL。 不过，连接并不局限于 SQL 数据库中的单个 Azure Database for MySQL。 VM 仍可连接到“美国西部”区域中的任何 Azure Database for MySQL，包括不在订阅中的数据库。 尽管我们在上述场景中已将数据渗透范围缩小到了特定的区域，但我们并未完全消除这种渗透。</br>

借助专用链接，你现在可以设置 NSG 之类的网络访问控制来限制对专用终结点的访问。 然后，将单个 Azure PaaS 资源映射到特定的专用终结点。 恶意的预览体验成员只能访问映射的 PaaS 资源（例如 Azure Database for MySQL），而不能访问其他资源。

## <a name="on-premises-connectivity-over-private-peering"></a>通过专用对等互连建立本地连接

当你从本地计算机连接到公共终结点时，需要使用服务器级防火墙规则将 IP 地址添加到基于 IP 的防火墙。 尽管此模型非常适合用于允许对开发或测试工作负荷的单个计算机进行访问，但在生产环境中却难以管理。

借助专用链接，你可以使用 [Express Route](https://azure.microsoft.com/services/expressroute/) (ER)、专用对等互连或 [VPN 隧道](../vpn-gateway/index.yml)实现对专用终结点的跨界访问。 随后，它们可以禁用通过公共终结点的所有访问，而不使用基于 IP 的防火墙。

> [!NOTE]
> 在某些情况下，Azure Database for MySQL 和 VNet 子网位于不同的订阅中。 在这些情况下，必须确保以下配置：
> - 确保两个订阅都注册了 Microsoft.DBforMySQL 资源提供程序。 有关详细信息，请参阅[资源管理器注册][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置专用链接

### <a name="creation-process"></a>创建过程

启用专用链接需要专用终结点。 可按照以下操作指南完成此操作。

* [Azure 门户](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>审批过程
网络管理员创建专用终结点 (PE) 后，MySQL 管理员可以管理与 Azure Database for MySQL 建立的专用终结点连接 (PEC)。 网络管理员和 DBA 之间的这种职责分离有助于 Azure Database for MySQL 连接的管理。 

* 在 Azure 门户中导航到 Azure Database for MySQL 服务器资源。 
    * 在左窗格中选择专用终结点连接
    * 显示所有专用终结点连接 (PEC) 的列表
    * 创建的相应专用终结点 (PE)

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="选择专用终结点门户":::

* 在列表中选择单个 PEC。

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="选择待批准的专用终结点":::

* MySQL 服务器管理员可以选择批准或拒绝 PEC，并可以选择添加简短的文本响应。

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="选择专用终结点消息":::

* 批准或拒绝后，该列表将反映相应的状态以及回复文本

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="选择专用终结点最终状态":::

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>适用于 Azure Database for MySQL 的专用链接用例

客户端可以通过以下方式连接到专用终结点：同一 VNet；同一区域中或跨区域的[对等互连 VNet](../virtual-network/virtual-network-peering-overview.md)；或者跨区域的 [VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 此外，客户端可以使用 ExpressRoute、专用对等互连或 VPN 隧道从本地进行连接。 以下简化示意图显示了常见用例。

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="选择专用终结点概述":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>从对等互连虚拟网络 (VNet) 中的 Azure VM 进行连接
配置 [VNet 对等互连](../virtual-network/tutorial-connect-virtual-networks-powershell.md)，以便从对等互连的 VNet 中的 Azure VM 建立与 Azure Database for MySQL 的连接。

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>从 VNet 到 VNet 环境中的 Azure VM 进行连接
配置 [VNet 到 VNet VPN 网关连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)，以便从另一区域或订阅中的 Azure VM 建立与 Azure Database for MySQL 的连接。

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>通过 VPN 从本地环境进行连接
若要建立从本地环境到 Azure Database for MySQL 的连接，请选择并实施以下选项之一：

* [点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [站点到站点 VPN 连接](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>将专用链接与防火墙规则结合使用

将专用链接与防火墙规则结合使用时，可能会出现以下情况和结果：

* 如果未配置任何防火墙规则，则默认情况下，任何流量都无法访问 Azure Database for MySQL。

* 如果配置公共流量或服务终结点并创建专用终结点，则不同类型的传入流量将由相应类型的防火墙规则授权。

* 如果未配置任何公共流量或服务终结点，但创建了专用终结点，则只能通过专用终结点访问 Azure Database for MySQL。 如果未配置公共流量或服务终结点，则在拒绝或删除所有已批准的专用终结点后，任何流量都无法访问 Azure Database for MySQL。

## <a name="deny-public-access-for-azure-database-for-mysql"></a>拒绝对 Azure Database for MySQL 的公共访问

如果你希望只依赖专用终结点来访问其 Azure Database for MySQL，则可以通过在数据库服务器上设置“拒绝公用网络访问”配置来禁用所有公共终结点（即[防火墙规则](concepts-firewall-rules.md)和 [VNet 服务终结点](concepts-data-access-and-security-vnet.md)）的设置。 

当此设置设为“是”时，只允许通过专用终结点连接到 Azure Database for MySQL。 当此设置设为“否”时，客户端可以基于防火墙或 VNet 服务终结点设置连接到 Azure Database for MySQL。 此外，设置专用网络访问的值后，客户就无法添加和/或更新现有的“防火墙规则”和“VNet 服务终结点规则”。

> [!Note]
> 此功能适用于所有 Azure 区域，其中 Azure Database for MySQL 单一服务器支持“常规用途”和“内存优化”定价层。
>
> 此设置不会对 Azure Database for MySQL 的 SSL 和 TLS 配置产生任何影响。

若要了解如何从 Azure 门户为 Azure Database for MySQL 设置“拒绝公用网络访问”，请参阅[如何配置“拒绝公用网络访问”](howto-deny-public-network-access.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Database for MySQL 安全功能，请参阅以下文章：

* 若要为 Azure Database for MySQL 配置防火墙，请参阅[防火墙支持](./concepts-firewall-rules.md)。

* 若要了解如何为 Azure Database for MySQL 配置虚拟网络服务终结点，请参阅[配置从虚拟网络进行的访问](./concepts-data-access-and-security-vnet.md)。

* 有关 Azure Database for MySQL 连接性的概述，请参阅 [Azure Database for MySQL 连接性体系结构](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
