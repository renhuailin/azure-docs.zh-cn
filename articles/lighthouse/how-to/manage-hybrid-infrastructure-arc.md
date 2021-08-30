---
title: 利用 Azure Arc 大规模管理混合基础结构
description: Azure Lighthouse 可帮助你有效地管理 Azure 外部的客户计算机和 Kubernetes 群集。
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 926ec6d3fdca6b31ee5296832e12a63a2dfa9d65
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228837"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>利用 Azure Arc 大规模管理混合基础结构

作为服务提供商，你可能已经将多个客户租户载入了 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个 Azure Active Directory (Azure AD) 租户之间大规模执行操作，从而提高管理任务的效率。

[Azure Arc](../../azure-arc/overview.md) 有助于简化跨本地、边缘和多云的复杂分布式环境，以便可在任何位置部署 Azure 服务并将 Azure 管理扩展到任何基础结构。

借助[启用 Azure Arc 的服务器](../../azure-arc/servers/overview.md)，客户可以在其公司网络上管理在 Azure 之外托管的任何 Windows 和 Linux 计算机，管理方式与管理本机 Azure 虚拟机的方式相同。 通过将混合计算机链接到 Azure，它就会连接到 Azure 并被视为 Azure 中的资源。 然后，服务提供商便可管理这些非 Azure 计算机及其客户的 Azure 资源。

借助[启用 Azure Arc 的 Kubernetes](../../azure-arc/kubernetes/overview.md)，客户可以在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集附加到 Azure Arc 后，它将显示在 Azure 门户中，并具有 Azure 资源管理器 ID 和托管标识。 群集附加到标准 Azure 订阅，位于资源组中，可以像任何其他 Azure 资源一样接收标记。

本主题概述了服务提供商如何以可缩放方式使用启用了 Azure Arc 的服务器和启用了 Azure Arc 的 Kubernetes，以管理其客户的混合环境，并获得对所有托管客户租户的可见性。

> [!TIP]
> 尽管本主题中提及的是服务提供商和客户，但本指南同样适用于[使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>使用启用了 Azure Arc 的服务器大规模管理混合服务器

作为服务提供商，你可以使用 [Azure Connected Machine 代理](../../azure-arc/servers/agent-overview.md)管理客户将其订阅连接到的 Azure 外部的本地 Windows Server 或 Linux 计算机。

在 Azure 门户中查看委托订阅的资源时，你会看到这些使用 Azure Arc 标记的已连接计算机。你可以使用 Azure 构造（如 Azure Policy 和标记）管理这些已连接计算机，其方式与管理客户 Azure 资源的方式相同。 还可以跨客户租户工作，以同时管理所有连接的混合计算机。

例如，可以[确保在客户的混合计算机上应用一组相同的策略](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)。 还可以使用 Azure 安全中心来监视所有客户混合环境的符合性，或[使用 Azure Monitor 直接从混合计算机收集数据](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) 并传输到 Log Analytics 工作区。 可将[虚拟机扩展](../../azure-arc/servers/manage-vm-extensions.md)部署到非 Azure Windows 和 Linux VM，简化客户混合计算机的管理。

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>使用启用了 Azure Arc 的 Kubernetes 大规模管理混合 Kubernetes 群集

可以管理[使用 Azure Arc 连接到客户订阅](../../azure-arc/kubernetes/quickstart-connect-cluster.md)的 Kubernetes 群集，就像它们在 Azure 中运行一样。

如果客户创建了[服务主体帐户以将 Kubernetes 群集载入 Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md)，则你可以访问此服务主体帐户以载入和管理群集。 当包含服务主体帐户的订阅[载入 Azure Lighthouse](onboard-customer.md) 时，管理租户中获得“Kubernetes 群集 - Azure Arc 载入”Azure 内置角色的用户可以完成此操作。

可以使用 GitOps 为连接的群集部署[配置](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md)和 [Helm 图表](../../azure-arc/kubernetes/use-gitops-with-helm.md)。

还可以使用 Azure Monitor 监视连接的群集，以及[使用 Azure Policy 大规模应用群集配置](../../azure-arc/kubernetes/use-azure-policy.md)。

## <a name="next-steps"></a>后续步骤

- 浏览 [Azure Arc GitHub 存储库](https://github.com/microsoft/azure_arc)中的快速入门和示例。
- 了解[启用 Azure Arc 的服务器的支持方案](../../azure-arc/servers/overview.md#supported-cloud-operations)。
- 了解 [Azure Arc 支持的 Kubernetes 分布](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)。
- 了解如何[大规模部署策略](policy-at-scale.md)。
- 了解如何[大规模使用 Azure Monitor 日志](monitor-at-scale.md)。