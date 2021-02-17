---
title: '配置 Service Fabric 托管群集 (预览版) '
description: 了解如何为自动 OS 升级、NSG 规则等配置 Service Fabric 托管群集。
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642352"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric 托管群集 (预览版) 配置选项

除了在创建群集时选择 [Service Fabric 托管群集 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) 以外，还可以使用多种其他方法对其进行配置。 在当前预览中，可以：

* 向节点类型添加[虚拟机规模集扩展](how-to-managed-cluster-vmss-extension.md)
* 为你的节点启用[自动 OS 升级](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* 启用节点上的[OS 和数据磁盘加密](how-to-enable-managed-cluster-disk-encryption.md)
* 关于 [网络配置](how-to-managed-cluster-configuration.md#networking-configurations)
* 在节点类型上配置[托管标识](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md)

## <a name="networking-configurations"></a>网络配置

Service Fabric 使用默认网络配置创建托管群集。 此配置由基本群集功能的必需规则和一些可选规则组成，这些规则旨在使客户配置更容易。

除了默认网络配置之外，你还可以修改网络规则以满足你的方案的需求。 

### <a name="nsg-rules-guidance"></a>NSG 规则指南

* Service Fabric 托管群集将 NSG 规则的优先级范围从0到999保留，以实现基本功能。 不能创建优先级值小于1000的自定义 NSG 规则。 
* Service Fabric 托管群集为创建可选的 NSG 规则保留3001到4000的优先级范围。 将自动添加这些规则，以便快速轻松地进行配置。 可以通过在优先级范围1000到3000的范围内添加自定义 NSG 规则来覆盖这些规则。 
* 自定义 NSG 规则的优先级应在1000到3000的范围内。 


### <a name="apply-nsg-rules"></a>应用 NSG 规则

使用经典 (非托管) Service Fabric 群集时，必须声明和管理单独的 *networkSecurityGroups/* 资源，才能 [将网络安全组 (NSG) 规则应用到群集](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype)。 Service Fabric 托管群集，可以在部署模板的群集资源中直接分配 NSG 规则。

使用 *ServiceFabric/managedclusters* 资源 (版本或更高版本) 的 [NETWORKSECURITYRULES](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object)属性 `2021-01-01-preview` 分配 NSG 规则。 例如：

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>RDP 端口 

默认情况下，Service Fabric 托管群集不允许访问 RDP 端口。 可以通过在 Service Fabric 托管群集资源上设置以下属性来打开与 internet 的 RDP 端口。 

```json
"allowRDPAccess": true 
```

当 allowRDPAccess 属性设置为 true 时，以下 NSG 规则将添加到群集部署中。  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection 和 HttpGatewayConnection 端口 

**NSG 规则： SFMC_AllowServiceFabricGatewayToSFRP** 添加了一个默认的 NSG 规则，以允许 Service Fabric 资源提供程序访问群集的 clientConnectionPort 和 httpGatewayConnectionPort。 此规则允许通过服务标记 "ServiceFabric" 访问端口。

>[!NOTE]
>此规则始终会添加，不能重写。 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**NSG 规则： SFMC_AllowServiceFabricGatewayPorts** 这是一个可选的 NSG 规则，用于允许从 internet 访问 clientConnectionPort 和 httpGatewayPort。 此规则允许客户访问 SFX，使用 PowerShell 连接到群集，并从外部使用 Service Fabric 群集 API 终结点。 

>[!NOTE]
>如果有一条自定义规则具有相同端口的相同访问权限、方向和协议值，则不会添加此规则。 可以用自定义 NSG 规则替代此规则。 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>负载均衡器端口 

Service Fabric 托管群集在 ManagedCluster 属性下的 "loadBalancingRules" 部分中配置的所有 LB 端口的默认优先级范围内创建 NSG 规则。 此规则为来自 internet 的入站流量打开 LB 端口。  

>[!NOTE]
>此规则将添加到可选的优先级范围中，并可通过添加自定义 NSG 规则进行重写。 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>负载均衡器探测

Service Fabric 托管群集会自动为 Fabric 网关端口以及在托管群集属性的 "loadBalancingRules" 部分下配置的所有端口创建负载均衡器探测。

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>启用自动 OS 映像升级

你可以选择对运行托管群集节点的虚拟机启用自动 OS 映像升级。 尽管虚拟机规模集资源是由你代表 Service Fabric 托管群集来管理的，但你选择为群集节点启用自动 OS 映像升级。 与 [经典 Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) 群集一样，默认情况下不会升级托管群集节点，以防止群集意外中断。

若要启用自动 OS 升级：

* 使用 `2021-01-01-preview` (或更) 高版本的 *ServiceFabric/Managedclusters* 和 *ServiceFabric/managedclusters/nodetypes* 资源
* 将群集的属性设置 `enableAutoOSUpgrade` 为 *true*
* 将 cluster nodeTypes 的资源属性设置 `vmImageVersion` 为 "*最新*"

例如：

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

启用后，Service Fabric 将开始查询和跟踪托管群集中的 OS 映像版本。 如果有新的操作系统版本，则每次升级一个群集节点类型 (虚拟机规模集) 。 仅在确认没有群集节点 OS 映像升级正在进行时，才执行 Service Fabric 运行时升级。

如果升级失败，Service Fabric 将在24小时后重试，最多三次重试。 与经典 (非托管) Service Fabric 升级、不正常应用或节点可能会阻止 OS 映像升级。

有关映像升级的详细信息，请参阅 [Azure 虚拟机规模集的自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。

## <a name="next-steps"></a>后续步骤

[示例模板链接]

[托管群集概述]
