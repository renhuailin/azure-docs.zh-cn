---
title: 自定义位置 - 已启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文提供已启用 Azure Arc 的 Kubernetes 的自定义位置功能的概念性概述
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450679"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes 上的自定义位置

作为 Azure 位置构造的一个扩展，自定义位置为租户管理员提供了使用其已启用 Azure Arc 的 Kubernetes 群集作为目标位置来部署 Azure 服务实例的一种方式。 Azure 资源示例包括已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的超大规模 PostgreSQL。

类似于 Azure 位置，租户中有权访问自定义位置的最终用户可以使用其公司的专用计算来部署资源。

[ ![Arc 平台层](./media/conceptual-arc-platform-layers.png) ](./media/conceptual-arc-platform-layers.png#lightbox)

可将自定义位置可视化为已启用 Azure Arc 的 Kubernetes 群集、群集连接和群集扩展上的抽象层。 自定义位置为其他 Azure 服务创建所需的精细 [RoleBinding 和 ClusterRoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding)，使其能够访问群集。 其他这些 Azure 服务需要拥有群集访问权限才能管理客户想要在其群集上部署的资源。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>体系结构

当管理员在群集上启用自定义位置功能时，将在该群集上创建一个 ClusterRoleBinding，并为自定义位置资源提供程序 (RP) 使用的 Azure AD 应用程序授权。 获得授权后，自定义位置 RP 可以创建其他 Azure RP 在此群集上创建自定义资源时所需的 ClusterRoleBinding 或 RoleBinding。 群集上安装的群集扩展会确定要授权的 RP 列表。

[ ![使用自定义位置](./media/conceptual-custom-locations-usage.png) ](./media/conceptual-custom-locations-usage.png#lightbox)

当用户在群集上创建数据服务实例时： 
1. 向 Azure 资源管理器发送 PUT 请求。
1. 将 PUT 请求转发到已启用 Azure Arc 的数据服务 RP。 
1. RP 提取与存在自定义位置的已启用 Azure Arc 的 Kubernetes 群集关联的 `kubeconfig` 文件。 
   * 在原始 PUT 请求中，自定义位置称为 `extendedLocation`。 
1. 已启用 Azure Arc 的数据服务 RP 使用 `kubeconfig` 来与群集通信，以在映射到自定义位置的命名空间中创建已启用 Azure Arc 的数据服务类型的自定义资源。 
   * 在自定义位置存在之前，已通过创建群集扩展部署了已启用 Azure Arc 的数据服务操作程序。 
1. 已启用 Azure Arc 的数据服务操作程序读取在群集上创建的新自定义资源并创建数据控制器，这相当于在群集上实现了所需状态。 

用于创建 SQL 托管实例和 PostgreSQL 实例的步骤顺序与前面所述的步骤顺序相同。

## <a name="next-steps"></a>后续步骤

* 使用我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 在已启用 Azure Arc 的 Kubernetes 群集上[创建自定义位置](./custom-locations.md)。