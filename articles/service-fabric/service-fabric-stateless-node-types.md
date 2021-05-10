---
title: 在 Service Fabric 群集中部署纯无状态节点类型
description: 了解如何在 Azure Service Fabric 群集中创建和部署无状态节点类型。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 4847fd88a96e96788f8e6ebdb4ee3cfa7f15fbdc
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108135448"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>使用纯无状态节点类型部署 Azure Service Fabric 群集
关于 Service Fabric 节点类型，有一个固有的假设，即在某个时间点，有状态服务可能会被放置到节点上。 无状态节点类型放宽了对节点类型的这种假设，因此允许节点类型使用其他功能，例如更快的横向扩展操作、支持在“青铜”持续性级别下自动升级 OS，以及在一个虚拟机规模集中扩展到 100 多个节点。

* 主节点类型不能配置为无状态
* 无状态节点类型仅支持“青铜”持续性级别
* 无状态节点类型仅在 Service Fabric 运行时版本 7.1.409 或更高版本上受支持。


现提供示例模板：[Service Fabric 无状态节点类型模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-2-NodeTypes-Windows-Stateless-Secure)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>在 Service Fabric 群集中启用无状态节点类型
若要在群集资源中将一个或多个节点类型设置为无状态，请将“isStateless”属性设置为“true” 。 使用无状态节点类型部署 Service Fabric 群集时，请记住在群集资源中至少使用一个主节点类型。

* Service Fabric 群集资源 apiVersion 应为“2020-12-01-preview”或更高版本。

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>配置虚拟机规模集，以启用无状态节点类型
若要启用无状态节点类型，应按以下方式配置底层虚拟机规模集资源：

* singlePlacementGroup 属性的值应设置为 false（如果需要扩展到超过 100 个 VM） 。
* 应将规模集的“upgradeMode”设置为“滚动升级” 。
* 设置为滚动升级模式时，需要配置应用程序运行状况扩展或运行状况探测。 按照以下建议，使用无状态节点类型的默认配置来配置运行状况探测。 将应用程序部署到节点类型后，可以更改运行状况探测/运行状况扩展端口，以监视应用程序运行状况。

>[!NOTE]
> 在对无状态节点类型使用自动缩放时，纵向缩减操作后，不会自动清除节点状态。 若要在自动缩放期间清除已关闭节点的节点状态，建议使用 [Service Fabric 自动缩放帮助程序](https://github.com/Azure/service-fabric-autoscale-helper)。

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        },
        "platformFaultDomainCount": 5
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.1"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>配置具有多个可用性区域的无状态节点类型
若要配置跨多个可用性区域的无状态节点类型，请遵循[此处](/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)的文档进行操作，并作如下一些更改：

* 设置 singlePlacementGroup：如果需要启用多个放置组，将其设置为“false” 。
* 设置 upgradeMode：如上所述，将其设置为“滚动升级”，并添加应用程序运行状况扩展/运行状况探测 。
* 设置 platformFaultDomainCount：对于虚拟机规模集，将其设置为“5” 。

>[!NOTE]
> 无论群集中的 VMSSZonalUpgradeMode 如何配置，对于跨多个区域的无状态节点类型，总是会按顺序一次对一个可用性区域进行虚拟机规模集更新，因为该节点类型使用滚动升级模式。

有关参考，请查看[模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure)以配置具有多个可用性区域的无状态节点类型

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
为了能够在虚拟机规模集资源中扩展到超过 100 个 VM，该虚拟机规模集所引用的负载均衡器和 IP 资源必须都使用标准 SKU。 在未设置 SKU 属性的情况下创建负载均衡器或 IP 资源时将创建基本 SKU，而后者不支持扩展到超过 100 个 VM。 默认情况下，标准 SKU 负载均衡器会阻止外部的所有流量；若要允许外部流量，必须将 NSG 部署到子网。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 目前不能在公共 IP 和负载均衡器资源上就地更改 SKU。 

### <a name="virtual-machine-scale-set-nat-rules"></a>虚拟机规模集 NAT 规则
负载均衡器入站 NAT 规则应匹配虚拟机规模集中的 NAT 池。 每个虚拟机规模集必须有一个唯一的入站 NAT 池。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>标准 SKU 负载均衡器出站规则
与基本 SKU 相比，标准负载均衡器和标准公共 IP 为出站连接引入了新功能和不同的行为。 如果在使用标准 SKU 时需要出站连接，则必须使用标准公共 IP 地址或标准公共负载均衡器显式定义它。 有关详细信息，请参阅[出站连接](../load-balancer/load-balancer-outbound-connections.md)和 [Azure 标准负载均衡器](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
> 标准模板引用的 NSG 默认允许所有出站流量。 系统仅允许 Service Fabric 管理操作所需的端口上的入站流量。 你可以根据需要对 NSG 规则进行修改。

>[!NOTE]
> 使用标准 SKU SLB 的任何 Service Fabric 群集都需要确保每种节点类型都有一条规则，即允许端口 443 上的出站流量。 这是完成群集设置所必需的，没有此类规则的任何部署都将失败。



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>迁移为在群集中使用无状态节点类型
对于所有迁移方案，都需要添加一个新的纯无状态节点类型。 现有节点类型不能迁移到纯无状态类型。

若要迁移使用基本 SKU 负载均衡器和 IP 的群集，必须先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 目前无法就地更新这些资源。

应在要使用的新的无状态节点类型中引用新的 LB 和 IP。 上面的示例中添加了一个新的虚拟机规模集资源，用于无状态节点类型。 这些虚拟机规模集引用新创建的 LB 和 IP，并在 Service Fabric 群集资源中被标记为无状态节点类型。

首先，需要将新资源添加到现有资源管理器模板。 这些资源包括：
* 使用标准 SKU 的公共 IP 资源。
* 使用标准 SKU 的负载均衡器资源。
* 由在其中部署虚拟机规模集的子网所引用的 NSG。

资源完成部署后，你就可以开始禁用要从原始群集中删除的节点类型中的节点。

## <a name="next-steps"></a>后续步骤 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)