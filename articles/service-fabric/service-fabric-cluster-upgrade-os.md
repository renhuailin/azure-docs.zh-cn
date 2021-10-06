---
title: 升级适用于 Azure Service Fabric 的 Linux OS
description: 了解用于将 Azure Service Fabric 群集迁移到另一个 Linux OS 的选项
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 8f52481e7c457445dc842e86f7b05c3568502da4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278004"
---
# <a name="upgrading-linux-os-for-azure-service-fabric"></a>升级适用于 Azure Service Fabric 的 Linux OS

本文档提供有关将适用于 Linux 的 Azure Service Fabric 群集从 Ubuntu 版本 16.04 LTS 迁移到 18.04 LTS 的指南。 每个 OS（操作系统）版本都需要一个不同的 SF 运行时包，这需要执行本文档中所述的步骤来帮助顺利完成迁移。

## <a name="overview"></a>概述

一般方法是：

1. 将 Service Fabric 群集 ARM（Azure 资源管理器）资源“vmImage”切换到“Ubuntu18_04”，以拉取此 OS 版本的将来代码升级。 OS 与现有节点类型的这种暂时不匹配情况会导致无法通过自动代码升级部署来确保安全滚动更新。

    * 避免在 OS 迁移期间发出手动 SF 群集代码升级。 这样做可能导致旧类型的节点进入需要人工干预的状态。

2. 对于群集中的每个节点类型，为基础虚拟机规模集创建另一个以 Ubuntu 18.04 OS 映像为目标的节点类型。 每个新节点类型将充当其对应旧节点类型的角色。

    * 必须创建一个新的主节点类型来替换标记为 "isPrimary": true 的旧节点类型。
    
    * 对于其他每个非主节点类型，这些节点类型将类似地标记为 "isPrimary": false。

    * 确保在创建新的目标 OS 节点类型后，现有工作负载可继续正常运行。 如果发现了问题，请在继续删除旧节点类型之前解决应用或预装的计算机包中所需的更改。
3. 将旧的主节点类型标记为 "isPrimary": false。 这会导致通过一组长时间运行的升级来转换所有种子节点。
4. （仅适用于铜级持久性节点类型）：通过 [sfctl](service-fabric-sfctl.md) / [PowerShell](/powershell/module/ServiceFabric) / [FabricClient](/dotnet/api/system.fabric.fabricclient) 连接到群集，并禁用旧节点类型中的所有节点。
5. 删除旧节点类型。

> [!NOTE]
> Az PowerShell 为添加的节点类型生成新的 dns 名称，因此外部流量必须重定向到此终结点。


## <a name="ease-of-use-steps-for-non-production-clusters"></a>适用于非生产群集的易用步骤

> [!NOTE]
> 以下步骤演示如何在仅限测试的群集中通过 Az PowerShell cmdlet 快速生成节点类型迁移的原型。 对于面向实际业务流量的生产群集，应通过发出 ARM 升级来完成相同的步骤，以保持可重放性和一致的声明性事实来源。

1. 使用 [Update-AzServiceFabricVmImage](/powershell/module/az.servicefabric/update-azservicefabricvmimage) 更新 Service Fabric 群集资源上的 vmImage 设置：

    [Azure PowerShell](/powershell/azure/install-az-ps)：
    ```powershell
    # Replace subscriptionId, resourceGroup, clusterName with ones corresponding to your cluster.
    $subscriptionId="cea219db-0593-4b27-8bfa-a703332bf433"
    Login-AzAccount; Select-AzSubscription -SubscriptionId $subscriptionId

    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    # Update cluster vmImage to target OS. This registers the SF runtime package type that is supplied for upgrades.
    Update-AzServiceFabricVmImage -ResourceGroupName $resourceGroup -ClusterName $clusterName -VmImage Ubuntu18_04
    ```

