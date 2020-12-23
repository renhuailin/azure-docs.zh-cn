---
title: 使用虚拟节点
titleSuffix: Azure Kubernetes Service
description: '概述如何在 Azure Kubernetes 服务中使用虚拟节点 (AKS) '
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 2dd91e5c506f229d653fdf98bc0549c173cec793
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351863"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>创建和配置 Azure Kubernetes Services (AKS) 群集，以使用虚拟节点

若要在 AKS 群集中快速缩放应用程序工作负载，可以使用虚拟节点。 使用虚拟节点可快速预配 Pod，并且只需对其执行时间按秒付费。 无需等待 Kubernetes 群集自动缩放程序部署 VM 计算节点来运行其他 Pod。 只有 Linux Pod 和节点支持虚拟节点。

AKS 的虚拟节点外接程序基于开源项目 [Virtual Kubelet][virtual-kubelet-repo]。

本文概述了使用虚拟节点的区域可用性和网络要求，以及已知限制。

## <a name="regional-availability"></a>区域可用性

所有支持 VNET Sku 的区域都支持虚拟节点部署。

对于每个区域中可用的 CPU 和内存 Sku，请查看 azure [区域中 azure 容器实例的 Azure 容器实例资源可用性-Linux 容器组](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>网络要求

Azure 容器实例 (ACI) 和 AKS 群集中运行的 Pod 可以借助虚拟节点进行网络通信。 若要提供此通信，应创建虚拟网络子网并分配委派的权限。 虚拟节点仅适用于使用 *高级* 网络 (Azure CNI) 创建的 AKS 群集。 默认情况下，使用 *基本* 网络 (kubenet) 创建 AKS 群集。

在 Azure 容器实例中运行的 pod (ACI) 需要访问 AKS API 服务器终结点，以便配置网络。

## <a name="known-limitations"></a>已知的限制

虚拟节点功能很大程度上依赖于 ACI 的功能集。 除了 [Azure 容器实例的配额和限制](../container-instances/container-instances-quotas.md)之外，虚拟节点尚不支持以下方案：

* 使用服务主体拉取 ACR 映像。 [解决方法](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry)是使用 [Kubernetes 机密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [虚拟网络限制](../container-instances/container-instances-vnet.md)包括 VNet 对等互连、Kubernetes 网络策略和网络安全组发送到 Internet 的出站流量。
* 初始化容器
* [主机别名](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI 中的 exec 的[参数](../container-instances/container-instances-exec.md#restrictions)
* [Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets) 不会将 pod 部署到虚拟节点
* 虚拟节点支持计划 Linux Pod。 你可以手动安装开源 [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) 提供程序，以便将 Windows Server 容器调度到 ACI。
* 虚拟节点需要具有 Azure CNI 网络的 AKS 群集。
* 具有专用群集的虚拟节点。
* 使用 api 服务器的授权 ip 范围 AKS。
* 卷装载 Azure 文件共享支持 [常规用途 V1](../storage/common/storage-account-overview.md#types-of-storage-accounts)。 按照[使用 Azure 文件共享装入卷](azure-files-volume.md)的说明进行操作

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
