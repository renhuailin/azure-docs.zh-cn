---
title: 更新或删除由虚拟机规模集使用的现有 Azure 负载均衡器
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: 通过此操作说明文章，开始使用 Azure 标准负载均衡器和虚拟机规模集。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: f72ac3b3a799b97883586e5c2eebc4a42119ae6f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255701"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>更新或删除由虚拟机规模集使用的 Azure 负载均衡器

在使用虚拟机规模集和 Azure 负载均衡器的实例时，可以执行以下操作：

- 添加、更新和删除规则。
- 添加配置。
- 删除负载均衡器。

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>设置负载均衡器来对虚拟机规模集进行横向扩展

请确保 Azure 负载均衡器实例已经设置了[入站 NAT 池](/cli/azure/network/lb/inbound-nat-pool)，并且已将虚拟机规模集置于负载均衡器的后端池中。 在将新虚拟机实例添加到虚拟机规模集时，Azure 负载均衡器将会在入站 NAT 池中自动创建新的入站 NAT 规则。

若要检查入站 NAT 池是否设置正确：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单上，选择“所有资源”。 然后从资源列表中选择“MyLoadBalancer”。
1. 在“设置”下，选择“入站 NAT 规则” 。 如果在右侧窗格中看到为虚拟机规模集中每个实例创建的规则的列表，则说明你已经完成设置，可以随时进行纵向扩展。

## <a name="add-inbound-nat-rules"></a>添加入站 NAT 规则

无法添加单独的入站 NAT 规则。 但是，可以为虚拟机规模集中的所有实例都添加一组具有定义的前端端口范围和后端端口的入站 NAT 规则。

若要为虚拟机规模集添加一组完整的入站 NAT 规则，请先在负载均衡器中创建入站 NAT 池。 然后从虚拟机规模集的网络配置文件引用入站 NAT 池。 我们来看一个使用 CLI 的完整示例。

新的入站 NAT 池不应具与现有入站 NAT 池有重叠的前端端口范围。 若要查看已设置的现有入站 NAT 池，可以使用此 [CLI 命令](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)：
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          --instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>更新入站 NAT 规则

无法更新单独的入站 NAT 规则。 但是，可以为虚拟机规模集中的所有实例都更新一组具有定义的前端端口范围和后端端口的入站 NAT 规则。

若要为虚拟机规模集更新一组完整的入站 NAT 规则，请在负载均衡器中更新入站 NAT 池。
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>删除入站 NAT 规则

无法删除单独的入站 NAT 规则，但可以通过删除入站 NAT 池来删除整组入站 NAT 规则。

若要删除 NAT 池，请先从规模集中删除它。 我们来看一个使用 CLI 的完整示例：

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       --lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>添加多个 IP 配置

若要添加多个 IP 配置：

1. 在左侧菜单上，选择“所有资源”。 然后从资源列表中选择“MyLoadBalancer”。
1. 在“设置”下，选择“前端 IP 配置”。  然后选择“添加”  。
1. 在“添加前端 IP 地址”页上，键入值并选择“确定” 。
1. 如果需要新的负载均衡规则，请参阅[管理 Azure 负载均衡器的规则 - Azure 门户](manage-rules-how-to.md)。
1. 如果需要，请通过使用新创建的前端 IP 配置来创建一组新的入站 NAT 规则。 在上一部分中找到一个示例。

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>某一个负载均衡器后的多个虚拟机规模集

在负载均衡器中创建入站 NAT 池，在虚拟机规模集的网络配置文件中引用该入站 NAT 池，最后，更新实例以使更改生效。 对所有虚拟机规模集重复这些步骤。

请确保创建前端端口范围不重叠的单独的入站 NAT 池。
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          --instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          --instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>删除虚拟机规模集使用的前端 IP 配置

若要删除规模集使用的前端 IP 配置：

 1. 请首先删除引用前端 IP 配置的入站 NAT 池（一组入站 NAT 规则）。 上一部分中提供了有关如何删除入站规则的说明。
 1. 删除引用前端 IP 配置的负载均衡规则。
 1. 删除前端 IP 配置。

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>删除由虚拟机规模集使用的负载均衡器

若要删除规模集使用的前端 IP 配置：

 1. 请首先删除引用前端 IP 配置的入站 NAT 池（一组入站 NAT 规则）。 上一部分中提供了有关如何删除入站规则的说明。
 1. 删除引用包含虚拟机规模集的后端池的负载均衡规则。
 1. 从虚拟机规模集的网络配置文件中删除 `loadBalancerBackendAddressPool` 引用。
 
 我们来看一个使用 CLI 的完整示例：

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
最后，请删除负载均衡器资源。
 
## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 负载均衡器和虚拟机规模集，请阅读有关这些概念的更多内容。

> [Azure 负载均衡器与 Azure 虚拟机规模集](load-balancer-standard-virtual-machine-scale-sets.md)
