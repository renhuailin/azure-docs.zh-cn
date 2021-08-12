---
title: 跨可用性区域部署 Service Fabric 托管群集
description: 了解如何跨可用性区域部署 Service Fabric 托管群集以及如何在 ARM 模板中进行配置。
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5278ea170e0a60907813b9a79b151dde44ab4a12
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111956777"
---
# <a name="deploy-a-service-fabric-managed-cluster-across-availability-zones"></a>跨可用性区域部署 Service Fabric 托管群集

Azure 中的可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是一个唯一的物理位置，在 Azure 区域中配备独立电源、冷却系统和网络。

Service Fabric 托管群集支持跨多个可用性区域的部署以提供区域复原。 此配置可确保关键系统服务和应用程序的高可用性，以防止单点故障。 Azure 可用性区域仅在选定区域中可用。 有关详细信息，请参阅 [Azure 可用性区域概述](../availability-zones/az-overview.md)。

>[!NOTE]
>可用性区域跨越仅适用于标准 SKU 群集。

现提供示例模板：[Service Fabric 跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommendations-for-zone-resilient-azure-service-fabric-managed-clusters"></a>区域复原 Azure Service Fabric 托管群集的建议
跨可用性区域分布的 Service Fabric 群集可确保群集状态的高可用性。 

建议的托管群集拓扑需要下面概述的资源：

* 群集 SKU 必须为标准
* 主节点类型应该至少有九个节点才能获得最佳复原能力，但支持最少六个节点。
* 辅助节点类型应该至少有六个节点才能获得最佳复原能力，但支持最少三个节点。

>[!NOTE]
>仅支持 3 个可用性区域部署。

>[!NOTE]
> 不能将托管群集的就地更改从非跨区群集改为跨区群集。

显示 Azure Service Fabric 可用性区域体系结构的图示 ![Azure Service Fabric 可用性区域体系结构][sf-multi-az-arch]

描述跨区域虚拟机规模集的 FD/UD 格式的示例节点列表

 ![描述跨区域虚拟机规模集的 FD/UD 格式的示例节点列表。][sfmc-multi-az-nodes]

跨区域分发服务副本：在跨区域的节点类型上部署服务时，会放置副本以确保它们分别驻留在不同的区域中。 可以确保这一点，因为每个节点类型中存在的节点上的容错域都配置了区域信息（例如 FD = fd:/zone1/1 等）。 例如：对于 5 个副本或服务的实例，分发将为 2-2-1，运行时将尝试确保跨 AZ 的分布情况相等。

**用户服务副本配置**：在跨可用性区域节点类型上部署的有状态用户服务应进行以下配置：副本计数目标 = 9，最小 = 5。 即使一个区域出现故障，此配置也会帮助该服务正常工作，因为其他两个区域中仍有 6 个副本。 在这种情况下，应用程序也会升级。

**区域故障情况**：当某个区域出现故障时，该区域中的所有节点都将显示为故障。 这些节点上的服务副本也会出现故障。 由于其他区域中有副本，服务将继续响应，将主要副本故障转移到正常运行的区域。 服务将显示处于警告状态，因为未达到目标副本计数，并且 VM 计数仍大于定义的最小目标副本大小。 因此，Service Fabric 负载均衡器将打开正在工作的区域中的副本，以匹配配置的目标副本计数。 此时，服务将显示运行正常。 如果出现故障的区域恢复正常，负载均衡器会再次将所有服务副本均匀分布到所有区域。

## <a name="networking-configuration"></a>网络配置
有关详细信息，请参阅[配置 Service Fabric 托管群集的网络设置](./how-to-managed-cluster-networking.md)

## <a name="enabling-a-zone-resilient-azure-service-fabric-managed-cluster"></a>启用区域复原 Azure Service Fabric 托管群集
若要启用区域复原 Azure Service Fabric 托管群集，你必须将下列各项纳入托管群集资源定义中。

* ZonalResiliency 属性，用于指定群集是否为区域复原。

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters",
    "ZonalResiliency": "true"
    
}
```
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sfmc-multi-az-nodes]: ./media/how-to-managed-cluster-availability-zones/sfmc-multi-az-nodes.png