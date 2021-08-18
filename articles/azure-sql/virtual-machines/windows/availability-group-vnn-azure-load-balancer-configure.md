---
title: 为 AG VNN 侦听器配置负载均衡器
description: 了解如何将 Azure 负载均衡器配置为使用 Azure VM 上的 SQL Server 将流量路由到可用性组的虚拟网络名称 (VNN) 侦听器，以实现高可用性和灾难恢复 (HADR)。
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
ms.date: 06/14/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 902704052524a396812e4d9d3848c754c3a7c4a3
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580865"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>为 AG VNN 侦听器配置负载均衡器
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虚拟机上，群集使用负载均衡器来保存每次都需要位于一个群集节点上的 IP 地址。 在此解决方案中，负载均衡器保留 Always On 可用性组 (AG) 的虚拟网络名称 (VNN) 侦听器的 IP 地址。 

本文介绍了如何使用 Azure 负载均衡器服务配置负载均衡器。 负载均衡器会使用 Azure VM 上的 SQL Server 将流量路由到[可用性组 (AG) 侦听器](availability-group-overview.md)，以实现高可用性和灾难恢复 (HADR)。 

对于 SQL Server 2019 CU8 及更高版本上的客户的替代连接选项，请考虑改用 [DNN 侦听器](availability-group-vnn-azure-load-balancer-configure.md)，以便简化配置并改进故障转移。  



## <a name="prerequisites"></a>先决条件

在完成本文中的步骤之前，应已做好以下准备：

- 确定了 Azure 负载均衡器是适合[用于你的可用性组的连接选项](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)。
- 配置了你的[可用性组侦听程序](availability-group-overview.md)。
- 安装了最新版本的 [PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)。 


## <a name="create-load-balancer"></a>创建负载均衡器

可以创建内部负载均衡器或外部负载均衡器。 只能从网络内部的专用资源访问内部负载均衡器。  外部负载均衡器可将来自公共资源的流量路由到内部资源。 在配置内部负载均衡器期间，请在配置负载均衡规则时，将可用性组侦听器资源所用的同一个 IP 地址用作前端 IP。 配置外部负载均衡器时，不能使用可用性组侦听器所用的同一个 IP 地址，因为侦听器 IP 地址不能是公共 IP 地址。 因此，若要使用外部负载均衡器，请以逻辑方式在可用性组所在的同一子网中分配一个不与其他任何 IP 地址冲突的 IP 地址，并使用此地址作为负载均衡规则的前端 IP 地址。 

