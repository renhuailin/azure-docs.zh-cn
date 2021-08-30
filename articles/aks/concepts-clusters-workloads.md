---
title: 概念 - Azure Kubernetes 服务 (AKS) 的 Kubernetes 基础知识
description: 了解 Kubernetes 的基本群集和工作负荷组件以及它们与 Azure Kubernetes 服务 (AKS) 中各个功能的关系
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 4db6ee6d117f7743147dad43dcfff98dd99fd76d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733648"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念

应用程序开发继续朝着基于容器的方法发展，这增加了我们协调和管理资源的需求。 作为领先平台，Kubernetes 对容错应用程序工作负载提供可靠计划。 Azure Kubernetes 服务 (AKS) 是一种托管 Kubernetes 产品/服务，可进一步简化基于容器的应用程序部署和管理。

本文介绍了以下内容：
* 核心 Kubernetes 基础结构组件：
    * 控制平面
    * 节点
    * 节点池
* 工作负载资源： 
    * Pod
    * *部署*
    * 集 
* 如何将资源分组到命名空间中。

## <a name="what-is-kubernetes"></a>什么是 Kubernetes？

Kubernetes 是一个快速发展的平台，用于管理基于容器的应用程序及其相关网络和存储组件。 Kubernetes 重点关注应用程序工作负载，而不是底层基础结构组件。 Kubernetes 提供了一种声明性的部署方法，由一组针对管理操作的强大 API 提供支持。

你可构建和运行可移植的、基于微服务的现代应用程序，从而使用 Kubernetes 安排和管理这些应用程序组件的可用性。 由于团队通过采用基于微服务的应用程序而取得进展，因此 Kubernetes 支持无状态和有状态应用程序。

作为开放平台，Kubernetes 可使用首选的编程语言、OS、库或消息总线生成应用程序。 现有的持续集成和持续交付 (CI/CD) 工具可以与 Kubernetes 集成，以计划和部署版本。

AKS 提供一项托管 Kubernetes 服务，它可降低部署和核心管理任务（例如升级协调）的复杂性。 由 Azure 平台来管理 AKS 控制平面，你只需为运行应用程序的 AKS 节点付费。 AKS 建立在开源 Azure Kubernetes 服务引擎 [aks-engine][aks-engine] 的基础之上。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes 群集体系结构

Kubernetes 群集分为两个组件：

- 控制平面：提供 Kubernetes 核心服务和应用程序工作负载的业务流程。
- 节点：运行应用程序工作负载。

