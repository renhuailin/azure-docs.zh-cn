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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743043"
---
# <a name="traffic-manager-traffic-view"></a>流量管理器流量视图

流量管理器提供 DNS（域名系统）级别路由。 此服务允许根据所选择的路由方法将最终用户定向到正常运行的终结点。 流量视图为流量管理器提供用户群视图（在 DNS 解析程序粒度级别）及其流量模式。 启用流量视图时，系统将处理此信息以提供可执行的建议。 

使用流量视图，可以：
- 了解用户群所在的位置（最多可达本地 DNS 解析程序级别粒度）。
- 查看源自这些区域的流量（以 Azure 流量管理器处理的 DNS 查询形式监测）。
- 深入了解什么是这些用户体验到的典型延迟。
- 深入研究特定流量模式，从其中每个用户群到你拥有终结点的 Azure 区域。 

例如，可以使用流量视图来了解哪些区域具有大量流量，但遭受较长的延迟。 然后，使用此信息来计划将占用空间扩展到新的 Azure 区域。 这样，用户会经历更低的延迟。

## <a name="how-traffic-view-works"></a>流量视图的工作原理

流量视图的工作原理是查看过去7天内针对配置文件收到的传入查询。 流量视图从传入查询信息提取用于表示用户位置的 DNS 解析程序的源 IP。 此信息以 DNS 解析程序级别组合在一起，以创建用户群区域。 流量管理器维护 IP 地址的地理信息。 然后，流量管理器查找查询所路由到的 Azure 区域，并为这些区域中的用户构造流量流映射。
 
在下一步中，流量管理器使用网络智能延迟表将用户群区域关联到 Azure 区域映射。 此表针对不同的最终用户网络进行维护，以了解这些区域中的用户在连接到 Azure 区域时所经历的平均延迟。 然后按每个本地 DNS 解析程序 IP 级别将所有这些计算组合起来，再向你显示。 可以通过各种方式来使用信息。

流量视图数据更新的频率取决于多个内部服务变量。 但是，数据每 24 小时更新一次。

>[!NOTE]
>流量视图中描述的延迟是最终用户和其所连接到的 Azure 区域之间的代表性延迟，不是 DNS 查找延迟。 流量视图对本地 DNS 解析程序与查询被路由到的 Azure 区域之间的延迟进行了最佳估算，如果可用数据不足，返回的延迟将为 NULL。 

## <a name="visual-overview"></a>视觉概览

导航到流量管理器页中的“流量视图”部分时，会看到一个地理图，其中包含流量视图见解。 该图提供了有关用户群以及流量管理器配置文件终结点的信息。

![流量管理器流量视图地理视图][1]

### <a name="user-base-information"></a>用户群信息

对于那些可以使用位置信息的本地 DNS 解析程序，用户群信息显示在图中。 DNS 解析程序的颜色表示那些使用 DNS 解析程序进行流量管理器查询的最终用户体验到的平均延迟。

如果将鼠标悬停在图中 DNS 解析程序位置上方，则会显示：
- DNS 解析程序的 IP 地址
- 流量管理器从其看到的 DNS 查询流量
- 流量从 DNS 解析程序路由到的终结点，是终结点和 DNS 解析程序之间的一条线 
- 从该位置到终结点的平均延迟，以连接二者的线的颜色来表示

### <a name="endpoint-information"></a>终结点信息

终结点所在的 Azure 区域在图中显示为蓝色点。 如果终结点在外部，且不存在映射到该终结点的 Azure 区域，则终结点显示在图顶部。 选择任意终结点即可查看不同的位置（基于所使用的 DNS 解析程序），流量从这些位置引导到该终结点。 连接显示为终结点和 DNS 解析程序位置之间的一条线。 颜色取决于该对之间的代表性延迟。 可以看到终结点的名称和其运行时所在的 Azure 区域。 还显示了通过流量管理器配置文件引导到该区域的请求的总量。


## <a name="tabular-listing-and-raw-data-download"></a>表格式列表和原始数据下载

可以在 Azure 门户中以表格格式查看流量视图数据。 每个 DNS 解析程序 IP/终结点对都有一个条目，其中显示：

* DNS 解析程序的 IP 地址。
* 名称。
* 终结点所在的 Azure 区域的地理位置（如果可用）。
* 与该 DNS 解析程序到该终结点相关联的请求量。
* 与使用该 DNS 的最终用户相关联的代表性延迟（在可用处）。 

也可将流量视图数据下载为 CSV 文件，该文件可以用作所选分析工作流的一部分。

## <a name="billing"></a>计费

使用流量视图时，将根据用于创建所显示建议的数据点数目进行计费。 目前，所用的唯一数据点类型是根据流量管理器配置文件接收的查询。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="faqs"></a>常见问题

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