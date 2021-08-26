---
title: 网络概述 - Azure Database for PostgreSQL 灵活服务器
description: 了解 Azure Database for PostgreSQL 的灵活服务器部署选项中的连接和网络选项。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2021
ms.openlocfilehash: 3d35eed46082d162afed5a2c9685265812b1e2d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745110"
---
# <a name="networking-overview-for-azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL 灵活服务器（预览版）网络概述

本文介绍 Azure Database for PostgreSQL 灵活服务器的连接和网络概念。 “灵活服务器”部署选项处于预览阶段。

创建 Azure Database for PostgreSQL 灵活服务器实例（灵活服务器）时，必须选择以下网络选项之一：“专用访问(VNet 集成)”或“公共访问(允许的 IP 地址)” 。 

> [!NOTE]
> 创建服务器后，将无法更改网络选项。 

无论你是选择使用专用访问还是公共访问选项，以下特征都适用：

* 来自允许的 IP 地址的连接需要使用有效的凭据对 PostgreSQL 服务器进行身份验证。
* 对网络流量强制实施[连接加密](#tls-and-ssl)。
* 服务器具有完全限定的域名 (FQDN)。 对于连接字符串中的 `hostname` 属性，建议使用 FQDN 而不是 IP 地址。
* 这两个选项都在服务器级别控制访问，而不是在数据库或表级别控制访问。 你将使用 PostgreSQL 的角色属性来控制数据库、表和其他对象访问。

> [!NOTE]
> 由于 Azure Database for PostgreSQL 是托管数据库服务，因此，不会为用户提供查看或修改配置文件（例如 `pg_hba.conf`）的主机或 OS 访问权限。 文件内容会根据网络设置自动更新。

## <a name="private-access-vnet-integration"></a>专用访问（VNet 集成）

可以将灵活服务器部署到 [Azure 虚拟网络 (VNet)](../../virtual-network/virtual-networks-overview.md) 中。 Azure 虚拟网络提供专用的安全网络通信。 虚拟网络中的资源可以通过在此网络中分配的专用 IP 地址进行通信。

如果需要以下功能，请选择此网络选项：

* 使用专用 IP 地址从同一虚拟网络中的 Azure 资源连接到灵活服务器。
* 使用 VPN 或 Azure ExpressRoute 从非 Azure 资源连接到灵活服务器。
* 确保灵活服务器没有可以通过 Internet 访问的公共终结点。

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="显示虚拟网络之间对等互连工作原理的关系图，其中一个虚拟网络包含灵活服务器。":::

在上图中：
- 灵活服务器注入到 VNet-1 虚拟网络的子网 10.0.1.0/24 中。
- 部署在同一虚拟网络中不同子网上的应用程序可以直接访问灵活服务器。
- 部署在不同虚拟网络 (VNet-2) 上的应用程序不能直接访问灵活服务器。 必须先执行[专用 DNS 区域的虚拟网络对等互连](#private-dns-zone-and-virtual-network-peering)，然后它们才能访问灵活服务器。
   
### <a name="virtual-network-concepts"></a>虚拟网络概念

虚拟网络包含配置为供你使用的专用 IP 地址空间。 虚拟网络必须处于灵活服务器所在的 Azure 区域。 若要了解有关虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../../virtual-network/virtual-networks-overview.md)。

以下是在结合使用虚拟网络与 PostgreSQL 灵活服务器时需要熟悉的概念：

* 委派子网。 虚拟网络包含子网。 可以通过子网将虚拟网络分成较小的地址空间。 Azure 资源被部署到虚拟网络中的特定子网中。 

  灵活服务器必须位于委派的子网中。 也就是说，只有 Azure Database for PostgreSQL 灵活服务器实例才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。 通过将其委派属性分配为 `Microsoft.DBforPostgreSQL/flexibleServers` 来委派子网。

  > [!IMPORTANT]
  > 名称 `AzureFirewallSubnet`、`AzureFirewallManagementSubnet`、`AzureBastionSubnet` 和 `GatewaySubnet` 都保留在 Azure 中。 请勿将其中任何一个用作子网名称。

* **网络安全组 (NSG)** 。 通过 NSG 中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 有关详细信息，请参阅 [NSG 概述](../../virtual-network/network-security-groups-overview.md)部分。

  应用程序安全组 (ASG) 通过将 NSG 用于平面网络，能够轻松控制第 4 层安全性。 可以快速执行以下操作：
  
  - 将虚拟机加入 ASG，或者从 ASG 中删除虚拟机。
  - 动态地将规则应用于这些虚拟机，或者从这些虚拟机中删除规则。 
  
  有关详细信息，请参阅 [ASG 概述](../../virtual-network/application-security-groups.md)。 
  
  目前，不支持以下情况中的 NSG：ASG 是 Azure Database for PostgreSQL 灵活服务器规则的一部分。 目前建议在 NSG 中使用[基于 IP 的源或目标筛选](../../virtual-network/network-security-groups-overview.md#security-rules)。 

  > [!IMPORTANT]
  > Azure Database for PostgreSQL 灵活服务器的特征要求能够将出站流量发送到目标端口 5432、6432。 如果创建网络安全组 (NSG) 来拒绝来自 Azure Database for PostgreSQL 灵活服务器的出站流量，请确保允许将流量发送到这些目标端口。 

* 专用 DNS 区域集成。 使用 Azure 专用 DNS 区域集成，可以解析当前虚拟网络中或链接专用 DNS 区域的任何区域内对等互连虚拟网络中的专用 DNS。 

### <a name="using-a-private-dns-zone"></a>使用专用 DNS 区域

如果使用 Azure 门户或 Azure CLI 通过虚拟网络创建灵活服务器，系统会使用提供的服务器名称为订阅中的每个服务器自动预配一个新的专用 DNS 区域。 

如果使用 Azure API、Azure 资源管理器模板（ARM 模板）或 Terraform，请创建以 `postgres.database.azure.com` 结尾的专用 DNS 区域。 在配置具有专用访问权限的灵活服务器时使用这些区域。 有关详细信息，请参阅[专用 DNS 区域概述](../../dns/private-dns-overview.md)。


### <a name="integration-with-a-custom-dns-server"></a>与自定义 DNS 服务器集成

如果使用自定义 DNS 服务器，则必须使用 DNS 转发器来解析 Azure Database for PostgreSQL 灵活服务器的 FQDN。 转发器 IP 地址应为 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)。 

自定义 DNS 服务器应位于虚拟网络内，或可通过虚拟网络的 DNS 服务器设置访问。 有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

### <a name="private-dns-zone-and-virtual-network-peering"></a>专用 DNS区域和虚拟网络对等互连

专用 DNS 区域设置和虚拟网络对等互连彼此独立。 如果要从在另一个虚拟网络（位于相同或不同的区域）中预配的客户端连接到灵活服务器，必须将专用 DNS 区域链接到虚拟网络。 有关详细信息，请参阅[链接虚拟网络](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)。

> [!NOTE]
> 只能链接以 `postgres.database.azure.com` 结尾的专用 DNS 区域名称。

### <a name="unsupported-virtual-network-scenarios"></a>不受支持的虚拟网络场景

下面是使用虚拟网络的一些限制：

* 部署到虚拟网络的灵活服务器不能具有公共终结点（或公共 IP 或 DNS）。
* 将灵活服务器部署到虚拟网络和子网后，不能将它移动到其他虚拟网络或子网。 不能将虚拟网络移动到其他资源组或订阅中。
* 子网中存在资源后，不能增加子网的大小（地址空间）。
* 灵活服务器不支持 Azure 专用链接。 相反，它使用虚拟网络注入来使灵活服务器在虚拟网络中可用。 


## <a name="public-access-allowed-ip-addresses"></a>公共访问（允许的 IP 地址）

选择公共访问方法时，可以通过 Internet 上的公共终结点访问灵活服务器。 公共终结点是可公开解析的 DNS 地址。 “允许的 IP 地址”是指选择向其授予服务器访问权限的一系列 IP。 这些权限称为“防火墙规则”。 

如果需要以下功能，请选择此网络选项：

* 从不支持虚拟网络的 Azure 资源进行连接。
* 从 Azure 外部未通过 VPN 或 ExpressRoute 连接的资源进行连接。
* 确保灵活服务器具有可以通过 Internet 访问的公共终结点。

公共访问方法的特征包括：

* 只有允许的 IP 地址才有权访问 PostgreSQL 灵活服务器。 默认情况下，不允许任何 IP 地址。 可以在创建服务器期间或之后添加 IP 地址。
* PostgreSQL 服务器具有可公开解析的 DNS 名称。
* 灵活服务器不在 Azure 虚拟网络之一中。
* 服务器之间的网络流量不会通过专用网络传输。 该流量使用一般的 Internet 路径。

### <a name="firewall-rules"></a>防火墙规则

如果连接尝试来自不允许通过防火墙规则的 IP 地址，则发起客户端将收到错误。

### <a name="allowing-all-azure-ip-addresses"></a>允许所有 Azure IP 地址

如果某个固定的传出 IP 地址不适用于 Azure 服务，可以考虑启用来自所有 Azure 数据中心 IP 地址的连接。

> [!IMPORTANT]
> “允许来自 Azure 服务和 Azure 中资源的公共访问”选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 如果选择此选项，请确保登录名和用户权限将访问权限限制为仅已授权用户。

### <a name="troubleshooting-public-access-issues"></a>排查公共访问问题
在对 Azure Database for PostgreSQL 服务的访问与预期不符时，请考虑以下几点：

* 对允许列表的更改尚未生效。 对 Azure Database for PostgreSQL 服务器的防火墙配置所做的更改可能最多需要 5 分钟的延迟才能生效。

* 身份验证失败。 如果用户对 Azure Database for PostgreSQL 服务器没有权限或者使用的密码不正确，则与 Azure Database for PostgreSQL 服务器连接会被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会。 每个客户端必须提供必需的安全凭据。

* 动态客户端 IP 地址阻止访问。 如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

   * 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机、用于访问 Azure Database for PostgreSQL 服务器的 IP 地址范围。 然后，将该 IP 地址范围作为防火墙规则添加。
   * 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。

* 防火墙规则不适用于 IPv6 格式。 防火墙规则必须为 IPv4 格式。 如果以 IPv6 格式指定防火墙规则，则会显示验证错误。

## <a name="host-name"></a>主机名

无论选择哪个网络选项，我们都建议你在连接到灵活服务器时始终使用 (FQDN) 作为主机名。 服务器的 IP 地址不能保证保持静态。 使用 FQDN 有助于避免更改连接字符串。 

使用 FQDN 作为主机名的示例是 `hostname = servername.postgres.database.azure.com`。 如果可能，请避免使用 `hostname = 10.0.0.4`（专用地址）或 `hostname = 40.2.45.67`（公共地址）。


## <a name="tls-and-ssl"></a>TLS 和 SSL

Azure Database for PostgreSQL 灵活服务器强制使用传输层安全性 (TLS) 将客户端应用程序连接到 PostgreSQL 服务。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现加密网络连接。 TLS 是安全套接字层 (SSL) 的更新协议。 

Azure Database for PostgreSQL 支持 TLS 1.2 和更高版本。 在 [RFC 8996](https://datatracker.ietf.org/doc/rfc8996/) 中，Internet 工程任务组 (IETF) 明确指出不得使用 TLS 1.0 和 TLS 1.1。 这两个协议在 2019 年底已弃用。

使用早期版本的 TLS 协议（例如 TLS 1.0 和 TLS 1.1）的所有传入连接都将被拒绝。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure 门户](how-to-manage-virtual-network-portal.md)或 [Azure CLI](how-to-manage-virtual-network-cli.md) 中的“专用访问(VNet 集成)”选项创建灵活服务器。
* 了解如何使用 [Azure 门户](how-to-manage-firewall-portal.md)或 [Azure CLI](how-to-manage-firewall-cli.md) 中的“公共访问(允许的 IP 地址)”选项创建灵活服务器。
