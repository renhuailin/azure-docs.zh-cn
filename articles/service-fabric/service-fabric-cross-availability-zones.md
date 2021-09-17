---
title: 跨可用性区域部署群集
description: 了解如何跨可用性区域创建 Azure Service Fabric 群集。
author: peterpogorski
ms.topic: conceptual
ms.date: 05/24/2021
ms.author: pepogors
ms.openlocfilehash: aaf5be101320a5b8cccb24b89908c788996fc494
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445795"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性区域部署 Azure Service Fabric 群集

Azure 中的可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是一个唯一的物理位置，在 Azure 区域中配备独立电源、冷却系统和网络。

为了支持跨可用性区域的群集，Azure Service Fabric 提供了两种配置方法，如下文所述。 可用性区域仅在选择区域中可用。 有关详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

如需获取示例模板，请参阅 [Service Fabric 跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)。

## <a name="recommended-topology-for-spanning-a-primary-node-type-across-availability-zones"></a>可用性区域中跨越主节点类型的建议拓扑

* 设置为 `Platinum` 的群集可靠性级别
* 单个使用标准 SKU 的公共 IP 资源
* 单个使用标准 SKU 的负载均衡器资源
* 由在其中部署虚拟机规模集的子网所引用的网络安全组 (NSG)

>[!NOTE]
>虚拟机规模集的单个放置组属性必须设置为 `true`。

以下示例节点列表描述跨区域虚拟机规模集的 FD/UD 格式：

![屏幕截图显示跨区域虚拟机规模集的 FD/UD 格式的示例节点列表。][sf-multi-az-nodes]

### <a name="distribution-of-service-replicas-across-zones"></a>跨区域分布服务副本

在跨可用性区域的节点类型上部署服务时，将放置副本以确保它们进入不同的区域中。 每个节点类型中的节点上的容错域都配置有区域信息（即 FD = fd:/zone1/1 等）。 例如，对于五个副本或服务实例，分布为 2-2-1，运行时将尝试确保跨区域的平均分布。

### <a name="user-service-replica-configuration"></a>配置用户服务副本

在跨可用性区域的节点类型上部署的有状态用户服务应这样配置：目标副本计数 = 9，最小值 = 5。 此配置有助于服务正常工作（即使一个区域出现故障），因为其他两个区域中仍有六个副本。 此方案中的应用程序升级也将成功。

### <a name="cluster-reliabilitylevel"></a>群集 ReliabilityLevel

此值定义群集中种子节点的数量和系统服务的副本大小。 跨可用性区域设置具有更多的节点，这些节点分布在多个区域中以实现区域复原能力。
  
较高的 `ReliabilityLevel` 值可确保存在更多种子节点和系统服务副本，并且这些副本均匀地分布在各个区域中，这样，如果某个区域出现故障，群集和系统服务就不会受到影响。 `ReliabilityLevel = Platinum`（建议）确保群集中有九个种子节点跨区域分布，并且每个区域中有三个种子。

### <a name="zone-down-scenario"></a>区域故障情况

当某个区域出现故障时，该区域的所有节点和服务副本都将显示为出现故障。 由于其他区域中有副本，服务会继续响应。 主副本故障转移到正常工作的区域。 服务似乎处于警告状态，因为尚未达到目标副本计数，并且虚拟机 (VM) 计数仍高于最小目标副本大小。

Service Fabric 负载均衡器在工作区域中显示副本以匹配目标副本计数。 此时，服务显示运行正常。 如果出现故障的区域恢复正常，负载均衡器会将所有服务副本均匀分布到区域。

## <a name="upcoming-optimizations"></a>即将推出的优化
* 为了提供可靠的基础设施更新，Service Fabric 要求虚拟机规模集耐久性至少设置为银级。 这使基础虚拟机规模集和 Service Fabric 运行时能够提供可靠的更新。 此外，还需要每个区域至少具有 5 个 VM。 对于主节点和非主节点类型，我们正在努力将此要求降低到每个区域 3 和 2 个 VM。
* 下面提到的所有配置和即将进行的工作都提供了到客户的就地迁移，在其中可以通过添加新节点类型和停用旧节点类型来升级同一群集以使用新配置。

## <a name="networking-requirements"></a>网络要求

### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源

