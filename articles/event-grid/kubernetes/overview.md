---
title: Kubernetes 上的 Azure 事件网格 - 概述
description: 本文概括性介绍具有 Azure Arc 的 Kubernetes 上的事件网格。
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: fdc8c60f2c2cae7368a2e35317de2cfb8274a060
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414946"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-preview---overview"></a>具有 Azure Arc 的 Kubernetes 上的事件网格（预览）- 概述
本文概括性介绍 Kubernetes 上的事件网格、其用例、其提供的功能以及其与 Azure 事件网格的不同之处。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="what-is-event-grid"></a>什么是事件网格？
事件网格是一个事件代理，用于集成使用事件驱动的体系结构的工作负载。 事件驱动的体系结构使用事件来传达系统状态的更改，是分离式体系结构（例如使用微服务的体系结构）中的常见集成方法。 事件网格提供发布-订阅通信模型，也称为推送-推送通信模型，在该模型中，系统会向订阅者发送（推送）事件，而那些订阅者不一定知道发送事件的发布者是谁。 此模型与经典推送-拉取模型（例如，Azure 服务总线或 Azure 事件中心使用的模型）形成对比，在后者中，客户端从消息代理拉取消息，因此，消息代理与使用消息的客户端之间存在更强的耦合。

提供两个版本的事件网格：Azure 事件网格，这是 Azure 上完全托管的 PaaS 服务，以及具有 Azure Arc 的 Kubernetes 上的事件网格，它使你可以在 Kubernetes 群集上使用事件网格，无论它部署在本地还是云端。 

为清楚起见，本文中使用术语“事件网格”来指代常规服务功能，无论使用的是什么版本。 我们使用“Azure 事件网格”来指代 Azure 上托管的托管服务。 为简洁起见，我们将“具有 Azure Arc 的 Kubernetes 上的事件网格”称为“Kubernetes 上的事件网格” 。