使用 [Azure 门户](https://portal.azure.com)创建负载均衡器：

1. 在 Azure 门户中，转到包含虚拟机的资源组。

1. 选择 **添加** 。 在 Azure 市场中搜索“负载均衡器”。 选择“负载均衡器”。

1. 选择“创建”。

1. 使用以下值设置负载均衡器：

   - **订阅**：Azure 订阅。
   - **资源组**：包含你的虚拟机的资源组。
   - **名称**：标识负载均衡器的名称。
   - **区域**：包含你的虚拟机的 Azure 位置。
   - **类型**：公共或专用。 可从虚拟网络内部访问专用负载均衡器。 大多数 Azure 应用程序可以使用专用负载均衡器。 如果应用程序需要通过 Internet 直接访问 SQL Server，请使用公共负载均衡器。
   - **SKU**：标准。
   - **虚拟网络**：虚拟机所在的网络。
   - **IP 地址分配**：静态。 
   - **专用 IP 地址**：你分配给群集化网络资源的 IP 地址。

   下图显示“创建负载均衡器”用户界面：

   ![设置负载均衡器](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>配置后端池

1. 返回到包含虚拟机的 Azure 资源组，找到新的负载均衡器。 可能需要在资源组中刷新视图。 选择负载均衡器。

1. 依次选择“后端池”、“添加”。 

1. 将该后端池与包含 VM 的可用性集进行关联。

1. 在“目标网络 IP 配置”下选择“虚拟机”，并选择要作为群集节点参与操作的虚拟机。  请务必包含要托管该可用性组的所有虚拟机。

1. 选择“确定”以创建后端池。

## <a name="configure-health-probe"></a>配置运行状况探测

1. 在负载均衡器窗格上，选择“运行状况探测”。

1. 选择 **添加** 。

1. 在“添加运行状况探测”窗格上，<span id="probe"></span>设置以下运行状况探测参数：

   - 名称：运行状况探测的名称。
   - **协议**：TCP。
   - **端口**：[准备 VM 时](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)你在防火墙中为运行状况探测创建的端口。 在本文中，示例使用了 TCP 端口 `59999`。
   - **时间间隔**：5 秒。
   - **不正常阈值**：2 次连续失败。

1. 选择“确定”  。

## <a name="set-load-balancing-rules"></a>设置负载均衡规则

为负载均衡器设置负载均衡规则。 

# <a name="private-load-balancer"></a>[专用负载均衡器](#tab/ilb)

1. 在负载均衡器窗格中，选择“负载均衡规则”。

1. 选择 **添加** 。

1. 设置负载均衡规则参数：

   - **名称**：负载均衡规则的名称。
   - 前端 IP 地址：AG 侦听器的群集网络资源的 IP 地址。
   - **端口**：SQL Server TCP 端口。 默认实例端口为 1433。
   - **后端端口**：与启用“浮动 IP (直接服务器返回)”时使用的“端口”值相同的端口。 
   - **后端池**：前面配置的后端池名称。
   - **运行状况探测**：前面配置的运行状况探测。
   - **会话持久性**：无。
   - **空闲超时(分钟)** ：4.
   - **浮动 IP (直接服务器返回)** ：已启用。

1. 选择“确定”  。

# <a name="public-load-balancer"></a>[公共负载均衡器](#tab/elb)

1. 在负载均衡器窗格中，选择“负载均衡规则”。

1. 选择 **添加** 。

1. 设置负载均衡规则参数：

   - **名称**：负载均衡规则的名称。
   - 前端 IP 地址：供客户端用来连接到公共终结点的公共 IP 地址。 
   - **端口**：SQL Server TCP 端口。 默认实例端口为 1433。
   - 后端端口：AG 侦听器所用的同一端口。 默认情况下，该端口为 1433。 
   - **后端池**：前面配置的后端池名称。
   - **运行状况探测**：前面配置的运行状况探测。
   - **会话持久性**：无。
   - **空闲超时(分钟)** ：4.
   - 浮动 IP (直接服务器返回)：已禁用。

1. 选择“确定”  。

---

## <a name="configure-cluster-probe"></a>配置群集探测

在 PowerShell 中设置群集探测端口参数。

# <a name="private-load-balancer"></a>[专用负载均衡器](#tab/ilb)

若要设置群集探测端口参数，请使用环境中的值更新以下脚本中的变量。 从脚本中删除尖括号（`<` 和 `>`）。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<Availability group Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

下表介绍了需要更新的值：


|**值**|**说明**|
|---------|---------|
|`Cluster Network Name`| Windows Server 故障转移群集的网络名称。 在“故障转移群集管理器” > “网络”中，右键单击该网络并选择“属性”。   正确的值位于“常规”选项卡的“名称”下。|
|`AG listener IP Address Resource Name`|AG 侦听器 IP 地址的资源名称。 在“故障转移群集管理器” > “角色”中可用性组角色的“服务器名称”下，右键单击 IP 地址资源并选择“属性”   。 正确的值位于“常规”选项卡的“名称”下。|
|`ILBIP`|内部负载均衡器 (ILB) 的 IP 地址。 在 Azure 门户中将此地址配置为 ILB 的前端地址。  此地址是可用性组侦听器所用的同一个 IP 地址。 可在“故障转移群集管理器”中找到该地址，它与 `<AG listener IP Address Resource Name>` 位于同一属性页。|
|`nnnnn`|在负载均衡器的运行状况探测中配置的探测端口。 任何未使用的 TCP 端口都有效。|
|"SubnetMask"| 群集参数的子网掩码。 它必须是 TCP IP 广播地址：`255.255.255.255`。| 


设置群集探测后，可在 PowerShell 中看到所有群集参数。 运行此脚本：

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

# <a name="public-load-balancer"></a>[公共负载均衡器](#tab/elb)

若要设置群集探测端口参数，请使用环境中的值更新以下脚本中的变量。 从脚本中删除尖括号（`<` 和 `>`）。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<Availability group Listener IP Address Resource Name>" 
$ELBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ELBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

下表介绍了需要更新的值：


|**值**|**说明**|
|---------|---------|
|`Cluster Network Name`| Windows Server 故障转移群集的网络名称。 在“故障转移群集管理器” > “网络”中，右键单击该网络并选择“属性”。   正确的值位于“常规”选项卡的“名称”下。|
|`AG listener IP Address Resource Name`|AG 侦听器 IP 地址资源名称。在“故障转移群集管理器” > “角色”中可用性组角色的“服务器名称”下，右键单击 IP 地址资源并选择“属性”   。 正确的值位于“常规”选项卡的“名称”下。|
|`ELBIP`|外部负载均衡器 (ELB) 的 IP 地址。 此地址在 Azure 门户中配置为 ELB 的前端地址，用于从外部资源连接到公共负载均衡器。|
|`nnnnn`|在负载均衡器的运行状况探测中配置的探测端口。 任何未使用的 TCP 端口都有效。|
|"SubnetMask"| 群集参数的子网掩码。 它必须是 TCP IP 广播地址：`255.255.255.255`。| 


设置群集探测后，可在 PowerShell 中看到所有群集参数。 运行此脚本：

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

> [!NOTE]
> 由于外部负载均衡器没有专用 IP 地址，因此用户无法直接使用 VNN DNS 名称，因为它会解析子网中的 IP 地址。 使用公共 LB 的公共 IP 地址，或者在 DNS 服务器上配置另一个 DNS 映射。 


---

## <a name="modify-connection-string"></a>修改连接字符串 

对于支持它的客户端，请将 `MultiSubnetFailover=True` 添加到连接字符串。 在不需要 MultiSubnetFailover 连接选项时，它将提供更快进行子网故障转移的优势。 这是因为客户端驱动程序会尝试为每个 IP 地址并行打开一个 TCP 套接字。 客户端驱动程序将等待第一个 IP 响应成功，一旦成功，就将其用于连接。

如果客户端不支持 MultiSubnetFailover 参数，可以修改 RegisterAllProvidersIP 和 HostRecordTTL 设置，以防止故障转移后的连接延迟。 

使用 PowerShell 修改 RegisterAllProvidersIp 和 HostRecordTTL 设置： 

```powershell
Get-ClusterResource yourListenerName | Set-ClusterParameter RegisterAllProvidersIP 0  
Get-ClusterResource yourListenerName|Set-ClusterParameter HostRecordTTL 300 
```

若要了解详细信息，请参阅 SQL Server [侦听器连接超时](/troubleshoot/sql/availability-groups/listener-connection-times-out)文档。 


> [!TIP]
> - 即使是跨越单个子网的 HADR 解决方案，也可以在连接字符串中将 MultiSubnetFailover 参数设置为 true，以支持将来的跨越子网而无需更新连接字符串的功能。  
> - 默认情况下，客户端缓存 20 分钟的群集 DNS 记录。 通过减小 HostRecordTTL 来缩短缓存记录的生存时间 (TTL)，旧客户端重新连接的速度可以更快。 因此，减小 HostRecordTTL 设置可能导致到 DNS 服务器的流量增加。

## <a name="test-failover"></a>测试故障转移

测试群集化资源的故障转移以验证群集功能。 

执行以下步骤：

1. 打开 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 并连接到你的可用性组侦听程序。 
1. 在“对象资源管理器”中展开“Always On 可用性组”。  
1. 右键单击该可用性组，然后选择“故障转移”。 
1. 按照向导提示将该可用性组故障转移到一个次要副本。 

当副本切换角色并且两者同步后，故障转移成功。 


## <a name="test-connectivity"></a>测试连接

若要测试连接，请登录到同一虚拟网络中的另一个虚拟机。 打开 **SQL Server Management Studio** 并连接到可用性组侦听程序。

>[!NOTE]
>如果需要，可以[下载 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。

## <a name="next-steps"></a>后续步骤

创建 VNN 后，请考虑优化 [SQL Server VM 的群集设置](hadr-cluster-best-practices.md)。 

若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组与 Azure VM 上的 SQL Server](availability-group-overview.md)
- [Always On 可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)



