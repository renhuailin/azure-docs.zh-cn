---
title: AKS 业务连续性和灾难恢复的最佳做法
description: 了解群集操作员的最佳做法，以最大程度实现应用程序的正常运行时间，提供高可用性，并为 Azure Kubernetes 服务 (AKS) 中的灾难恢复情况做好准备。
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: eda7816050aedc0ff910d975f920d79771f7d0b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461615"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，应用程序的正常运行时间变得非常重要。 默认情况下，AKS 通过在[虚拟机规模集 (VMSS)](../virtual-machine-scale-sets/overview.md) 中使用多个节点来提供高可用性。 但是，这些节点不能避免系统受到区域故障的影响。 为了最大化正常运行时间，请提前规划以维持业务连续性并为灾难恢复做好准备。

本文重点介绍如何在 AKS 中规划业务连续性和灾难恢复。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 在多个区域规划 AKS 群集。
> * 使用 Azure 流量管理器跨多个群集路由流量。
> * 为容器映像注册表使用异地复制。
> * 跨多个群集计划应用程序状态。
> * 跨多个区域复制存储。

## <a name="plan-for-multiregion-deployment"></a>规划多区域部署

> 最佳做法
>
> 部署多个 AKS 群集时，选择 AKS 可用的区域。 使用配对区域。

一个 AKS 群集部署到单个区域中。 为避免系统受到区域故障的影响，可以跨不同区域将应用程序部署到多个 AKS 群集中。 规划 AKS 群集的部署位置时，请考虑：

