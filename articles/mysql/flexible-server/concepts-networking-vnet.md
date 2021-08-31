---
title: 专用网络访问概述 - Azure Database for MySQL 灵活服务器
description: 了解 Azure Database for MySQL 灵活服务器部署选项中的专用网络访问选项
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: fa85818b364a869114fe6e3b2de8d2cebfa8ca60
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253655"
---
# <a name="private-network-access-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器的专用网络访问（预览版）

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

本文介绍适用于 Azure MySQL 灵活服务器的专用连接选项。 你需要详细了解 Azure Database for MySQL 灵活服务器的虚拟网络概念，以便在 Azure 中安全地创建服务器。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器为预览版。

## <a name="private-access-vnet-integration"></a>专用访问（VNet 集成）

[Azure 虚拟网络 (VNet)](../../virtual-network/virtual-networks-overview.md) 是 Azure 中专用网络的基础构建基块。 虚拟网络 (VNet) 与 Azure Database for MySQL 灵活服务器集成，使 Azure 具有网络安全和隔离方面的优势。  

虚拟网络 (VNet) 与 Azure Database for MySQL 灵活服务器集成，使你能够将对服务器的访问锁定为只访问你的虚拟网络基础结构。 虚拟网络 (VNet) 可以将所有应用程序和数据库资源包含在单个虚拟网络中，也可以跨越同一区域或不同区域的不同 Vnet。 各种虚拟网络之间的无缝连接可以通过[对等互连](../../virtual-network/virtual-network-peering-overview.md)建立，它使用 Microsoft 的低延迟、高带宽私有主干基础结构。 出于连接目的，两个虚拟网络会显示为一个。

Azure Database for MySQL 灵活服务器支持来自下方的客户端连接：

* 同一 Azure 区域内的虚拟网络。 （本地对等互连 VNet）
* 跨 Azure 区域的虚拟网络。 （全局对等互连 VNet）

使用子网可将虚拟网络划分为一个或多个子网络，并分配一部分虚拟网络的地址空间，然后可以将 Azure 资源部署到这些地址空间。 Azure Database for MySQL 灵活服务器需要[委托子网](../../virtual-network/subnet-delegation-overview.md)。 委托子网是一个显式标识符，子网只能承载 Azure Database for MySQL 灵活服务器。 委托子网后，服务将获取显式权限，以便在子网中创建服务特定的资源，以便无缝管理 Azure Database for MySQL 灵活服务器。

Azure Database for MySQL 灵活服务器与 Azure [专用 DNS 区域](../../dns/private-dns-privatednszone.md)集成，提供可靠、安全的 DNS 服务来管理和解析虚拟网络中的域名，无需添加自定义 DNS 解决方案。 通过创建[虚拟网络链接](../../dns/private-dns-virtual-network-links.md)，可以将专用 DNS 区域链接到一个或多个虚拟网络 


:::image type="content" source="./media/concepts-networking/vnet-diagram.png" alt-text="灵活服务器 MySQL VNET":::

在上图中，

1. 灵活服务器被注入到委派子网 - VNET VNet-1 的 10.0.1.0/24。
2. 部署在同一 VNet 内不同子网上的应用程序可直接访问灵活服务器。
3. 部署在不同 VNET VNet-2 上的应用程序不能直接访问灵活服务器。 必须先执行[专用 DNS 区域 VNET 对等互连](#private-dns-zone-and-vnet-peering)，然后它们才能访问灵活服务器。

## <a name="virtual-network-concepts"></a>虚拟网络概念

以下是在结合使用虚拟网络与 MySQL 灵活服务器时需要熟悉的一些概念。

* **虚拟网络** -

   Azure 虚拟网络 (VNet) 包含配置为供你使用的专用 IP 地址空间。 若要了解有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../../virtual-network/virtual-networks-overview.md)。

    虚拟网络必须处于灵活服务器所在的 Azure 区域。

* **委派子网** -

   一个虚拟网络包含多个子网（子网络）。 可以通过子网将虚拟网络分成较小的地址空间。 Azure 资源被部署到虚拟网络的特定子网中。

   MySQL 灵活服务器必须位于委托为仅供 MySQL 灵活服务器使用的子网中。 该委派意味着只有 Azure Database for MySQL 灵活服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。 通过将子网的委托属性指定为 Microsoft.DBforMySQL/flexibleServers 来委托子网。

* **网络安全组 (NSG)**

   通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 有关详细信息，请参阅[网络安全组概述](../../virtual-network/network-security-groups-overview.md)。

* **专用 DNS 区域集成** -

   使用 Azure 专用 DNS 区域集成，可以解析当前 VNET 中或链接专用 DNS 区域的任何区域内对等互连 VNET 中的专用 DNS。

