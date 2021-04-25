---
title: 群集隔离的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关 Azure Kubernetes 服务 (AKS) 中的隔离的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105164"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的群集隔离的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，通常需要隔离团队和工作负荷。 AKS 可让你灵活运行多租户群集和隔离资源。 为了最大化 Kubernetes 的投资回报，请先了解并实施 AKS 多租户和隔离功能。

本最佳做法文章向群集操作员重点介绍隔离。 在本文中，学习如何：

> [!div class="checklist"]
> * 规划多租户群集和资源隔离
> * 在 AKS 群集中使用逻辑或物理隔离

## <a name="design-clusters-for-multi-tenancy"></a>设计多租户群集

通过 Kubernetes 在同一群集中以逻辑方式隔离团队和工作负载。 目标是提供最少量的特权，特权范围限定为每个团队所需的资源。 Kubernetes [命名空间][k8s-namespaces]创建逻辑隔离边界。 其他 Kubernetes 功能以及有关隔离和多租户的注意事项包括以下几个方面：

### <a name="scheduling"></a>计划

“计划”使用资源配额和 Pod 中断预算等基本功能。 有关这些功能的详细信息，请参阅[有关 AKS 中基本计划程序功能的最佳做法][aks-best-practices-scheduler]。

更高级的计划程序功能包括：
* 排斥和容许
* 节点选择器
* 节点和 Pod 相关性和反相关性。 

有关这些功能的详细信息，请参阅[有关 AKS 中高级计划程序功能的最佳做法][aks-best-practices-advanced-scheduler]。

### <a name="networking"></a>网络

“网络”使用用于控制传入和传出 Pod 的流量流的网络策略。

### <a name="authentication-and-authorization"></a>身份验证和授权

身份验证和授权使用：
* 基于角色的访问控制 (RBAC)
* Active Directory (AD) 集成
* Pod 标识
* Azure Key Vault 中的机密 

有关这些功能的详细信息，请参阅[有关 AKS 中身份验证和授权的最佳做法][aks-best-practices-identity]。

### <a name="containers"></a>容器
容器包括：
* 适用于 AKS 的 Azure Policy 附加产品，用于强制执行 Pod 安全性。
* 使用 Pod 安全性上下文。
* 扫描映像和运行时中的漏洞。 
* 使用 App Armor 或 Seccomp（安全计算）来限制容器对基础节点的访问。

## <a name="logically-isolate-clusters"></a>逻辑隔离群集

> **最佳实践指南**
>
> 使用逻辑隔离分隔团队和项目。 减少要部署的物理 AKS 群集数，以隔离团队或应用程序。

使用逻辑隔离可将单个 AKS 群集用于多个工作负荷、团队或环境。 Kubernetes [命名空间][k8s-namespaces]构成了工作负荷和资源的逻辑隔离边界。

![AKS 中 Kubernetes 群集的逻辑隔离](media/operator-best-practices-cluster-isolation/logical-isolation.png)

群集的逻辑隔离提供的 Pod 密度通常比物理隔离的群集更高，而且群集中闲置的超额计算容量更少。 与 Kubernetes 群集自动缩放程序相结合，可根据需求增加或减少节点数目。 采用这种自动缩放最佳做法，可以通过只运行所需数目的节点来尽量降低成本。

目前，Kubernetes 环境并不完全安全，因为可能存在恶意的多租户使用情况。 在多租户环境中，多个租户使用公共的共享基础设施。 如果所有租户都不受信任，你将需要额外的计划来防止租户影响其他部分的安全性和服务。

其他安全性功能（如 Pod 安全策略或用于节点的 Kubernetes RBAC）可有效阻止攻击。 若要在运行恶意多租户工作负载时获得真正的安全性，应只信任虚拟机监控程序。 Kubernetes 的安全域成为整个群集，而不是单个节点。 

对于这些类型的恶意多租户工作负荷，应使用物理隔离的群集。

## <a name="physically-isolate-clusters"></a>物理隔离群集

> **最佳实践指南** 
>
> 对于每个独立的团队或应用程序部署，尽量减少使用物理隔离。 应改用上一部分所述的逻辑隔离。

物理隔离 AKS 群集是群集隔离的常用方法。 在此隔离模型中，将为团队或工作负荷分配其自身的 AKS 群集。 虽然物理隔离看上去是最简单的隔离工作负载或团队的方法，但会增加管理和财务开销。 现在必须维护多个群集，并单独提供访问权限和分配权限。 你还将为每个节点付费。

![AKS 中各个 Kubernetes 群集的物理隔离](media/operator-best-practices-cluster-isolation/physical-isolation.png)

物理上独立的群集的 pod 密度通常较低。 由于每个团队或工作负载具有自身的 AKS 群集，因此往往会为群集过度预配计算资源。 通常在这些节点上计划少量的 pod。 未声明的节点容量不可由其他团队用于开发中的应用程序或服务。 这些超额的资源会导致物理独立群集的成本增加。

## <a name="next-steps"></a>后续步骤

本文重点介绍了群集隔离。 有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]
* [高级 Kubernetes 计划程序功能][aks-best-practices-advanced-scheduler]
* [身份验证和授权][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
