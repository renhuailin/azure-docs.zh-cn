---
title: 启用 Azure Arc Kubernetes 常见问题
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文包含与已启用 Azure Arc 相关的常见问题的列表 Kubernetes
keywords: Kubernetes、Arc、Azure、容器、配置、GitOps、faq
ms.openlocfilehash: e0d7501dc1a82940571d0168222c396f61a70bce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652490"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>常见问题-Azure Arc 已启用 Kubernetes

本文介绍有关启用了 Azure Arc Kubernetes 的常见问题。

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>已启用 Azure Arc 的 Kubernetes 和 Azure Kubernetes 服务 (AKS) 之间有何区别？

AKS 是 Azure 托管的 Kubernetes 产品/服务。 AKS 通过卸载 Azure 的许多复杂性和运营开销，简化了 Azure 中的托管 Kubernetes 群集的部署。 由于 Kubernetes 主机由 Azure 管理，因此你只需要管理和维护代理节点。

使用启用了 azure Arc 的 Kubernetes，可以通过将 Kubernetes 群集连接到 Azure 来扩展 Azure 的管理功能 (例如 Azure Monitor 和 Azure 策略) 。 维护基础 Kubernetes 群集本身。

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>是否需要将在 Azure 上运行的 AKS 群集连接到 Azure Arc？

不是。 所有启用了 Azure Arc 的 Kubernetes 功能（包括 Azure Monitor 和 Azure 策略 (守卫) ）都可在 Azure 资源管理器) 中的本机资源 (AKS 上获得。
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>是否应该将 AKS-HCI 群集和 Kubernetes 群集连接 Azure Stack 集线器上，并将 Azure Stack 边缘连接到 Azure Arc？

是，将 AKS 或 Azure Stack 集线器 Azure Stack 上的-HCI 群集或 Kubernetes 群集连接到 Azure Arc 为群集提供了 Azure 资源管理器中的资源表示形式。 此资源表示形式将群集配置、Azure Monitor 和 Azure 策略 (守卫) 等功能扩展到连接的 Kubernetes 群集。

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>如何处理已过期的已启用 Azure Arc Kubernetes 资源？

与启用 Kubernetes 的 Azure Arc) 证书相关联的托管服务标识 (的过期时段为90天。 此证书过期后，资源被视为， `Expired` 所有功能 (例如配置、监视和策略) 停止在此群集上工作。 若要使 Kubernetes 群集再次使用 Azure Arc：

1. 删除群集上的已启用 Azure Arc Kubernetes 资源和代理。 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. 通过在群集上部署代理，重新创建启用了 Azure Arc 的 Kubernetes 资源。
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` 还将删除群集上的配置。 运行后 `az connectedk8s connect` ，请手动或使用 Azure 策略重新创建群集上的配置。

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>如果我已在使用 CI/CD 管道，是否仍可使用启用了 Azure Arc 的 Kubernetes 和配置？

是的，你仍可以在通过 CI/CD 管道接收部署的群集上使用配置。 与传统的 CI/CD 管道相比，配置具有两个额外的优势：

**偏移对帐**

在管道运行期间，CI/CD 管道仅应用一次更改。 但是，群集上的 GitOps 运算符会持续轮询 Git 存储库，以获取群集上的 Kubernetes 资源的所需状态。 如果 GitOps 运算符发现资源的所需状态与群集上资源的实际状态不同，则会对此偏差进行协调。

**大规模应用 GitOps**

CI/CD 管道适用于事件驱动的部署到 Kubernetes 群集 (例如，推送到 Git 存储库) 。 但是，如果想要将相同的配置部署到所有 Kubernetes 群集，则需要手动将每个 Kubernetes 群集的凭据配置到 CI/CD 管道。 

对于启用了 Azure Arc 的 Kubernetes，由于 Azure 资源管理器管理你的配置，因此你可以使用 Azure 策略，在订阅或资源组范围内使用 Azure 策略自动创建相同的配置。 此功能甚至适用于启用了策略分配后创建的 Azure Arc Kubernetes 资源。

此功能在整个 Kubernetes 群集清单中应用基准配置 (例如网络策略、角色绑定和 pod 安全策略) ，以满足合规性和监管要求。

## <a name="next-steps"></a>后续步骤

* [将群集连接到 Azure Arc](./connect-cluster.md)
* [在启用了 Arc 的 Kubernetes 群集上创建配置](./use-gitops-connected-cluster.md)
* [使用 Azure 策略大规模应用配置](./use-azure-policy.md)
