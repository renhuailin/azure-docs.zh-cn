---
title: 教程：使用 Azure PowerShell 保护虚拟中心
description: 本文介绍如何在中心内已启用 Azure 防火墙的区域中创建 Azure 虚拟 WAN。
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0a8973887f179f2b05f2694e932f50cafa26c69c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469392"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>教程：使用 Azure PowerShell 保护虚拟中心

本教程将使用一个区域中的虚拟中心创建一个虚拟 WAN 实例，并在该虚拟中心部署 Azure 防火墙以确保连接安全。 此示例将演示虚拟网络之间的安全连接。 虚拟网络和站点到站点、点到站点或 ExpressRoute 分支之间的流量也受虚拟安全中心支持。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 部署虚拟 WAN
> * 部署 Azure 防火墙并配置自定义路由
> * 测试连接

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- PowerShell 7

   本教程要求在 PowerShell 7 上本地运行 Azure PowerShell。 若要安装 PowerShell 7，请参阅[从 Windows PowerShell 5.1 迁移到 PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7&preserve-view=true)。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>初始虚拟 WAN 部署

第一步，需要设置一些变量并创建资源组、虚拟 WAN 实例和虚拟中心：

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

创建两个虚拟网络并将其作为分支连接到中心：

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1 -EnableInternetSecurityFlag $True
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2 -EnableInternetSecurityFlag $True
```

这样就产生了一个功能齐全的虚拟 WAN，可提供任意连接性。 为了增强安全性，需要将 Azure 防火墙部署到每个虚拟中心。 防火墙策略可用于有效地管理虚拟 WAN Azure 防火墙实例。 所以在此示例中还创建了防火墙策略：

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

启用从 Azure 防火墙到 Azure Monitor 的日志记录是一项可选操作，但在此示例中，将使用防火墙日志来证明流量正在遍历防火墙：

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>部署 Azure 防火墙并配置自定义路由

现在中心已具有 Azure 防火墙，但仍需要修改路由，以便让虚拟 WAN 通过防火墙发送来自虚拟网络和分支的流量。 可以通过两个步骤达到此目的：

1. 配置要传播到 `None` 路由表的所有虚拟网络连接（以及存在的所有分支连接）。 此配置的效果是，其他虚拟网络和分支不会学习其前缀，因此没有路由来访问它们。
1. 现在可以在 `Default` 路由表（默认情况下关联所有虚拟网络和分支）中插入静态路由，以便将所有流量发送到 Azure 防火墙。

> [!NOTE]
> 这是在使用 Azure 防火墙管理器保护来自 Azure 门户的连接时部署的配置

从第一步开始，将虚拟网络连接配置为传播到 `None` 路由表：

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

现在可以继续执行第二步，将静态路由添加到 `Default` 路由表。 此示例应用 Azure 防火墙管理器在保护虚拟 WAN 中的连接时生成的默认配置，但可以更改静态路由中的前缀列表以满足特定要求：

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "all_traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```
> [!NOTE]
> 字符串“all_traffic”作为上述 New-AzVHubRoute 命令中参数“-Name”的值具有特殊含意：如果原封不动地使用这个字符串，则会在 Azure 门户中正确反映本文中应用的配置（防火墙管理器 --> 虚拟中心 --> [你的中心] --> 安全配置）。 如果使用另一名称，则会应用所需的配置，但该配置不会反映在 Azure 门户中。 

## <a name="test-connectivity"></a>测试连接

现在，你已具有功能正常的安全中心。 若要测试连接性，需要在连接到中心的每个分支虚拟网络中都具有一个虚拟机：

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

防火墙策略中的默认配置是删除所有内容。 因此需要配置一些规则。 首先是配置 DNAT 规则，以便能通过防火墙的公共 IP 地址访问测试虚拟机：

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

现在可以配置一些示例规则。 定义允许 SSH 流量的网络规则，以及允许通过 Internet 访问完全限定的域名 `ifconfig.co` 的应用程序规则。 此 URL 返回它在 HTTP 请求中获取的源 IP 地址：

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

在实际发送任何流量之前，可以检查虚拟机的有效路由。 它们应包含从虚拟 WAN（`0.0.0.0/0` 加 RFC1918）获知的前缀，但不包含其他分支的前缀：

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

现在，生成从一个虚拟机到另一个虚拟机的流量，然后验证 Azure 防火墙中是否已删除该流量。 在以下 SSH 命令中，需要接受虚拟机指纹，并提供在创建虚拟机时定义的密码。 在此示例中，将从 spoke1 中的虚拟机向 spoke2 发送五个 ICMP 回显请求数据包，并使用 Linux 实用工具 `nc` 在端口 22 上尝试 TCP 连接（使用 `-vz` 标志，只发送连接请求并显示结果）。 结果应为 ping 失败，而端口 22 上的 TCP 连接尝试成功，因为之前配置的网络规则允许该连接：

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

还可以验证 Internet 流量。 通过实用工具 `curl` 对防火墙策略 (`ifconfig.co`) 中允许的 FQDN 的 HTTP 请求应成功，但对任何其他目标的 HTTP 请求应会失败（本示例使用 `bing.com` 进行测试）：

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

若要验证防火墙是否删除了数据包，最简单的方法是检查日志。 由于已将 Azure 防火墙配置为将日志发送到 Azure Monitor，因此可以使用 Kusto 查询语言从 Azure Monitor 检索相关日志：

> [!NOTE]
> 可能需要大约 1 分钟的时间才能看到日志被发送到 Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

在前面的命令中，应看到不同的条目：

* 正在对 SSH 连接应用 DNAT 规则
* 已删除分支（10.1.1.4 和 10.1.2.4）中 VM 之间的 ICMP 数据包
* 允许在分支中的 VM 之间建立 SSH 连接

下面是上述命令生成的输出示例：

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

若要查看应用程序规则（描述允许和拒绝的 HTTP 连接）的日志或更改日志显示方式，可以尝试使用其他 KQL 查询。 可以在 [Azure 防火墙的 Azure Monitor 日志](../firewall/firewall-workbook.md)中找到一些示例。


## <a name="clean-up-resources"></a>清理资源

若要删除测试环境，可以删除带有所有包含的对象的资源组：

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解受信任的安全合作伙伴](trusted-security-partners.md)
