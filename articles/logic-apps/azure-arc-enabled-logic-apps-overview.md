---
title: 概述 - 已启用 Azure Arc 的逻辑应用
description: 了解在可以运行 Kubernetes 的任何位置也可运行的单租户逻辑应用工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: 880e194fefdf49b05f2b531699a83d1f0e3e70cc
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429473"
---
# <a name="what-is-azure-arc-enabled-logic-apps-preview"></a>什么是已启用 Azure Arc 的逻辑应用？ （预览版）

> [!NOTE]
> 此功能以预览版提供，受 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)限制。

使用已启用 Azure Arc 的逻辑应用，可以在可运行 Kubernetes 的任何位置开发并运行基于单租户的逻辑应用。 例如，可以在 Azure、Azure Kubernetes 服务、本地甚至其他云平台中运行你的逻辑应用工作流。 此产品/服务通过 Azure Arc 和 Azure 门户提供集中式的单一视图管理平台来实现以下功能：

- 使用 Azure 逻辑应用作为集成平台。
- 无论工作流托管在何处，都可以将其连接到所有服务。
- 直接与服务一起运行集成解决方案。
- 使用 Visual Studio Code 创建和编辑工作流。
- 使用你选择的 DevOps 管道进行部署。
- 控制 Azure、非 Azure、多个云、本地和边缘环境中的基础结构与资源。

有关详细信息，请查看以下文档：

- [什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)
- [单租户环境与其他逻辑应用环境](../logic-apps/single-tenant-overview-compare.md)
- [Azure Arc 概述](../azure-arc/overview.md)
- [Azure Kubernetes 服务概述](../aks/intro-kubernetes.md)
- [什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)
- [什么是 Kubernetes？](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

<a name="why-use"></a>

## <a name="why-use-arc-enabled-logic-apps"></a>为何要使用已启用 Arc 的逻辑应用

使用已启用 Azure Arc 的逻辑应用，可以像在 Azure 逻辑应用的单租户体验中那样创建和部署逻辑应用工作流。 在你操作和管理的 Kubernetes 基础结构上运行逻辑应用时，还可以获得更高的控制度和灵活性。

在创建、设计和部署逻辑应用方面，Azure Arc 和单租户逻辑应用的体验存在细微的差别。 使用已启用 Azure Arc 的逻辑应用时，主要差别在于逻辑应用将在自定义位置运行。 此位置已映射到你在其中安装并启用了 Azure 应用服务平台扩展捆绑包的、已启用 Arc 的 Kubernetes 群集。

例如，此群集可以是 Azure Kubernetes 服务、裸机 Kubernetes 或其他设置。 通过扩展捆绑包，可在 Kubernetes 群集上运行 Azure 逻辑应用、Azure Functions 和 Azure 应用服务等平台服务。

有关详细信息，请查看以下文档：

- [单租户环境与其他 Azure 逻辑应用环境](../logic-apps/single-tenant-overview-compare.md)
- [Azure Kubernetes 服务概述](../aks/intro-kubernetes.md)
- [什么是已启用 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)
- [已启用 Azure Arc 的 Kubernetes 上的自定义位置](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [Azure Arc 上的应用服务、函数和逻辑应用（预览版）](../app-service/overview-arc-integration.md)
- [设置启用了 Azure Arc 的 Kubernetes 群集，以便运行应用服务、函数和逻辑应用（预览）](../app-service/manage-create-arc-environment.md)

<a name="when-to-use"></a>

## <a name="when-to-use-arc-enabled-logic-apps"></a>何时使用已启用 Arc 的逻辑应用

尽管 Kubernetes 可提供更高的控制度和灵活性，但它也会产生操作开销。 如果你对逻辑应用服务感到满意，觉得它能够满足你的需求，则我们建议你继续使用此服务。 但对于以下情况，请考虑使用已启用 Azure Arc 的逻辑应用：

- 已在 Kubernetes 上运行所有应用和服务。 你想要将这些流程和控制扩展到所有其他 PaaS 服务。

- 你想要使用逻辑应用作为集成平台。 但是，你需要获得精细的网络和计算控制度以及灵活性。 你不想要使用集成服务环境 (ISE) 或应用服务环境 (ASE)。

- 出于安全原因，你需要控制逻辑应用的运行位置，例如，在自己的区域或自己的数据中心内运行。 

- 你想要在多云方案中运行逻辑应用，并使用逻辑应用服务作为所有应用程序（无论它们在何处运行）的唯一集成平台。

<a name="compare"></a>

## <a name="compare-offerings"></a>比较产品/服务

下表对当前 Azure 逻辑应用产品/服务中的功能做了概要性的比较：

:::row:::
   :::column:::
      **功能**
   :::column-end:::
   :::column:::
      多租户逻辑应用（消耗）
   :::column-end:::
   :::column:::
      单租户逻辑应用（标准）
   :::column-end:::
   :::column:::
      独立容器
   :::column-end:::
   :::column:::
      **Azure Arc**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      本地开发
   :::column-end:::
   :::column:::
      Visual Studio Code、Visual Studio
   :::column-end:::
   :::column:::
      Visual Studio Code，包括断点调试的运行历史记录和概述
   :::column-end:::
   :::column:::
      Visual Studio Code
   :::column-end:::
   :::column:::
      Visual Studio Code，包括断点调试的运行历史记录和概述
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Hosting
   :::column-end:::
   :::column:::
      仅在 Azure 中运行
   :::column-end:::
   :::column:::
      仅在 Azure 中运行
   :::column-end:::
   :::column:::
      在运行容器的任何位置运行
   :::column-end:::
   :::column:::
      在包含已启用 Arc 的 Kubernetes 群集的任何位置运行
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      管理
   :::column-end:::
   :::column:::
      完全托管的逻辑应用体验
   :::column-end:::
   :::column:::
      完全托管的逻辑应用体验
   :::column-end:::
   :::column:::
      不受管理
   :::column-end:::
   :::column:::
      托管逻辑应用体验，包含 Kubernetes 级别的操作控制
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      监视
   :::column-end:::
   :::column:::
      可根据需要在 Azure 门户中进行监视，包括查看运行历史记录、重新提交运行和使用 Application Insights 功能
   :::column-end:::
   :::column:::
      可根据需要在 Azure 门户中进行监视，包括查看运行历史记录、重新提交运行和使用 Application Insights 功能
   :::column-end:::
   :::column:::
      只能使用 Application Insights 或其他容器监视工具进行监视
   :::column-end:::
   :::column:::
      可根据需要在 Azure 门户中进行监视，包括查看运行历史记录、重新提交运行和使用 Application Insights 功能
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      扩展
   :::column-end:::
   :::column:::
      使用消耗计划控制缩放
   :::column-end:::
   :::column:::
      使用标准计划控制缩放
   :::column-end:::
   :::column:::
      不可用
   :::column-end:::
   :::column:::
      使用[基于 Kubernetes 的事件驱动的自动缩放 (KEDA)](https://keda.sh/) 控制缩放。 基于队列长度配置缩放事件。
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用已启用 Arc 的逻辑应用创建和部署工作流](azure-arc-enabled-logic-apps-create-deploy-workflows.md)
