---
title: 使用 Azure 专用链接将网络连接到 Azure Monitor
description: 设置 Azure Monitor 专用链接范围，以安全地将网络连接到 Azure Monitor。
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: bdd47962b56324f9832070b13644b5489ee38989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727236"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>使用 Azure 专用链接将网络连接到 Azure Monitor

通过 [Azure 专用链接](../../private-link/private-link-overview.md)，可以使用专用终结点将 Azure 平台即服务 (PaaS) 服务安全地链接到虚拟网络。 如果你有很多的服务，只需为每个资源设置一个终结点即可。 不过，Azure Monitor 是不同互连服务的集合，它们相互协同，共同监视你的工作负载。 

## <a name="advantages"></a>优势

借助专用链接，你可以：

- 以专用方式连接到 Azure Monitor，无需开放任何公共网络访问权限
- 确保仅可通过授权的专用网络访问你正在监视的数据
- 定义通过你的专用终结点连接的特定 Azure Monitor 资源，从而防止你的专用网络出现数据泄露的情况
- 使用 ExpressRoute 和专用链接将你的专用本地网络安全地连接到 Azure Monitor
- 将所有流量保留在 Microsoft Azure 主干网络中

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

Azure Monitor 专用链接范围 (AMPLS) 将专用终结点连接（及包含它们的 VNet）连接到一个或多个 Azure Monitor 资源 - Log Analytics 工作区和 Application Insights 组件。

![基本资源拓扑关系图](./media/private-link-security/private-link-basic-topology.png)

* VNet 中的专用终结点允许其通过网络池中的专用 IP 而不是终结点的公共 IP 来访问 Azure Monitor 终结点。 这样就可以继续使用 Azure Monitor 资源，无需向不需要的出站流量开放 VNet。 
* 从专用终结点到 Azure Monitor 资源的流量将通过 Microsoft Azure 主干，而不会路由到公用网络。 
* 可以配置每个工作区或组件，以允许或拒绝从公用网络引入和查询。 这提供了资源级保护，因此可以控制流入特定资源的流量。

> [!NOTE]
> 一个 Azure Monitor 资源可属于多个 AMPLS，但无法将一个 VNet 连接到多个 AMPLS。 

### <a name="azure-monitor-private-links-and-your-dns-its-all-or-nothing"></a>Azure Monitor 专用链接和 DNS：全有或全无
一些 Azure Monitor 服务使用全局终结点，这意味着它们会针对任何工作区/组件提供请求。 设置专用链接连接时，DNS 会更新为将 Azure Monitor 终结点映射到专用 IP，以便通过专用链接发送流量。 当涉及到全局终结点时，设置专用链接（即使是为单个资源设置）会影响发往所有资源的流量。 换言之，无法做到仅为特定的组件或工作区创建专用链接连接。

#### <a name="global-endpoints"></a>全局终结点
最重要的是，发往以下全局终结点的流量将通过专用链接发送：
* 所有 Application Insights 终结点 - 用于处理 Application Insights 资源中的引入、实时指标、探查器、调试器等的终结点是全局性的。
* 查询终结点 - 用于处理对 Application Insights 和 Log Analytics 资源的查询的终结点是全局性的。

这实际上意味着，所有 Application Insights 流量将发送到专用链接，对 Application Insights 和 Log Analytics 资源的所有查询将发送到专用链接。

发往未添加到 AMPLS 的 Application Insights 资源的流量将通不过专用链接验证，因此将会失败。

![“全有或全无”行为示意图](./media/private-link-security/all-or-nothing.png)

#### <a name="resource-specific-endpoints"></a>特定于资源的终结点
除查询终结点以外的所有 Log Analytics 终结点都特定于工作区。 因此，创建指向特定 Log Analytics 工作区的专用链接不会影响发往其他工作区的引入（或其他）流量，这些流量会继续使用公共 Log Analytics 终结点。 不过，所有查询将通过专用链接发送。

### <a name="azure-monitor-private-link-applies-to-all-networks-that-share-the-same-dns"></a>Azure Monitor 专用链接适用于共享同一 DNS 的所有网络
某些网络由多个 VNet 或其他互连网络组成。 如果这些网络共享同一 DNS，则在其中任一网络中设置专用链接都会更新 DNS，并影响所有网络中的流量。 由于上述“全有或全无”行为，注意到这一点特别重要。

![多个 VNet 中的 DNS 替代示意图](./media/private-link-security/dns-overrides-multiple-vnets.png)

在上面的关系图中，VNet 10.0.1.x 首先连接到 AMPLS1，并将 Azure Monitor 的全局终结点映射到其范围内的 IP。 稍后，VNet 10.0.2.x 连接到 AMPLS2，并替代具备其范围内 IP 的“相同全局终结点”的 DNS 映射。 由于这些 VNet 不对等互连，因此第一个 VNet 现在无法访问这些终结点。


## <a name="next-steps"></a>后续步骤
- [设计专用链接设置](private-link-design.md)
- 了解如何[配置专用链接](private-link-configure.md)

<h3><a id="connect-to-a-private-endpoint"></a></h3>
