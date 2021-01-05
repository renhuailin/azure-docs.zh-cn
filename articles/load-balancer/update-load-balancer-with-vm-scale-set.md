---
title: 更新或删除虚拟机规模集使用的现有 Azure 负载均衡器
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
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
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893254"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>如何更新/删除虚拟机规模集使用的 Azure 负载均衡器

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>如何设置用于扩展虚拟机规模集的 Azure 负载均衡器
  * 请确保已设置了负载均衡器的 [入站 NAT 池](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) ，并且虚拟机规模集已放入负载均衡器的后端池中。 将新虚拟机实例添加到虚拟机规模集时，Azure 负载均衡器会自动在入站 NAT 池中创建新的入站 NAT 规则。 
  * 若要检查是否正确设置了入站 NAT 池， 
  1. 通过 https://portal.azure.com 登录到 Azure 门户。
  
  1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
  
  1. 在 " **设置**" 下，选择 " **入站 NAT 规则**"。
如果在右窗格中看到为虚拟机规模集中的每个单独实例创建的规则列表，则恭喜将所有设置为 "随时"，以便随时进行扩展。

## <a name="how-to-add-inbound-nat-rules"></a>如何添加入站 NAT 规则？ 
  * 无法添加单独的入站 NAT 规则。 但是，你可以为虚拟机规模集中的所有实例添加一组具有定义的前端端口范围和后端端口的入站 NAT 规则。
  * 若要为虚拟机规模集添加一组完整的入站 NAT 规则，需要先在负载均衡器中创建一个入站 NAT 池，然后从虚拟机规模集的网络配置文件中引用入站 NAT 池。 下面显示了使用 CLI 的完整示例。
  * 新的入站 NAT 池的前端端口范围不应与现有的入站 NAT 池重叠。 若要查看设置的现有入站 NAT 池，你可以使用此 [CLI 命令](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
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
## <a name="how-to-update-inbound-nat-rules"></a>如何更新入站 NAT 规则？ 
  * 无法更新单个入站 NAT 规则。 但是，可以使用为虚拟机规模集中的所有实例定义的前端端口范围和后端端口来更新一组入站 NAT 规则。
  * 若要更新虚拟机规模集的一组完整的入站 NAT 规则，需要更新负载均衡器中的入站 NAT 池。 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>如何删除入站 NAT 规则？ 
* 不能删除单独的入站 NAT 规则。 不过，您可以删除一组完整的入站 NAT 规则。
* 若要删除规模集使用的一组完整的入站 NAT 规则，需要先从规模集中删除 NAT 池。 下面显示了一个使用 CLI 的完整示例：
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>如何添加多个 IP 配置：
1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在 " **设置**" 下，选择 " **前端 IP 配置**"，然后选择 " **添加**"。
   
1. 在 " **添加前端 IP 地址** " 页上，键入值，然后选择 **"确定"**

1. 如果需要新的负载均衡规则，请按照本教程中的 [步骤 5](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) 和 [步骤 6](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) 操作

1. 如果需要，使用新创建的前端 IP 配置创建一组新的入站 NAT 规则。 可在 [上一节] 中找到示例。

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>如何删除虚拟机规模集使用的前端 IP 配置： 
 1. 若要删除规模集使用的前端 IP 配置，需要先删除入站 NAT 池 (组) 引用前端 IP 配置的入站 NAT 规则。 有关如何删除入站规则的说明，请参阅上一节。
 1. 删除引用前端 IP 配置的负载均衡规则。 
 1. 删除前端 IP 配置。
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>如何删除虚拟机规模集使用的 Azure 负载均衡器： 
 1. 若要删除规模集使用的前端 IP 配置，需要先删除入站 NAT 池 (组) 引用前端 IP 配置的入站 NAT 规则。 有关如何删除入站规则的说明，请参阅上一节。
 
 1. 删除引用包含虚拟机规模集的后端池的负载均衡规则。
 
 1. 从虚拟机规模集的网络配置文件中删除 loadBalancerBackendAddressPool 引用。 下面显示了一个使用 CLI 的完整示例：
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

> [Azure 负载均衡器和 Azure 虚拟机规模集](load-balancer-standard-virtual-machine-scale-sets.md)
