---
title: 群集连接 — 已启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文为已启用 Azure Arc 的 Kubernetes 群集连接功能提供了概念性概述
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450671"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes 群集连接

已启用 Azure Arc 的 Kubernetes “群集连接”功能提供了与集群 `apiserver` 的连接，无需在防火墙上启用任何入站端口。 在群集上运行的反向代理程序可以通过出站方式安全地启动与 Azure Arc 服务的会话。 

群集连接使开发者可以从任何位置访问他们的群集以进行交互式开发和调试。 它还使群集用户和管理员可以从任何位置访问或管理他们的群集。 你甚至可以使用 Azure Pipelines、GitHub 操作或任何其他托管 CI/CD 服务的托管代理/运行程序，将应用程序部署到本地群集，且无需自托管代理。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>体系结构

[ ![群集连接体系结构](./media/conceptual-cluster-connect.png) ](./media/conceptual-cluster-connect.png#lightbox)

在群集端，部署反向代理程序代理 `clusterconnect-agent` 作为代理 helm 的一部分，会对 Azure Arc 服务发出出站调用，以建立会话。

当用户调用 `az connectedk8s proxy` 时：
1. 在客户端计算机上下载 Azure Arc 代理二进制文件，并作为进程启动。 
1. Azure Arc 代理会提取与调用 `az connectedk8s proxy` 的已启用 Azure arc 的 Kubernetes 群集相关的 `kubeconfig` 文件。
    * Azure Arc 代理使用调用方的 Azure 访问令牌和 Azure 资源管理器 ID 名。 
1. 在计算机上通过 Azure arc 代理保存的 `kubeconfig` 文件，将服务器 URL 指向 Azure arc 代理进程上的终结点。

当用户使用此 `kubeconfig` 文件发送请求时：
1. Azure Arc 代理将接收请求的终结点映射到 Azure Arc 服务。 
1. 然后，Azure Arc 服务将请求转发到在群集上运行的 `clusterconnect-agent`。 
1. `clusterconnect-agent` 向 `kube-aad-proxy` 组件传递请求，后者对调用实体执行 Azure AD 身份验证。 
1. Azure AD 身份验证后，`kube-aad-proxy` 使用 Kubernetes [用户模拟](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation)功能将请求转接到群集的 `apiserver`。

## <a name="next-steps"></a>后续步骤

* 使用我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 使用群集连接从任何位置安全地[访问群集](./cluster-connect.md)。