* **虚拟网络对等**

   使用虚拟网络对等互连可以无缝连接 Azure 中的两个或更多个虚拟网络。 出于连接目的，对等互连虚拟网络会显示为一个网络。 对等互连虚拟网络中虚拟机之间的流量使用 Microsoft 主干基础结构。 对等互连 VNet 中客户端应用程序和灵活服务器之间的流量仅通过 Microsoft 的专用网络进行路由，并且仅隔离到该网络。

## <a name="using-private-dns-zone"></a>使用专用 DNS 区域

* 如果使用 Azure 门户或 Azure CLI 通过 VNET 创建灵活服务器，则会使用提供的服务器名称为订阅中的每个服务器自动预配一个新的专用 DNS 区域。 或者，如果你想要设置自己的专用 DNS 区域用于灵活服务器，请参阅[专用 DNS 概述](../../dns/private-dns-overview.md)文档。
* 如果使用 Azure API、Azure 资源管理器模板（ARM 模板）或 Terraform，请创建以 `mysql.database.azure.com` 结尾的专用 DNS 区域，并在为灵活服务器配置私密访问时使用这些区域。 有关详细信息，请参阅[专用 DNS 区域概述](../../dns/private-dns-overview.md)。

   > [!IMPORTANT]
   > 专用 DNS 区域名称必须以 `mysql.database.azure.com` 结尾。

了解如何在 [Azure 门户](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 中创建具有专用访问（VNet 集成）的灵活服务器。

## <a name="integration-with-custom-dns-server"></a>与自定义 DNS 服务器集成

如果使用自定义 DNS 服务器，则必须使用 DNS 转发器解析 Azure Database for MySQL 灵活服务器的 FQDN。 转发器 IP 地址应为 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。 自定义 DNS 服务器应位于 VNet 内，或者可通过 VNET 的 DNS 服务器设置进行访问。 有关详细信息，请参阅[使用你自己的 DNS 服务器的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

## <a name="private-dns-zone-and-vnet-peering"></a>专用 DNS 区域和 VNET 对等互连

专用 DNS 区域设置和 VNET 对等互连彼此独立。 有关创建和使用专用 DNS 区域的详细信息，请参阅前面的[使用专用 DNS 区域](concepts-networking-vnet.md#using-private-dns-zone)部分。

如果要从在另一个 VNET（位于相同或不同的区域）中预配的客户端连接到灵活服务器，必须将专用 DNS 区域链接到 VNET。 请参阅[如何链接虚拟网络](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)文档。

> [!NOTE]
> 只能链接以 `mysql.database.azure.com` 结尾的专用 DNS 区域名称。

## <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>使用 ExpressRoute 或 VPN 从本地连接到虚拟网络中的灵活服务器

对于需要从本地网络访问虚拟网络中的灵活服务器的工作负载，你将需要 [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) 或 [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) 以及[连接到本地](/azure/architecture/reference-architectures/hybrid-networking/)的虚拟网络。 此设置准备就绪后，如果要从本地虚拟网络上运行的客户端应用程序（例如 MySQL Workbench）进行连接，则需要使用 DNS 转发器来解析灵活服务器的服务器名称。 此 DNS 转发器负责通过服务器级转发器将所有 DNS 查询解析为 Azure 提供的 DNS 服务 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

若要正确进行配置，需要以下资源：

* 本地网络
* 预配了专用访问的 MySQL 灵活服务器（VNet 集成）
* [连接到本地](/azure/architecture/reference-architectures/hybrid-networking/)的虚拟网络
* 使用 Azure 中部署的 DNS 转发器 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)

然后，可以使用灵活服务器的服务器名称 (FQDN) 从对等互连虚拟网络或本地网络中的客户端应用程序连接到灵活服务器。

## <a name="unsupported-virtual-network-scenarios"></a>不受支持的虚拟网络场景

* 公共终结点（或公共 IP 或 DNS）- 部署到虚拟网络的灵活服务器不能有公共终结点
* 将灵活服务器部署到虚拟网络和子网后，不能将它移动到其他虚拟网络或子网。 无法将虚拟网络移动到其他资源组或订阅中。
* 一旦子网中存在资源，子网大小（地址空间）便不能增加
* 灵活服务器不支持专用链接。 它使用 VNet 注入来使灵活服务器可在 VNet 中使用。

> [!NOTE]
> 如果使用自定义 DNS 服务器，则必须使用 DNS 转发器解析 Azure Database for MySQL 灵活服务器的 FQDN。 有关详细信息，请参阅[使用你自己的 DNS 服务器的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure 门户](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 启用专用访问（VNet 集成）
* 了解如何[使用 TLS](how-to-connect-tls-ssl.md)
