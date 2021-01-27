---
title: 跨可用性区域部署群集
description: 了解如何跨可用性区域创建 Azure Service Fabric 群集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 3db31431c24edd3377f6299046cc31067310b2ef
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876204"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性区域部署 Azure Service Fabric 群集
Azure 中的可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 可用性区域是一种独特的物理位置，它在 Azure 区域内配有独立的电源、冷却和网络。

Service Fabric 通过部署固定到特定区域的节点类型来支持跨可用性区域的群集。 这将确保应用程序的高可用性。 Azure 可用性区域仅在选择区域中可用。 有关详细信息，请参阅 [Azure 可用性区域概述](../availability-zones/az-overview.md)。

示例模板可用： [Service Fabric 跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性区域的 Azure Service Fabric 群集的主节点类型的建议拓扑
分布在可用性区域上的 Service Fabric 群集可确保群集状态的高可用性。 若要跨区域跨 Service Fabric 群集，必须在该区域支持的每个可用性区域中创建主节点类型。 这会在每个主节点类型之间平均分配种子节点。

建议用于主节点类型的拓扑需要如下所述的资源：

* 群集可靠性级别设置为 "白金"。
* 三个标记为主节点的节点类型。
    * 每个节点类型都应映射到其自己的虚拟机规模集，位于不同的区域中。
    * 每个虚拟机规模集应具有至少五个节点 (银持续性) 。
* 使用标准 SKU 的单个公共 IP 资源。
* 使用标准 SKU 的单个负载均衡器资源。
* 由在其中部署虚拟机规模集的子网所引用的 NSG。

>[!NOTE]
> "虚拟机规模集单位置组" 属性必须设置为 "true"，因为 Service Fabric 不支持跨区域的单个虚拟机规模集。

 ![显示 Azure Service Fabric 可用性区域体系结构的关系图。][sf-architecture]

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
若要启用虚拟机规模集资源的 "区域" 属性，该虚拟机规模集所引用的负载均衡器和 IP 资源必须都使用 *标准* SKU。 如果不使用 SKU 属性创建负载平衡器或 IP 资源，将会创建不支持可用性区域的基本 SKU。 默认情况下，标准 SKU 负载均衡器会阻止外部的所有流量；若要允许外部流量，必须将 NSG 部署到子网。

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


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集上启用区域
若要在虚拟机规模集上启用区域，必须在虚拟机规模集资源中包含以下三个值：

* 第一个值是 **zone** 属性，它指定虚拟机规模集将部署到哪个可用性区域。
* 第二个值为 "singlePlacementGroup" 属性，该属性必须设置为 true。
* 第三个值是 Service Fabric 虚拟机规模集扩展中的 "faultDomainOverride" 属性。 此属性的值应该只包含将在其中放置此虚拟机规模集的区域。 例如： "faultDomainOverride"： "az1" 所有虚拟机规模集资源必须置于同一区域，因为 Azure Service Fabric 群集没有跨区域支持。

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>在 Service Fabric 群集资源中启用多个主节点类型
若要将一个或多个节点类型设置为群集资源中的主要节点类型，请将 "isPrimary" 属性设置为 "true"。 跨可用性区域部署 Service Fabric 群集时，在不同的区域应具有三种节点类型。

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>使用基本 SKU 负载均衡器和基本 SKU IP 从群集迁移到使用可用性区域
若要迁移使用基本 SKU 负载均衡器和 IP 的群集，必须先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 目前无法就地更新这些资源。

新的 LB 和 IP 应在你想要使用的新的跨可用性区域节点类型中进行引用。 在上面的示例中，在区域1、2和3中添加了三个新的虚拟机规模集资源。 这些虚拟机规模集引用新创建的 LB 和 IP，并标记为 Service Fabric 群集资源中的主节点类型。

首先，需要将新资源添加到现有资源管理器模板。 这些资源包括：
* 使用标准 SKU 的公共 IP 资源。
* 使用标准 SKU 的负载均衡器资源。
* 由在其中部署虚拟机规模集的子网所引用的 NSG。
* 三个标记为主节点的节点类型。
    * 每个节点类型都应映射到其自己的虚拟机规模集，位于不同的区域中。
    * 每个虚拟机规模集应具有至少五个节点 (银持续性) 。

可在 [示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到这些资源的示例。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

资源完成部署后，可以开始从原始群集中禁用主节点类型中的节点。 因为节点被禁用，所以系统服务将迁移到前面步骤中部署的新的主节点类型。

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

节点全部禁用后，系统服务将在跨区域的主节点类型上运行。 然后，可以从群集中删除禁用的节点。 删除节点后，可以删除原始 IP、负载均衡器和虚拟机规模集资源。

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

然后，应从已部署的资源管理器模板中删除对这些资源的引用。

最后一个步骤将涉及更新 DNS 名称和公共 IP。

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a> (预览) 在单个虚拟机规模集中启用多个可用性区域

前面提到的解决方案使用每 AZ 一个 nodeType。 以下解决方案允许用户在同一个 nodeType 中部署 3 AZ。

[此处](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)提供了完整的示例模板。

![Azure Service Fabric 可用性区域体系结构][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集上配置区域
若要在虚拟机规模集上启用区域，必须在虚拟机规模集资源中包含以下三个值。

* 第一个值为 **zones** 属性，该属性指定虚拟机规模集中存在的可用性区域。
* 第二个值为 "singlePlacementGroup" 属性，该属性必须设置为 true。 **即使 "singlePlacementGroup = true"，规模集跨越 3 AZ 的规模集也可扩展到最多300个 Vm。**
* 第三个值是 "zoneBalance"，如果设置为 true，则将确保严格区域平衡。 建议将此值设置为 true，以避免跨区域分布 Vm 不平衡。 阅读有关 [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)的信息。
* 不需要配置 FaultDomain 和 UpgradeDomain 重写。

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
> * **SF 群集应该至少有一个主节点。主要 nodeTypes 的 DurabilityLevel 应为银或更高版本。**
> * AZ 跨虚拟机规模集应配置至少3个可用性区域，而不考虑 durabilityLevel。
> * AZ 跨虚拟机规模集 (或更高) ，应至少具有15个 Vm。
> * AZ 跨虚拟机规模集，具有铜牌持久性，应至少包含6个 Vm。

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>在 Service Fabric nodeType 中启用多个区域的支持
必须启用 Service Fabric nodeType 才能支持多个可用性区域。

* 第一个值为 **multipleAvailabilityZones** ，它应设置为适用于 nodeType 的 true。
* 第二个值为 **sfZonalUpgradeMode** ，它是可选的。 如果群集中已存在具有多个 AZ 的 nodetype，则无法修改此属性。
      属性控制升级域中 Vm 的逻辑分组。
          如果 value 设置为 false (平面模式) ：节点类型下的 Vm 将按 UD 分组，忽略5个 UDs 中的区域信息。
          如果省略值或将值设置为 true (分层模式) ： Vm 将分组，以反映最多15个 UDs 的区域性分布。 3个区域中的每一个都有5个 UDs。
          此属性仅定义 ServiceFabric 应用程序和代码升级的升级行为。 底层虚拟机规模集升级仍将在所有 AZ 的中并行进行。
      对于未启用多个区域的节点类型，此属性不会产生任何影响。
* 第三个值为 **vmssZonalUpgradeMode = Parallel**。 如果添加了具有多个 AZs 的 nodeType，则这是在群集中配置的 *必需* 属性。 此属性定义虚拟机规模集更新的升级模式，这将同时在所有 AZ 中并行发生。
      现在，此属性只能设置为 "并行"。
* Service Fabric 群集资源 apiVersion 应为“2020-12-01-preview”或更高版本。
* 群集代码版本应为 "7.2.445" 或更高版本。

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
> * 公共 IP 和负载均衡器资源应使用标准 SKU，如本文前面所述。
> * 节点上的 "multipleAvailabilityZones" 属性只能在创建 nodeType 时定义，以后不能修改。 因此，无法用此属性配置现有的 nodeTypes。
> * 如果省略 "sfZonalUpgradeMode" 或将其设置为 "分层"，则群集和应用程序部署将会变慢，因为群集中存在更多的升级域。 必须正确调整升级策略的超时，以合并15个升级域的升级时间持续时间，这一点非常重要。
> * 建议将群集的可靠性级别设置为 "白金"，确保群集置。

>[!NOTE]
> 为获得最佳实践，建议将 sfZonalUpgradeMode 设置为 "分层" 或 "省略"。 部署将遵循影响少量副本和/或实例的 Vm 的区域分发，使其更安全。
> 如果在具有多个 AZ 的节点类型上运行部署速度是优先级别或只运行无状态工作负荷，请使用 sfZonalUpgradeMode 设置为 "并行"。 这将导致 UD 遍历全部在 AZ 的中发生。

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>迁移到具有多个可用性区域的节点类型
对于所有迁移方案，需要添加新的 nodeType，这将支持多个可用性区域。 无法迁移现有 nodeType 以支持多个区域。
[此处](./service-fabric-scale-up-primary-node-type.md)的文章将介绍添加新的 nodetype 的详细步骤，还会添加新节点（如 IP 和 LB 资源）所需的其他资源。 本文还介绍了如何在将具有多个可用性区域的 nodeType 添加到群集后停用现有 nodeType。

* 从使用基本 LB 和 IP 资源的 nodeType 进行迁移：此项已在 [此处](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) 针对每 AZ 具有一种节点类型的解决方案进行了说明。 
    对于新的节点类型，唯一的区别在于，对于所有 AZ，每个 AZ 都只有1个虚拟机规模集和1个 nodetype，而不是每个 AZ 都有1个。
* 从将标准 SKU LB 和 IP 资源与 NSG 结合使用的 nodeType 进行迁移：按照前面所述的相同过程操作，但不需要添加新的 LB、IP 和 NSG 资源，也可以在新的 nodeType 中重复使用相同的资源。


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png