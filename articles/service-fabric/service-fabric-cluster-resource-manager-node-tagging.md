---
title: Azure Service Fabric 动态节点标记
description: Azure Service Fabric 允许动态添加和删除节点标记。
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741122"
---
# <a name="introduction-to-dynamic-node-tags"></a>动态节点标记简介
节点标记使你可以在节点中动态添加和删除标记，以便影响服务的放置。 节点标记非常灵活，可以在不进行应用程序或群集升级的情况下更改服务放置。 可随时在节点中添加或删除标记，并且服务可以在创建特定标记时为它们指定要求。 服务还可以在运行时动态更新其标记要求。

节点标记类似于[放置约束](service-fabric-cluster-resource-manager-configure-services.md)，通常用于控制服务在哪些节点上运行。 每个 Service Fabric 服务都可以配置为要求放置标记或保持运行状态。

所有 Service Fabric 托管服务类型（Reliable Services、来宾可执行文件和容器）都支持节点标记。 若要使用节点标记，需要运行 Service Fabric 运行时 8.0 或更高版本。

本文的其余部分介绍启用或禁用节点标记的方式，并举例说明如何使用此功能。


## <a name="describing-dynamic-node-tags"></a>介绍动态节点标记
服务可以指定所需的标记。 有两种类型的标记：
* 放置所需的标记描述只有服务放置才需要的一组标记。 放置副本后，可以在不中断服务的情况下删除这些标记。 如果从节点中删除了这些标记中的任何标记，服务副本会保持正常运行，Service Fabric 不会删除服务

* 运行所需的标记描述放置和运行服务所需的一组标记。 如果删除了任何所需运行标记，Service Fabric 会将服务移动到指定了这些标记的另一个节点。

示例：在使用某种类型的容器激活器服务时，可以利用放置所需标记，你需要放置容器的该服务，一旦激活容器，便不再需要激活器，可以删除与之关联的标记，但容器应保持运行状态。
如果你有计费服务，则可以使用运行所需标记，这对于与面向用户的服务并置非常有用。 当节点上的计费服务失败时，你会删除与之关联的标记，面向用户的服务会移动到另一个具有计费服务及其标记的节点。

可以使用标准 Service Fabric 接口机制（如 C# API、REST API 或 PowerShell 命令）在单个节点中添加、更新或删除一个标记或一组标记。

> [!NOTE]
> 使用节点标记时，Service Fabric 不会维持 UD/FD 分发。 请相应地管理节点标记，以便不会因为跨域错误地分发标记而导致 FD/UD 冲突。

## <a name="enabling-dynamic-node-tags"></a>启用动态节点标记
若要使此功能正常工作，需要使用 XML 或 JSON 在群集清单的 PlacementAndLoadBalancing 节中启用 NodeTaggingEnabled 配置：

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

通过用于独立部署的 ClusterConfig.json 或用于 Azure 托管群集的 Template.json：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>设置动态节点标记

### <a name="using-powershell"></a>使用 PowerShell

向节点添加节点标记：

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
此命令在节点 DB.1 上添加标记“SampleTag1”和“SampleTag2”。

从节点中删除节点标记：

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
此命令在节点 DB.1 上删除标记“SampleTag1”和“SampleTag2”。

### <a name="using-c-apis"></a>使用 C# API

向节点添加节点标记：

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

从节点中删除节点标记：

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>为服务设置所需标记

### <a name="using-powershell"></a>使用 PowerShell

创建新服务：

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
此命令会创建一个服务，该服务要求“SampleTag2”存在于某个节点上以便在其中放置服务，并要求“SampleTag1”存在以便使服务在该节点上继续运行。

更新现有服务：

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
此命令会更新一个服务，该服务要求“SampleTag2”存在于某个节点上以便在其中放置服务，并要求“SampleTag1”存在以便使服务在该节点上继续运行。

### <a name="using-c-apis"></a>使用 C# API

创建新服务：

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

更新现有服务：

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

所有这些命令都同样适用于无状态服务。

## <a name="next-steps"></a>后续步骤
了解有关[放置约束](service-fabric-cluster-resource-manager-configure-services.md)的详细信息