无论使用哪种版本的事件网格，都会有一个“事件发布者”，他们将事件发送到事件网格，以及一个或多个“事件订阅者”，他们会公开从事件网格接收事件的终结点 。 并非所有发布到事件网格的事件都需要传递到所有事件订阅者。 利用事件网格，可以通过“事件订阅”中定义的一组配置设置来选择应路由到特定目标的事件。 可以在事件订阅中使用筛选器，以将特定事件路由到一个终结点或多播到多个终结点。 事件网格也提供具有重试逻辑的可靠发送机制。 事件网格也基于开放标准，并支持[云事件 1.0 架构规范](https://github.com/cloudevents/spec/blob/master/spec.md)。


## <a name="event-grid-on-kubernetes-with-azure-arc"></a>具有 Azure Arc 的 Kubernetes 上的事件网格
具有 Azure Arc 的 Kubernetes 上的事件网格是一种产品/服务，可让你在自己的 Kubernetes 群集中运行事件网格。 此功能是通过使用[已启用 Azure Arc 的 Kubernetes](../../azure-arc/kubernetes/overview.md) 启用的。 通过已启用 Azure Arc 的 Kubernetes，可将[受支持的 Kubernetes 群集](install-k8s-extension.md#supported-kubernetes-distributions)连接到 Azure。 连接后，可在其上[安装事件网格](install-k8s-extension.md)。 

### <a name="use-case"></a>用例
Kubernetes 上的事件网格支持各种事件驱动的集成方案。 但是，受支持且表述为用户情景的主要涵盖方案是：

“作为部署到 Kubernetes 群集的系统的所有者，我想通过发布事件并配置这些事件的路由来传达系统的状态更改，以便由我控制或通过其他方式控制的事件处理程序可以按其认为合适的方式处理系统的事件。”

可帮助你实现上述要求的功能：[事件网格主题](/rest/api/eventgrid/version2020-10-15-preview/topics)。

### <a name="event-grid-on-kubernetes-at-a-glance"></a>Kubernetes 上的事件网格概览
从用户的角度来看，Kubernetes 上的事件网格由以下蓝色资源组成：

:::image type="content" source="./media/overview/event-grid-topics.png" alt-text="资源" lightbox="./media/overview/event-grid-topics.png":::

* 主题是一种输入通道，用于公开发布者向事件网格发送事件时发送到的终结点。
* 事件订阅是包含配置设置的资源，用于筛选事件，并将其路由到传递事件的目标。
* 事件是有关状态更改的公告。
* 事件处理程序是一种应用程序或服务，用于接收事件，并以某种方式响应或处理事件。 有时，我们也将事件处理程序称为“事件订阅服务器”。 在上图中，事件处理程序是部署到 Kubernetes 群集 (K8s) 和 Azure 服务总线服务的 API。

有关这些概念的详细信息，请参阅 [Azure 事件网格中的概念](concepts.md)。

### <a name="sample-workload-integration-scenarios-and-destinations"></a>工作负载集成方案和目标示例

可以集成群集上运行的工作负载。 发布者可以是 Kubernetes 群集上运行的任何服务，也可以是有权访问发布者将事件发送到的主题终结点（由事件网格代理托管）的任何工作负载。

:::image type="content" source="./media/overview/event-grid-intra-cluster-integration.png" alt-text="群集内集成" lightbox="./media/overview/event-grid-intra-cluster-integration.png":::


还可以在网络中的其他位置部署一个发布者，以将事件发送到部署到某个 Kubernetes 群集的事件网格：

:::image type="content" source="./media/overview/event-grid-in-network-integration.png" alt-text="网络内集成" lightbox="./media/overview/event-grid-in-network-integration.png":::

利用 Kubernetes 上的事件网格，可以将事件转发到 Azure 以便进一步处理、存储或可视化：

:::image type="content" source="./media/overview/event-grid-forward-events.png" alt-text="将事件转发到 Azure":::

#### <a name="destinations"></a>Destinations
事件处理程序目标可以是事件网格可通过网络（公共或专用）访问且具有访问权限（不受某些身份验证机制的保护）的任何 HTTPS 或 HTTP 终结点。 创建事件订阅时，可定义事件发送目标。 有关详细信息，请参阅[事件处理程序](event-handlers.md)。 

## <a name="features"></a>功能
Kubernetes 上的事件网格支持[事件网格主题](/rest/api/eventgrid/version2020-10-15-preview/topics)，这也是 [Azure 事件网格](../custom-topics.md)提供的一项功能。 事件网格主题可帮助你实现[主集成用例](#use-case)，在该用例中，你需要将系统与你拥有的或以其他方式可供系统访问的另一个工作负载集成。

可通过 Kubernetes 上的 Azure 事件网格获取的一些功能包括：

* **[事件筛选](filter-events.md)** ：筛选事件类型、事件主题或事件数据，以确保事件处理程序仅接收相关事件。
* **扇出**：订阅到相同事件的多个终结点，以将该事件的副本发送到多个位置。
* **基于开放标准**：使用 CNCF 的 [云事件 1.0 架构规范](https://github.com/cloudevents/spec/blob/master/spec.md)定义事件。
* **可靠性**：事件网格具有事件发送重试逻辑，可确保事件到达其目标。

有关详细信息，请参阅 [Kubernetes 上的事件网格支持的功能](features.md)。

## <a name="pricing"></a>定价 
具有 Azure Arc 的 Kubernetes 上的事件网格在预览版期间免费提供。

## <a name="next-steps"></a>后续步骤
按照以下步骤开始使用 Kubernetes 上的事件网格来路由事件。

1. [将群集连接到 Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。
1. [安装事件网格扩展](install-k8s-extension.md)，这是将事件网格部署到 Kubernetes 群集的实际资源。 若要详细了解扩展，请参阅[事件网格扩展](install-k8s-extension.md#event-grid-extension)部分了解详细信息。 
1. [创建自定义位置](../../azure-arc/kubernetes/custom-locations.md)。 自定义位置表示群集中的命名空间，是主题和事件订阅的部署位置。
1. [创建主题和一个或多个订阅](create-topic-subscription.md)。
1. [发布事件](create-topic-subscription.md)。

以下是可以使用的更多资源：

* [数据平面 SDK](../sdk-overview.md#data-plane-sdks)。
* [使用数据平面 SDK 发布事件示例](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)。
* [事件网格 CLI](/cli/azure/eventgrid)。
* [管理 SDK](../sdk-overview.md#management-sdks)。