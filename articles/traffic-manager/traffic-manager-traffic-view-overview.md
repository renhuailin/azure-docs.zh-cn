---
title: Azure 流量管理器中的流量视图
description: 在本简介中，了解流量管理器流量视图的工作原理。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743043"
---
# <a name="traffic-manager-traffic-view"></a>流量管理器流量视图

流量管理器提供 DNS (域名系统) 级别路由。 此服务允许最终用户根据你选择的路由方法，定向到正常运行的终结点。 流量视图为流量管理器提供用户群视图（在 DNS 解析程序粒度级别）及其流量模式。 启用流量视图时，系统将处理此信息以提供可执行的建议。 

使用流量视图，可以：
- 了解用户群所在的位置（最多可达本地 DNS 解析程序级别粒度）。
- 查看源自这些区域的流量（以 Azure 流量管理器处理的 DNS 查询形式监测）。
- 深入了解什么是这些用户体验到的典型延迟。
- 深入研究特定流量模式，从其中每个用户群到你拥有终结点的 Azure 区域。 

例如，你可以使用流量视图来了解哪些区域有大量流量，但会受到更高的延迟。 然后，你将使用此信息来规划你在新的 Azure 区域中的空间扩展。 这样，你的用户将会有更低的延迟体验。

## <a name="how-traffic-view-works"></a>流量视图的工作原理

流量视图的工作方式是查看过去7天内针对配置文件接收的传入查询。 在传入查询信息中，流量视图提取用于表示用户位置的 DNS 解析程序的源 IP。 此信息将在 DNS 解析器级别组合在一起，以创建用户基区域。 流量管理器维护 IP 地址的地理信息。 然后，流量管理器会查看查询要路由到的 Azure 区域，并为这些区域中的用户构造一个流量流程图。
 
在下一步中，流量管理器会将用户基区域与网络智能延迟表关联到 Azure 区域映射。 此表适用于不同的最终用户网络，以了解用户在连接到 Azure 区域时所经历的平均延迟。 然后，所有这些计算都按每个本地 DNS 解析程序 IP 级别进行合并，并显示给你。 可以通过各种方式来使用信息。

流量视图数据更新的频率取决于多个内部服务变量。 但是，数据每24小时更新一次。

>[!NOTE]
>流量视图中描述的延迟是最终用户和其所连接到的 Azure 区域之间的代表性延迟，不是 DNS 查找延迟。 流量视图对本地 DNS 解析程序与查询被路由到的 Azure 区域之间的延迟进行了最佳估算，如果可用数据不足，返回的延迟将为 NULL。 

## <a name="visual-overview"></a>视觉概览

导航到 "流量管理器" 页中的 " **流量视图** " 部分时，会看到一个地图，其中包含流量视图 insights 的覆盖区。 该图提供了有关用户群以及流量管理器配置文件终结点的信息。

![流量管理器流量视图地理视图][1]

### <a name="user-base-information"></a>用户群信息

对于其位置信息可用的本地 DNS 解析程序，它们显示在映射中。 DNS 解析程序的颜色表示那些使用 DNS 解析程序进行流量管理器查询的最终用户体验到的平均延迟。

如果将鼠标悬停在图中 DNS 解析程序位置上方，则会显示：
- DNS 解析程序的 IP 地址
- 流量管理器从其看到的 DNS 查询流量
- 流量从 DNS 解析程序路由到的终结点，是终结点和 DNS 解析程序之间的一条线 
- 从该位置到终结点的平均延迟，以连接二者的线的颜色来表示

### <a name="endpoint-information"></a>终结点信息

终结点所在的 Azure 区域在地图中显示为蓝色点。 如果你的终结点是外部终结点，并且没有 Azure 区域映射，则它们将显示在地图的顶部。 选择 "任何终结点"，以查看不同位置 (基于使用的 DNS 解析程序) 从中将流量定向到该终结点。 连接在终结点和 DNS 解析程序位置之间显示为一条线。 它们根据该对之间的典型延迟进行着色。 你可以看到终结点的名称以及它在其中运行的 Azure 区域。 还显示了通过此流量管理器配置文件定向到它的请求总数。


## <a name="tabular-listing-and-raw-data-download"></a>表格式列表和原始数据下载

可以在 Azure 门户中以表格格式查看流量视图数据。 每个 DNS 解析程序 IP/终结点对都有一个条目，其中显示：

* DNS 解析程序的 IP 地址。
* 名称。
* 终结点所在的 Azure 区域的地理位置 (（如果有）) 。
* 与该终结点的 DNS 解析程序关联的请求量。
* 与使用该 DNS 的最终用户关联的代表性延迟 (在可用) 。 

也可将流量视图数据下载为 CSV 文件，该文件可以用作所选分析工作流的一部分。

## <a name="billing"></a>计费

使用流量视图时，将根据用于创建所提供见解的数据点的数量进行计费。 目前，所用的唯一数据点类型是根据流量管理器配置文件接收的查询。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="faqs"></a>常见问题解答

* [流量视图有什么作用？](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [如何从使用流量视图中受益？](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [流量视图与 Azure 监视器提供的流量管理器指标有何不同？](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [流量视图是否使用 EDNS 客户端子网信息？](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [流量视图使用多少天的数据？](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [流量视图如何处理外部终结点？](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [是否需要对我的订阅中的每个配置文件启用流量视图？](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [如何关闭流量视图？](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [流量视图计费如何工作？](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>后续步骤

- 了解[流量管理器的工作原理](traffic-manager-overview.md)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png