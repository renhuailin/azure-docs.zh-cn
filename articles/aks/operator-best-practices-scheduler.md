---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的基本计划程序功能
description: 了解有关使用 Azure Kubernetes 服务 (AKS) 中的基本计划程序功能（例如资源配额和 pod 中断预算）的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 034b7e18d3114804f846d77aa4feb001492c8883
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467057"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的基本计划程序功能的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，通常需要隔离团队和工作负荷。 利用 Kubernetes 计划程序，你可以控制计算资源的分配，或限制维护事件造成的影响。

本最佳做法文章重点介绍面向群集操作员的基本 Kubernetes 计划功能。 在本文中，学习如何：

> [!div class="checklist"]
> * 使用资源配额向团队或工作负荷提供固定的资源量
> * 使用 pod 中断预算限制计划性维护造成的影响

## <a name="enforce-resource-quotas"></a>强制实施资源配额

> **最佳实践指南** 
> 
> 在命名空间级别规划和应用资源配额。 如果 pod 未定义资源请求和限制，则拒绝部署。 监视资源用量并根据需要调整配额。

在 pod 规范中放置资源请求和限制。 在部署时，Kubernetes 计划程序使用限制在群集中查找可用的节点。 限制和请求在单个 Pod 级别应用。 有关如何定义这些值的详细信息，请参阅[定义 pod 资源请求和限制][resource-limits]

若要通过某个方式来保留和限制整个开发团队或项目的资源，应使用资源配额。 这些配额在命名空间中定义，可用于根据以下条件设置配额：

* **计算资源**：例如 CPU 和内存，或 GPU。
* **存储资源**：包括给定存储类的总卷数或磁盘空间量。
* **对象计数**：例如，可创建的最大机密、服务或作业数。

Kubernetes 不会过度使用资源。 累积资源请求总数超过了分配的配额后，所有进一步的部署都将失败。

定义资源配额时，命名空间中创建的所有 pod 必须在其 pod 规范中提供限制或请求。 如果它们未提供这些值，则你可以拒绝部署。 可以[针对命名空间配置默认请求和限制][configure-default-quotas]。

以下名为 *dev-app-team-quotas.yaml* 的示例 YAML 清单设置了总共 *10* 个 CPU、*20Gi* 内存和 *10* 个 pod 的硬限制：

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

可以通过指定命名空间（例如 *dev-apps*）来应用此资源配额：

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

请咨询应用程序开发人员和所有者以了解其需求，并应用适当的资源配额。

有关可用资源对象、范围和优先级的详细信息，请参阅 [Kubernetes 中的资源配额][k8s-resource-quotas]。

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>使用 pod 中断预算进行可用性规划

> **最佳实践指南** 
>
> 若要维持应用程序的可用性，请定义 Pod 中断预算 (PDB)，以确保群集中可用的 Pod 数量最少。

有两个中断性事件会导致 pod 被删除：

### <a name="involuntary-disruptions"></a>非自愿性中断

非自愿性中断是群集操作员或应用程序所有者无法以一般方式进行控制的事件。 包含：
* 物理计算机上的硬件失败
* 内核死机
* 删除节点 VM

可以通过以下方式来缓解非自愿性中断：
* 在部署中使用 Pod 的多个副本。 
* 在 AKS 群集中运行多个节点。 

### <a name="voluntary-disruptions"></a>自愿性中断

自愿性中断是群集操作员或应用程序所有者请求的事件。 包含：
* 群集升级
* 更新了部署模板
* 意外删除 Pod

Kubernetes 为自愿性中断提供 Pod 中断预算，以便规划发生自愿性中断事件时部署或副本集的响应方式。 使用 Pod 中断预算，群集操作员可以定义可用资源数下限或不可用资源数上限。 

如果升级群集或更新部署模板，则 Kubernetes 计划程序将在允许自愿性中断事件继续之前，在其他节点上安排额外的 Pod。 计划程序将等待重启节点，直到在群集中的其他节点上成功安排了定义的 Pod 数为止。

让我们探讨一个副本集示例，其中包含五个运行 NGINX 的 pod。 为该副本集中的 Pod 分配了标签 `app: nginx-frontend`。 在发生自愿性中断事件（例如群集升级）期间，你想要确保至少有三个 pod 可继续运行。 *PodDisruptionBudget* 对象的以下 YAML 清单定义了这些要求：

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
    matchLabels:
      app: nginx-frontend
```

还可以定义一个百分比（例如 *60%*），以便在扩展 pod 数目时可以自动补偿副本集。

可在副本集中定义最大不可用实例数。 同样，也可以定义最大不可用 pod 数的百分比。 以下 pod 中断预算 YAML 清单定义副本集中不可用的 pod 数不能超过两个：

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
    matchLabels:
      app: nginx-frontend
```

定义 pod 中断预算后，可以像创建其他任何 Kubernetes 对象一样，在 AKS 群集中创建中断预算：

```console
kubectl apply -f nginx-pdb.yaml
```

请咨询应用程序开发人员和所有者以了解其需求，并应用适当的 pod 中断预算。

有关使用 pod 中断预算的详细信息，请参阅[为应用程序指定中断预算][k8s-pdbs]。

## <a name="next-steps"></a>后续步骤

本文重点介绍了基本 Kubernetes 计划程序功能。 有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [多租户和群集隔离][aks-best-practices-cluster-isolation]
* [高级 Kubernetes 计划程序功能][aks-best-practices-advanced-scheduler]
* [身份验证和授权][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
