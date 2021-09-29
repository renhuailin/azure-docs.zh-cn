---
title: Tutorial:使用 Azure PowerShell 创建跨区域负载均衡器
titleSuffix: Azure Load Balancer
description: 利用本教程开始使用 Azure PowerShell 部署跨区域 Azure 负载均衡器。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3718cbacedf6f8770d0b7d1c83eb4035cb5676b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784843"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Tutorial:使用 Azure PowerShell 创建跨区域 Azure 负载均衡器

跨区域负载均衡器可确保服务在多个 Azure 区域中全局可用。 如果一个区域出现故障，则会将流量路由到下一个最近的正常运行的区域负载均衡器。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建跨区域负载均衡器。
> * 创建负载均衡器规则。
> * 创建包含两个区域负载均衡器的后端池。
> * 测试负载均衡器。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。
- 两个标准 SKU Azure 负载均衡器，其后端池部署在两个不同的 Azure 区域中。
    - 有关为后端池创建区域标准负载均衡器和虚拟机的信息，请参阅[快速入门：使用 Azure PowerShell 创建公共负载均衡器以对 VM 进行负载均衡](quickstart-load-balancer-standard-public-powershell.md)。
        - 为每个区域中的负载均衡器和虚拟机的名称追加 -R1 和 -R2 。 
- Azure PowerShell（本地安装）或 Azure Cloud Shell。


如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-cross-region-load-balancer"></a>创建跨区域负载均衡器


### <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>创建跨区域负载均衡器资源

在本部分，你将创建跨区域负载均衡器所需的资源。

全局标准 SKU 公共 IP 用于跨区域负载均衡器的前端。

* 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建该公共 IP 地址。

* 使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 创建前端 IP 配置。

* 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 创建一个后端地址池。

* 使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 创建一个负载均衡器规则。

* 使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 创建跨区域负载均衡器。

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>配置后端池

在本部分中，你将向跨区域负载均衡器的后端池添加两个区域标准负载均衡器。

> [!IMPORTANT]
> 若要完成这些步骤，请确保已在订阅中部署了两个具有后端池的区域负载均衡器。  有关详细信息，请参阅[快速入门：使用 Azure PowerShell 创建公共负载均衡器以对 VM 进行负载均衡](quickstart-load-balancer-standard-public-powershell.md)。

* 使用 [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) 和 [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) 将区域负载均衡器信息存储在变量中。

* 使用 [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) 创建负载均衡器的后端地址池配置。

* 使用 [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) 将区域负载均衡器前端添加到跨区域后端池。

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>测试负载均衡器

在本部分中，你将测试跨区域负载均衡器。 你需要在 Web 浏览器中连接到公共 IP 地址。  你将停止其中一个区域负载均衡器后端池中的虚拟机并观察故障转移。

1. 使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 获取负载均衡器的公共 IP 地址：

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器上。

3. 停止其中一个区域负载均衡器的后端池中的虚拟机。

4. 刷新 Web 浏览器，观察与其他区域负载均衡器的连接的故障转移。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器和剩余资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

* 创建了全局 IP 地址。
* 创建了跨区域负载均衡器。
* 创建了负载均衡规则。
* 向跨区域负载均衡器的后端池添加了区域负载均衡器。
* 测试了负载均衡器。


转到下一篇文章，了解如何…
> [!div class="nextstepaction"]
> [跨可用性区域的负载均衡器 VM](./quickstart-load-balancer-standard-public-portal.md)