---
title: 在 Azure 中部署使用 IPv6 的虚拟机规模集
titlesuffix: Azure Virtual Network
description: 本文介绍如何在 Azure 虚拟网络中部署使用 IPv6 的虚拟机规模集。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: allensu
ms.openlocfilehash: 0f8bce99f26f175f3103ccc4824c094b2e1c4654
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367503"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>在 Azure 中部署使用 IPv6 的虚拟机规模集

本文介绍如何在 Azure 虚拟网络中部署使用双堆栈外部负载均衡器的双堆栈 (IPv4 + IPv6) 虚拟机规模集。 创建支持 IPv6 的虚拟机规模集的过程，与[此处](../../load-balancer/ipv6-configure-standard-load-balancer-template-json.md)所述的创建单个 VM 的过程基本相同。 首先执行类似于创建单个 VM 的步骤：
1.    创建 IPv4 和 IPv6 公共 IP。
2.    创建双堆栈负载均衡器。  
3.    创建网络安全组 (NSG) 规则。  

只有一个步骤与创建单个 VM 不同，那就是创建使用虚拟机规模集资源的网络接口 (NIC) 配置：networkProfile/networkInterfaceConfigurations。 JSON 结构类似于单个 VM 使用的 Microsoft.Network/networkInterfaces 对象，不过，其中添加了使用 "primary": true 属性将 NIC 和 IPv4 IpConfiguration 设置为主接口的代码，如以下示例中所示：

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>示例虚拟机规模集模板 JSON

若要部署使用双堆栈外部负载均衡器和虚拟网络的双堆栈 (IPv4 + IPv6) 虚拟机规模集，请查看[此处](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)的示例模板。
## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 虚拟网络中的 IPv6 支持，请参阅 [Azure 虚拟网络 IPv6 是什么？](ipv6-overview.md)。
