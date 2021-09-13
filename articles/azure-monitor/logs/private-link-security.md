---
title: 使用 Azure 专用链接将网络连接到 Azure Monitor
description: 设置 Azure Monitor 专用链接范围，以安全地将网络连接到 Azure Monitor。
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: e175439cacb75fc50574f172d9e1e34cba4cdbd7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426393"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>使用 Azure 专用链接将网络连接到 Azure Monitor

通过 [Azure 专用链接](../../private-link/private-link-overview.md)，可以使用专用终结点将 Azure 平台即服务 (PaaS) 资源安全地链接到虚拟网络。 Azure Monitor 是不同互连服务的集合，它们相互协同，共同监视你的工作负载。 Azure Monitor 专用链接将专用终结点连接到一组 Azure Monitor 资源，从而定义监视网络的边界。 该集合称为 Azure Monitor 专用链接范围 (AMPLS)。


## <a name="advantages"></a>优势

借助专用链接，你可以：

- 以专用方式连接到 Azure Monitor，无需开放任何公共网络访问权限
- 确保仅可通过授权的专用网络访问你正在监视的数据
- 定义通过你的专用终结点连接的特定 Azure Monitor 资源，从而防止你的专用网络出现数据泄露的情况
- 使用 ExpressRoute 和专用链接将你的专用本地网络安全地连接到 Azure Monitor
- 将所有流量保留在 Microsoft Azure 主干网络中

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

### <a name="overview"></a>概述
Azure Monitor 专用链接范围将专用终结点（及包含它们的 VNet）连接到一个或多个 Azure Monitor 资源 - Log Analytics 工作区和 Application Insights 组件。

![基本资源拓扑关系图](./media/private-link-security/private-link-basic-topology.png)

* Azure Monitor 专用链接会将专用终结点连接到一组 Azure Monitor 资源，即 Log Analytics 工作区和 Application Insights 资源。 该集合称为 Azure Monitor 专用链接范围 (AMPLS)。
* VNet 中的专用终结点允许其通过网络池中的专用 IP 而不是终结点的公共 IP 来访问 Azure Monitor 终结点。 这样就可以继续使用 Azure Monitor 资源，无需向不需要的出站流量开放 VNet。 
* 从专用终结点到 Azure Monitor 资源的流量将通过 Microsoft Azure 主干，而不会路由到公用网络。
* 可将 Azure Monitor 专用链接范围（或连接到它的特定网络）配置为使用首选访问模式 - 仅允许到专用链接资源的流量，或允许到专用链接资源和非专用链接资源（AMPLS 外的资源）的流量
* 可以配置每个工作区或组件，以允许或拒绝从公用网络引入和查询。 这提供了资源级保护，因此可以控制流入特定资源的流量。

> [!NOTE]
> 一个 VNet 只能连接到单个 AMPLS，其中列出了最多 50 个可以通过专用链接访问的资源。

### <a name="azure-monitor-private-link-relies-on-your-dns"></a>Azure Monitor专用链接依赖于 DNS
设置专用链接连接时，DNS 区域设置为将 Azure Monitor 终结点映射到专用 IP，以便通过专用链接发送流量。 Azure Monitor 使用特定于资源的终结点以及处理多个工作区/组件的流量的区域或全局终结点。 当涉及到区域或全局终结点时，设置专用链接（即使是为单个资源设置）会影响控制发往所有资源的流量的 DNS 映射。 换句话说，发往所有工作区或组件的流量可能会受到单个专用链接设置的影响。

#### <a name="global-endpoints"></a>全局终结点
最重要的是，发往以下全局终结点的流量将通过专用链接发送：
* 所有 Application Insights 终结点 - 用于处理 Application Insights 资源中的引入、实时指标、探查器、调试器等的终结点是全局性的。
* 查询终结点 - 用于处理对 Application Insights 和 Log Analytics 资源的查询的终结点是全局性的。

这实际上意味着，所有 Application Insights 流量将发送到专用链接，对 Application Insights 和 Log Analytics 资源的所有查询将发送到专用链接。

发往未添加到 AMPLS 的 Application Insights 资源的流量将通不过专用链接验证，因此将会失败。

#### <a name="resource-specific-endpoints"></a>特定于资源的终结点
除查询终结点以外的所有 Log Analytics 终结点都特定于工作区。 因此，创建指向特定 Log Analytics 工作区的专用链接不会影响发往其他工作区的数据引入，这些数据引入会继续使用公共终结点。


> [!NOTE]
> 仅为共享同一 DNS 的所有网络创建单个 AMPLS。 创建多个 AMPLS 资源将导致 Azure Monitor DNS 区域相互替代，并破坏现有环境。

### <a name="private-link-access-modes-private-only-vs-open"></a>专用链接访问模式：仅专用与公开
正如 [Azure Monitor 专用链接依赖于 DNS](#azure-monitor-private-link-relies-on-your-dns) 中所述，对于共享同一 DNS 的所有网络，只应创建单个 AMPLS 资源。 因此，使用单个全球或区域 DNS 的组织实际上有单个专用链接来管理发往所有全球或区域网络中所有 Azure Monitor 资源的流量。

对于 2021 年 9 月之前创建的专用链接，这意味着 - 
* 日志引入仅适用于 AMPLS 中的资源。 无论订阅或租户如何，都拒绝引入所有其他资源（跨共享同一 DNS 的所有网络）。
* 查询具有更开放的行为，允许查询请求甚至访问不在 AMPLS 中的资源。 这里的目的是避免将客户查询中断到不在 AMPLS 中的资源，并允许以资源为中心的查询返回完整的结果集。

然而，事实证明，这种行为对某些客户来说过于严格（因为它中断了对不在 AMPLS 中的资源的引入），对其他客户来说过于宽松（因为它允许查询不在 AMPLS 中的资源）并且通常会令人困惑。

因此，从 2021 年 9 月开始创建的专用链接具有新的强制性 AMPLS 设置，明确设置专用链接应如何影响网络流量。 创建新的 AMPLS 资源时，现在需要选择所需的访问模式，分别用于引入和查询。 
* 仅专用模式 - 仅允许流量发往专用链接资源
* 公开模式 - 使用专用链接与 AMPLS 中的资源进行通信，但也允许流量继续发往其他资源。 请参阅[控制专用链接如何应用于网络](./private-link-design.md#control-how-private-links-apply-to-your-networks)，了解详细信息。

> [!NOTE]
> Log Analytics 引入操作使用资源特定的终结点。 因此，它不遵循 AMPLS 访问模式。 通过专用链接引入到 AMPLS 中的工作区，而如果引入到 AMPLS 之外的工作区，则使用默认公共终结点。 若要确保引入请求无法访问 AMPLS 之外的资源，请阻止网络访问公共终结点。

## <a name="next-steps"></a>后续步骤
- [设计专用链接设置](private-link-design.md)
- 了解如何[配置专用链接](private-link-configure.md)

<h3><a id="connect-to-a-private-endpoint"></a></h3>
