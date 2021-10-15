---
title: 使用 Azure 容器注册表从 Kubernetes 进行身份验证的方案
description: 从 Kubernetes 群集向 Azure 容器注册表进行身份验证以拉取容器映像的选项和方案概述
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/20/2021
ms.openlocfilehash: 2fc24714183438f9d740de79f9a9e9ce11f17391
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545390"
---
# <a name="scenarios-to-authenticate-with-azure-container-registry-from-kubernetes"></a>使用 Azure 容器注册表从 Kubernetes 进行身份验证的方案


可以使用 Azure 容器注册表作为 Kubernetes 的容器映像源，包括你管理的群集、托管在 [Azure Kubernetes 服务 (AKS)](../aks/intro-kubernetes.md) 或其他云中的托管群集，以及“本地”Kubernetes 配置（如 [minikube](https://minikube.sigs.k8s.io/) 和 [kind](https://kind.sigs.k8s.io/)）。 

若要从 Azure 容器注册表将映像拉取到 Kuberentes 群集，需要建立身份验证和授权机制。 根据群集环境，选择以下方法之一：

## <a name="scenarios"></a>方案

| Kubernetes 群集 |身份验证方法  | 说明  | 示例 | 
|---------|---------|---------|----------|
| AKS 群集 |AKS 托管标识    |  启用 AKS kubelet [托管标识](../aks/use-managed-identity.md)，从附加的 Azure 容器注册表拉取映像。<br/><br/> 注册表和群集必须在相同的 Active Directory 租户中，但可以在相同或不同的 Azure 订阅中。      | [使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)| 
| AKS 群集 | AKS 服务主体     | 启用对目标 Azure 容器注册表具有权限的 [AKS 服务主体](../aks/kubernetes-service-principal.md)。<br/><br/>注册表可以位于相同或不同的 Azure 订阅或 Azure Active Directory 租户中。        | [将映像从 Azure 容器注册表拉取到不同 AD 租户中的 AKS 群集](authenticate-aks-cross-tenant.md)
| AKS 以外的 Kubernetes 群集 |Pod [imagePullSecrets](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)   |  使用常规 Kubernetes 机制管理 Pod 部署的注册表凭据。<br/><br/>配置 AD 服务主体、存储库范围的令牌或其他支持的[注册表凭据](container-registry-authentication.md)。  | [使用拉取机密将映像从 Azure 容器注册表拉取到 Kubernetes 群集](container-registry-auth-kubernetes.md) | 



## <a name="next-steps"></a>后续步骤

* 详细了解如何[使用 Azure 容器注册表进行身份验证](container-registry-authentication.md)
