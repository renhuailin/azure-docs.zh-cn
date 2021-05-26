---
title: 资源管理最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关 Azure Kubernetes 服务 (AKS) 中的资源管理的应用程序开发人员最佳做法
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: f45c3a0bb425b9b4d780a78bb32afa3186232b11
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467091"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>有关管理 Azure Kubernetes 服务 (AKS) 中的资源的应用程序开发人员最佳做法

在 Azure Kubernetes 服务 (AKS) 中开发和运行应用程序时，需要考虑到几个重要方面。 如何应对应用程序部署可能对所提供服务的最终用户体验造成的负面影响。 为获得成功，在 AKS 中开发和运行应用程序时，请遵循一些最佳做法。

本文从应用程序开发人员的角度重点介绍如何运行群集和工作负荷。 有关管理最佳做法的信息，请参阅[有关 Azure Kubernetes 服务 (AKS) 中隔离和资源管理的群集操作员最佳做法][operator-best-practices-isolation]。 本文内容：

> [!div class="checklist"]
> * Pod 资源请求和限制。
> * 使用 Bridge to Kubernetes 与 Visual Studio Code 开发和部署应用程序的方法。

## <a name="define-pod-resource-requests-and-limits"></a>定义 pod 资源请求和限制

> **最佳实践指南**
> 
> 在 YAML 清单中针对所有 pod 设置 pod 请求和限制。 如果 AKS 群集使用资源配额，而你未定义这些值，则可能会拒绝你的部署。

可使用 pod 请求和限制管理 AKS 群集中的计算资源。 Pod 请求和限制会向 Kubernetes 计划程序告知要分配给 pod 的计算资源。

### <a name="pod-cpumemory-requests"></a>Pod CPU/内存请求
Pod 请求定义 pod 定期需要的固定 CPU 和内存量。

在 Pod 规范中，必须根据上述信息定义这些请求和限制，这是 **最佳做法且很重要**。 如果不包含这些值，则 Kubernetes 计划程序无法考虑应用程序在制定决策时所需的资源。

监视应用程序的性能以调整 pod 请求。 
* 如果低估了 Pod 请求，应用程序可能会因节点计划过度而导致性能下降。 
* 如果估算的请求数过高，则应用程序可能会更加难以进行计划。

### <a name="pod-cpumemory-limits"></a>Pod CPU/内存限制** 
Pod 限制设置 pod 可以使用的最大 CPU 和内存量。 

* 内存限制定义在节点因资源不足而不稳定时应终止的 pod。 如果没有适当的限制，则会终止固定的 Pod，直到解除资源压力。 
* 虽然 pod 可能会周期性超过 CPU 限制，但 pod 不会因为超过 CPU 限制而被终止。 

Pod 限制定义 Pod 何时失去对资源消耗的控制。 当超过限制时，pod 会进行标记以便终止。 此行为可维护节点健康状况，并最大程度地减少对共享节点的 pod 的影响。 如果未设置 Pod 限制，则会将其默认设置为给定节点上的最高可用值。

请避免设置的 pod 限制超过节点可以支持的限制。 每个 AKS 节点将为核心 Kubernetes 组件保留一定的 CPU 和内存量。 应用程序可能会尝试消耗节点上的大量资源，使其他 pod 能够成功运行。

请在一天或一周的不同时间监视应用程序的性能。 确定峰值需求时间，并根据满足最大需求所需的资源来调整 pod 限制。

> [!IMPORTANT]
>
> 在 Pod 规范中，根据上述信息定义这些请求和限制。 未包含这些值会阻止 Kubernetes 计划程序考虑应用程序帮助进行计划决策所需的资源。

如果计划程序在资源不足的节点上放置一个 Pod，则应用程序性能会下降。 群集管理员必须针对需要设置资源请求和限制的命名空间设置资源配额。 有关详细信息，请参阅 [AKS 群集上的资源配额][resource-quotas]。

定义 CPU 请求或限制时，值以 CPU 单位计量。 
* *1.0* CPU 相当于节点上的一个基础虚拟 CPU 核心。 
    * GPU 使用与此相同的计量方法。
* 可以定义以毫核心数度量的分数。 例如，*100m* 表示 *0.1* 个基础 vCPU 核心。

在以下单个 NGINX pod 的基本示例中，pod 请求 *100m* 的 CPU 时间和 *128 Mi* 的内存。 pod 的资源限制设置为 *250m* CPU 和 *256Mi* 内存：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

有关资源度量和分配的详细信息，请参阅[管理容器的计算资源][k8s-resource-limits]。

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>针对 AKS 群集开发和调试应用程序

> **最佳实践指南** 
>
> 开发团队应该使用 Bridge to Kubernetes 针对 AKS 群集进行部署和调试。

使用 Bridge to Kubernetes 可直接针对 AKS 群集开发、调试和测试应用程序。 在整个应用程序生命周期，团队中的开发人员协作进行生成和测试。 可以继续将现有工具（例如 Visual Studio 或 Visual Studio Code）与 Bridge to Kubernetes 扩展结合使用。 

使用 Bridge to Kubernetes 的集成式开发和测试过程减少了对 [minikube][minikube] 之类的本地测试环境的需求。 而是针对 AKS 群集（甚至是受保护的隔离群集）进行开发和测试。 

> [!NOTE]
> Bridge to Kubernetes 适用于在 Linux Pod 和节点上运行的应用程序。

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>使用适用于 Kubernetes 的 Visual Studio Code (VS Code) 扩展

> **最佳实践指南** 
>
> 编写 YAML 清单时安装并使用适用于 Kubernetes 的 VS Code 扩展。 还可将该扩展用于集成式部署解决方案，不经常与 AKS 群集交互的应用程序所有者也许可从中获得帮助。

[适用于 Kubernetes 的 Visual Studio Code 扩展][vscode-kubernetes]可帮助你开发应用程序并将其部署到 AKS。 该扩展提供：
* 适用于 Kubernetes 资源、Helm 图表和模板的 Intellisense 功能。 
* 从 VS Code 内部对 Kubernetes 资源进行浏览、部署和编辑的功能。 
* 针对 pod 规范中设置的资源请求或限制的 intellisense 检查：

    ![适用于 Kubernetes 的 VS Code 扩展中有关缺少内存限制的警告](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="next-steps"></a>后续步骤

本文从群集操作员的角度重点介绍了如何运行群集和工作负荷。 有关管理最佳做法的信息，请参阅[有关 Azure Kubernetes 服务 (AKS) 中隔离和资源管理的群集操作员最佳做法][operator-best-practices-isolation]。

若要实施其中的某些最佳做法，请参阅以下文章：

* [使用 Bridge to Kubernetes 进行开发][btk]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
