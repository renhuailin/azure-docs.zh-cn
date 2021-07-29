---
title: 配置可用性组侦听程序和负载均衡器 (PowerShell)
description: 使用具有一个或多个 IP 地址的内部负载均衡器在 Azure 资源管理器模型中配置可用性组侦听程序。
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 3c58814943d844ec1e644412eef926745e2df620
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572390"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>配置一个或多个 Always On 可用性组侦听器 - Resource Manager

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文档演示如何使用 PowerShell 执行以下任务之一：
- 创建负载均衡器
- 将 IP 地址添加到 SQL Server 可用性组的现有负载均衡器。

可用性组侦听器是客户端为了访问数据库而连接的虚拟网络名称。 在 Azure 虚拟机上，负载均衡器持有侦听程序的 IP 地址。 负载均衡器将流量路由到侦听探测端口的 SQL Server 实例。 通常情况下，可用性组使用内部负载均衡器。 Azure 内部负载均衡器可以托管一个或多个 IP 地址。 每个 IP 地址使用特定的探测端口。 

将多个 IP 地址分配到内部负载均衡器是 Azure 的一项新增功能，只能在资源管理器模型中使用。 若要完成此任务，需要在资源管理器模型中的 Azure 虚拟机上部署 SQL Server 可用性组。 这两个 SQL Server 虚拟机必须属于同一个可用性集。 可以使用 [Microsoft 模板](./availability-group-quickstart-template-configure.md)在 Azure 资源管理器中自动创建可用性组。 此模板会自动可用性组，包括内部负载均衡器。 如果需要，可以[手动配置 Always On 可用性组](availability-group-manually-configure-tutorial.md)。

若要完成本文中的步骤，需已配置可用性组。  

相关主题包括：

* [在 Azure VM (GUI) 中配置 AlwaysOn 可用性组](availability-group-manually-configure-tutorial.md)   
* [使用 Azure 资源管理器和 PowerShell 配置 VNet 到 VNet 连接](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>验证 PowerShell 版本

本文中的示例是使用 Azure PowerShell 模块版本 5.4.1 测试的。

请验证你的 PowerShell 模块是否为 5.4.1 或更高版本。

请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

## <a name="configure-the-windows-firewall"></a>配置 Windows 防火墙

配置 Windows 防火墙以允许 SQL Server 访问。 防火墙规则允许到端口（由 SQL Server 实例使用）的 TCP 连接，并且允许侦听器探测。 有关详细的说明，请参阅[为数据库引擎访问配置 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1)。 为 SQL Server 端口和探测端口创建入站规则。

如果要限制 Azure 网络安全组的访问权限，请确保“允许”规则包含后端 SQL Server VM IP 地址、可用性组侦听器的负载均衡器浮动 IP 地址、群集核心 IP 地址（如适用）。

## <a name="determine-the-load-balancer-sku-required"></a>确定所需的负载均衡器 SKU

[Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)以两种 SKU 提供：“基本”和“标准”。 建议使用标准负载均衡器。 如果虚拟机在可用性集中，则可以使用基本负载均衡器。 如果虚拟机在可用性区域中，则需要使用标准负载均衡器。 标准负载均衡器要求所有 VM IP 地址都使用标准 IP 地址。

可用性组的当前 [Microsoft 模板](./availability-group-quickstart-template-configure.md)使用具有基本 IP 地址的基本负载均衡器。

   > [!NOTE]
   > 如果将标准负载均衡器和 Azure 存储用于云见证，则需要配置[服务终结点](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)。 
   > 

本文中的示例指定了一个标准负载均衡器。 在示例中，脚本包括了 `-sku Standard`。

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

若要创建基本负载均衡器，请从创建负载均衡器的行中删除 `-sku Standard`。 例如：

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>示例脚本：使用 PowerShell 创建内部负载均衡器

> [!NOTE]
> 如果使用 [Microsoft 模板](./availability-group-quickstart-template-configure.md)创建可用性组，则已创建内部负载均衡器。

以下 PowerShell 脚本创建内部负载均衡器、配置负载均衡规则，并设置负载均衡器的 IP 地址。 要运行该脚本，请打开 Windows PowerShell ISE，然后将脚本粘贴到“脚本”窗格中。 使用 `Connect-AzAccount` 登录到 PowerShell。 如果有多个 Azure 订阅，请使用 `Select-AzSubscription` 设置订阅。 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> 示例脚本：使用 PowerShell 将 IP 地址添加到现有负载均衡器

要使用多个可用性组，请将附加的 IP 地址添加到负载均衡器。 每个 IP 地址都需要有自身的负载均衡规则、探测端口和前端端口。

前端端口是应用程序用来连接到 SQL Server 实例的端口。 不同可用性组的 IP 地址可以使用相同的前端端口。

> [!NOTE]
> 对于 SQL Server 可用性组，每个 IP 地址需要一个特定的探测端口。 例如，如果负载均衡器上有一个 IP 地址使用探测端口 59999，该负载均衡器上的其他任何 IP 地址就不能使用探测端口 59999。

* 有关负载均衡器限制的信息，请参阅 [网络限制 - Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits) 下面的 **每个负载均衡器的专用前端 IP**。
* 有关可用性组限制的信息，请参阅[限制（可用性组）](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG)。

以下脚本将新的 IP 地址添加到现有负载均衡器。 ILB 使用侦听程序端口作为负载均衡前端端口。 此端口可以是 SQL Server 正在侦听的端口。 对于 SQL Server 的默认实例，此端口为 1433。 可用性组的负载均衡规则需要浮动 IP（直接服务器返回），因此后端端口与前端端口相同。 请更新环境的变量。 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>配置侦听器

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中设置侦听器端口

1. 启动 SQL Server Management Studio 并连接到主副本。

1. 导航到“AlwaysOn 高可用性” > “可用性组” > “可用性组侦听器”。   

1. 现在应看到在故障转移群集管理器中创建的侦听器名称。 右键单击侦听程序名称，并选择“属性”。

1. 在“端口”框中，通过使用先前使用的 $EndpointPort 为可用性组侦听程序指定端口号（默认值为 1433），并选择“确定”。

## <a name="test-the-connection-to-the-listener"></a>测试与侦听器的连接

若要测试连接，请执行以下操作：

1. 使用远程桌面协议 (RDP) 连接到同一虚拟网络中不拥有副本的 SQL Server。 这可能是群集中的其他 SQL Server。

1. 使用 **sqlcmd** 实用工具测试连接。 例如，以下脚本通过侦听器与 Windows 身份验证来与主副本建立 sqlcmd 连接：
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    如果侦听器使用的端口不是默认端口 (1433)，请在连接字符串中指定该端口。 例如，以下 sqlcmd 命令连接到位于端口 1435 的侦听器： 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD 连接自动连接到托管主副本的 SQL Server 实例。 

> [!NOTE]
> 确保指定的端口已在两个 SQL Server 的防火墙上打开。 这两个服务器需要所用 TCP 端口的入站规则。 有关详细信息，请参阅 [添加或编辑防火墙规则](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11))。 
> 

## <a name="guidelines-and-limitations"></a>指导原则和限制

请注意有关 Azure 中使用内部负载均衡器的可用性组侦听器的以下指导原则：

* 使用内部负载均衡器只能从同一个虚拟网络中访问侦听器。

* 如果要使用 Azure 网络安全组来限制访问权限，请确保允许规则包括：
  - 后端 SQL Server VM IP 地址
  - 负载均衡器浮动 AG 侦听程序的 IP 地址
  - 群集核心 IP 地址（如果适用）。

* 将标准负载均衡器与 Azure 存储一起用作云见证时，请创建服务终结点。 有关详细信息，请参阅[从虚拟网络授予访问权限](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)。

## <a name="powershell-cmdlets"></a>PowerShell cmdlet

使用以下 PowerShell cmdlet 为 Azure 虚拟机创建内部负载均衡器。

* 使用 [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) 创建负载均衡器。 
* 使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) 创建负载均衡器的前端 IP 配置。 
* 使用 [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) 创建负载均衡器的规则配置。 
* 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) 创建负载均衡器的后端地址池配置。 
* 使用 [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) 创建负载均衡器的探测配置。
* 使用 [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) 从 Azure 资源组中删除负载均衡器。

## <a name="next-steps"></a>后续步骤 


若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组与 Azure VM 上的 SQL Server](availability-group-overview.md)
- [Always On 可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)
