---
title: 在 Service Fabric 群集中部署无状态节点类型
description: 了解如何在 Azure Service fabric 群集中创建和部署无状态节点类型。
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 6259de345b534bfb51ef6ba1a9c3895800546caf
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605490"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>使用无状态节点类型部署 Azure Service Fabric 群集 (预览版) 
Service Fabric 节点类型随附固有假设，在某个时间点，可能会在节点上放置有状态服务。 无状态节点类型使此假设适用于节点类型，因此允许节点类型使用其他功能，例如更快的 scale out 操作、支持在青铜耐用性上进行自动 OS 升级以及在单个虚拟机规模集中横向扩展到超过100个节点。

* 主节点类型不能配置为无状态
* 无状态节点类型仅支持铜牌持久性级别
* 无状态节点类型仅在 Service Fabric 运行时7.1.409 或更高版本上受支持。


示例模板可用： [Service Fabric 无状态节点类型模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>在 Service Fabric 群集中启用无状态节点类型
若要将一个或多个节点类型设置为群集资源中的无状态节点类型，请将 **isStateless** 属性设置为 "true"。 在部署具有无状态节点类型的 Service Fabric 群集时，请记住在群集资源中使用至少一个主节点类型。

* Service Fabric 群集资源 apiVersion 应为 "2020-12-01-preview" 或更高版本。

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
        "isStateles": false,
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

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>为无状态节点类型配置虚拟机规模集
若要启用无状态节点类型，应按以下方式配置底层虚拟机规模集资源：

* 值  **singlePlacementGroup** 属性，该属性应设置为 true/false，具体取决于需要扩展到超过 100 vm 的要求。
* 应设置为滚动的规模集的 **upgradeMode** 。
* 滚动升级模式需要配置应用程序运行状况扩展或运行状况探测。 如下所示，为无状态节点类型的默认配置配置运行状况探测。 将应用程序部署到 nodetype 后，可以更改运行状况探测/运行状况扩展端口来监视应用程序运行状况。

```json
{
    "apiVersion": "2018-10-01",
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
        }
    }
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
        "typeHandlerVersion": "1.0"
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

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
若要在虚拟机规模集资源中启用到超过 100 Vm 的缩放，则该虚拟机规模集所引用的负载均衡器和 IP 资源必须都使用 *标准* SKU。 如果不使用 SKU 属性创建负载平衡器或 IP 资源，将会创建不支持缩放到超过 100 Vm 的基本 SKU。 默认情况下，标准 SKU 负载均衡器会阻止从外部的所有流量。若要允许外部流量，必须将 NSG 部署到子网。

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
> 不能在公共 IP 和负载均衡器资源上对 SKU 进行就地更改。 如果要从具有基本 SKU 的现有资源进行迁移，请参阅本文的迁移部分。

### <a name="virtual-machine-scale-set-nat-rules"></a>虚拟机规模集 NAT 规则
负载均衡器入站 NAT 规则应该匹配虚拟机规模集中的 NAT 池。 每个虚拟机规模集都必须具有唯一的入站 NAT 池。

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
与使用基本 Sku 相比，标准负载均衡器和标准公共 IP 向出站连接引入了新功能和不同的行为。 如果在使用标准 SKU 时需要出站连接，则必须使用标准公共 IP 地址或标准公共负载均衡器显式定义它。 有关详细信息，请参阅 [出站连接](../load-balancer/load-balancer-outbound-connections.md) 和 [Azure 标准负载均衡器](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
> 标准模板引用了默认情况下允许所有出站流量的 NSG。 入站流量仅限于 Service Fabric 管理操作所需的端口。 可对 NSG 规则进行修改以满足你的要求。

>[!NOTE]
> 使用标准 SKU SLB 的任何 Service Fabric 群集都需要确保每个节点类型都有一个允许端口443上的出站流量的规则。 这是完成群集设置所必需的，没有此类规则的任何部署都将失败。



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>使用基本 SKU 负载均衡器和基本 SKU IP 从群集迁移到使用无状态节点类型
对于所有迁移方案，都需要添加一个新的无状态节点类型。 现有节点类型不能迁移为仅有状态。

若要迁移将负载均衡器和 IP 与基本 SKU 一起使用的群集，必须先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 不能就地更新这些资源。

应在要使用的新的无状态节点类型中引用新的 LB 和 IP。 在上面的示例中，添加了一个新的虚拟机规模集资源，用于无状态节点类型。 这些虚拟机规模集引用新创建的 LB 和 IP，并将其标记为 Service Fabric 群集资源中的无状态节点类型。

若要开始，需要将新资源添加到现有资源管理器模板。 这些资源包括：
* 使用标准 SKU 的公共 IP 资源。
* 使用标准 SKU 的负载均衡器资源。
* 用于部署虚拟机规模集的子网所引用的 NSG。

资源完成部署后，可以开始禁用要从原始群集中删除的节点类型中的节点。


## <a name="next-steps"></a>后续步骤 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)

