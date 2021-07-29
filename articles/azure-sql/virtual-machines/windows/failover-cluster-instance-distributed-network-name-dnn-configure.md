---
title: 为故障转移群集实例配置 DNN
description: 了解如何配置分布式网络名称 (DNN)，以将流量路由到 Azure VM 故障转移群集实例 (FCI) 上的 SQL Server。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d27223906727af3b45617c7162f5c5da5133d2e3
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111569496"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>为故障转移群集实例配置 DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虚拟机上，分布式网络名称 (DNN) 将流量路由到相应的群集化资源。 它提供了一种比虚拟网络名称 (VNN) 更简单的方法来连接到 SQL Server 故障转移群集实例 (FCI)，不需要使用 Azure 负载均衡器。 

本文介绍了如何配置 DNN 资源，以使用 Azure VM 上的 SQL Server 将流量路由到故障转移群集实例，以实现高可用性和灾难恢复 (HADR)。 

DNN 功能目前仅适用于 Windows Server 2016 及更高版本上的 SQL Server 2019 CU2 及更高版本。 

对于备用连接选项，请考虑改用[虚拟网络名称和 Azure 负载均衡器](failover-cluster-instance-vnn-azure-load-balancer-configure.md)。 

## <a name="overview"></a>概述

当与 [SQL Server VM 上的 Always On 故障转移群集实例](failover-cluster-instance-overview.md)一起使用时，分布式网络名称 (DNN) 将虚拟网络名称 (VNN) 替换为连接点。 这样就不需通过 Azure 负载均衡器将流量路由到 VNN，简化了部署、维护并改进了故障转移。 

对于 FCI 部署，VNN 仍然存在，但是客户端会连接到 DNN DNS 名称而非 VNN 名称。 

## <a name="prerequisites"></a>先决条件 

在完成本文中的步骤之前，应已做好以下准备：

