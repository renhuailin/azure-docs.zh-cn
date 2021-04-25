---
title: 群集扩展 - 已启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文概述了已启用 Azure Arc 的 Kubernetes 的群集扩展功能
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450753"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes 的群集扩展

[Helm 图表](https://helm.sh/)提供定义、安装和升级最复杂的 Kubernetes 应用程序所需的构建基块，有助于管理 Kubernetes 应用程序。 群集扩展功能要在 Helm 的打包组件的基础上进行构建。 该功能通过提供 Azure 资源管理器驱动的群集扩展安装和生命周期管理体验（例如 Azure Monitor 和 Azure Defender for Kubernetes）来实现此目的。 除使用 Helm 图表时本机可实现的功能之外，群集扩展功能还提供如下更多优势：

- 获取所有群集的清单以及这些群集上安装的扩展。
- 使用 Azure Policy 自动大规模部署群集扩展。
- 订阅每个扩展的火车发版。
- 设置扩展的自动升级。
- 支持扩展实例创建以及更新和删除事件的生命周期管理。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>体系结构

[ ![群集扩展体系结构](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

群集扩展实例基于 Azure 资源管理器中已启用 Azure Arc 的 Kubernetes 资源（由 `Microsoft.Kubernetes/connectedClusters` 表示）创建为扩展 Azure 资源管理器资源 (`Microsoft.KubernetesConfiguration/extensions`)。 可通过 Azure 资源管理器中的表示创作一个策略，用于检查所有已启用 Azure Arc 的 Kubernetes 资源，无论这些资源是否具有特定群集扩展。 确定缺少具有所需属性值的群集扩展的群集后，即可使用 Azure Policy 修正这些不符合的资源。

群集中运行的 `config-agent` 跟踪已启用 Azure Arc 的 Kubernetes 资源上的新增或更新的扩展资源。 群集中运行的 `extensions-manager` 从 Azure 容器注册表或 Microsoft 容器注册表提取 Helm 图表，并将其安装在群集上。 

群集中运行的 `config-agent` 和 `extensions-manager` 组件处理版本更新和扩展实例删除。

> [!NOTE]
> * `config-agent` 监视已启用 Arc 的 Kubernetes 资源上可用的新增或更新的扩展资源。 因此，代理需要将所需状态的连接下拉到群集。 若代理无法连接到 Azure，则延迟将所需状态传播到群集。
> * 扩展的受保护配置设置可在已启用 Azure Arc 的 Kubernetes 服务中存储 48 小时。 因此，若在 Azure 上创建扩展资源后的 48 小时内群集处于断开连接状态，则扩展将状态从“`Pending`”转换为“`Failed`”。 建议定期使群集处于联机状态。

## <a name="next-steps"></a>后续步骤

* 通过我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 在已启用 Azure Arc 的 Kubernetes 群集上[部署群集扩展](./extensions.md)。