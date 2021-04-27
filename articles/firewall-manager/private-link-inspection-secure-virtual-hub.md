---
title: 保护发往 Azure 虚拟 WAN 中专用终结点的流量
description: 了解如何使用网络规则和应用程序规则来保护发往 Azure 虚拟 WAN 中专用终结点的流量
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259347"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>保护发往 Azure 虚拟 WAN 中专用终结点的流量

> [!NOTE]
> 本文仅适用于安全虚拟中心。 如果要在中心虚拟网络中使用 Azure 防火墙检查流向专用终结点的流量，请参阅[使用 Azure 防火墙检查流向专用终结点的流量](../private-link/inspect-traffic-with-azure-firewall.md)。

[Azure 专用终结点](../private-link/private-endpoint-overview.md)是 [Azure 专用链接](../private-link/private-link-overview.md)的构建基块。 在虚拟网络中部署的 Azure 资源可以通过专用终结点来与专用链接资源进行私密通信。

专用终结点允许资源访问虚拟网络中部署的专用链接服务。 通过虚拟网络对等互连和本地网络连接对专用终结点进行访问扩展了连接性。

你可能需要筛选从本地客户端或 Azure 中的客户端发往通过与虚拟 WAN 连接的虚拟网络中的专用终结点公开的服务的流量。 本文逐步讲解如何将[安全虚拟中心](../firewall-manager/secured-virtual-hub.md)与充当安全提供程序的 [Azure 防火墙](../firewall/overview.md)配合使用来完成此任务。

Azure 防火墙使用以下任何方法筛选流量：

