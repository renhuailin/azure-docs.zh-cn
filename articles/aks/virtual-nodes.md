---
title: 使用虚拟节点
titleSuffix: Azure Kubernetes Service
description: 概述如何将虚拟节点与 Azure Kubernetes 服务 (AKS) 配合使用
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 78be2edacdcf72a6274755413810fe1308510a79
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669117"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>创建 Azure Kubernetes 服务 (AKS) 群集并将其配置为使用虚拟节点

若要快速缩放 AKS 群集中的应用程序工作负荷，可以使用虚拟节点。 使用虚拟节点可快速预配 Pod，并且只需对其执行时间按秒付费。 无需等待 Kubernetes 群集自动缩放程序部署 VM 计算节点来运行其他 Pod。 只有 Linux Pod 和节点支持虚拟节点。

AKS 的虚拟节点加载项基于开源项目[虚拟 Kubelet][virtual-kubelet-repo]。

本文概述了使用虚拟节点的区域可用性和网络要求，以及已知限制。

## <a name="regional-availability"></a>区域可用性

ACI 支持 VNET SKU 的所有区域都支持虚拟节点部署。 请参阅 [Azure 容器实例在 Azure 区域的资源可用性](../container-instances/container-instances-region-availability.md)。

有关每个区域中可用的 CPU 和内存 SKU，请查看 [Azure 容器实例在 Azure 区域的 Azure 容器实例资源可用性 - Linux 容器组](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>网络要求

Azure 容器实例 (ACI) 和 AKS 群集中运行的 Pod 可以借助虚拟节点进行网络通信。 若要提供此通信，应创建虚拟网络子网并分配委派的权限。 虚拟节点仅适用于使用高级网络 (Azure CNI) 创建的 AKS 群集。 默认情况下，AKS 群集是使用基本网络 (kubenet) 创建的。

在 Azure 容器实例 (ACI) 中运行的 Pod 需要访问 AKS API 服务器终结点才能配置网络。

## <a name="known-limitations"></a>已知的限制

虚拟节点功能很大程度上依赖于 ACI 的功能集。 除了 [Azure 容器实例的配额和限制](../container-instances/container-instances-quotas.md)之外，虚拟节点尚不支持以下方案：

* 使用服务主体拉取 ACR 映像。 [解决方法](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry)是使用 [Kubernetes 机密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [虚拟网络限制](../container-instances/container-instances-vnet.md)包括 VNet 对等互连、Kubernetes 网络策略和网络安全组发送到 Internet 的出站流量。
* 初始化容器
* [主机别名](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI 中的 exec 的[参数](../container-instances/container-instances-exec.md#restrictions)
* [DaemonSet](concepts-clusters-workloads.md#statefulsets-and-daemonsets) 不会将 Pod 部署到虚拟节点
* 虚拟节点支持计划 Linux Pod。 你可以手动安装开源 [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) 提供程序，以便将 Windows Server 容器调度到 ACI。
* 虚拟节点需要那些具有 Azure CNI 网络的 AKS 群集。
* 对 AKS 使用 API 服务器授权的 IP 范围。
* 卷装载 Azure 文件共享支持[常规用途 V1](../storage/common/storage-account-overview.md#types-of-storage-accounts)。 按照说明装载[带有 Azure 文件共享的卷](azure-files-volume.md)
* 不支持使用 IPv6。

## <a name="next-steps"></a>后续步骤

为群集配置虚拟节点：

- [使用 Azure CLI 创建虚拟节点](virtual-nodes-cli.md)
- [在 Azure Kubernetes 服务 (AKS) 中使用门户创建虚拟节点](virtual-nodes-portal.md)

虚拟节点通常是 AKS 中缩放解决方案的一个组件。 有关缩放解决方案的详细信息，请参阅以下文章：

- [使用 Kubernetes 水平 Pod 自动缩放程序][aks-hpa]
- [使用 Kubernetes 群集自动缩放程序][aks-cluster-autoscaler]
- [查看虚拟节点的自动缩放示例][virtual-node-autoscale]
- [阅读有关虚拟 Kubelet 开放源代码库的详细信息][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
