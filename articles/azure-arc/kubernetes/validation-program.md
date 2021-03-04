---
title: 启用 Azure Arc Kubernetes 验证计划
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 描述 Kubernetes 分布的 Arc 验证计划
keywords: Kubernetes，Arc，Azure，K8s，验证
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121365"
---
# <a name="azure-arc-validation-program"></a>Azure Arc 验证计划

已启用 Azure Arc 的 Kubernetes 适用于任何经云原生计算基础 (CNCF) 认证的 Kubernetes 群集。 Azure Arc 团队也与关键行业 Kubernetes 产品/服务提供商合作，通过其 Kubernetes 分发来验证启用了 Azure Arc 的 Kubernetes。 将验证这些提供程序发布的 Kubernetes 分发的未来主要和次要版本是否兼容启用了 Azure Arc 的 Kubernetes。

## <a name="validated-distributions"></a>验证的分发

以下 Microsoft 提供的 Kubernetes 分发和基础结构提供程序已成功通过了适用于 Azure Arc Kubernetes 的一致性测试：

| 分发和基础结构提供程序 | 版本 |
| ---------------------------------------- | ------- |
| Azure 上的群集 API 提供程序            | Release 版本： [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12);Kubernetes 版本： [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| Azure Stack HCI 上的 AKS                   | 发布版本： [2020 年12月更新](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012);Kubernetes 版本： [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

以下提供程序及其相应的 Kubernetes 分发已成功通过启用了 Azure Arc Kubernetes 的一致性测试：

| 提供程序名称 | 分布名称 | 版本 |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift 容器平台](https://www.openshift.com/products/container-platform) | [4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)、 [4.6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html)、 [4.7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes 网格](https://tanzu.vmware.com/kubernetes-grid) | Kubernetes 版本： v 1.17。5 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes 引擎](https://rancher.com/products/rke/) | RKE CLI 版本： [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4);Kubernetes 版本： [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)) 、 [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)) 、 [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))   |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | 版本2.2。1 |

Azure Arc 团队还在以下公有云提供商上运行了一致性测试和已验证的支持 Azure Arc 的 Kubernetes 方案：

| 公有云提供商名称 | 分布名称 | 版本 |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | 弹性 Kubernetes 服务 (EKS)  | v 1.18。9  |
| Google Cloud Platform      | Google Kubernetes 引擎 (GKE) | v 1.17.15 |

## <a name="scenarios-validated"></a>已验证方案

符合性测试在 Azure Arc 启用时运行 Kubernetes 验证涵盖以下方案：

1. 将 Kubernetes 群集连接到 Azure Arc： 
    * 在群集上部署启用了 Azure Arc 的 Kubernetes agent Helm 图表。
    * 在群集上 (MSI) 证书中设置托管系统标识。
    * 代理将群集元数据发送到 Azure。

2. 配置: 
    * 在启用了 Azure Arc Kubernetes 资源的基础上创建配置。
    * 设置 GitOps 工作流所需的[Flux](https://docs.fluxcd.io/)部署在群集上。
    * Flux 从 demo Git 存储库中提取清单和 Helm 图表，并将其部署到群集。

## <a name="next-steps"></a>后续步骤

了解如何将群集连接到 Azure Arc。
> [!div class="nextstepaction"]
> [将群集连接到 Azure Arc](./quickstart-connect-cluster.md)