若要在虚拟机规模集资源上启用 `zones` 属性，该虚拟机规模集所引用的负载均衡器和 IP 资源必须都使用标准 SKU。 在未设置 SKU 属性的情况下创建负载均衡器或 IP 资源时会创建基本 SKU，而后者不支持可用性区域。 默认情况下，标准 SKU 负载均衡器会阻止来自外部的所有流量。 若要允许外部流量，请将 NSG 部署到子网。

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
```

```json
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
> 目前不能在公共 IP 和负载均衡器资源上就地更改 SKU。 如果要从具有基本 SKU 的现有资源进行迁移，请参阅本文的迁移部分。

### <a name="nat-rules-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 NAT 规则

负载均衡器的入站网络地址转换 (NAT) 规则应与虚拟机规模集中的 NAT 池匹配。 每个虚拟机规模集必须有一个唯一的入站 NAT 池。

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

### <a name="outbound-rules-for-a-standard-sku-load-balancer"></a>标准 SKU 负载均衡器的出站规则

与基本 SKU 相比，标准 SKU 负载均衡器和公共 IP 为出站连接引入了新的功能和不同的行为。 如果在使用标准 SKU 时需要出站连接，则必须使用标准 SKU 公共 IP 地址或标准 SKU 负载均衡器显式定义它。 有关详细信息，请参阅[出站连接](../load-balancer/load-balancer-outbound-connections.md)和[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
> 标准模板引用的 NSG 默认允许所有出站流量。 系统仅允许 Service Fabric 管理操作所需的端口上的入站流量。 你可以根据需要对 NSG 规则进行修改。

>[!IMPORTANT]
> Service Fabric 群集中使用标准 SKU 负载均衡器的每个节点类型都需要使用一条规则来允许端口 443 上的出站流量。 这是完成群集设置所必需的。 任何没有此规则的部署都将失败。


## <a name="1-preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>1.（预览版）在单个虚拟机规模集中启用多个可用性区域

此解决方案使用户可以在同一节点类型中跨越三个可用性区域。 这是建议的部署拓扑，因为它使你能够跨可用性区域进行部署，同时维护单个虚拟机规模集。

> [!NOTE]
> 由于此功能目前处于预览状态，因此暂不支持用于生产情况。

有关完整的示例模板，请参阅 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)。

