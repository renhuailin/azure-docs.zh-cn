---
title: 已启用 Azure Arc 的 Kubernetes 常见问题解答
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文包含与已启用 Azure Arc 的 Kubernetes 相关的常见问题解答列表
keywords: Kubernetes, Arc, Azure, 容器, 配置, GitOps, faq
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025843"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>常见问题解答 - 已启用 Azure Arc 的 Kubernetes

本文介绍有关已启用 Azure Arc 的 Kubernetes 的常见问题解答。

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>已启用 Azure Arc 的 Kubernetes 和 Azure Kubernetes 服务 (AKS) 之间有何区别？

AKS 是 Azure 提供的托管 Kubernetes 服务。 AKS 通过将大量的复杂性和运营开销卸载到 Azure，简化了在 Azure 中部署托管 Kubernetes 群集的过程。 由于 Kubernetes 主节点由 Azure 管理，因此你只需要管理和维护代理节点。

借助已启用 Azure Arc 的 Kubernetes，可通过将 Kubernetes 群集连接到 Azure 来扩展 Azure 的管理功能（例如 Azure Monitor 和 Azure Policy）。 维护基础 Kubernetes 群集本身。

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>是否需要将在 Azure 上运行的 AKS 群集连接到 Azure Arc？

不是。 包括 Azure Monitor 和 Azure Policy (Gatekeeper) 在内的所有已启用 Azure Arc 的 Kubernetes 功能都可在 AKS（Azure 资源管理器中的本机资源）上获得。
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>是否应将我在 Azure Stack Hub 和 Azure Stack Edge 上的 AKS-HCI 群集和 Kubernetes 群集连接到 Azure Arc？

是，将 Azure Stack Edge 或 Azure Stack Hub 上的 AKS-HCI 群集或 Kubernetes 群集连接到 Azure Arc，可在 Azure 资源管理器中为群集提供资源表示形式。 此资源表示形式可以将群集配置、Azure Monitor 和 Azure Policy (Gatekeeper) 等功能扩展到连接的 Kubernetes 群集。

如果已启用 Azure Arc 的 Kubernetes 群集位于 Azure Stack Edge、Azure Stack HCI 上的 AKS（>= 2021 年 4 月更新）或 Windows Server 2019 数据中心上的 AKS（>= 2021 年 4 月更新）上，则免费包含 Kubernetes 配置。

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>如何处理过期的已启用 Azure Arc 的 Kubernetes 资源？

与已启用 Azure Arc 的 Kubernetes 相关联的托管服务标识 (MSI) 证书的有效期限为 90 天。 此证书过期后，会将资源视为 `Expired`，所有功能（例如配置、监视和策略）将在此群集上停止工作。 若要使 Kubernetes 群集再次使用 Azure Arc，请执行以下操作：

1. 删除群集上已启用 Azure Arc 的 Kubernetes 资源和代理。 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. 通过在群集上部署代理重新创建已启用 Azure Arc 的 Kubernetes 资源。
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` 还将删除群集顶部的配置。 运行 `az connectedk8s connect` 后，请手动或使用 Azure Policy 在群集上重新创建配置。

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>如果我已在使用 CI/CD 管道，是否仍可使用已启用 Azure Arc 的 Kubernetes 和配置？

是的，你仍可以在通过 CI/CD 管道接收部署的群集上使用配置。 与传统的 CI/CD 管道相比，配置具有两个额外的优势：

偏移协调

在管道运行期间，CI/CD 管道仅应用一次更改。 但是，群集上的 GitOps 运算符会持续轮询 Git 存储库，以获取群集上的 Kubernetes 资源的所需状态。 如果 GitOps 运算符发现资源的所需状态与群集上资源的实际状态不同，则会对此偏移进行协调。

大规模应用 GitOps

CI/CD 管道对于事件驱动的 Kubernetes 群集部署（例如，推送到 Git 存储库）很有用。 但是，如果想要将相同的配置部署到所有 Kubernetes 群集，则需要手动将每个 Kubernetes 群集的凭据配置到 CI/CD 管道。 

对于已启用 Azure Arc 的 Kubernetes，由于 Azure 资源管理器会管理你的配置，因此可以在订阅或资源组范围内，使用 Azure Policy 在所有已启用 Azure Arc 的 Kubernetes 资源中自动创建相同的配置。 此功能甚至适用于在策略分配后创建的已启用 Azure Arc 的 Kubernetes 资源。

此功能在整个 Kubernetes 群集清单中应用基准配置（例如网络策略、角色绑定和 Pod 安全策略），以满足合规性和治理要求。

## <a name="next-steps"></a>后续步骤

* 参考我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 已有一个连接了 Azure Arc 的 Kubernetes 群集？ [在已启用 Arc 的 Kubernetes 群集上创建配置](./tutorial-use-gitops-connected-cluster.md)。
* 了解如何[使用 Azure Policy 大规模应用配置](./use-azure-policy.md)。