* [AKS 区域可用性](./quotas-skus-regions.md#region-availability)
    * 选择靠近用户的区域。 
    * AKS 不断向新区域扩展。
* [**Azure 配对区域**](../best-practices-availability-paired-regions.md)
    * 对于地理区域，请选择两个相互配对的区域。
    * AKS 平台更新（计划内维护）进行序列化时，在配对区域之间至少有 24 小时的延迟。 
    * 根据需要设置配对区域的恢复工作的优先级。 
* **服务可用性**
    * 确定配对区域应采用热/热、热/暖还是热/冷配置。
    * 是否要同时运行两个区域，其中一个区域已准备好开始提供流量？ 或者，
    * 是否要运行一个区域，以便有时间来准备好提供流量？

AKS 区域可用性和配对区域是共同考虑的因素。 将 AKS 群集部署到配对区域中，这些区域旨在一起管理区域灾难恢复。 例如，AKS 现已在美国东部和美国西部推出。 这些区域是配对的。 创建 AKS BC/DR 策略时，请选择这两个区域。

在部署应用程序时，请向 CI/CD 管道添加另一个步骤以部署到这些 AKS 群集中。 更新部署管道会阻止应用程序仅部署到其中一个区域和 AKS 群集。 在这种情况下，定向到次要区域的客户流量将接收不到最新代码更新。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

> 最佳做法
>
> Azure 流量管理器可以将你定向到最近的 AKS 群集和应用程序实例。 为获得最佳性能和冗余，在进入 AKS 群集之前，通过流量管理器来定向所有应用程序流量。

如果在不同的区域中创建了多个 AKS 群集，请使用流量管理器控制传送到每个群集中运行的应用程序的流量。 [Azure 流量管理器](../traffic-manager/index.yml)是可以在区域间分布网络流量的基于 DNS 的流量负载均衡器。 使用流量管理器根据群集响应时间或地理位置路由用户。

![将 AKS 与流量管理器配合使用](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

如果只有一个 AKS 群集，通常需要连接到给定应用程序的服务 IP 或 DNS 名称。 在多群集部署中，应连接到指向每个 AKS 群集上的服务的流量管理器 DNS 名称。 使用流量管理器终结点定义这些服务。 每个终结点都是服务负载均衡器 IP。 使用此配置可将网络流量从一个区域的流量管理器终结点定向到另一个区域的终结点。

![通过流量管理器进行地理路由](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

流量管理器执行 DNS 查找，并返回最适当的终结点。 嵌套的配置文件可为主位置指定优先级。 例如，应连接到最近的地理区域。 如果该区域有问题，流量管理器会将你定向到次要区域。 此方法确保可以连接到应用程序实例，即使最近的地理区域不可用。

有关如何设置终结点和路由的信息，请参阅[使用流量管理器配置地理流量路由方法](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)。

### <a name="application-routing-with-azure-front-door-service"></a>应用程序路由与 Azure Front Door 服务

使用基于拆分 TCP 的任意播协议，[Azure Front Door 服务](../frontdoor/front-door-overview.md)可迅速将你的最终用户连接到最近的 Front Door POP（接入点）。 Azure Front Door 服务的更多功能：
* TLS 终止
* 自定义域
* Web 应用程序防火墙
* URL 重写
* 会话相关性 

查看应用程序流量的需求，以了解哪种解决方案是最合适。

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>使用虚拟网络对等互连将区域互连

通过[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)将两个虚拟网络互相连接，以启用群集之间的通信。 虚拟网络对等互连可以将虚拟网络互连，从而在 Microsoft 的主干网络中提供高带宽，甚至可以在不同地理区域中实现这一点。

将正在运行 AKS 群集的虚拟网络对等互连之前，请在 AKS 群集中使用标准负载均衡器。 此先决条件使 Kubernetes 服务可通过虚拟网络对等互连进行访问。

## <a name="enable-geo-replication-for-container-images"></a>为容器映像启用异地复制

> 最佳做法
> 
> 将容器映像存储在 Azure 容器注册表，并将注册表异地复制到每个 AKS 区域。

若要在 AKS 中部署和运行应用程序，需要一种方法来存储和提取容器映像。 容器注册表与 AKS 集成，因此可以安全存储容器映像或 Helm 图表。 容器注册表支持多主数据库异地复制来自动将映像复制到世界各地的 Azure 区域。 

提高性能和可用性：
1. 使用容器注册表异地复制在拥有 AKS 群集的每个区域中创建一个注册表。 
1. 然后每个 AKS 群集将从同一区域的本地容器注册表中拉取容器映像：

![用于容器映像的容器注册表异地复制](media/operator-best-practices-bc-dr/acr-geo-replication.png)

使用容器注册表异地复制从同一区域中提取映像可获得以下优势：

* **更快速**：可以从同一个 Azure 区域内的高速、低延迟网络连接中提取映像。
* **更可靠**：如果一个区域不可用，AKS 群集将从可用的容器注册表提取映像。
* **更经济实惠**：数据中心之间没有任何网络出口费用。

异地复制是高级 SKU 容器注册表的一项功能。 有关如何配置异地复制的信息，请参阅[容器注册表异地复制](../container-registry/container-registry-geo-replication.md)。

## <a name="remove-service-state-from-inside-containers"></a>从容器内删除服务状态

> 最佳做法
> 
> 避免在容器中存储服务状态。 请改用支持多区域复制的 Azure 平台即服务 (PaaS)。

服务状态指的是服务正常运行所需的内存中数据或磁盘上数据。 状态包括服务读取和写入的数据结构和成员变量。 状态可能还包括存储在磁盘上的文件或其他资源，具体取决于服务的体系结构。 例如，状态可能包括数据库用来存储数据和事务日志的文件。

状态可以外部化或与操作状态的代码共存。 通常，你会使用一个数据库或其他数据存储（在网络中不同计算机上运行或同一计算机进程外部运行）来实现状态的外部化。

当容器和微服务中运行的进程不保持状态时，它们最有弹性。 由于应用程序几乎始终包含某种状态，因此请使用 PaaS 解决方案，如：
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL 数据库

如何构建可移植的应用程序，请参阅以下指导原则：

* [12 因素应用方法](https://12factor.net/)
* [在多个 Azure 区域中运行 Web 应用程序](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>创建存储迁移计划

> 最佳做法
>
> 如果使用 Azure 存储，准备和测试如何将存储从主要区域迁移到备份区域。

应用程序可能会为其数据使用 Azure 存储。 如果是这样，应用程序将分布在不同区域的多个 AKS 群集中。 需要保持存储同步。 下面是复制存储的两种常用方法：

* 基于基础结构的异步复制
* 基于应用程序的异步复制

### <a name="infrastructure-based-asynchronous-replication"></a>基于基础结构的异步复制

即使删除了 pod，应用程序也可能需要持久存储。 在 Kubernetes 中，可以使用持久性卷来持久保存数据存储。 持久性卷会装载到节点 VM，然后公开给 pod。 持久性卷遵循 pod，即使 pod 被移动到同一群集内的其他节点也是如此。

使用的复制策略取决于存储解决方案。 以下常见存储解决方案在灾难恢复和复制方面都提供了自身的指导：
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

通常是提供一个通用存储点，应用程序可在其中写入数据。 然后跨区域复制此数据，并在本地进行访问。

![基于基础结构的异步复制](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

如果使用 Azure 托管磁盘，则可以使用 [Azure 上的 Velero][velero] 和 [Kasten][kasten] 来处理复制和灾难恢复。 这些选项是 Kubernetes 的本地备份解决方案，但不受支持。

### <a name="application-based-asynchronous-replication"></a>基于应用程序的异步复制

目前，Kubernetes 不会针对基于应用程序的异步复制提供本机实现。 由于容器和 Kubernetes 是松散耦合的，因此任何传统应用程序或语言方法都应适用。 通常，应用程序本身会复制存储请求，然后将这些存储请求写入每个群集的基础数据存储中。

![基于应用程序的异步复制](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>后续步骤

本文重点介绍了 AKS 群集的业务连续性和灾难恢复注意事项。 有关 AKS 中的群集操作的详细信息，请参阅以下文章中的最佳做法：

* [多租户和群集隔离][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/
