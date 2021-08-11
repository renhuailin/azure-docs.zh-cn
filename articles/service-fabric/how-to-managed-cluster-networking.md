---
title: 配置 Service Fabric 托管群集的网络设置
description: 了解如何为 NSG 规则、RDP 端口访问和负载均衡规则等配置 Service Fabric 托管群集。
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5164a7e3aeb1e82700bd5c5bc4d44e55de64421b
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111895582"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>配置 Service Fabric 托管群集的网络设置

Service Fabric 托管群集使用默认网络配置创建。 此配置由基本群集功能的必需规则和一些可选规则（比如默认允许所有出站流量）组成，这些规则旨在简化客户配置。

除了默认网络配置之外，还可以修改网络规则来满足你的方案需求。

## <a name="nsg-rules-guidance"></a>NSG 规则指南

为托管群集创建新的 NSG 规则时，请注意以下几点。

* Service Fabric 托管群集将 NSG 规则的优先级范围保持在 0 到 999，以实现基本功能。 不能创建优先级值小于 1000 的自定义 NSG 规则。
* Service Fabric 托管群集为创建可选的 NSG 规则保留 3001 到 4000 的优先级范围。 将自动添加这些规则，以便快速轻松地进行配置。 可以通过添加优先级为 1000 到 3000 的自定义 NSG 规则来覆盖这些规则。
* 自定义 NSG 规则的优先级范围应在 1000 到 3000。

## <a name="apply-nsg-rules"></a>应用 NSG 规则

使用经典（非托管）Service Fabric 群集时，必须声明和管理单独的 Microsoft.Network/networkSecurityGroups 资源，才能[将网络安全组 (NSG) 规则应用到群集](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.servicefabric/service-fabric-secure-nsg-cluster-65-node-3-nodetype)。 使用 Service Fabric 托管群集，可以在部署模板的群集资源中直接分配 NSG 规则。

使用 Microsoft.ServiceFabric/managedclusters 资源（版本 `2021-05-01` 或更高版本）的 [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) 属性来分配 NSG 规则。 例如：

```json
            "apiVersion": "2021-05-01",
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

默认情况下，Service Fabric 托管群集不能访问 RDP 端口。 可以通过在 Service Fabric 托管群集资源上设置以下属性来打开到 Internet 的 RDP 端口。

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

Service Fabric 托管群集会为节点类型中的每个实例自动创建入站 NAT 规则。 若要查找端口映射以访问特定实例（群集节点），请执行以下步骤：

使用 Azure 门户，找到为远程桌面协议 (RDP) 创建入站 NAT 规则的托管群集。

1. 在采用以下格式命名的订阅中，导航到此托管群集资源组：SFC_{cluster-id}

2. 为以下格式的群集选择负载均衡器：LB-{cluster-name}

3. 在负载均衡器页上，选择“入站 NAT 规则”。 查看入站 NAT 规则，以确认节点目标端口映射的入站前端端口。 

   以下屏幕截图显示三种不同节点类型的入站 NAT 规则：

   ![入站 NAT 规则][Inbound-NAT-Rules]

   默认情况下，对于 Windows 群集，前端端口在 50000 及更大范围内，目标端口是端口 3389，此端口已映射到目标节点的 RDP 服务。

4. 远程连接到特定节点（规模集实例）。 可以使用创建群集时设置的用户名和密码，也可使用已配置的其他任意凭据。

以下屏幕截图显示在 Windows 群集中使用远程桌面连接连接到应用（实例 0）节点：

![远程桌面连接][sfmc-rdp-connect]

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

使用此可选规则，客户可访问 SFX，通过 PowerShell 连接到群集，以及通过开启 clientConnectionPort 和 httpGatewayPort 的 LB 端口使用 Internet 中的 Service Fabric 群集 API 终结点。

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

Service Fabric 托管群集可在默认优先级范围内，为在“ManagedCluster”属性的“loadBalancingRules”部分所配置的所有负载均衡器 (LB) 端口创建 NSG 规则。 此规则为来自 Internet 的入站流量打开 LB 端口。  

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

Service Fabric 托管群集会为 Fabric 网关端口以及在托管群集属性的“loadBalancingRules”部分配置的所有端口自动创建负载均衡器探测。

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

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png

