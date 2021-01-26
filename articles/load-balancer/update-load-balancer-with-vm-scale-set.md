---
title: 更新或删除虚拟机规模集使用的现有 Azure 负载均衡器
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: 通过此操作说明文章，开始使用 Azure 标准负载均衡器和虚拟机规模集。
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
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790083"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>如何更新/删除虚拟机规模集使用的 Azure 负载均衡器

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>如何设置 Azure 负载均衡器以横向扩展虚拟机规模集
  * 确保负载均衡器已设置[入站 NAT 池](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest)，并且虚拟机规模集已放在负载均衡器的后端池中。 新的虚拟机实例被添加到虚拟机规模集时，Azure 负载均衡器将在入站 NAT 池中自动创建新的入站 NAT 规则。 
  * 若要检查入站 NAT 池是否设置正确， 
  1. 通过 https://portal.azure.com 登录到 Azure 门户。
  
  1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
  
  1. 在“设置”下，选择“入站 NAT 规则” 。
如果在右侧窗格中看到为虚拟机规模集中每个实例创建的规则的列表，那么恭喜你，你已经完成设置，可以随时进行扩展。

## <a name="how-to-add-inbound-nat-rules"></a>如何添加入站 NAT 规则？ 
  * 无法添加单独的入站 NAT 规则。 但是，可以为虚拟机规模集中的所有实例添加一组具有定义的前端端口范围和后端端口的入站 NAT 规则。
  * 若要为虚拟机规模集添加一组完整的入站 NAT 规则，需要首先在负载均衡器中创建入站 NAT 池，然后从虚拟机规模集的网络配置文件引用入站 NAT 池。 下面显示了一个使用 CLI 的完整示例。
  * 新的入站 NAT 池不应具有与现有入站 NAT 池重叠的前端端口范围。 若要查看已设置的现有入站 NAT 池，可以使用此 [CLI 命令](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
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
  * 无法更新单独的入站 NAT 规则。 但是，可以为虚拟机规模集中的所有实例更新一组具有定义的前端端口范围和后端端口的入站 NAT 规则。
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
* 无法删除单独的入站 NAT 规则。 但是，可以删除整组入站 NAT 规则。
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
   
1. 在“设置”下，依次选择“前端 IP 配置”、“添加”  。
   
1. 在“添加前端 IP 地址”页上，键入值并选择“确定” 

1. 如果需要新的负载均衡规则，请按照此教程中的[步骤 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) 和[步骤 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) 进行操作

1. 如果需要，使用新创建的前端 IP 配置创建一组新的入站 NAT 规则。 可以在 [上一部分] 中找到相关示例。

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>如何删除虚拟机规模集使用的前端 IP 配置： 
 1. 若要删除规模集使用的前端 IP 配置，需要首先删除引用前端 IP 配置的入站 NAT 池（一组入站 NAT 规则）。 有关如何删除入站规则的说明，请参阅上一部分。
 1. 删除引用前端 IP 配置的负载平衡规则。 
 1. 删除前端 IP 配置。
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>如何删除虚拟机规模集使用的 Azure 负载均衡器： 
 1. 若要删除规模集使用的前端 IP 配置，需要首先删除引用前端 IP 配置的入站 NAT 池（一组入站 NAT 规则）。 有关如何删除入站规则的说明，请参阅上一部分。
 
 1. 删除引用包含虚拟机规模集的后端池的负载平衡规则。
 
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

若要详细了解 Azure 负载均衡器和虚拟机规模集，请阅读有关概念的详细信息。

> [Azure 负载均衡器和 Azure 虚拟机规模集](load-balancer-standard-virtual-machine-scale-sets.md)