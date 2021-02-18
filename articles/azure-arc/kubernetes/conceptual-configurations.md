---
title: 配置和 GitOps-启用 Azure Arc Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文提供了有关启用了 Azure Arc 的 Kubernetes 的 GitOps 和配置功能的概念性概述。
keywords: Kubernetes、Arc、Azure、容器、配置、GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652541"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>启用了 Azure Arc 的配置和 GitOps Kubernetes

相对于 Kubernetes，GitOps 是在 Git 存储库中声明 Kubernetes 群集配置 (部署、命名空间等 ) 的所需状态的做法。 此声明后跟使用运算符的这些群集配置的轮询和基于请求的部署。 Git 存储库可包含：
* 描述任何有效 Kubernetes 资源的 YAML 格式清单，包括命名空间、ConfigMaps、部署、Daemonset 等。
* 用于部署应用程序的 Helm 图表。

[Flux](https://docs.fluxcd.io/)是 GitOps 空间中一种常用的开源工具，可部署在 Kubernetes 群集上，使从 Git 存储库到 Kubernetes 群集的配置流动。 Flux 支持在群集和命名空间范围内部署其运算符。 使用命名空间范围部署的 flux 运算符只能部署该特定命名空间中的 Kubernetes 对象。 在群集或命名空间范围之间进行选择的功能可帮助你在同一 Kubernetes 群集上实现多租户部署模式。

## <a name="configurations"></a>配置

[![配置体系结构 ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

群集和 Git 存储库之间的连接是 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 在 Azure Arc Enabled Kubernetes 资源上作为扩展资源创建的，该资源 (由 `Microsoft.Kubernetes/connectedClusters` azure 资源管理器中) 表示。 

`sourceControlConfiguration`资源属性用于使用适当的参数在群集上部署 Flux 操作员，如从中提取清单的 Git 存储库和用于提取清单的轮询间隔。 `sourceControlConfiguration`数据将以加密的和静态存储在 Azure Cosmos DB 数据库中，以确保数据的保密性。

`config-agent`群集中运行的负责：
* `sourceControlConfiguration`在启用了 Azure Arc 的 Kubernetes 资源上跟踪新的或更新的扩展资源。
* 部署 Flux 运算符来监视每个的 Git 存储库 `sourceControlConfiguration` 。
* 应用对任何内容所做 `sourceControlConfiguration` 的任何更新。 

可 `sourceControlConfiguration` 在启用了同一 Azure Arc 的 Kubernetes 群集上创建多个命名空间范围的资源，以实现多租户。

> [!NOTE]
> * `config-agent` 持续监视启用了 Azure Arc 的 Kubernetes 资源上的新的或更新的 `sourceControlConfiguration` 扩展资源。 因此，代理需要一致的连接才能将所需状态属性提取到群集。 如果代理无法连接到 Azure，则群集上不会应用所需状态。
> * 在启用了 Azure Arc 的 Kubernetes services 中，敏感客户输入（如私钥、已知主机内容、HTTPS 用户名和令牌或密码）最多可存储48小时。 如果使用敏感输入进行配置，请定期使群集处于联机状态。

## <a name="apply-configurations-at-scale"></a>大规模应用配置

由于 Azure 资源管理器管理你的配置，因此你可以使用 Azure 策略，在订阅或资源组范围内使用 Azure 策略自动创建相同的配置 Kubernetes 资源。 

这一大规模强制确保了包含 ClusterRoleBindings、RoleBindings 和) NetworkPolicy 等配置的通用基线配置 (可以应用于整个汽油或 Azure Arc 已启用 Kubernetes 群集的清单。

## <a name="next-steps"></a>后续步骤

* [将群集连接到 Azure Arc](./connect-cluster.md)
* [在启用了 Arc 的 Kubernetes 群集上创建配置](./use-gitops-connected-cluster.md)
* [使用 Azure 策略大规模应用配置](./use-azure-policy.md)