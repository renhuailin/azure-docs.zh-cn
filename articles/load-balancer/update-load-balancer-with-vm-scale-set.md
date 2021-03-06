---
title: 更新或删除虚拟机规模集使用的现有负载均衡器
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: 本操作指南文章介绍了如何开始 Azure 标准负载均衡器和虚拟机规模集。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 52f2a2ed301bf734ad605a2ee68a0ab672a97014
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218717"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>更新或删除虚拟机规模集使用的负载均衡器

使用虚拟机规模集和 Azure 负载均衡器的实例时，可以执行以下操作：

- 添加、更新和删除规则。
- 添加配置。
- 删除负载均衡器。

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>设置用于扩展虚拟机规模集的负载均衡器

请确保 Azure 负载均衡器实例已设置 [入站 NAT 池](/cli/azure/network/lb/inbound-nat-pool) ，并且虚拟机规模集已放入负载均衡器的后端池中。 将新的虚拟机实例添加到虚拟机规模集时，负载均衡器会自动在入站 NAT 池中创建新的入站 NAT 规则。

若要检查是否正确设置了入站 NAT 池：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单上，选择“所有资源”。 然后从资源列表中选择 " **MyLoadBalancer** "。
1. 在 " **设置**" 下，选择 " **入站 NAT 规则**"。 在右侧窗格中，如果看到为虚拟机规模集中的每个单独实例创建的规则列表，则可以随时进行扩展。

## <a name="add-inbound-nat-rules"></a>添加入站 NAT 规则

无法添加单独的入站 NAT 规则。 但你可以为虚拟机规模集中的所有实例添加一组具有定义的前端端口范围和后端端口的入站 NAT 规则。

若要为虚拟机规模集添加一组完整的入站 NAT 规则，请先在负载均衡器中创建一个入站 NAT 池。 然后从虚拟机规模集的网络配置文件中引用入站 NAT 池。 显示了使用 CLI 的完整示例。

新的入站 NAT 池的前端端口范围不应与现有的入站 NAT 池重叠。 若要查看设置的现有入站 NAT 池，请使用以下 [CLI 命令](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)：
  
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
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>更新入站 NAT 规则

无法更新单个入站 NAT 规则。 但你可以使用定义的前端端口范围和虚拟机规模集中所有实例的后端端口来更新一组入站 NAT 规则。

若要更新虚拟机规模集的一组完整的入站 NAT 规则，请在负载均衡器中更新入站 NAT 池。
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>删除入站 NAT 规则

不能删除单独的入站 NAT 规则，但可以通过删除入站 NAT 池来删除整个入站 NAT 规则集。

若要删除 NAT 池，请首先从规模集中删除它。 下面显示了使用 CLI 的完整示例：

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
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>添加多个 IP 配置

添加多个 IP 配置：

1. 在左侧菜单上，选择“所有资源”。 然后从资源列表中选择 " **MyLoadBalancer** "。
1. 在“设置”下，选择“前端 IP 配置”。  然后选择“添加”  。
1. 在 " **添加前端 IP 地址** " 页上，输入值，然后选择 **"确定"**。
1. 如果需要新的负载均衡规则，请遵循本教程中的 [步骤 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) 和 [步骤 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) 。
1. 如果需要，请使用新创建的前端 IP 配置创建一组新的入站 NAT 规则。 在上一节中找到了一个示例。

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>单个负载均衡器后的多个虚拟机规模集

在负载均衡器中创建入站 NAT 池，引用虚拟机规模集的网络配置文件中的入站 NAT 池，最后更新实例以使更改生效。 对所有虚拟机规模集重复这些步骤。

请确保创建具有非重叠前端端口范围的单独入站 NAT 池。
  
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
          -–instance-ids *
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
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>删除虚拟机规模集使用的前端 IP 配置

删除规模集使用的前端 IP 配置：

 1. 首先删除入站 NAT 池 (一组引用前端 IP 配置的入站 NAT 规则) 。 上一部分介绍了如何删除入站规则的说明。
 1. 删除引用前端 IP 配置的负载均衡规则。
 1. 删除前端 IP 配置。

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>删除虚拟机规模集使用的负载均衡器

删除规模集使用的前端 IP 配置：

 1. 首先删除入站 NAT 池 (一组引用前端 IP 配置的入站 NAT 规则) 。 上一部分介绍了如何删除入站规则的说明。
 1. 删除引用包含虚拟机规模集的后端池的负载均衡规则。
 1. `loadBalancerBackendAddressPool`从虚拟机规模集的网络配置文件中删除引用。
 
 下面显示了使用 CLI 的完整示例：

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
最后，删除负载均衡器资源。
 
## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 负载均衡器和虚拟机规模集的详细信息，请阅读有关概念的详细信息。

> [具有虚拟机规模集的 Azure 负载均衡器](load-balancer-standard-virtual-machine-scale-sets.md)