![Kubernetes 控制平面和节点组件](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>控制面板

创建 AKS 群集时，系统会自动创建和配置控制平面。 此控制平面作为提取自用户的 Azure 托管资源免费提供。 你只需为附加到 AKS 群集的节点付费。 控制平面及其资源仅驻留在创建群集的区域。

控制平面包括以下 Kubernetes 核心组件：

| 组件 | 说明 |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | API 服务器是公开基础 Kubernetes API 的方式。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。                                                        |  
| etcd | 高可用性 etcd 是 Kubernetes 中的键值存储，可维护 Kubernetes 群集和配置的状态。                                      |  
| *kube-scheduler*                                                                            | 创建或缩放应用程序时，计划程序可确定哪些节点可运行工作负载并启动这些节点。                                                                                    |  
| *kube-controller-manager*                                                                            | 控制器管理器可监视许多较小的控制器，这些控制器执行 Pod 复制和节点处理等操作。                                                                  |  

AKS 提供单租户控制平面、专用 API 服务器、计划程序等。由你定义节点的数量和大小，Azure 平台负责配置控制平面和节点之间的安全通信。 通过 Kubernetes API（例如 `kubectl` 或 Kubernetes 仪表板）与控制平面进行交互。

虽然你无需使用此托管控制平面配置高可用性 etcd 存储等组件，但你无法直接访问控制平面。 Kubernetes 控制平面和节点升级通过 Azure CLI 或 Azure 门户进行协调。 要解决可能出现的问题，可以通过 Azure Monitor 日志查看控制平面日志。

若要配置或直接访问控制平面，请使用 [aks-engine][aks-engine] 部署你自己的 Kubernetes 群集。

如需相关的最佳做法，请参阅 [AKS 中群集安全性和升级的最佳做法][operator-best-practices-cluster-security]。

## <a name="nodes-and-node-pools"></a>节点和节点池

要运行应用程序和支持服务，需要 Kubernetes 节点。 一个 AKS 群集至少有一个节点，这是运行 Kubernetes 节点组件和容器运行时的 Azure 虚拟机 (VM)。

| 组件 | 说明 |  
| ----------------- | ------------- |  
| `kubelet` | Kubernetes 代理，用于处理来自控制平面的业务流程请求并计划运行请求的容器。 |  
| kube-proxy | 处理每个节点上的虚拟网络。 代理路由流量并管理服务和 Pod 的 IP 地址。 |  
| *container runtime* | 使容器化应用程序可运行并与其他资源（如虚拟网络和存储）进行交互。 如果 AKS 群集使用适用于 Linux 节点池的 Kubernetes 1.19 版及更高版，则这些群集使用 `containerd` 作为它们的容器运行时。 从适用于 Windows 节点池的 Kubernetes 1.20 版开始，可在预览版中使用 `containerd` 作为容器运行时，但 Docker 仍是默认的容器运行时。 如果 AKS 群集使用适用于节点池的 Kubernetes 以前版本，则这些集群使用 Docker 作为它们的容器运行时。 |  

![Azure 虚拟机和 Kubernetes 节点的支持资源](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

节点的 Azure VM 大小定义了存储 CPU、内存、大小和可用类型（例如高性能 SSD 或常规 HDD）。 围绕应用程序是否需要大量 CPU 和内存或高性能存储来计划节点大小。 根据需要横向扩展 AKS 群集中的节点数。

在 AKS 中，群集的节点的 VM 映像基于 Ubuntu Linux 或 Windows Server 2019。 创建 AKS 群集或横向扩展节点数时，Azure 平台会自动创建和配置所请求数量的 VM。 代理节点按标准虚拟机计费，因此会自动应用任何虚拟机大小折扣（包括 [Azure 预留][reservation-discounts]）。

如果使用不同的主机 OS、容器运行时或包含不同的自定义包，请使用 [aks-engine][aks-engine] 部署你自己的 Kubernetes 群集。 上游 `aks-engine` 在 AKS 群集中受支持之前，会发布功能并提供配置选项。 因此，如果要使用 `containerd` 或 Docker 以外的容器运行时，可运行 `aks-engine` 来配置和部署满足当前需求的 Kubernetes 群集。

### <a name="resource-reservations"></a>资源预留

AKS 使用节点资源来帮助节点作为群集的一部分运行。 这种用法可能会造成节点的资源总数与 AKS 中可分配的资源数之间存在差异。 在为用户部署的 Pod 设置请求和限制时，请注意此信息。

若要查找节点的可分配资源，运行：
```kubectl
kubectl describe node [NODE_NAME]
```

为了保持节点性能和功能，AKS 会在每个节点上预留资源。 随着资源中节点的扩大，由于管理用户部署的 Pod 的需求更高，资源预留也会增加。

>[!NOTE]
> 使用容器见解 (OMS) 等 AKS 附加产品将消耗更多节点资源。

保留两种类型的资源：

- **CPU**  
    预留的 CPU 取决于节点类型和群集配置，这可能会由于运行其他功能而导致可分配的 CPU 变少。

   | 主机上的 CPU 核心数 | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube 预留 (millicore)|60|100|140|180|260|420|740|

- **内存**  
    AKS 使用的内存包含两个值的和。

   1. `kubelet` 守护程序   
       `kubelet` 守护程序安装在所有 Kubernetes 代理节点上，用于管理容器的创建和停止使用。 
   
        在 AKS 上，`kubelet` 守护程序默认具有逐出规则 memory.available<750Mi，从而确保一个节点必须始终具有至少 750 Mi 的可分配内存。 当主机低于该可用内存阈值时，`kubelet` 将触发终止某个正在运行的 Pod 并释放主机上的内存。

   2. 为 kubelet 守护程序正常运行而预留 (kube-reserved) 的内存的递减速率。
      - 前 4 GB 内存的 25%
      - 下一个 4 GB 内存的 20%（最多 8 GB）
      - 下一个 8 GB 内存的 10%（最多 16 GB）
      - 下一个 112 GB 内存的 6%（最多 128 GB）
      - 128 GB 以上任何内存的 2%

内存和 CPU 分配规则：
* 使代理节点保持正常运行，包括某些对群集运行状况至关重要的托管系统 Pod。 
* 使节点报告的可分配内存和 CPU 少于它不是 Kubernetes 群集一部分的情况。 

上述资源预留无法更改。

例如，如果一个节点提供 7 GB 内存，它会报告 34% 的内存不可分配，包括 750Mi 硬逐出阈值。

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

除了 Kubernetes 本身的预留外，基础节点 OS 还预留了一定数量的 CPU 和内存资源以维持 OS 功能的运行。

如需相关的最佳做法，请参阅 [AKS 中适用于基本计划程序功能的最佳做法][operator-best-practices-scheduler]。

### <a name="node-pools"></a>节点池

具有相同配置的节点将统一合并成节点池。 一个 Kubernetes 群集至少包含一个节点池。 创建 AKS 群集时会定义初始节点数和大小，从而创建默认节点池。 AKS 中的此默认节点池包含运行代理节点的基础 VM。

> [!NOTE]
> 为了确保群集可靠运行，应在默认节点池中至少运行两 (2) 个节点。

针对默认节点池缩放或升级 AKS 群集。 你可选择缩放或升级特定节点池。 对于升级操作，将在节点池中的其他节点上计划正在运行的容器，直到成功升级所有节点。

若要详细了解如何在 AKS 中使用多个节点池，请参阅[为 AKS 中的群集创建和管理多个节点池][use-multiple-node-pools]。

### <a name="node-selectors"></a>节点选择器

在具有多个节点池的 AKS 群集中，可能需要告知 Kubernetes 计划程序要将哪个节点池用于给定的资源。 例如，入口控制器不应在 Windows Server 节点上运行。 

可通过节点选择器定义各种参数（例如节点 OS），来控制应在哪里计划 Pod。

以下基本示例使用节点选择器 "kubernetes.io/os": linux 在 Linux 节点上调度 NGINX 实例：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "kubernetes.io/os": linux
```

若要详细了解如何控制对 Pod 进行计划的位置，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][operator-best-practices-advanced-scheduler]。

## <a name="pods"></a>Pod

Kubernetes 使用 Pod 来运行应用程序的实例。 Pod 表示应用程序的单个实例。 

Pod 通常与容器是一对一映射关系。 在高级方案中，一个 Pod 可能包含多个容器。 多容器 Pod 是在同一节点上共同计划的，容器可通过它们共享相关资源。

创建 Pod 时，可定义资源请求以请求一定数量的 CPU 或内存资源。 Kubernetes 计划程序尝试通过计划将要在具有可用资源的节点上运行的 Pod 来满足请求。 你还可指定最大资源限制，防止某 Pod 从基础节点消耗过多计算资源。 最佳做法是包括所有 Pod 的资源限制，以帮助 Kubernetes 计划程序确定必需的允许资源。

有关详细信息，请参阅 [Kubernetes Pod][kubernetes-pods] 和 [Kubernetes Pod 生命周期][kubernetes-pod-lifecycle]。

Pod 是逻辑资源，但应用程序工作负载在容器中运行。 Pod 通常是临时的、可释放的资源。 单个计划的 Pod 缺少某些高可用性和冗余 Kubernetes 功能。 相反，Pod 由 Kubernetes 控制器（例如 Deployment 控制器）进行部署和管理。

## <a name="deployments-and-yaml-manifests"></a>部署和 YAML 清单

部署表示由 Kubernetes 部署控制器管理的相同 Pod。 部署定义了要创建的 Pod 副本数量。 Kubernetes 计划程序可确保当 Pod 或节点遇到问题时，在正常节点上计划其他 Pod。

可以更新部署以更改 Pod 的配置、使用的容器映像或附加存储。 部署控制器：
* 排出并终止给定数量的副本。
* 根据新的部署定义创建副本。
* 继续此过程，直到部署中的所有副本都已更新。

AKS 中的大多数无状态应用程序应使用部署模型，而不是计划单个 Pod。 Kubernetes 可监视部署运行状况和状态，确保在群集中运行所需数量的副本。 单个计划时，如果 Pod 遇到问题则不会重启；如果当前节点遇到问题，则不在正常节点上重新计划。

如果你的应用程序需要最低数量的可用实例，那么你不希望更新过程干扰管理决定。 Pod 中断预算定义了在更新或节点升级期间部署中可删除的副本数。 例如，如果部署中有五 (5) 个副本，则可定义四 (4) 个 Pod 中断，以便一次只允许删除或重新计划一个副本 。 与 Pod 资源限制一样，最佳做法是在需要始终存在最低数量副本的应用程序上定义 Pod 中断预算。

通常使用 `kubectl create` 或 `kubectl apply` 来创建和管理部署。 通过定义 YAML 格式的清单文件来穿件部署。 

以下示例创建 NGINX Web 服务器的基本部署。 部署指定要创建的三 (3) 个副本，并要求在容器上打开端口 80 。 还为 CPU 和内存定义了资源请求和限制。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

通过在 YAML 清单中包含负载均衡器等服务，可创建更复杂的应用程序。

有关详细信息，请参阅 [Kubernetes 部署][kubernetes-deployments]。

### <a name="package-management-with-helm"></a>使用 Helm 进行包管理

[Helm][helm] 通常用于管理 Kubernetes 中的应用程序。 可生成和使用包含应用程序代码打包版本和 Kubernetes YAML 清单的现有公共 Helm 图表来部署资源。 Helm 图表可存储在本地，也可存储在远程存储库中，例如 [Azure 容器注册表 Helm 图表存储库][acr-helm]。

要使用 Helm，请在计算机上安装 Helm 客户端，或使用 [Azure Cloud Shell][azure-cloud-shell] 中的 Helm 客户端。 搜索或创建 Helm 图表，然后将其安装到 Kubernetes 群集。 有关详细信息，请参阅[在 AKS 中使用 Helm 安装现有应用程序][aks-helm]。

## <a name="statefulsets-and-daemonsets"></a>StatefulSet 和 DaemonSet

借助 Kubernetes 计划程序，部署控制器能够在具有可用资源的任何可用节点上运行副本。 虽然此方法对无状态应用程序来说可能已经足够，但部署控制器并不适合需要下列项的应用程序：
* 永久性命名约定或存储。 
* 群集中每个特选节点上存在的副本。

不同，可通过下面两种 Kubernetes 资源管理这类应用程序：

- StatefulSet 维护超出单个 Pod 生命周期的应用程序的状态（如存储）。
- DaemonSet 确保在 Kubernetes 启动进程早期每个节点上都有正在运行的实例。

### <a name="statefulsets"></a>StatefulSet

现代应用程序开发通常以无状态应用程序为目标。 对于有状态应用程序（例如包含数据库组件的应用程序），可使用 StatefulSets。 与部署类似，StatefulSet 创建和管理至少一个相同的 Pod。 StatefulSet 中的副本按照正常有序的方法来部署、缩放、升级和终止。 以副本形式保存的命名约定、网络名称和存储用 StatefulSet 重新计划。

使用 `kind: StatefulSet` 按 YAML 格式定义应用程序。 从这里，由 StatefulSet 控制器处理所需副本的部署和管理。 数据会写入到由 Azure 托管磁盘或 Azure 文件提供的永久性存储。 如果使用 StatefulSet，甚至在删除 StatefulSet 时，基础持久性存储仍然保持不变。

有关详细信息，请参阅 [Kubernetes StatefulSet][kubernetes-statefulsets]。

计划 StatefulSet 中的副本，并在 AKS 群集中的任何可用节点上运行这些副本。 若要确保集中至少有一个 Pod 在节点上运行，请改用 DaemonSet。

### <a name="daemonsets"></a>DaemonSet

对于特定的日志集合或监视，可能需要在所有或选定的节点上运行 Pod。 可以在一个或多个相同的 Pod 上使用 DaemonSet 部署，但 DaemonSet 控制器可确保指定的每个节点都运行该 Pod 的一个实例。

在默认的 Kubernetes 计划程序启动之前，DaemonSet 控制器可以在群集启动进程的早期计划节点上的 Pod。 此功能可确保在计划 Deployment 或 StatefulSet 中的传统 Pod 之前启动 DaemonSet 中的 Pod。

与 StatefulSet 一样，系统使用 `kind: DaemonSet` 将 DaemonSet 定义为 YAML 定义的一部分。

有关详细信息，请参阅 [Kubernetes DaemonSet][kubernetes-daemonset]。

> [!NOTE]
> 如果使用[虚拟节点附加产品](virtual-nodes-cli.md#enable-virtual-nodes-addon)，DaemonSet 将不会在虚拟节点上创建 Pod。

## <a name="namespaces"></a>命名空间

Kubernetes 资源（例如 Pod 和部署）按逻辑分组到一个命名空间中，以划分 AKS 群集并限制创建、查看或管理资源的权限。 例如，可创建命名空间来分隔业务组。 用户只能与分配的命名空间内的资源进行交互。

![Kubernetes 命名空间以逻辑方式划分资源和应用程序](media/concepts-clusters-workloads/namespaces.png)

创建 AKS 群集时，以下命名空间可用：

| 命名空间 | 说明 |  
| ----------------- | ------------- |  
| *default*                                                                                 | 不提供任何命名空间时，默认在此命名空间中创建 Pod 和部署。 在小型环境中，可以将应用程序直接部署到默认命名空间，而无需创建其他逻辑分隔。 与 Kubernetes API（例如 `kubectl get pods`）交互时，如果未指定命名空间，则使用默认值。                                                        |  
| *kube-system* | 此命名空间中有核心资源，例如 DNS 和代理等网络功能或 Kubernetes 仪表板。 通常不会将应用程序部署到此命名空间中。                                      |  
| *kube-public*                                                                            | 通常不使用此命名空间，但可用于让资源在整个群集中可见，并可供任何用户查看。                                                                                    |  


有关详细信息，请参阅 [Kubernetes 命名空间][kubernetes-namespaces]。

## <a name="next-steps"></a>后续步骤

本文涵盖了一些核心 Kubernetes 组件以及如何将它们应用于 AKS 群集的内容。 有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 访问和标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS 规模][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
