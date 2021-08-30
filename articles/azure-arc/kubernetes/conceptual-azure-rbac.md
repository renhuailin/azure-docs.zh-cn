---
title: Azure RBAC — 启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文提供已启用 Azure Arc 的 Kubernetes 的 Azure RBAC 功能的概念性概述
ms.openlocfilehash: 126e85eb312b977eb90e27971e8e65180542b069
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679955"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>启用 azure Arc Kubernetes 的 azure RBAC

Kubernetes [ClusterRoleBinding 和 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) 对象类型可以帮助你以本机方式在 Kubernetes 中定义授权。 利用 Azure RBAC，可以在 Azure 中使用 Azure Active Directory (Azure AD) 和角色分配来控制群集上的授权检查。

使用此功能，Azure 角色分配的所有优点（如显示 azure 资源的所有 Azure RBAC 更改的活动日志）现在适用于启用了 Azure Arc 的 Kubernetes 群集。

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>体系结构 — 启用 azure Arc Kubernetes 的 Azure RBAC

[ ![Azure RBAC 体系结构 ](./media/conceptual-azure-rbac.png) ](./media/conceptual-azure-rbac.png#lightbox)

为了将所有授权访问检查路由至 Azure 中的授权服务，在群集上部署了一个 Webhook 服务器（[临界子句](https://github.com/appscode/guard)）。

`apiserver`群集的配置为使用 [Webhook 令牌身份验证](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication)和 [Webhook 授权](https://kubernetes.io/docs/reference/access-authn-authz/webhook/)，以便 `TokenAccessReview` 和 `SubjectAccessReview` 请求路由到 guard webhook 服务器。 `TokenAccessReview` 和 `SubjectAccessReview` 请求由发送到 `apiserver` 的 Kubernetes 资源的请求触发。

然后，临界子句对 Azure 中的授权服务进行 `checkAccess` 调用，以查看请求 Azure AD 实体是否有权访问相关资源。 

如果分配中允许此访问的角色存在，则将从授权服务防护发送 `allowed` 响应。 临界子句将 `allowed` 响应发送到 `apiserver`，并使调用实体能够访问请求的 Kubernetes 资源。


如果分配中的角色允许此访问不存在，则从授权服务向临界子句发送 `denied` 响应。 临界子句向 `apiserver` 发送 `denied` 响应，在请求的资源上给调用实体一个 403 禁止的错误。

## <a name="next-steps"></a>后续步骤

* 使用我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 在启用了 Azure Arc 的 Kubernetes 群集群集上[设置 Azure RBAC](./azure-rbac.md)。