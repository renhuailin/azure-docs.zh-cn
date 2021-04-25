---
title: 配置和 GitOps - 已启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文提供已启用 Azure Arc 的 Kubernetes 的 GitOps 和配置功能的概念性概述。
keywords: Kubernetes, Arc, Azure, 容器, 配置, GitOps
ms.openlocfilehash: 88a30876b25730e4cb0b4b1e19fac94b9e556adc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121790"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes 中的配置和 GitOps

对于 Kubernetes 而言，GitOps 是在 Git 存储库中声明 Kubernetes 群集配置（部署、命名空间等）所需状态的具体实践。 在此声明的后面，是使用 operator 对这些群集配置进行基于轮询和拉取的部署。 Git 存储库可以包含：
* YAML 格式的清单，用于描述任何有效的 Kubernetes 资源（包括 Namespaces、ConfigMaps、Deployments、DaemonSets 等）。
* 用于部署应用程序的 Helm 图表。

[Flux](https://docs.fluxcd.io/)，GitOps 空间中一个热门的开源工具，可部署在 Kubernetes 群集上，以方便将配置从 Git 存储库传送到 Kubernetes 群集。 Flux 支持将其 operator 部署在群集和命名空间范围内。 使用命名空间范围部署的 Flux operator 只能在该特定命名空间内部署 Kubernetes 对象。 在群集与命名空间范围之间进行选择的功能可帮助你在同一个 Kubernetes 群集上实现多租户部署模式。

## <a name="configurations"></a>配置

[ ![配置体系结构 ](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

群集与 Git 存储库之间的连接是作为 Azure 资源管理器中已启用 Azure Arc 的 Kubernetes 资源（由 `Microsoft.Kubernetes/connectedClusters` 表示）之上的配置资源 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) 创建的。 

配置资源属性可与相应的参数（例如，从中拉取清单的 Git 存储库，以及拉取清单的轮询间隔）配合使用，以便在群集上部署 Flux operator。 配置资源数据以静态加密的形式存储在 Azure Cosmos DB 数据库中，以确保数据机密性。

群集中运行的 `config-agent` 负责：
* 跟踪已启用 Azure Arc 的 Kubernetes 资源上的新配置资源或已更新的配置资源。
* 部署 Flux operator 用于监视 Git 存储库中的每个配置资源。
* 应用对任何配置资源所做的任何更新。 

可在同一个已启用 Azure Arc 的 Kubernetes 群集上创建多个命名空间范围的配置资源，以实现多租户。

> [!NOTE]
> * `config-agent` 监视要在已启用 Arc 的 Kubernetes 资源上提供的新配置资源或已更新的配置资源。 因此，代理需要建立连接才能使所需状态可以拉取到群集。 如果代理无法连接到 Azure，则将所需状态传播到群集时会出现延迟。
> * 敏感的客户输入内容（例如私钥、已知 hosts 内容、HTTPS 用户名以及令牌/密码）不会在已启用 Azure Arc 的 Kubernetes 服务中存储 48 小时以上。 如果要将敏感输入内容用于配置，请尽量有规律地使群集联机。

## <a name="apply-configurations-at-scale"></a>大规模应用配置

由于 Azure 资源管理器会管理你的配置，因此可以在订阅或资源组范围内，使用 Azure Policy 在所有已启用 Azure Arc 的 Kubernetes 资源上自动创建相同的配置。 

这种大规模强制措施确保可在已启用 Azure Arc 的 Kubernetes 群集的整个机群或库存中应用一个通用基线配置（包含 ClusterRoleBindings、RoleBindings 和 NetworkPolicy 等配置）。

## <a name="next-steps"></a>后续步骤

* 参考我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./connect-cluster.md)。
* 已将 Kubernetes 群集连接到 Azure Arc？ [在已启用 Arc 的 Kubernetes 群集上创建配置](./use-gitops-connected-cluster.md)。
* 了解如何[使用 Azure Policy 大规模应用配置](./use-azure-policy.md)。