2. 为每个现有节点类型添加新的节点类型对应项：

    ```powershell
    $nodeTypeName="nt1u18"
    # You can customize this to fetch a password from a secure store.
    $securePassword = ConvertTo-SecureString -String 'Yourpassword123!@#' -AsPlainText -Force

    # Ensure last upgrade is done - Ready means the next command can be issued.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Add new primary node type. Omit the IsPrimaryNodeType parameter for non-primary node types.
    Add-AzServiceFabricNodeType -ResourceGroupName $resourceGroup  -ClusterName $clusterName -NodeType $nodeTypeName -Capacity 5 -VmUserName testuser -VmPassword $securePassword -DurabilityLevel Silver -Verbose -VMImageSku 18.04-LTS -IsPrimaryNodeType $true

    # Redirect traffic to new node type dns
    # dns-Contoso01SFCluster-nt1u18.westus2.cloudapp.azure.com
    ```

3. 将旧的主节点类型更新为非主节点类型，以便将种子节点和系统服务滚动更新为新节点类型：

    ```powershell
    # Query to ensure background upgrades are done.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Update old nodetype to isPrimary: false
    $oldNodeTypeName="nt1"
    Update-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -IsPrimaryNodeType $false -NodeType $oldNodeTypeName -Verbose

    # Ensure node type is showing isPrimary: False before proceeding.
    Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup
    ```

    示例输出：
    ```
    NodeTypes :
              NodeTypeDescription :
                  Name : nt1
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Bronze
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : False
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
              NodeTypeDescription :
                  Name : nt1u18
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Silver
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : True
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
    ```

4. 若要删除铜级持久性节点类型，请先禁用节点，然后继续删除旧节点类型。 通过 ssh 连接到群集节点并运行以下命令：

    ```bash
    # as root user:

    # install jq tool to automatically parse JSON responses
    apt-get install jq -fy

    # retrieve the thumbprint to be used for establishing a fabric client
    dataroot=$(cat /etc/servicefabric/FabricDataRoot)
    nodename=_nt1_0
    cat $dataroot/$nodename/Fabric/ClusterManifest.current.xml | grep ClientCertThumbprints
    # 0777FE1A43E306F332D96DA339EF6834D0E4A453

    # verify node count
    sfctl cluster select --endpoint https://Contoso01SFCluster.westus2.cloudapp.azure.com:19080 --pem /var/lib/waagent/0777FE1A43E306F332D96DA339EF6834D0E4A453.pem --no-verify

    # sample command to list all nodes
    sfctl node list

    # for each node part of the node type to be removed, disable the node:
    nodeTypeBeingDisabled=nt1
    nodes=$(sfctl node list | jq --arg nodeTypeBeingDisabled "$nodeTypeBeingDisabled" '.items[] | select(.type==$nodeTypeBeingDisabled) | .name' | sed s/\"//g)
    echo $nodes
    for n in $nodes; do echo "Disabling $n"; sfctl node disable --node-name $n --deactivation-intent RemoveNode --timeout 300; done
    ```

5. 通过删除 SF 群集资源节点类型属性并解除分配关联的虚拟机规模集和网络资源，来删除旧节点类型。

    ```powershell
    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    $oldNodeTypeName="nt1"

    # Remove the Service Fabric node type, associated virtual machine scale set resource, and any trailing networking resources that are no longer used. 
    Remove-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -NodeType $oldNodeTypeName
    ```

    > [!NOTE]
    > 在某些情况下，此操作可能会遇到以下错误。 在这种情况下，你可能会通过 Service Fabric Explorer (SFX) 发现已删除的节点类型的 InfrastructureService 处于错误状态。 若要解决此问题，请重试删除。
    ```
    Remove-AzServiceFabricNodeType : Code: ClusterUpgradeFailed, Message: Long running operation failed with status 'Failed'
    ```

确认工作负载已成功迁移到新节点类型并且旧节点类型已清除后，可以在群集上继续进行后续的 Service Fabric 运行时代码版本和配置升级。

## <a name="next-steps"></a>后续步骤

* [管理 Service Fabric 升级](service-fabric-cluster-upgrade-version-azure.md)
* 自定义 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)
* 了解有关群集[持续性特征](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)的更多信息。
* 了解有关[节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)的更多信息。
* 了解有关 [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)的更多信息。
* [扩展和缩减群集](service-fabric-cluster-scale-in-out.md)
* [删除 Azure Service Fabric 中的节点类型](service-fabric-how-to-remove-node-type.md)

