---
title: 后端池管理
titleSuffix: Azure Load Balancer
description: 开始了解如何配置和管理 Azure 负载均衡器的后端池
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 4e8be77851d0d7102d7c0cef85d9fbfefd8dc2a2
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137159"
---
# <a name="backend-pool-management"></a>后端池管理
后端池是负载均衡器的一个关键组成部分。 后端池定义将在给定负载均衡规则下提供流量的资源的组。

可以通过两种方法配置后端池：
* 网络接口卡 (NIC)
* IP 地址和虚拟网络 (VNET) 资源 ID 的组合

使用现有的虚拟机和虚拟机规模集时，通过 NIC 配置后端池。 此方法会在资源与后端池之间生成最直接的链接。 

在为后端池预分配一个 IP 地址范围（计划稍后创建虚拟机和虚拟机规模集的范围）时，请结合 IP 地址和 VNET ID 配置后端池。

你可以为同一负载均衡器配置基于 IP 和基于 NIC 的后端池，但不能创建混合使用 NIC 面向的备用地址和同一池中 IP 地址的后端池。

本文的配置部分将重点关注：

* Azure PowerShell
* Azure CLI
* REST API
* Azure 资源管理器模板 

这些部分将介绍对于每一配置选项而言，后端池是如何构建的。

## <a name="configuring-backend-pool-by-nic"></a>通过 NIC 配置后端池
后端池是作为负载均衡器操作的一部分而创建的。 NIC 的 IP 配置属性用于添加后端池成员。

为了突出强调此工作流和关系，以下示例重点演示了后端池的创建和填充操作。

  >[!NOTE] 
  >请务必注意一点，通过网络接口配置的后端池不能作为后端池操作的一部分更新。 对后端资源的任何添加或删除都必须在资源的网络接口上进行。

### <a name="powershell"></a>PowerShell
创建新的后端池：
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

创建新的网络接口并将它添加到后端池：

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

检索负载均衡器的后端池信息，确认此网络接口已添加到后端池：

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

创建新的虚拟机，并附加网络接口以将它放入后端池中：

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
创建后端池：

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

创建新的网络接口并将它添加到后端池：

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

检索后端池以确认已正确添加了 IP 地址：

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

创建新的虚拟机，并附加网络接口以将它放入后端池中：

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="resource-manager-template"></a>Resource Manager 模板

按照此[快速入门资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/)部署负载均衡器和虚拟机，并通过网络接口将虚拟机添加到后端池。

按照此[快速入门资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool)部署负载均衡器和虚拟机，并通过 IP 地址将虚拟机添加到后端池。


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>通过 IP 地址和虚拟网络配置后端池
在预填充了后端池的情况下，使用 IP 和虚拟网络。

所有后端池管理都是直接在后端池对象上完成的，如下例中突出显示的那样。

### <a name="powershell"></a>PowerShell
创建新的后端池：

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

使用现有虚拟网络中的新 IP 更新后端池：
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

检索负载均衡器的后端池信息，确认此后端地址已添加到后端池：

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
创建网络接口并将它添加到后端池。 将 IP 地址设置为后端地址之一：

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

创建 VM 并使用后端池中的 IP 地址附加 NIC：
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
使用 CLI 时，可以通过命令行参数或 JSON 配置文件填充后端池。 

通过命令行参数创建并填充后端池：

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

通过 JSON 配置文件创建并填充后端池：

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON 配置文件：
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

检索负载均衡器的后端池信息，确认此后端地址已添加到后端池：

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

创建网络接口并将它添加到后端池。 将 IP 地址设置为后端地址之一：

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

创建 VM 并使用后端池中的 IP 地址附加 NIC：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
 
### <a name="limitations"></a>限制
IP 地址配置的后端池具有以下限制：
  * 只能用于标准负载均衡器
  * 后端池中的 100 IP 地址限制
  * 后端资源必须与负载均衡器位于同一虚拟网络中
  * 具有基于 IP 的后端池的负载均衡器不能充当专用链接服务
  * Azure 门户目前不支持此功能
  * 此功能当前不支持 ACI 容器
  * 负载均衡器或应用程序网关等服务不能放置在负载均衡器的后端池中
  * 不能通过 IP 地址指定入站 NAT 规则

## <a name="next-steps"></a>后续步骤
本文介绍了有关 Azure 负载均衡器后端池管理的信息，以及如何通过 IP 地址和虚拟网络配置后端池。

详细了解 [Azure 负载均衡器](load-balancer-overview.md)。

查看用于基于 IP 的后端池管理的 [REST API](/rest/api/load-balancer/loadbalancerbackendaddresspools/createorupdate)。