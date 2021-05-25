---
title: 配置 Service Fabric 托管群集的网络设置（预览）
description: 了解如何为 NSG 规则、RDP 端口访问和负载均衡规则等配置 Service Fabric 托管群集。
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744527"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>配置 Service Fabric 托管群集的网络设置（预览）

Service Fabric 托管群集使用默认网络配置创建。 此配置由基本群集功能的必需规则和一些可选规则组成，这些规则旨在简化客户配置。

除了默认网络配置之外，还可以修改网络规则来满足你的方案需求。

## <a name="nsg-rules-guidance"></a>NSG 规则指南

为托管群集创建新的 NSG 规则时，请注意以下几点。

* Service Fabric 托管群集将 NSG 规则的优先级范围保持在 0 到 999，以实现基本功能。 不能创建优先级值小于 1000 的自定义 NSG 规则。
* Service Fabric 托管群集为创建可选的 NSG 规则保留 3001 到 4000 的优先级范围。 将自动添加这些规则，以便快速轻松地进行配置。 可以通过添加优先级为 1000 到 3000 的自定义 NSG 规则来覆盖这些规则。
* 自定义 NSG 规则的优先级范围应在 1000 到 3000。

## <a name="apply-nsg-rules"></a>应用 NSG 规则

使用经典（非托管）Service Fabric 群集时，必须声明和管理单独的 Microsoft.Network/networkSecurityGroups 资源，才能[将网络安全组 (NSG) 规则应用到群集](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype)。 使用 Service Fabric 托管群集，可以在部署模板的群集资源中直接分配 NSG 规则。

使用 Microsoft.ServiceFabric/managedclusters 资源（版本 `2021-01-01-preview` 或更高版本）的 [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) 属性来分配 NSG 规则。 例如：

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

## <a name="rdp-ports"></a>RDP 端口

默认情况下，Service Fabric 托管群集不允许访问 RDP 端口。 可以通过在 Service Fabric 托管群集资源上设置以下属性来打开到 Internet 的 RDP 端口。

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

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection 和 HttpGatewayConnection 端口

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG 规则：SFMC_AllowServiceFabricGatewayToSFRP

添加了一个默认 NSG 规则，以允许 Service Fabric 资源提供程序访问群集的 clientConnectionPort 和 httpGatewayConnectionPort。 此规则允许通过服务标记“ServiceFabric”访问端口。

>[!NOTE]
>此规则始终可以添加，但不能重写。

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

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG 规则：SFMC_AllowServiceFabricGatewayPorts

这是一个可选的 NSG 规则，允许从 Internet 访问 clientConnectionPort 和 httpGatewayPort。 此规则允许客户访问 SFX，使用 PowerShell 连接到群集，并从外部使用 Service Fabric 群集 API 终结点。 

>[!NOTE]
>如果自定义规则对同一个端口具有相同的访问权限、方向和协议值，则不会添加该规则。 可以用自定义 NSG 规则替代此规则。 

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

## <a name="load-balancer-ports"></a>负载均衡器端口

Service Fabric 托管群集为在 ManagedCluster 属性的“loadBalancingRules”部分下配置的所有负载均衡器 (LB) 端口创建默认优先级范围的 NSG 规则。 此规则为来自 Internet 的入站流量打开 LB 端口。  

>[!NOTE]
>此规则将添加到可选的优先级范围，并可通过添加自定义 NSG 规则进行重写。

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

## <a name="load-balancer-probes"></a>负载均衡器探测

Service Fabric 托管群集会自动为 Fabric Gateway 端口以及在托管群集属性的“loadBalancingRules”部分下配置的所有端口创建负载均衡器探测。

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

## <a name="next-steps"></a>后续步骤

[Service Fabric 托管群集配置选项](how-to-managed-cluster-configuration.md)

[Service Fabric 托管群集概述](overview-managed-cluster.md)