- CU2 或更高版本上的 SQL Server 2019（在 Windows Server 2016 及更高版本上）
- 确定了分布式网络名称是适合[用于你的 HADR 解决方案的连接性选项](hadr-cluster-best-practices.md#connectivity)。
- 配置了你的[故障转移群集实例](failover-cluster-instance-overview.md)。 
- 安装了最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 
- 连接到 DNN 侦听器的客户端必须支持连接字符串中的 `MultiSubnetFailover=True` 参数。 

## <a name="create-dnn-resource"></a>创建 DNN 资源 

DNN 资源在 SQL Server FCI 所在的群集组中创建。 请使用 PowerShell 在 FCI 群集组中创建 DNN 资源。 

以下 PowerShell 命令将 DNN 资源添加到资源名称为 `<dnnResourceName>` 的 SQL Server FCI 群集组。 资源名称用于独一无二地标识资源。 请使用对你来说有意义且在整个群集中独一无二的资源名称。 资源类型必须为 `Distributed Network Name`。 

`-Group` 值必须是群集组的名称，该群集组对应于你要在其中添加分布式网络名称的 SQL Server FCI。 对于默认实例，典型格式为 `SQL Server (MSSQLSERVER)`。 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

例如，若要为默认 SQL Server FCI 创建 DNN 资源 `dnn-demo`，请使用以下 PowerShell 命令：

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>设置群集 DNN DNS 名称

在群集中设置 DNN 资源的 DNS 名称。 然后，群集使用此值将流量路由到当前承载着 SQL Server FCI 的节点。 

客户端使用 DNS 名称连接到 SQL Server FCI。 你可以选择一个唯一值。 或者，如果你已有现有的 FCI 并且不想更新客户端连接字符串，则可以将 DNN 配置为使用客户端已在使用的当前 VNN。 为此，你需要[重命名 VNN](#rename-the-vnn)，然后在 DNS 中设置 DNN。

使用此命令设置 DNN 的 DNS 名称： 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName` 值是客户端用来连接到 SQL Server FCI 的值。 例如，若要使客户端连接到 `FCIDNN`，请使用以下 PowerShell 命令：

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

当连接到 SQL Server FCI 时，客户端现在会在其连接字符串中输入 `FCIDNN`。 

   > [!WARNING]
   > 请勿删除当前的虚拟网络名称 (VNN)，因为它是 FCI 基础结构的必需组件。 


### <a name="rename-the-vnn"></a>重命名 VNN 

如果你有现有的虚拟网络名称，并且希望客户端继续使用该值连接到 SQL Server FCI，则必须将当前 VNN 重命名为一个占位符值。 重命名当前的 VNN 后，可以将 DNN 的 DNS 名称值设置为 VNN。 

重命名 VNN 存在一些限制。 有关详细信息，请参阅[重命名 FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)。

如果你的业务不需要使用当前 VNN，请跳过此部分。 重命名 VNN 后，[设置群集 DNN DNS 名称](#set-cluster-dnn-dns-name)。 

   
## <a name="set-dnn-resource-online"></a>联机设置 DNN 资源

正确命名你的 DNN 资源并在群集中设置 DNS 名称值后，请使用 PowerShell 在群集中联机设置 DNN 资源： 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

例如，若要启动 DNN 资源 `dnn-demo`，请使用以下 PowerShell 命令： 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>配置可能的所有者

默认情况下，群集会将 DNN DNS 名称绑定到群集中的所有节点。 但是，应当将群集中不属于 SQL Server FCI 的节点从 DNN 可能的所有者的列表中排除。 

若要更新可能的所有者，请执行以下步骤：

1. 在故障转移群集管理器中转到你的 DNN 资源。 
1. 右键单击该 DNN 资源并选择“属性”。 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="DNN 资源的快捷菜单，其中突出显示了“属性”命令。":::

1. 清除不参与故障转移群集实例的任何节点的复选框。 DNN 资源的可能所有者的列表应与 SQL Server 实例资源的可能所有者的列表匹配。 例如，假设 Data3 不参与 FCI，下图是从 DNN 资源的可能所有者的列表中删除 Data3 的示例： 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="对于 DNN 资源的可能所有者，清除不参与 FCI 的节点旁边的复选框":::

1. 选择“确定”保存设置。 


## <a name="restart-sql-server-instance"></a>重启 SQL Server 实例 

使用故障转移群集管理器重启 SQL Server 实例。 执行以下步骤：

1. 在故障转移群集管理器中转到你的 SQL Server 资源。
1. 右键单击该 SQL Server 资源，使其脱机。 
1. 在所有关联资源脱机后，右键单击该 SQL Server 资源并再次使其联机。 

## <a name="update-connection-string"></a>更新连接字符串

若要确保故障转移时的快速连接，请在 SQL 客户端版本低于 4.6.1 时将 `MultiSubnetFailover=True` 添加到连接字符串。 

此外，如果 DNN 未使用原始 VNN，则连接到 SQL Server FCI 的 SQL 客户端需要将其连接字符串更新为 DNN DNS 名称。 若要规避此要求，可以将 DNS 名称值更新为 VNN 的名称。 但是，你首先需要[将现有的 VNN 替换为占位符](#rename-the-vnn)。 

## <a name="test-failover"></a>测试故障转移


测试群集化资源的故障转移以验证群集功能。 


若要测试故障转移，请执行以下步骤： 

1. 使用 RDP 连接到 SQL Server 群集节点之一。
1. 打开“故障转移群集管理器”。 选择“角色”  。 观察哪个节点拥有 SQL Server FCI 角色。
1. 右键单击“SQL Server FCI”角色。 
1. 选择“移动”，再选择“最佳节点” 。

“故障转移群集管理器”显示该角色，其资源脱机。 然后资源会移动，并在另一个节点上恢复联机。

## <a name="test-connectivity"></a>测试连接

若要测试连接，请登录到同一虚拟网络中的另一个虚拟机。 打开 SQL Server Management Studio 并使用 DNN DNS 名称连接到 SQL Server FCI。

如果需要，可以[下载 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="avoid-ip-conflict"></a>避免 IP 冲突

这是一个可选步骤，用于防止将 FCI 资源使用的虚拟 IP (VIP) 地址重复分配给 Azure 中的其他资源。 

尽管客户现在使用 DNN 连接到 SQL Server FCI，但虚拟网络名称 (VNN) 和虚拟 IP 无法删除，因为它们是 FCI 基础结构的必需组件。 但是，由于 Azure 中不再有保留虚拟 IP 地址的负载均衡器，因此虚拟网络上的另一资源可能会被分配与 FCI 使用的虚拟 IP 地址相同的 IP 地址。 这可能会导致重复 IP 冲突问题。 

请配置 APIPA 地址或配置专用网络适配器来保留 IP 地址。 

### <a name="apipa-address"></a>APIPA 地址

为避免使用重复的 IP 地址，请配置 APIPA 地址（也称为链路本地地址）。 为此，请运行以下命令：

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

在此命令中，"virtual IP address" 是群集化 VIP 地址资源的名称，"169.254.1.1" 是为 VIP 地址选择的 APIPA 地址。 请选择最适合你的业务的地址。 设置 `OverrideAddressMatch=1` 以允许 IP 地址在任何网络（包括 APIPA 地址空间）中。 

### <a name="dedicated-network-adapter"></a>专用网络适配器

或者，在 Azure 中配置一个网络适配器来保留虚拟 IP 地址资源使用的 IP 地址。 但是，这会消耗子网地址空间中的地址，而且还需要额外的开销来确保网络适配器不被用于任何其他用途。

## <a name="limitations"></a>限制

- 目前，只有 Windows Server 2016 及更高版本上的 SQL Server 2019 CU2 及更高版本支持与 FCI 一起使用的 DNN。 
- 将其他 SQL Server 功能与包含 DNN 的 FCI 一起使用时，可能有更多注意事项。 有关详细信息，请参阅[包含 DNN 的 FCI 的互操作性](failover-cluster-instance-dnn-interoperability.md)。 

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [故障转移群集实例与 Azure VM 上的 SQL Server](failover-cluster-instance-overview.md)
- [故障转移群集实例概述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)

