---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的可持续软件工程
description: 了解 Azure Kubernetes 服务 (AKS) 中的可持续软件工程。
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 3fb40b278a4107cb1cdba2230602b99e2bdfff85
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355472"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的可持续软件工程原则

可持续软件工程原则是一组可帮助你定义、构建和运行可持续应用程序的能力。 总体目标是降低应用程序每个方面的碳足迹。 [可持续软件工程的原则][principles-sse]概述了可持续软件工程的原则。

可持续软件工程在优先级和重点方面有所转变。 在很多情况下，大多数软件的设计和运行方式强调了高性能和低延迟。 同时，可持续软件工程侧重于尽可能多地降低碳排放量。 请注意以下几点：

* 应用可持续软件工程原则可以提高性能或降低延迟，例如，降低总体网络行程就可以实现该目标。 
* 减少碳排放可能会导致性能降低或延迟增加，如延迟低优先级工作负荷。 

将可持续软件工程原则应用到应用程序之前，请查看应用程序的优先级、需求和利弊。

## <a name="measure-and-optimize"></a>度量和优化

若要降低 AKS 群集的碳足迹，需要了解群集资源的使用方式。 [Azure Monitor][azure-monitor] 提供有关群集资源使用情况的详细信息，例如内存和 CPU 使用率。 这些数据可以帮助你做出决定，以减少群集的碳足迹并观察所做更改的效果。 

你还可以安装 [Microsoft 可持续性计算器][sustainability-calculator]，以查看所有 Azure 资源的碳足迹。

## <a name="increase-resource-utilization"></a>提高资源利用率

降低碳足迹的一种方法是减少空闲时间。 缩短空闲时间涉及提高计算资源的利用率。 例如：
1. 群集中有四个节点，每个节点运行的容量为 50%。 因此，所有四个节点都有 50% 的未使用容量空闲。 
1. 将群集减为三个节点，每个节点都在相同工作负载下以 67% 的容量运行。 在每个节点上，可以成功地将未使用的容量减少到 33%，并提高利用率。

> [!IMPORTANT]
> 考虑更换群集中的资源时，请验证[系统池][system-pools]是否有足够的资源来维持群集的核心系统组件的稳定性。 不要将群集的资源减少到群集可能会变得不稳定的程度。

查看群集利用率之后，请考虑使用[多节点池][multiple-node-pools]提供的功能： 

* 节点大小调整

    可以使用[节点大小调整][node-sizing]通过特定的 CPU 和内存配置文件定义节点池，以便根据工作负载需求定制节点。 根据工作负载需求调整节点大小，用户可以在运行较少节点的情况下提高利用率。 

* 群集缩放

    配置群集的[缩放][scale]方式。 使用[横向 Pod 自动缩放程序][scale-horizontal]和[群集自动缩放程序][scale-auto]基于配置自动缩放群集。 控制群集的缩放方式可以让所有节点保持以高利用率运行，同时与对群集工作负载进行的更改保持同步。 

* 现成池

    对于可以承受突然中断或终止的工作负载的情况，可以使用[现成池][spot-pools]。 现成池利用 Azure 中的空闲容量。 例如，现成池可能很适合用于批处理作业或开发环境。

> [!NOTE]
>提高利用率还可以减少过多的节点，这可以减少[每个节点上的资源预留][resource-reservations]所消耗的能量。

最后，可在应用程序的 Kubernetes 清单中查看 CPU 和内存“请求”与“限制”。  
* 由于降低了内存和 CPU 的值，因此有更多的内存和 CPU 可供群集用来运行其他工作负载。 
* 由于以较少的 CPU 和内存运行较多的工作负载，因此可以更加密集地分配群集，从而提高利用率。 

降低应用程序的 CPU 和内存时，如果将 CPU 和内存值设置得过低，应用程序的行为可能会降级或变得不稳定。 在更改 CPU 和内存“请求”与“限制”之前，运行一些基准测试，以验证这些值的设置是否合适。  切勿将这些值减少到应用程序不稳定的程度。

## <a name="reduce-network-travel"></a>减少网络行程

通过减少往返群集的请求和响应行程距离，可以减少网络设备的碳排放和电力消耗。 查看网络流量后，请考虑在更靠近你的网络流量来源的[区域中][regions]创建群集。 可以使用 [Azure 流量管理器][azure-traffic-manager]将流量路由到最近的群集和[邻近放置组][proiximity-placement-groups]，以便缩短 Azure 资源之间的距离。

> [!IMPORTANT]
> 考虑对群集的网络进行更改时，切勿以必须满足的工作负荷要求为代价来减少网络行程。 例如，使用[可用性区域][availability-zones]会导致群集上的网络行程增多时，处理工作负载要求可能需要使用可用性区域。

## <a name="demand-shaping"></a>需求调整

如果可能，请考虑将对群集资源的需求转移到可以使用过量容量的时间或区域。 例如，请考虑：
* 更改要运行的批处理作业的时间或区域。
* 使用[现成池][spot-pools]。 
* 重构应用程序，以使用队列来延迟运行不需要立即处理的工作负载。

## <a name="next-steps"></a>后续步骤

详细了解本文中提到的 AKS 的功能：

* [多个节点池][multiple-node-pools]
* [节点大小调整][node-sizing]
* [缩放群集][scale]
* [水平 Pod 自动缩放程序][scale-horizontal]
* [群集自动缩放程序][scale-auto]
* [现成池][spot-pools]
* [系统池][system-pools]
* [资源预留][resource-reservations]
* [邻近放置组][proiximity-placement-groups]
* [可用性区域][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: /learn/modules/sustainable-software-engineering-overview/