![Azure Service Fabric 可用性区域体系结构图示。][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集上配置区域

若要在虚拟机规模集上启用区域，请在虚拟机规模集资源中包含以下三个值：

* 第一个值为 `zones` 属性，该属性指定虚拟机规模集中存在的可用性区域。
* 第二个值是 `singlePlacementGroup` 属性，该属性应设置为 `true`。 即使 `singlePlacementGroup = true`，跨三个可用性区域的规模集也可纵向扩展至 300 个 VM。
* 第三个值为 `zoneBalance`，该值可确保严格进行区域均衡。 该值应为 `true`。 它确保了 VM 跨区域均匀分布，这意味着当一个区域出现故障时，其他两个区域有足够的 VM 来保持群集运行。

  在一个区域出现故障的情况下，VM 分发不均衡的群集可能无法运行，因为该区域可能拥有大多数 VM。 VM 跨区域分发不均衡还会导致服务放置问题和基础结构更新停滞。 阅读有关 [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing) 的详细信息。

不需要配置 `FaultDomain` 和 `UpgradeDomain` 替代。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
>
> * Service Fabric 群集应至少有一个主节点类型。 主节点类型的持久性级别应为银级或更高级别。
> * 跨虚拟机规模集的可用性区域应至少配置三个可用性区域，无论其持久性级别如何。
> * 跨具有银级或更高级别持久性的虚拟机规模集的可用性区域应至少有 15 个 VM。
> * 跨具有铜级持久性的虚拟机规模集的可用性区域应至少有六个 VM。

### <a name="enable-support-for-multiple-zones-in-the-service-fabric-node-type"></a>启用对 Service Fabric 节点类型中多个区域的支持

必须启用 Service Fabric 节点类型才能支持多个可用性区域。

* 第一个值为 `multipleAvailabilityZones`，对于节点类型该值应设置为 `true`。
* 第二个值为 `sfZonalUpgradeMode`，该值为可选。 如果群集中已存在具有多个可用性区域的节点类型，则无法修改此属性。
  此属性控制升级域 (UD) 中虚拟机的逻辑分组。

  * 如果此值设置为 `Parallel`：节点类型下的 VM 将分组到 UD，并忽略五个 UD 中的区域信息。 此设置会导致所有区域中的 UD 同时升级。 此部署模式用于升级速度会更快，但不建议使用，因为它违反了 SDP 准则，该准则规定一次只能对一个区域应用更新。
  * 如果此值被省略或设置为 `Hierarchical`：VM 将分组以反映最多 15 个 UD 中的区域性分布。 三个区域各有五个 UD。 这样可以确保一次更新一个区域，仅在第一个区域内完成五个 UD 之后才移动到下一个区域。 此更新过程对于群集和用户应用程序更安全。

  此属性仅定义 Service Fabric 应用程序和代码升级的升级行为。 基础虚拟机规模集升级仍在所有可用性区域中并行进行。 此属性不影响未启用多个区域的节点类型的 UD 分布。
* 第三个值为 `vmssZonalUpgradeMode = Parallel`。 如果添加了具有多个可用性区域的节点类型，则此属性是必需的。 此属性定义同时发生在所有可用性区域中的虚拟机规模集更新的升级模式。

  目前，此属性只能设置为“并行”。

>[!IMPORTANT]
>Service Fabric 群集资源 API 版本应为 2020-12-01-preview 或更高版本。
>
>群集代码版本至少应为 8.1.321 或更高版本。

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
>
> * 公共 IP 和负载均衡器资源应使用本文前面所述的标准 SKU。
> * 节点类型上的 `multipleAvailabilityZones` 属性只能在创建节点类型时定义，且以后不能修改。 无法使用此属性配置现有的节点类型。
> * 如果 `sfZonalUpgradeMode` 被省略或设置为 `Hierarchical`，则群集和应用程序部署将会变慢，因为群集中存在更多升级域。 必须正确调整升级策略的超时，以考虑 15 个升级域所需的升级时间。 应更新应用程序和群集的升级策略，以确保部署不会超过 12 小时的 Azure 资源服务部署时间限制。 这意味着部署 15 个 UD 不应超过 12 小时（也就是说，每个 UD 不应超过 40 分钟）。
> * 将群集可靠性级别设置为 `Platinum`，以确保群集在一个区域出现故障的情况下能正常运行。

>[!TIP]
> 建议将 `sfZonalUpgradeMode` 设置为 `Hierarchical` 或将其省略。 部署将遵循 VM 的区域性分布并将影响少量副本或实例，使其更安全。
> 如果部署速度是优先考虑的事项，或者只有无状态工作负载在具有多个可用性区域的节点类型上运行，请使用设置为 `Parallel` 的 `sfZonalUpgradeMode`。 这会使 UD 遍历在所有可用性区域中并行发生。

### <a name="migrate-to-the-node-type-with-multiple-availability-zones"></a>迁移到具有多个可用性区域的节点类型

对于所有迁移方案，都需要添加支持多个可用性区域的新节点类型。 无法迁移现有节点类型以支持多个区域。
[纵向扩展 Service Fabric 群集主节点类型](./service-fabric-scale-up-primary-node-type.md)一文包括添加新节点类型和该新节点类型所需的其他资源（如 IP 和负载均衡器资源）的详细步骤。 本文还介绍了如何在将具有多个可用性区域的新节点类型添加到群集后停用现有节点类型。

* 从使用基本负载均衡器和 IP 资源的节点类型迁移：对于每个可用性区域有一个节点类型的解决方案，[下面的子部分](#migrate-to-availability-zones-from-a-cluster-by-using-a-basic-sku-load-balancer-and-a-basic-sku-ip)已描述了此过程。

  对于新的节点类型，唯一的区别是所有可用性区域只有一个虚拟机规模集和一个节点类型，而不是每个可用性区域各一个。
* 从使用标准 SKU 负载均衡器和带有 NSG 的 IP 资源的节点类型进行迁移：遵循前面描述的相同过程。 但是，无需添加新的负载均衡器、IP 和 NSG 资源。 可以在新节点类型中重复使用相同的资源。


## <a name="2-deploy-zones-by-pinning-one-virtual-machine-scale-set-to-each-zone"></a>2. 将一个虚拟机规模集固定到每个区域，以部署区域

这是目前提供的一般配置。
若要使 Service Fabric 群集跨越可用性区域，必须在该区域支持的每个可用性区域中创建一个主节点类型。 这会在每个主节点类型上平均分布种子节点。

建议用于主节点类型的拓扑需要以下内容：
* 三个标记为主节点的节点类型
  * 每个节点类型都应映射到其自己的位于不同区域的虚拟机规模集
  * 每个虚拟机规模集应至少具有五个节点（白银持续性）。

下图显示了 Azure Service Fabric 可用性区域体系结构：

![显示 Azure Service Fabric 可用性区域体系结构的图示。][sf-architecture]

### <a name="enable-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集上启用区域

若要在虚拟机规模集上启用区域，请在虚拟机规模集资源中包含以下三个值：

* 第一个值为 `zones` 属性，它指定虚拟机规模集部署到的可用性区域。
* 第二个值是 `singlePlacementGroup` 属性，该属性应设置为 `true`。
* 第三个值为 Service Fabric 虚拟机规模集扩展中的 `faultDomainOverride` 属性。 此属性应只包含放置此虚拟机规模集的区域。 示例：`"faultDomainOverride": "az1"`。 所有虚拟机规模集资源必须放置在同一区域，因为 Azure Service Fabric 群集没有跨区域支持。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
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
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enable-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>在 Service Fabric 群集资源中启用多个主节点类型

若要在群集资源中将一个或多个节点类型设置为无状态，请将 `isPrimary` 属性设置为 `true`。 跨可用性区域部署 Service Fabric 群集时，应该在不同的区域有三种节点类型。

```json
{
    "reliabilityLevel": "Platinum",
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
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-availability-zones-from-a-cluster-by-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>从使用基本 SKU 负载均衡器和基本 SKU IP 的群集迁移到可用性区域

若要迁移使用基本 SKU 负载均衡器和 IP 的群集，必须先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 目前无法更新这些资源。

在要使用的新跨可用性区域节点类型中引用新的负载均衡器和 IP。 在上一个示例中，区域 1、2 和 3 中添加了三个新的虚拟机规模集资源。 这些虚拟机规模集引用新创建的负载均衡器和 IP，并在 Service Fabric 群集资源中被标记为主节点类型。

1. 首先，将新资源添加到现有 Azure 资源管理器模板。 这些资源包括：

   * 使用标准 SKU 的公共 IP 资源
   * 使用标准 SKU 的负载均衡器资源
   * 由在其中部署虚拟机规模集的子网所引用的 NSG
   * 三个标记为主节点的节点类型
     * 每个节点类型都应映射到其自己的位于不同区域的虚拟机规模集
     * 每个虚拟机规模集应至少具有五个节点（白银持续性）。

   可在[示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到这些资源的示例。

   ```powershell
   New-AzureRmResourceGroupDeployment `
       -ResourceGroupName $ResourceGroupName `
       -TemplateFile $Template `
       -TemplateParameterFile $Parameters
   ```

1. 部署完资源后，便可以从原始群集禁用主节点类型中的节点。 禁用节点之后，系统服务将迁移到之前部署的新主节点类型。

   ```powershell
   Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
       -KeepAliveIntervalInSec 10 `
       -X509Credential `
       -ServerCertThumbprint $thumb  `
       -FindType FindByThumbprint `
       -FindValue $thumb `
       -StoreLocation CurrentUser `
       -StoreName My 

   Write-Host "Connected to cluster"

   $nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

   Write-Host "Disabling nodes..."
   foreach($name in $nodeNames) {
       Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
   }
   ```

1. 禁用全部节点之后，系统服务将在跨区域分布的主节点类型上运行。 然后，可以从群集中删除禁用的节点。 删除节点后，可以删除原始 IP、负载均衡器和虚拟机规模集资源。

   ```powershell
   foreach($name in $nodeNames){
       # Remove the node from the cluster
       Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
       Write-Host "Removed node state for node $name"
   }

   $scaleSetName="nt0"
   Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

   $lbname="LB-cluster-nt0"
   $oldPublicIpName="LBIP-cluster-0"
   $newPublicIpName="LBIP-cluster-1"

   Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
   Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
   ```

1. 然后，从已部署的资源管理器模板中删除对这些资源的引用。

1. 最后，更新 DNS 名称和公共 IP。

   ```powershell
   $oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
   $primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
   $primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

   Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
   Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

   $PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
   $PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
   $PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
   Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
 
   ```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
