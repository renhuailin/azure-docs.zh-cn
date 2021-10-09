---
title: Azure Kubernetes 服务 (AKS) 中的群集配置
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中配置群集
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 6c454c23eec0bb5b0fef1ceca3ad8f8e4c52493d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812604"
---
# <a name="configure-an-aks-cluster"></a>配置 AKS 群集

在创建 AKS 群集的过程中，你可能需要自定义群集配置来满足你的需求。 本文介绍了几个用于自定义 AKS 群集的选项。

## <a name="os-configuration"></a>OS 配置

对于群集，AKS 支持将 Ubuntu 18.04 作为正式发布 (GA) 的默认节点操作系统 (OS)。

## <a name="container-runtime-configuration"></a>容器运行时配置

容器运行时是在节点上执行容器和管理容器映像的软件。 运行时有助于抽掉系统调用或操作系统 (OS) 特定功能，以便在 Linux 或 Windows 上运行容器。 对于 Linux 节点池，`containerd` 用于使用 Kubernetes 1.19 及更高版本的节点池。 对于 Windows Server 2019 节点池，`containerd` 以预览版提供，并且可以在使用 Kubernetes 1.20 及更高版本的节点池中使用，但默认情况下仍使用 Docker。

[`Containerd`](https://containerd.io/) 是一个符合 [OCI](https://opencontainers.org/)（开放式容器计划）要求的核心容器运行时，该运行时提供在节点上执行容器和管理映像所需的最小功能集。 它是在 2017 年 3 月[捐赠](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/)给云原生计算基金会 (CNCF) 的。 如上所示，AKS 使用的当前 Moby（上游 Docker）版本已经利用 `containerd` 并在此基础上进行构建。

如果使用了基于 `containerd` 的节点和节点池，kubelet 会通过 CRI（容器运行时接口）插件直接与 `containerd` 通信，而不是与 `dockershim` 通信，这样，流中就没有额外的跃点（与 Docker CRI 实现相比）。 因此，你会看到 Pod 启动延迟情况得到改善，资源（CPU 和内存）使用量降低。

通过对 AKS 节点使用 `containerd`，可以改善 Pod 启动延迟情况，并降低容器运行时的节点资源消耗。 这些改善是通过这个新体系结构实现的，其中的 kubelet 通过 CRI 插件直接与 `containerd` 通信，而在 Moby/docker 体系结构中，kubelet 则会在到达 `containerd` 之前与 `dockershim` 和 docker 引擎通信，因此在流中有额外的跃点。

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` 适用于 AKS 中的每个 Kubernetes GA 版本和每个高于 v1.19 的上游 Kubernetes 版本，并且支持所有 Kubernetes 和 AKS 功能。

> [!IMPORTANT]
> Linux 节点池基于 Kubernetes v1.19 或更高版本创建的群集默认对其容器运行时使用 `containerd`。 在早期受支持的 Kubernetes 版本上具有节点池的群集支持使用 Docker 作为其容器运行时。 将节点池 Kubernetes 版本更新为支持 `containerd` 的版本后，Linux 节点池将更新为 `containerd`。 你仍可以使用在早期的受支持版本上创建的 Linux 节点池和群集，直到这些版本不再受支持。
> 
> 将 `containerd` 与 Windows Server 2019 节点池一起使用目前处于预览阶段。 有关详细信息，请参阅[使用 `containerd` 添加 Windows Server 节点池][aks-add-np-containerd]。
> 
> 强烈建议在使用具有支持节点池 `containerd` 的 Kubernetes 版本的群集之前，使用 `containerd` 测试 AKS 节点池上的工作负载。

### <a name="containerd-limitationsdifferences"></a>`Containerd` 限制/差异

* 对于 `containerd`，建议使用 [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) 作为替代 CLI（而不是使用 Docker CLI），以便对 Kubernetes 节点上的 Pod、容器和容器映像进行故障排除（例如 `crictl ps`）。 
   * 它不提供 Docker CLI 的完整功能。 它仅用于故障排除目的。
   * `crictl` 提供更适合 Kubernetes 的容器视图，其中存在 Pod 等概念。
* `Containerd` 使用标准化 `cri` 日志记录格式（不同于你当前从 Docker 的 JSON 驱动程序获取的内容）设置日志记录。 日志记录解决方案需要支持 `cri` 日志记录格式（例如，[用于容器的 Azure Monitor](../azure-monitor/containers/container-insights-enable-new-cluster.md)）
* 你不能再访问 docker 引擎 `/var/run/docker.sock` 或使用 Docker-in-Docker (DinD)。
  * 如果目前从 Docker 引擎提取应用程序日志或监视数据，请改用其他工具，例如[用于容器的 Azure Monitor](../azure-monitor/containers/container-insights-enable-new-cluster.md)。 此外，AKS 不支持在可能导致不稳定的代理节点上运行任何带外命令。
  * 强烈建议不要通过上述方法构建映像并直接利用 Docker 引擎，即使在使用 Docker 的情况下也是如此。 Kubernetes 并不能完全感知这些已使用的资源，并且这些方法会导致许多问题，详见[此处](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)和[此处](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)的示例。
* 生成映像 - 可以继续像往常一样使用当前的 docker 生成工作流，除非是在 AKS 群集中生成映像。 在这种情况下，请考虑切换到建议的方法，以便使用 [ACR 任务](../container-registry/container-registry-quickstart-task-cli.md)或更安全的群集中选项（如 [docker buildx](https://github.com/docker/buildx)）来生成映像。

## <a name="generation-2-virtual-machines"></a>第 2 代虚拟机

Azure 支持[第 2 代 (Gen2) 虚拟机 (VM)](../virtual-machines/generation-2.md)。 第 2 代 VM 支持第 1 代 VM (Gen1) 所不支持的某些关键特性。 这些特性包括更大的内存、Intel Software Guard Extensions (Intel SGX) 和虚拟化持久性内存 (vPMEM)。

第 2 代 VM 使用新的基于 UEFI 的启动体系结构，而不是使用第 1 代 VM 所用的基于 BIOS 的体系结构。
只有特定的 SKU 和大小支持第 2 代 VM。 查看[受支持的大小列表](../virtual-machines/generation-2.md#generation-2-vm-sizes)，查看你的 SKU 是否支持或需要第 2 代。

此外，并非所有 VM 映像都支持第 2 代，在 AKS 第 2 代 VM 上，将使用新的 [AKS Ubuntu 18.04 映像](#os-configuration)。 此映像支持所有第 2 代 SKU 和大小。

## <a name="ephemeral-os"></a>临时 OS

默认情况下，Azure 会自动将虚拟机的操作系统磁盘复制到 Azure 存储，以避免在 VM 需要重定位到另一台主机时丢失数据。 但是，由于容器并未设计为保留本地状态，因此该行为提供的价值有限且存在一些缺点，其中包括节点预配速度较慢、读/写延迟较高。

相比而言，临时 OS 磁盘只存储在主机上，就像临时磁盘一样。 这样的读/写延迟较低，且节点缩放和群集升级速度较快。

与临时磁盘类似，临时 OS 磁盘包含在虚拟机的价格中，因此不会产生额外的存储成本。

> [!IMPORTANT]
>如果用户未显式请求用于 OS 的托管磁盘，则在可能的情况下，AKS 会针对给定的节点池配置默认使用临时 OS。

使用临时 OS 时，OS 磁盘必须适合 VM 缓存。 VM 缓存的大小在 [Azure 文档](../virtual-machines/dv3-dsv3-series.md)中以括号的形式提供，位于 IO 吞吐量旁边（“以 GiB 为单位的缓存大小”）。

以 AKS 默认 VM 大小 Standard_DS2_v2 和默认 OS 磁盘大小 100GB 为例，此 VM 大小支持临时 OS，但只有 86GB 的缓存大小。 如果用户未进行显式指定，则此配置默认为托管磁盘。 如果用户显式请求了临时 OS，则用户会收到验证错误。

如果用户请求 OS 磁盘大小为 60GB 的同一 Standard_DS2_v2，则此配置将默认为临时 OS：请求的 60GB 大小小于最大缓存大小 86GB。

将 Standard_D8s_v3 与 100GB OS 磁盘配合使用时，此 VM 大小支持临时 OS，有 200GB 的缓存空间。 如果用户未指定 OS 磁盘类型，则默认情况下，节点池会收到临时 OS。 

临时 OS 至少需要 2.15.0 版的 Azure CLI。

### <a name="use-ephemeral-os-on-new-clusters"></a>在新群集上使用临时 OS

配置群集，以便在创建群集时使用临时 OS 磁盘。 使用 `--node-osdisk-type` 标志将临时 OS 设置为新群集的 OS 磁盘类型。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

若要使用通过网络附加的 OS 磁盘来创建常规群集，可以指定 `--node-osdisk-type=Managed`。 还可以选择添加更多的临时 OS 节点池，如下所示。

### <a name="use-ephemeral-os-on-existing-clusters"></a>在现有群集上使用临时 OS
配置新节点池，以使用临时 OS 磁盘。 使用 `--node-osdisk-type` 标志将 OS 磁盘类型设置为该节点池的 OS 磁盘类型。

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> 如果使用临时 OS，可以部署不超过 VM 缓存大小的 VM 和实例映像。 在使用 AKS 的情况下，默认节点 OS 磁盘配置使用 128GB，这意味着所需 VM 大小的缓存大于 128GB。 默认 Standard_DS2_v2 的缓存大小为 86GB，不够大。 Standard_DS3_v2 的缓存大小为 172GB，足够大。 还可以通过使用 `--node-osdisk-size` 来减小 OS 磁盘的默认大小。 AKS 映像的最小大小为 30GB。 

若要使用通过网络附加的 OS 磁盘来创建节点池，可以指定 `--node-osdisk-type Managed`。

## <a name="custom-resource-group-name"></a>自定义资源组名称

在 Azure 中部署 Azure Kubernetes 服务群集时，会为工作器节点创建第二个资源组。 默认情况下，AKS 会将节点资源组命名为 `MC_resourcegroupname_clustername_location`，但你也可以提供自己的名称。

若要指定自己的资源组名称，请安装 aks-preview Azure CLI 扩展版本 0.3.2 或更高版本。 使用 Azure CLI 时，通过 `az aks create` 命令的 `--node-resource-group` 参数为资源组指定自定义名称。 如果使用 Azure 资源管理器模板部署 AKS 群集，可以使用 `nodeResourceGroup` 属性定义资源组名称。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

第二个资源组由订阅中的 Azure 资源提供程序自动创建。 只有创建了群集，才能指定自定义资源组名称。 

请注意，对于节点资源组，不能执行以下操作：

- 不能为节点资源组指定现有资源组。
- 为节点资源组指定不同的订阅。
- 创建群集后更改节点资源组名称。
- 不能为节点资源组内的受管理资源指定名称。
- 不能修改或删除节点资源组内受管理资源中由 Azure 创建的标记。

## <a name="next-steps"></a>后续步骤

- 了解如何在群集中[升级节点映像](node-image-upgrade.md)。
- 若要了解如何将群集升级到最高版本的 Kubernetes，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集](upgrade-cluster.md)。
- 详细了解 [`containerd` 和 Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- 若要查找有关一些常用 AKS 问题的答案，请参阅 [AKS 常见问题解答](faq.md)。
- 详细了解[临时 OS 磁盘](../virtual-machines/ephemeral-os-disks.md)。


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview