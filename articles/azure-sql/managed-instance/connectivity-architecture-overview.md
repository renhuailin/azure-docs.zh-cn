---
title: 连接体系结构
titleSuffix: Azure SQL Managed Instance
description: 了解 Azure SQL 托管实例的通信和连接体系结构，同时了解组件如何将流量定向到托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 04/29/2021
ms.openlocfilehash: 259bd0128a4c5ce677e4d01f44b114aaba0cb977
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889147"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL 托管实例的连接体系结构
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍 Azure SQL 托管实例中的通信。 此外还将介绍连接体系结构，以及组件将流量定向到托管实例的方式。  

SQL 托管实例位于 Azure 虚拟网络和托管实例专用子网中。 此部署提供：

- 安全的专用 IP 地址。
- 将本地网络连接到 SQL 托管实例的能力。
- 将 SQL 托管实例连接到链接服务器或其他本地数据存储的能力。
- 将 SQL 托管实例连接到 Azure 资源的能力。

## <a name="communication-overview"></a>通信概述

下图显示了连接到 SQL 托管实例的实体。 它还显示了需要与托管实例通信的资源。 关系图底部的通信过程表示作为数据源连接到 SQL 托管实例的客户应用程序和工具。  

![连接体系结构中的实体](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL 托管实例是一种平台即服务 (PaaS) 产品。 Azure 使用自动化代理（管理、部署和维护）基于遥测数据流管理此服务。 由于管理工作由 Azure 负责，客户无法通过远程桌面协议 (RDP) 访问 SQL 托管实例的虚拟群集计算机。

某些由最终用户或应用程序发起的操作可能需要 SQL 托管实例来与平台进行交互。 一种情况是创建 SQL 托管实例数据库。 此资源是通过 Azure 门户、PowerShell、Azure CLI 和 REST API 公开的。

SQL 托管实例依赖于 Azure 服务，例如用于备份的 Azure 存储、用于遥测的 Azure 事件中心、用于身份验证的 Azure Active Directory (Azure AD)、用于透明数据加密 (TDE) 的 Azure Key Vault，以及提供安全性和可支持性功能的几项 Azure 平台服务。 SQL 托管实例与这些服务建立连接。

所有通信都使用证书进行加密和签名。 为了检查通信方的可信度，SQL 托管实例会通过证书吊销列表不断验证这些证书。 如果证书被吊销，SQL 托管实例将关闭连接以保护数据。

## <a name="high-level-connectivity-architecture"></a>高级连接体系结构

在较高级别，SQL 托管实例是一组服务组件。 这些组件托管在客户虚拟网络子网中运行的一组专用隔离虚拟机上。 这些计算机构成了虚拟群集。

一个虚拟群集可以承载多个托管实例。 当客户更改子网中预配的实例数时，群集可根据需要自动扩展或收缩。

客户应用程序可连接到 SQL 托管实例，还可查询和更新虚拟网络、对等虚拟网络或通过 VPN 或 Azure ExpressRoute 连接的网络中的数据库。 此网络必须使用一个终结点和一个专用 IP 地址。  

![连接体系结构示意图](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure 管理和部署服务在虚拟网络外部运行。 SQL 托管实例和 Azure 服务通过采用公共 IP 地址的终结点进行连接。 当 SQL 托管实例创建出站连接时，在接收端，网络地址转换 (NAT) 使该连接看起来像来自此公共 IP 地址的连接。

管理流量通过客户的虚拟网络传送。 这意味着，虚拟网络基础结构的要素可能会使实例发生故障并变得不可用，从而对管理流量造成不利影响。

> [!IMPORTANT]
> 为改善客户体验和服务可用性，Azure 会针对 Azure 虚拟网络基础结构要素应用网络意向策略。 该策略可影响 SQL 托管实例的工作方式。 此平台机制以透明方式向用户传达网络要求。 该策略的主要目的是防止网络配置不当，并确保 SQL 托管实例正常运行。 删除某个托管实例时，会一并删除网络意向策略。

## <a name="virtual-cluster-connectivity-architecture"></a>虚拟群集连接体系结构

让我们更深入地了解 SQL 托管实例的连接体系结构。 以下关系图演示了虚拟群集的概念布局。

![虚拟群集的连接体系结构](./media/connectivity-architecture-overview/connectivityarch003.png)

客户端使用 `<mi_name>.<dns_zone>.database.windows.net` 格式的主机名连接到 SQL 托管实例。 此主机名将解析为专用 IP 地址，不过它将在公共域名系统 (DNS) 区域中注册，且可公开解析。 `zone-id` 是创建群集时自动生成的。 如果新创建的群集托管辅助托管实例，它会将其区域 ID 与主群集共享。 有关详细信息，请参阅[使用自动故障转移组可以实现多个数据库的透明、协调式故障转移](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)。

此专用 IP 地址属于 SQL 托管实例的内部负载均衡器。 该负载均衡器将流量定向到 SQL 托管实例网关。 由于多个托管实例可在同一群集中运行，因此网关使用 SQL 托管实例主机名来将流量重新定向到正确的 SQL 引擎服务。

管理和部署服务使用映射到外部负载均衡器的[管理终结点](#management-endpoint)连接到 SQL 托管实例。 只有在仅由 SQL 托管实例的管理组件使用的预定义端口上接收到流量时，才会将流量路由到节点。 节点上的内置防火墙设置为只允许来自 Microsoft IP 范围的流量。 证书将对管理组件与管理平面之间的所有通信进行相互身份验证。

## <a name="management-endpoint"></a>管理终结点

Azure 使用一个管理终结点来管理 SQL 托管实例。 此终结点位于该实例的虚拟群集内部。 管理终结点在网络级别受到内置防火墙的保护。 在应用程序级别，管理终结点受到证书相互验证的保护。 若要查找终结点的 IP 地址，请参阅[确定管理终结点的 IP 地址](management-endpoint-find-ip-address.md)。

在 SQL 托管实例内开始连接时（与备份和审核日志一样），流量似乎从管理终结点的公共 IP 地址开始。 可通过将防火墙规则设置为只允许 SQL 托管实例的 IP 地址，来限制从 SQL 托管实例访问公共服务。 有关详细信息，请参阅[验证 SQL 托管实例的内置防火墙](management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 发往 SQL 托管实例所在区域中的 Azure 服务的流量经过优化，因此不会通过 NAT 转换为管理终结点公共 IP 地址。 因此，如果需要使用基于 IP 的防火墙规则（往往用于存储），服务需要与 SQL 托管实例位于不同的区域中。

## <a name="service-aided-subnet-configuration"></a>服务辅助的子网配置

为了满足客户的安全性和管理性要求，SQL 托管实例即将从手动配置转换为服务辅助的子网配置。

使用服务辅助的子网配置时，客户可完全控制数据 (TDS) 流量，而 SQL 托管实例控制平面将负责确保管理流量不间断流动，以满足 SLA 的规定。

服务辅助子网配置以虚拟网络[子网委派](../../virtual-network/subnet-delegation-overview.md)功能为基础，可提供自动网络配置管理和启用服务终结点。 

服务终结点可用于对保存备份和审核日志的存储帐户配置虚拟网络防火墙规则。 即使启用了服务终结点，也建议客户使用[专用链接](../../private-link/private-link-overview.md)，该链接会为服务终结点提供额外的安全性。

> [!IMPORTANT]
> 由于控制平面配置的特殊性，服务辅助子网配置将无法在国家云中启用服务终结点。 

### <a name="network-requirements"></a>网络要求

在虚拟网络中的专用子网内部署 SQL 托管实例。 该子网必须具有以下特征：

- **专用子网：** 托管实例的子网不能包含与它关联的任何其他云服务，但是允许其他托管实例，并且不能是网关子网。 该子网不能包含除该托管实例以外的其他任何资源，以后无法在该子网中添加其他类型的资源。
- **子网委派**：需要将 SQL 托管实例的子网委托给 `Microsoft.Sql/managedInstances` 资源提供程序。
- **网络安全组 (NSG)** ：NSG 需与 SQL 托管实例的子网相关联。 当 SQL 托管实例配置为使用重定向连接时，可使用某个 NSG 通过筛选端口 1433 和端口 11000-11999 上的流量，来控制对 SQL 托管实例数据终结点的访问。 该服务会自动预配并保留当前的[规则](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，使管理流量能够不间断地流动。
- **用户定义的路由 (UDR) 表：** UDR 表需与 SQL 托管实例的子网相关联。 可将条目添加到路由表，以通过虚拟网络网关或虚拟网络设备 (NVA) 路由发往本地专用 IP 范围的流量。 服务会自动预配并保留当前的[条目](#mandatory-user-defined-routes-with-service-aided-subnet-configuration)，使管理流量能够不间断地流动。
- **足够的 IP 地址：** SQL 托管实例子网必须至少有 32 个 IP 地址。 有关详细信息，请参阅[确定 SQL 托管实例的子网大小](vnet-subnet-determine-size.md)。 根据 [SQL 托管实例的网络要求](#network-requirements)配置托管实例后，可将其部署在[现有网络](vnet-existing-add-subnet.md)中。 否则，请创建[新的网络和子网](virtual-network-subnet-create-arm-template.md)。

> [!IMPORTANT]
> 创建托管实例时，将会针对子网应用网络意向策略，以防止对网络设置进行不合规的更改。 从子网中删除最后一个实例后，网络意向策略也会一并删除。 下面的规则仅供参考，不应使用 ARM 模板/PowerShell/CLI 部署它们。 如果要使用最新的官方模板，你始终可以[从门户中检索它](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>采用服务辅助子网配置的必需入站安全规则
需要这些规则来确保入站管理流量流。 有关连接体系结构和管理流量的详细信息，请参阅[上面的段落](#high-level-connectivity-architecture)。

| 名称       |端口                        |协议|Source           |目标|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |SqlManagement    |MI SUBNET  |允许 |
|            |9000、9003                  |TCP     |CorpnetSaw       |MI SUBNET  |允许 |
|            |9000、9003                  |TCP     |CorpnetPublic    |MI SUBNET  |允许 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |MI SUBNET  |允许 |
|health_probe|任意                         |任意     |AzureLoadBalancer|MI SUBNET  |允许 |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>采用服务辅助子网配置的必需出站安全规则
需要这些规则来确保出站管理流量流。 有关连接体系结构和管理流量的详细信息，请参阅[上面的段落](#high-level-connectivity-architecture)。

| 名称       |端口          |协议|Source           |目标|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |443、12000    |TCP     |MI SUBNET        |AzureCloud |允许 |
|mi_subnet   |任意           |任意     |MI SUBNET        |MI SUBNET  |允许 |

### <a name="mandatory-user-defined-routes-with-service-aided-subnet-configuration"></a>采用服务辅助子网配置的必需用户定义路由
需要这些路由来确保管理流量直接路由到目标。 有关连接体系结构和管理流量的详细信息，请参阅[上面的段落](#high-level-connectivity-architecture)。

|名称|地址前缀|下一跃点|
|----|--------------|-------|
|subnet-to-vnetlocal|MI SUBNET|虚拟网络|
|mi-azurecloud-REGION-internet|AzureCloud.REGION|Internet|
|mi-azurecloud-REGION_PAIR-internet|AzureCloud.REGION_PAIR|Internet|
|mi-azuremonitor-internet|AzureMonitor|Internet|
|mi-corpnetpublic-internet|CorpNetPublic|Internet|
|mi-corpnetsaw-internet|CorpNetSaw|Internet|
|mi-eventhub-REGION-internet|EventHub.REGION|Internet|
|mi-eventhub-REGION_PAIR-internet|EventHub.REGION_PAIR|Internet|
|mi-sqlmanagement-internet|SqlManagement|Internet|
|mi-storage-internet|存储|Internet|
|mi-storage-REGION-internet|Storage.REGION|Internet|
|mi-storage-REGION_PAIR-internet|Storage.REGION_PAIR|Internet|
|mi-azureactivedirectory-internet|AzureActiveDirectory|Internet|
||||

\* MI SUBNET 是指子网的 IP 地址范围，采用 x.x.x.x/y 格式。 可以在 Azure 门户的“子网属性”中找到此信息。

\** 如果目标地址是用于某个 Azure 服务的，Azure 会将流量通过 Azure 的主干网络直接路由到该服务，而不是将流量路由到 Internet。 Azure 服务之间的流量不跨越 Internet，不管虚拟网络存在于哪个 Azure 区域，也不管 Azure 服务的实例部署在哪个 Azure 区域。 有关更多详细信息，请查看 [UDR 文档页](../../virtual-network/virtual-networks-udr-overview.md)。

此外，还可以将条目添加到路由表，以通过虚拟网络网关或虚拟网络设备 (NVA) 路由发往本地专用 IP 范围的流量。

如果虚拟网络包含自定义 DNS，则自定义 DNS 服务器必须能够解析公共 DNS 记录。 使用其他功能（例如 Azure AD 身份验证）可能需要解析其他 FQDN。 有关详细信息，请参阅[设置自定义 DNS](custom-dns-configure.md)。

### <a name="networking-constraints"></a>网络约束

**出站连接上会强制实施 TLS 1.2**：2020 年 1 月，Microsoft 对所有 Azure 服务中的服务内流量强制实施了 TLS 1.2。 对于 Azure SQL 托管实例，这导致在用于复制的出站连接上和到 SQL Server 的链接服务器连接上强制实施了 TLS 1.2。 如果对 SQL 托管实例使用低于 2016 版的 SQL Server，请确保已应用[特定于 TLS 1.2 的更新](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)。

SQL 托管实例当前不支持以下虚拟网络功能：

- **Microsoft 对等互连**：如果在与 SQL 托管实例所在的虚拟网络直接或暂时对等互连的 ExpressRoute 线路上启用 [Microsoft 对等互连](../../expressroute/expressroute-faqs.md#microsoft-peering)，会影响虚拟网络内的 SQL 托管实例组件与它依赖的服务之间的流量，从而导致可用性问题。 向已启用 Microsoft 对等互连的虚拟网络部署 SQL 托管实例预计会失败。
- **全局虚拟网络对等互连**：跨 Azure 区域的 [虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)连接不适用于在 2020 年 9 月 22 日之前创建的子网中放置的 SQL 托管实例。
- **AzurePlatformDNS**：使用 AzurePlatformDNS [服务标记](../../virtual-network/service-tags-overview.md)阻止平台 DNS 解析会导致 SQL 托管实例不可用。 尽管 SQL 托管实例支持将客户定义的 DNS 用于引擎内的 DNS 解析，但平台操作依赖于平台 DNS。
- **NAT 网关**：使用 [Azure 虚拟网络 NAT](../../virtual-network/nat-overview.md) 控制具有特定公共 IP 地址的出站连接会导致 SQL 托管实例不可用。 SQL 托管实例服务当前仅限于使用基本负载均衡器，该均衡器不提供入站和出站流与虚拟网络 NAT 共存的功能。
- Azure 虚拟网络的 IPv6：将 SQL 托管实例部署到[双堆栈 IPv4/IPv6 虚拟网络](../../virtual-network/ipv6-overview.md)预计会失败。 如果将包含 IPv6 地址前缀的网络安全组 (NSG) 或路由表 (UDR) 关联到 SQL 托管实例子网，或将 IPv6 地址前缀添加到已与托管实例子网关联的 NSG 或 UDR，则会导致 SQL 托管实例不可用。 将 SQL 托管实例部署到使用 NSG 和 UDR 且已具有 IPv6 前缀的子网预计会失败。
- 为 Microsoft 服务保留名称的 Azure DNS 专用区域：以下是保留名称的列表： windows.net、database.windows.net、core.windows.net、blob.core.windows.net、table.core.windows.net、management.core.windows.net、monitoring.core.windows.net、queue.core.windows.net、graph.windows.net、login.microsoftonline.com、login.windows.net、servicebus.windows.net、vault.azure.net。 将 SQL 托管实例部署到具有关联的 [Azure DNS 专用区域](../../dns/private-dns-privatednszone.md)（其名称保留用于 Microsoft 服务）的虚拟网络将失败。 将具有保留名称的 Azure DNS 专用区域与包含托管实例的虚拟网络相关联会导致 SQL 托管实例不可用。 请参照 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)，获取正确的专用链接配置。
- **Azure 存储的服务终结点策略**：将 SQL 托管实例部署到具有关联 [服务终结点策略](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)的子网将失败。 服务终结点策略无法关联到托管托管实例的子网。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 了解如何设置可用于部署 SQL 托管实例的[新的 Azure 虚拟网络](virtual-network-subnet-create-arm-template.md)或[现有 Azure 虚拟网络](vnet-existing-add-subnet.md)。
- 在要部署 SQL 托管实例的位置[计算子网的大小](vnet-subnet-determine-size.md)。
- 了解如何通过以下方式创建托管实例：
  - 通过 [Azure 门户](instance-create-quickstart.md)。
  - 使用 [PowerShell](scripts/create-configure-managed-instance-powershell.md)。
  - 使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet/)。
  - 使用 [Azure 资源管理器模板（使用包含 SSMS 的 JumpBox）](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet-w-jumpbox/)。
