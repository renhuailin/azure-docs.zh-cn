---
title: 启用 Azure Arc Kubernetes 常见问题
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文包含与已启用 Azure Arc 相关的常见问题的列表 Kubernetes
keywords: Kubernetes、Arc、Azure、容器、配置、GitOps、faq
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561374"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>常见问题-Azure Arc 已启用 Kubernetes

本文介绍有关启用了 Azure Arc Kubernetes 的常见问题。

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>已启用 Azure Arc 的 Kubernetes 和 Azure Kubernetes 服务 (AKS) 之间有何区别？

AKS 是 Azure 托管的 Kubernetes 产品/服务。 AKS 通过卸载 Azure 的许多复杂性和运营开销，简化了 Azure 中的托管 Kubernetes 群集的部署。 由于 Kubernetes 主机由 Azure 管理，因此你只需要管理和维护代理节点。

使用启用了 azure Arc 的 Kubernetes，可以通过将 Kubernetes 群集连接到 Azure 来扩展 Azure 的管理功能 (例如 Azure Monitor 和 Azure 策略) 。 维护基础 Kubernetes 群集本身。

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>是否需要将在 Azure 上运行的 AKS 群集连接到 Azure Arc？

否。 所有启用了 Azure Arc 的 Kubernetes 功能（包括 Azure Monitor 和 Azure 策略 (守卫) ）都可在 Azure 资源管理器) 中的本机资源 (AKS 上获得。
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>是否应该将 AKS-HCI 群集和 Kubernetes 群集连接 Azure Stack 集线器上，并将 Azure Stack 边缘连接到 Azure Arc？

是，将 AKS 或 Azure Stack 集线器 Azure Stack 上的-HCI 群集或 Kubernetes 群集连接到 Azure Arc 为群集提供了 Azure 资源管理器中的资源表示形式。 此资源表示形式将群集配置、Azure Monitor 和 Azure 策略 (守卫) 等功能扩展到连接的 Kubernetes 群集。

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>如何处理已过期的已启用 Azure Arc Kubernetes 资源？

与启用了 Azure Arc Kuberenetes 的 Azure Arc) 证书关联的托管服务标识 (的过期时段为90天。 此证书过期后，会考虑资源， `Expired` 所有功能（如配置、监视和策略）都将停止在此群集上运行。 请按照以下步骤操作，使 Kubernetes 群集再次使用 Azure Arc：

1. 删除群集上已启用的 Azure Arc Kubernetes 资源和代理 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. 再次在群集上部署代理，重新创建启用了 Azure Arc 的 Kubernetes 资源。
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` 还将删除群集上的配置。 运行后 `az connectedk8s connect` ，可以手动或使用 Azure 策略在群集上创建配置。

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>如果我已在使用 CI/CD 管道，是否仍可使用启用了 Azure Arc 的 Kubernetes 和配置？

是的，你仍可以在通过 CI/CD 管道接收部署的群集上使用配置。 与传统的 CI/CD 管道相比，配置具有两个额外的优势：

**偏移对帐**

在管道运行期间，CI/CD 管道仅应用一次更改。 但是，群集上的 GitOps 运算符会持续轮询 Git 存储库，以获取群集上的 Kubernetes 资源的所需状态。 如果 GitOps 运算符发现资源的所需状态与群集上资源的实际状态不同，则会对此偏差进行协调。

**大规模应用 GitOps**

CI/CD 管道适用于事件驱动的部署到 Kubernetes 群集，其中事件可能是推送到 Git 存储库。 但是，将相同配置部署到所有 Kubernetes 群集需要将 CI/CD 管道手动配置为其中每个 Kubernetes 群集的凭据。 另一方面，在启用了 Azure Arc 的情况下，由于 Azure 资源管理器管理你的配置，因此你可以使用 Azure 策略来自动将所需的配置应用到订阅或资源组范围内的所有 Kubernetes 群集上的 Kubernetes。 此功能甚至适用于启用了策略分配后创建的 Azure Arc Kubernetes 资源。

配置功能用于在整个 Kubernetes 群集清单中应用基本配置（如网络策略、角色绑定和 pod 安全策略），以满足合规性和监管要求。

## <a name="next-steps"></a>后续步骤

* [将群集连接到 Azure Arc](./connect-cluster.md)
* [在启用了 Arc 的 Kubernetes 群集上创建配置](./use-gitops-connected-cluster.md)
* [使用 Azure 策略大规模应用配置](./use-azure-policy.md)