* [网络规则中的 FQDN](../firewall/fqdn-filtering-network-rules.md)，适用于 TCP 和 UDP 协议
* [应用程序规则中的 FQDN](../firewall/features.md#application-fqdn-filtering-rules)，适用于 HTTP、HTTPS 和 MSSQL。
* 使用[网络规则](../firewall/features.md#network-traffic-filtering-rules)的源和目标 IP 地址、端口与协议

使用应用程序规则而不是网络规则来检查发往专用终结点的流量。
安全虚拟中心由 Microsoft 管理，无法链接到[专用 DNS 区域](../dns/private-dns-privatednszone.md)。 需要建立这种链接才能将[专用链接资源](../private-link/private-endpoint-overview.md#private-link-resource) FQDN 解析为其相应的专用终结点 IP 地址。

仅在[代理模式](../azure-sql/database/connectivity-architecture.md#connection-policy)下支持 SQL FQDN 筛选（端口 1433）。 与重定向相比，代理模式可能会导致更高的延迟 。 如果你要继续使用重定向模式（这是在 Azure 中进行连接的客户端的默认模式），可以使用防火墙网络规则中的 FQDN 来筛选访问。

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>使用网络和应用程序规则中的 FQDN 筛选流量

以下步骤可让 Azure 防火墙使用网络和应用程序规则中的 FQDN 来筛选流量：

1. 在已连接到安全虚拟中心并已链接到托管专用终结点 A 记录类型的专用 DNS 区域的虚拟网络中，部署一个 [DNS 转发器](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)虚拟机。

2. 将[自定义 DNS 设置](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal)配置为指向 DNS 转发器虚拟机 IP 地址，并在与安全虚拟中心内部署的 Azure 防火墙关联的防火墙策略中启用 DNS 代理。

3. 将已连接到安全虚拟中心的虚拟网络的[自定义 DNS 服务器](../virtual-network/manage-virtual-network.md#change-dns-servers)配置为指向与安全虚拟中心内部署的 Azure 防火墙关联的专用 IP 地址。

4. 将本地 DNS 服务器配置为向与安全虚拟中心内部署的 Azure 防火墙关联的专用 IP 地址转发针对专用终结点公共 DNS 区域的 DNS 查询。

5. 在与安全虚拟中心内部署的 Azure 防火墙关联的防火墙策略中，根据需要配置一个[应用程序规则](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)或[网络规则](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)，其中包含“目标类型”FQDN，以及充当“目标”的专用链接资源公共 FQDN 。

6. 在与安全虚拟中心内部署的 Azure 防火墙关联的防火墙策略中导航到“安全虚拟中心”，然后选择要在其中配置筛选发往专用终结点的流量的安全虚拟中心。

7. 导航到“安全配置”，选择“专用流量”下的“通过 Azure 防火墙发送”  。

8. 选择“专用流量前缀”以编辑要通过安全虚拟中心内的 Azure 防火墙检查的 CIDR 前缀，并为每个专用终结点添加一个 /32 前缀，如下所示：

   > [!IMPORTANT]
   > 如果未配置这些 /32 前缀，发往专用终结点的流量将绕过 Azure 防火墙。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="防火墙管理器安全配置" border="true":::

仅当客户端和专用终结点部署在已连接到同一安全虚拟中心的不同虚拟网络中时，这些步骤才适用，并且仅适用于本地客户端。 如果客户端和专用终结点部署在同一虚拟网络中，则必须为专用终结点创建一个包含 /32 路由的 UDR。 配置这些路由：将“下一跃点类型”设置为“虚拟设备”，将“下一跃点地址”设置为安全虚拟中心内部署的 Azure 防火墙的专用 IP 地址  。 “传播网关路由”必须设置为“是” 。

下图演示了要连接到 Azure 虚拟 WAN 中部署的专用终结点的不同客户端的 DNS 和数据流量流：

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="流量流" border="true":::

## <a name="troubleshooting"></a>故障排除

尝试通过安全虚拟中心筛选发往专用终结点的流量时可能遇到的主要问题包括：

- 客户端无法连接到专用终结点。

- Azure 防火墙被绕过。 如果 Azure 防火墙中缺少网络或应用程序规则日志条目，则可以证实此症状。

在大多数情况下，这些问题是由以下问题之一造成的：

- DNS 名称解析不正确

- 路由配置不正确

### <a name="incorrect-dns-name-resolution"></a>DNS 名称解析不正确

1. 验证虚拟网络 DNS 服务器是否已设置为“自定义”，以及 IP 地址是否为安全虚拟中心内 Azure 防火墙的专用 IP 地址。

   Azure CLI：

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. 通过直接查询配置为 DNS 转发器的虚拟机，验证 DNS 转发器虚拟机所在的虚拟网络中的客户端是否可将专用终结点公共 FQDN 解析为其相应的专用 IP 地址。

   Linux：

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. 检查 AzureFirewallDNSProxy Azure 防火墙日志条目，验证它是否可以从客户端接收和解析 DNS 查询。

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. 验证是否已启用 DNS 代理，以及是否在与安全虚拟中心内 Azure 防火墙关联的防火墙策略中，配置了指向 DNS 转发器虚拟机 IP 地址的自定义 DNS 服务器 。

   Azure CLI：

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>路由配置不正确

1. 验证与安全虚拟中心内部署的 Azure 防火墙关联的防火墙策略中的安全配置。 确保在“专用流量”列下，此配置对你要筛选其流量的所有虚拟网络和分支连接显示为“受 Azure 防火墙保护” 。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="专用流量 - 受 Azure 防火墙保护" border="true":::

2. 验证与安全虚拟中心内部署的 Azure 防火墙关联的防火墙策略中的安全配置。 确保在“专用流量前缀”下，你要筛选其流量的每个专用终结点专用 IP 地址都有一个 /32 条目。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="防火墙管理器安全配置 - 专用流量前缀" border="true":::

3. 在虚拟 WAN 下的安全虚拟中心内，检查与你要筛选其流量的虚拟网络和分支连接关联的路由表的有效路由。 确保你要筛选其流量的每个专用终结点专用 IP 地址都有对应的 /32 条目。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="安全虚拟中心 - 有效路由" border="true":::

4. 检查已附加到你要筛选其流量的虚拟网络中部署的虚拟机的 NIC 上的有效路由。 确保你要筛选其流量的每个专用终结点专用 IP 地址都有对应的 /32 条目。
 
   Azure CLI：

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. 检查本地路由设备的路由表。 确保了解专用终结点所部署到的虚拟网络的地址空间。

   Azure 虚拟 WAN 不会将防火墙策略安全配置中“专用流量前缀”下配置的前缀播发到本地 。 /32 条目应不会显示在本地路由设备的路由表中。

6. 检查 AzureFirewallApplicationRule 和 AzureFirewallNetworkRule Azure 防火墙日志 。 确保正在记录发往专用终结点的流量。

   AzureFirewallNetworkRule 日志条目不包含 FQDN 信息。 检查网络规则时请按 IP 地址和端口进行筛选。

   筛选发往 [Azure 文件存储](../storage/files/storage-files-introduction.md)专用终结点的流量时，只有在客户端首次装载或连接到文件共享的情况下，才会生成 AzureFirewallNetworkRule 日志条目。 Azure 防火墙不会为针对文件共享中的文件执行的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作生成日志。 这是因为，CRUD 操作是通过在客户端首次连接或装载到文件共享时打开的持久性 TCP 通道传送的。

   应用程序规则日志查询示例：

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>后续步骤

- [使用 Azure 防火墙检查流向专用终结点的流量](../private-link/inspect-traffic-with-azure-firewall.md)
