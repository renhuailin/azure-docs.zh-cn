---
title: 已启用 Azure Arc 的 Kubernetes 验证
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 描述用于 Kubernetes 分发的 Arc 验证计划
keywords: Kubernetes, Arc, Azure, K8s, 验证
ms.openlocfilehash: 6ebce59a16c994c676cfe81df4df4e9433d1fb70
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966242"
---
# <a name="azure-arc-enabled-kubernetes-validation"></a>已启用 Azure Arc 的 Kubernetes 验证

已启用 Azure Arc 的 Kubernetes 适用于任何经云原生计算基础 (CNCF) 认证的 Kubernetes 群集。 Azure Arc 团队还与关键行业 Kubernetes 产品/服务提供商合作，通过其 Kubernetes 分发来验证已启用 Azure Arc 的 Kubernetes。 将验证这些提供商发布的 Kubernetes 分发的未来主要和次要版本是否兼容已启用 Azure Arc 的 Kubernetes。

## <a name="validated-distributions"></a>经验证的分发

以下 Microsoft 提供的 Kubernetes 分发和基础结构提供商已成功通过已启用 Azure Arc 的 Kubernetes 的一致性测试：

| 分发和基础结构提供商 | 版本 |
| ---------------------------------------- | ------- |
| Azure 上的群集 API 提供商            | 发布版本：[0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12)Kubernetes 版本：[1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| Azure Stack HCI 上的 AKS                   | 发布版本：[2020 年 12 月更新](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012)Kubernetes 版本：[1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

以下提供商及其相应的 Kubernetes 分发已成功通过已启用 Azure Arc 的 Kubernetes 的一致性测试：

| 提供程序名称 | 分发名称 | 版本 |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift 容器平台](https://www.openshift.com/products/container-platform) | [4.5.41+](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)、[4.6.35+](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html)、[4.7.18+](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes 网格](https://tanzu.vmware.com/kubernetes-grid) | Kubernetes 版本：v1.17.5 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes 引擎](https://rancher.com/products/rke/) | RKE CLI 版本：[v1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4)Kubernetes 版本：[1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6))、[1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14))、[1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | 版本 2.2.1 |
| Platform9      | [Platform9 托管 Kubernetes (PMK)](https://platform9.com/managed-kubernetes/)    | PMK 版本 [5.3.0](https://platform9.com/docs/kubernetes/release-notes#platform9-managed-kubernetes-version-53-release-notes)；Kubernetes 版本：v1.20.5、v1.19.6、v1.18.10 |

Azure Arc 团队还在以下公有云提供商上运行了符合性测试和经验证的已启用 Azure Arc 的 Kubernetes 场景：

| 公有云提供商名称 | 分发名称 | 版本 |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | 弹性 Kubernetes 服务 (EKS) | v1.18.9  |
| Google Cloud Platform      | Google Kubernetes 引擎 (GKE) | v1.17.15 |

## <a name="scenarios-validated"></a>经验证的场景

一致性测试作为已启用 Azure Arc 的 Kubernetes 验证的一部分运行，涵盖以下场景：

1. 将 Kubernetes 群集连接到 Azure Arc： 
    * 在群集上部署已启用 Azure Arc 的 Kubernetes 代理 Helm 图表。
    * 在群集上设置托管系统标识 (MSI) 证书。
    * 代理将群集元数据发送到 Azure。

2. 配置: 
    * 在已启用 Azure Arc 的 Kubernetes 资源的基础上创建配置。
    * 设置 GitOps 工作流所需的[Flux](https://docs.fluxcd.io/)部署在群集上。
    * Flux 从演示 Git 存储库中提取清单和 Helm 图表，并将它们部署到群集。

## <a name="next-steps"></a>后续步骤

了解如何将群集连接到 Azure Arc。
> [!div class="nextstepaction"]
> [将群集连接到 Azure Arc](./quickstart-connect-cluster.md)
