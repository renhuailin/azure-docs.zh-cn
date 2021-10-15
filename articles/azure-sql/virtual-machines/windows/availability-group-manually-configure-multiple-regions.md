---
title: 在不同的区域中配置 SQL Server Always On 可用性组
description: 本文介绍如何使用不同区域中的副本在 Azure 虚拟机上配置 SQL Server Always On 可用性组。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: dc516c9631eda2904ff311af6ca779872d9802f0
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537193"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>在不同的 Azure 区域中配置 SQL Server Always On 可用性组

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了如何在位于远程 Azure 位置的 Azure 虚拟机上配置 SQL Server Always On 可用性组副本。 使用此配置可为灾难恢复提供支持。

本文适用于处于 Resource Manager 模式的 Azure 虚拟机。

下图显示了 Azure 虚拟机上可用性组的常见部署：

   ![此图显示 Azure 负载均衡器和包含“Windows Server 故障转移群集”和“Always On 可用性组”的可用性集。](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

在此部署中，所有虚拟机位于一个 Azure 区域。 在 SQL-1 和 SQL-2 上，可以使用自动故障转移为可用性组副本配置同步提交。 若要构建此体系结构，请参阅[可用性组模板或教程](availability-group-overview.md)。

如果无法访问 Azure 区域，此体系结构很容易停机。 为了解决此漏洞，可在不同的 Azure 区域添加一个副本。 下图显示了新体系结构的大致形式：

   ![可用性组 DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

上图显示了名为 SQL-3 的新虚拟机。 SQL-3 位于不同的 Azure 区域中。 SQL-3 已添加到 Windows Server 故障转移群集。 SQL-3 可以托管可用性组副本。 最后，请注意，SQL-3 所在的 Azure 区域具有一个新的 Azure 负载均衡器。

>[!NOTE]
> 如果同一区域中有多个虚拟机，则需要创建 Azure 可用性集。 如果区域中只有一个虚拟机，则不需要可用性集。 只能在创建虚拟机时将虚拟机放入可用性集。 如果虚拟机已在可用性集中，以后可为附加的副本添加虚拟机。

在此体系结构中，远程区域中的副本通常配置了异步提交可用性模式和手动故障转移模式。

如果可用性组副本位于不同 Azure 区域中的 Azure 虚拟机上，则每个区域需要：

* 一个虚拟网络网关
* 一个虚拟网络网关连接

下图显示了数据中心之间的网络通信方式。

   ![此图显示不同 Azure 区域中的两个虚拟网络在使用 VPN 网关进行通信。](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>在 Azure 区域之间复制数据时，使用此体系结构需支付出站数据费用。 请参阅[带宽定价](https://azure.microsoft.com/pricing/details/bandwidth/)。  

## <a name="create-remote-replica"></a>创建远程副本

若要在远程数据中心创建副本，请执行以下步骤：

1. [在新区域中创建虚拟网络](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)。

1. [使用 Azure 门户配置 VNet 到 VNet 连接](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。

   >[!NOTE]
   >在某些情况下，可能需要使用 PowerShell 创建 VNet 到 VNet 连接。 例如，如果使用不同的 Azure 帐户，则无法在门户中配置该连接。 这种情况请参阅[使用 Azure 门户配置 VNet 到 VNet 连接](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

1. [在新区域中创建域控制器](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)。

   如果主站点中的域控制器不可用，此域控制器可提供身份验证。

1. [在新区域中创建 SQL Server 虚拟机](create-sql-vm-portal.md)。

1. [在新区域的网络中创建 Azure 负载均衡器](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)。

   此负载均衡器必须：

   - 与新虚拟机位于同一网络和子网中。
   - 可用性组侦听器具有静态 IP 地址。
   - 包括后端池，该池只由负载均衡器所在区域中的虚拟机构成。
   - 使用特定于 IP 地址的 TCP 端口探测。
   - 具有特定于同一区域中 SQL Server 的负载均衡规则。  
   - 如果后端池中的虚拟机不是单个可用性集或虚拟机规模集的一部分，则为标准负载均衡器。 有关其他信息，请查看 [Azure 负载均衡器标准概述](../../../load-balancer/load-balancer-overview.md)。
   - 如果两个不同区域的两个虚拟网络通过全局 VNet 对等互连进行对等互连，则为标准负载均衡器。 有关详细信息，请参阅 [Azure 虚拟网络常见问题解答 (FAQ)](../../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

1. [向新的 SQL Server 添加故障转移群集功能](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

1. [将新的 SQL Server 添加到域](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain)。

1. [将新的 SQL Server 服务帐户设置为使用域帐户](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount)。

1. [将新的 SQL Server 添加到 Windows Server 故障转移群集](availability-group-manually-configure-tutorial.md#addNode)。

1. 将 IP 地址资源添加到群集。

   可在故障转移群集管理器中创建 IP 地址资源。 选择群集的名称，然后在“群集核心资源”下右键单击群集名称，并选择“属性”： 

   ![该屏幕截图显示了“故障转移群集管理器”，其中群集名称、“服务器名称”和“属性”处于选中状态。](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   在“属性”对话框的“IP 地址”下选择“添加”，然后从远程网络区域添加群集名称的 IP 地址。  选择“IP 地址”对话框上的“确定”，然后在“群集属性”对话框中再次选择“确定”，以保存新的 IP 地址   。 

   ![添加群集 IP](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. 为核心群集名称添加 IP 地址作为依赖项。

   再次打开群集属性，选择“依赖关系”选项卡。为两个 IP 地址配置 OR 依赖关系： 

   ![群集属性](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. 将 IP 地址资源添加到群集中的可用性组角色。 

   在故障转移群集管理器中右键单击可用性组角色，选择“添加资源”，“更多资源”，然后选择“IP 地址”  。

   ![创建 IP 地址](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   按如下所示配置此 IP 地址：

   - 使用远程数据中心内的网络。
   - 从新的 Azure 负载均衡器分配 IP 地址。 

1. 将 IP 地址资源设成侦听器客户端访问点（网络名称）群集的依赖项。

   以下屏幕截图显示了正确配置的 IP 地址群集资源：

   ![可用性组](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >该群集资源组包含这两个 IP 地址。 这两个 IP 地址是侦听器客户端接入点的依赖项。 在群集依赖项配置中使用 **OR** 运算符。

1. [在 PowerShell 中设置群集参数](availability-group-manually-configure-tutorial.md#setparam)。

   使用在新区域中的负载均衡器上配置的群集网络名称、IP 地址和探测端口运行 PowerShell 脚本。

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. 在 SQL Server 配置管理器中的新 SQL Server 上，[启用 Always On 可用性组](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server)。

1. 在 SQL Server Management Studio 中的新 SQL Server 上，[配置系统帐户权限](availability-group-manually-configure-prerequisites-tutorial.md#configure-system-account-permissions)。

1. [在新的 SQL Server 上打开防火墙端口](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)。

   需要打开的端口号取决于环境。 打开镜像终结点和 Azure 负载均衡器运行状况探测的端口。


1. [将副本添加到新 SQL Server 上的可用性组](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio)。

   对于远程 Azure 区域中的副本，请将它设置为使用手动故障转移进行异步复制。  
   

## <a name="set-connection-for-multiple-subnets"></a>设置多个子网的连接

远程数据中心内的副本是可用性组的一部分，但位于不同的子网。 如果此副本成为主副本，可能会发生应用程序连接超时。 多子网部署中的本地可用性组也存在相同的行为。 若要允许从客户端应用程序建立连接，请更新客户端连接，或者在群集网络名称资源上配置名称解析缓存。

最好更新群集配置以设置 `RegisterAllProvidersIP=1`，并更新客户端连接字符串以设置 `MultiSubnetFailover=Yes`。 请参阅[使用 MultiSubnetFailover 进行连接](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0)。

如果无法修改连接字符串，可以配置名称解析缓存。 请参阅[出现超时错误并且在多子网环境中无法连接到 SQL Server 2012 AlwaysOn 可用性组侦听程序](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av)。

## <a name="fail-over-to-remote-region"></a>故障转移到远程区域

要测试侦听器与远程区域之间的连接，可将副本故障转移到远程区域。 尽管副本是异步的，但故障转移存在丢失数据的可能性。 要故障转移并防止丢失数据，请将可用性模式更改为同步，将故障转移模式设置为自动。 请使用以下步骤：

1. 在“对象资源管理器”中连接到承载主副本的 SQL Server 实例。
1. 在“AlwaysOn 可用性组”的“可用性组”下，右键单击可用性组，然后选择“属性”  。
1. 在“常规”页上的“可用性副本”下，将灾难恢复站点中的辅助副本设置为使用“同步提交”可用性模式和“自动”故障转移模式。
1. 如果辅助副本和主副本位于同一站点，且辅助副本具有高可用性，则将辅助副本设置为“异步提交”和“手动”。
1. 选择“确定”。
1. 在“对象资源管理器”中，右键单击可用性组中，然后选择“显示仪表板” 。
1. 在仪表板中，验证 DR 站点中的副本是否已同步。
1. 在“对象资源管理器”中，右键单击可用性组中，然后选择“故障转移...” 。SQL Server Management Studio 将打开向导对 SQL Server 进行故障转移。  
1. 选择“下一步”，然后选择灾难恢复站点中的 SQL Server 实例。 再次选择“下一步”。
1. 连接到灾难恢复站点中的 SQL Server 实例，然后选择“下一步”。
1. 在“摘要”页上查看设置，然后选择“完成” 。

测试连接后，将主副本移回主数据中心，将可用性模式设置回其正常运行的设置。 下表显示了本文档所述体系结构的正常运行的设置：

| 位置 | 服务器实例 | 角色 | 可用性模式 | 故障转移模式
| ----- | ----- | ----- | ----- | -----
| 主数据中心 | SQL-1 | 主要 | 同步 | 自动
| 主数据中心 | SQL-2 | 次要 | 同步 | 自动
| 辅助或远程数据中心 | SQL-3 | 辅助副本 | 异步 | 手动


### <a name="more-information-about-planned-and-forced-manual-failover"></a>有关计划内和强制手动故障转移的详细信息

有关详细信息，请参阅以下主题：

- [对可用性组执行计划的手动故障转移 (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [对可用性组执行强制的手动故障转移 (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组与 Azure VM 上的 SQL Server](availability-group-overview.md)
- [Always On 可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)
