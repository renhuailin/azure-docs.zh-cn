---
title: 跨可用性区域部署群集
description: 了解如何跨可用性区域创建 Azure Service Fabric 群集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 95ee4e5f326dd9b76645d22ff735bc36437c72fb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870104"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性区域部署 Azure Service Fabric 群集
Azure 中的可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是一个唯一的物理位置，在 Azure 区域中配备独立电源、冷却系统和网络。

Service Fabric 通过部署固定到特定区域的节点类型，支持跨可用性区域的群集。 这将确保应用程序的高可用性。 Azure 可用性区域仅在选定区域中可用。 有关详细信息，请参阅 [Azure 可用性区域概述](../availability-zones/az-overview.md)。

现提供示例模板：[Service Fabric 跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性区域的 Azure Service Fabric 群集的主节点类型的建议拓扑
跨可用性区域分布的 Service Fabric 群集可确保群集状态的高可用性。 若要使 Service Fabric 群集跨区域，必须在该区域支持的每个可用性区域中创建一个主节点类型。 这会在每个主节点类型上平均分布种子节点。

建议用于主节点类型的拓扑需要下述资源：

* 设置为“白金”的群集可靠性级别。
* 三个标记为主节点的节点类型。
    * 每个节点类型都应映射到其自己的位于不同区域的虚拟机规模集。
    * 每个虚拟机规模集应至少具有五个节点（白银持续性）。
* 单个使用标准 SKU 的公共 IP 资源。
* 单个使用标准 SKU 的负载均衡器资源。
* 由在其中部署虚拟机规模集的子网所引用的 NSG。

>[!NOTE]
> 虚拟机规模集单个放置组属性必须设置为 true，因为 Service Fabric 不支持跨区域的单个虚拟机规模集。

显示 Azure Service Fabric 可用性区域体系结构的图 ![显示 Azure Service Fabric 可用性区域体系结构的图。][sf-architecture]

描述跨区域虚拟机规模集的 FD/UD 格式的示例节点列表

 ![描述跨区域虚拟机规模集的 FD/UD 格式的示例节点列表。][sf-multi-az-nodes]

**跨区域分发服务副本**：在跨区域的节点类型上部署服务时，会放置副本以确保它们驻留在不同的区域中。 可以确保这一点，因为每个节点类型中存在的节点上的容错域都配置了区域信息（例如 FD = fd:/zone1/1 等）。 例如：对于服务的 5 个副本或实例，分发比例将为 2-2-1，并且运行时将尝试确保跨可用性区域的分发情况相等。

**用户服务副本配置**：在跨可用性区域节点类型上部署的有状态用户服务应进行以下配置：副本计数目标 = 9，最小 = 5。 即使一个区域出现故障，此配置也会帮助该服务正常工作，因为其他两个区域中仍有 6 个副本。 在这种情况下，应用程序也会升级。

**群集 ReliabilityLevel**：定义群集中种子节点的数目，以及系统服务的副本大小。 由于跨可用性区域设置具有更多的节点，这些节点分布在各个区域中以实现区域复原能力，因此，更高的可靠性值将确保节点存在更多的种子节点和系统服务副本，并且均匀分布在各个区域中，这样，在发生区域故障时，群集和系统服务仍然不受影响。 “ReliabilityLevel = Platinum”将确保有 9 个种子节点分布在群集中的各个区域之间，并且每个区域 3 个种子节点，因此，这是建议的跨可用性区域设置。

**区域故障情况**：当某个区域出现故障时，该区域中的所有节点都将显示为故障。 这些节点上的服务副本也会出现故障。 由于其他区域中有副本，服务将继续响应，将主要副本故障转移到正常运行的区域。 服务将显示处于警告状态，因为尚未达到目标副本计数，并且 VM 计数仍大于最小目标副本大小。 接下来，Service Fabric 负载均衡器将打开正在工作的区域中的副本，以匹配配置的目标副本计数。 此时，服务将显示运行正常。 如果出现故障的区域恢复正常，负载均衡器会再次将所有服务副本均匀分布到所有区域。

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
若要在虚拟机规模集资源上启用 zones 属性，该虚拟机规模集所引用的负载均衡器和 IP 资源必须都使用“标准”SKU。 在未设置 SKU 属性的情况下创建负载均衡器或 IP 资源时将创建基本 SKU，而后者不支持可用性区域。 默认情况下，标准 SKU 负载均衡器会阻止外部的所有流量；若要允许外部流量，必须将 NSG 部署到子网。

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
若要在虚拟机规模集上启用区域，必须在虚拟机规模集资源中包含以下三个值。

* 第一个值为 **zones** 属性，它指定虚拟机规模集将部署到的可用性区域。
* 第二个值为“singlePlacementGroup”属性，该属性必须设置为 true。
* 第三个值为 Service Fabric 虚拟机规模集扩展中的“faultDomainOverride”属性。 此属性的值应只包含放置此虚拟机规模集的区域。 例如："faultDomainOverride": "az1"所有虚拟机规模集资源必须放置在同一区域，因为 Azure Service Fabric 群集没有跨区域支持。

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
若要在群集资源中将一个或多个节点类型设置为主节点，请将“isPrimary”属性设置为“true”。 跨可用性区域部署 Service Fabric 群集时，应该在不同的区域有三种节点类型。

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>从使用基本 SKU 负载均衡器和基本 SKU IP 的群集迁移为使用可用性区域
若要迁移使用基本 SKU 负载均衡器和 IP 的群集，必须先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 目前无法就地更新这些资源。

应在要使用的新的跨可用性区域节点类型中引用新的 LB 和 IP。 在上面的示例中，在区域 1、2 和 3 中添加了三个新的虚拟机规模集资源。 这些虚拟机规模集引用新创建的 LB 和 IP，并在 Service Fabric 群集资源中被标记为主节点类型。

首先，需要将新资源添加到现有资源管理器模板。 这些资源包括：
* 使用标准 SKU 的公共 IP 资源。
* 使用标准 SKU 的负载均衡器资源。
* 由在其中部署虚拟机规模集的子网所引用的 NSG。
* 三个标记为主节点的节点类型。
    * 每个节点类型都应映射到其自己的位于不同区域的虚拟机规模集。
    * 每个虚拟机规模集应至少具有五个节点（白银持续性）。

可在[示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到这些资源的示例。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

部署完资源后，就可以开始从原始群集禁用主节点类型中的节点。 因为节点被禁用，所以系统服务将迁移到前面步骤中部署的新的主节点类型。

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

节点全部禁用后，系统服务将在跨区域分布的主节点类型上运行。 然后，可以从群集中删除禁用的节点。 删除节点后，可以删除原始 IP、负载均衡器和虚拟机规模集资源。

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>（预览版）在单个虚拟机规模集中启用多个可用性区域

在前面提到的解决方案中，每个可用性区域使用一个节点类型。 以下解决方案允许用户在同一个节点类型中部署 3 个可用性区域。

[此处](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)提供了完整的示例模板。

![Azure Service Fabric 可用性区域体系结构][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集上配置区域
若要在虚拟机规模集上启用区域，必须在虚拟机规模集资源中包含以下三个值。

* 第一个值为 **zones** 属性，该属性指定虚拟机规模集中存在的可用性区域。
* 第二个值为“singlePlacementGroup”属性，该属性必须设置为 true。 **即使“singlePlacementGroup = true”，跨 3 个可用性区域的规模集也可扩展到多达 300 个虚拟机。**
* 第三个值为“zoneBalance”，该值可确保严格进行区域平衡。 此值应为“true”。 这可确保跨区域的虚拟机分布均衡，从而确保其中一个区域出现故障时，其他两个区域具有足够的虚拟机以确保群集保持不间断运行。 在一个区域出现故障的情况下，虚拟机分发不均衡的群集可能无法运行，因为该区域可能拥有大多数虚拟机。 虚拟机跨区域分发不均衡还会导致与服务放置相关的问题和基础结构更新停滞。 了解 [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)。
* 不需要配置 FaultDomain 和 UpgradeDomain 替代。

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
> * **Service Fabric 群集应至少有一个主节点类型。主节点类型的 DurabilityLevel 应为“白银”或以上。**
> * 不管 DurabilityLevel 是什么，跨可用性区域的虚拟机规模集应至少配置 3 个可用性区域。
> * 具有白银（或以上）持续性的跨可用性区域的虚拟机规模集应至少具有 15 个虚拟机。
> * 具有铜级持续性的跨可用性区域的虚拟机规模集应至少具有 6 个虚拟机。

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>启用对 Service Fabric 节点类型中多个区域的支持
必须启用 Service Fabric 节点类型才能支持多个可用性区域。

* 第一个值为 **multipleAvailabilityZones**，该值对于节点类型应设置为 true。
* 第二个值为 **sfZonalUpgradeMode**，该值为可选。 如果群集中已存在具有多个可用性区域的节点类型，则无法修改此属性。
  此属性控制升级域中虚拟机的逻辑分组。
  **如果值设置为“并行”：** 节点类型下的虚拟机将在 UD 中分组，忽略 5 个 UD 中的区域信息。 这将导致所有区域中的 UD0 同时升级。 此部署模式用于升级速度更快，但不建议使用，因为它违反了 SDP 准则，该准则规定一次只能对一个区域应用更新。
  **如果值被省略或设置为“分层”：** 虚拟机将分组，以反映最多 15 个 UD 中的区域性分布。 3 个区域都各有 5 个 UD。 这可确保更新按区域进行，即仅在完成第一个区域内的 5 个 UD 后，才移至下一个区域，缓慢跨越 15 个 UD（3 个区域，5 个 UD），这从群集和用户应用程序的角度来说更安全。
  此属性仅定义 ServiceFabric 应用程序和代码升级的升级行为。 底层虚拟机规模集升级仍将在所有可用性区域中并行进行。
  对于未启用多个区域的节点类型，此属性不会对 UD 分布产生任何影响。
* 第三个值为 **vmssZonalUpgradeMode = Parallel**。 如果添加了具有多个可用性区域的节点类型，则“必须”在群集中配置此属性。 此属性定义虚拟机规模集更新的升级模式，更新将同时在所有可用性区域中并行进行。
  现在，此属性只能设置为“并行”。
* Service Fabric 群集资源 apiVersion 应为“2020-12-01-preview”或更高版本。
* 群集代码版本应为“7.2.445”或更高版本。

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
> * 公共 IP 和负载均衡器资源应使用本文前面所述的标准 SKU。
> * 节点类型上的“multipleAvailabilityZones”属性只能在创建节点类型时定义，以后不能修改。 因此，无法使用此属性配置现有的节点类型。
> * 如果“sfZonalUpgradeMode”被省略或设置为“分层”，则群集和应用程序部署将会变慢，因为群集中存在更多的升级域。 必须正确调整升级策略的超时，以合并 15 个升级域的升级时间持续时间。 应更新应用和群集的升级策略，以确保部署不会超过 12 小时的 Azure 资源 Serbice 部署超时。 这意味着，15 个 UD 的部署应不超过 12 小时，即 每个 UD 不应超过 40 分钟。
> * 设置群集 **reliabilityLevel = Platinum**，以确保群集在一个区域出现故障的情况下能正常运行。

>[!NOTE]
> 作为最佳做法，建议将 sfZonalUpgradeMode 设置为“分层”或将其省略。 部署将遵循影响少量副本和/或实例的虚拟机的区域性分发，使其更安全。
> 如果优先考虑部署速度，或仅在具有多个可用性区域的节点类型上运行无状态工作负载，请将 sfZonalUpgradeMode 设置为“并行”。 这将导致 UD 在所有可用性区域中并行进行。

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>迁移到具有多个可用性区域的节点类型
对于所有迁移方案，需要添加支持多个可用性区域的新的节点类型。 无法迁移现有节点类型以支持多个区域。
[此处](./service-fabric-scale-up-primary-node-type.md)的文章介绍了添加新节点类型的详细步骤，以及添加新节点类型所需的其他资源（如 IP 和 LB 资源）的详细步骤。 本文还介绍了如何在将具有多个可用性区域的节点类型添加到群集后停用现有节点类型。

* 从使用基本 LB 和 IP 资源的节点类型迁移：[此处](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip)每个可用性区域一个节点类型的解决方案中已说明了此过程。 
    对于新的节点类型，唯一的区别在于，所有可用性区域都只有 1 个虚拟机规模集和 1 个节点类型，而不是每个可用性区域有 1 个。
* 从使用标准 SKU LB 和 IP 资源及 NSG 的节点类型迁移：遵循上述相同的过程，但不需要添加新的 LB、IP 和 NSG 资源，在新节点类型中可重复使用相同的资源。


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png