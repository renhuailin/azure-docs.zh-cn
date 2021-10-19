---
title: 跨区域负载均衡器（预览版）
titleSuffix: Azure Load Balancer
description: Azure 负载均衡器的跨区域负载均衡器层概述。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: cf094664fab07e9a75c890899dff9cd0118d12fc
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614396"
---
# <a name="cross-region-load-balancer-preview"></a>跨区域负载均衡器（预览版）

Azure 标准负载均衡器支持跨区域负载均衡，来实现如下所述的异地冗余高可用性方案：

* 源自多个区域的传入流量。
* [即时全局故障转移](#regional-redundancy)到下一个最佳区域部署。
* 将负载跨区域分配到[延迟超低](#ultra-low-latency)的最近 Azure 区域。
* 可在单个终结点后面进行[纵向扩展/缩减](#ability-to-scale-updown-behind-a-single-endpoint)。
* [静态 IP](#static-ip)
* [客户端 IP 保留](#client-ip-preservation)
* 无需接受培训即可[在现有负载均衡器的基础上构建解决方案](#build-cross-region-solution-on-existing-azure-load-balancer)

> [!IMPORTANT]
> 跨区域负载均衡器目前以预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

跨区域负载均衡器的前端 IP 配置是静态的，将播发到[大多数 Azure 区域](#participating-regions)。

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="跨区域负载均衡器示意图。" border="true":::

> [!NOTE]
> 跨区域负载均衡器上负载均衡规则的后端端口应与区域标准负载均衡器上负载均衡规则/入站 NAT 规则的前端端口相匹配。 

### <a name="regional-redundancy"></a>区域冗余

通过将全局前端公共 IP 地址添加到现有负载均衡器来配置区域冗余。 

如果一个区域出现故障，则会将流量路由到下一个最近的正常运行的区域负载均衡器。  

跨区域负载均衡器的运行状况探测每隔 20 秒收集有关可用性的信息。 如果一个区域负载均衡器的可用性降到了 0，跨区域负载均衡器将检测到故障。 然后，会将区域负载均衡器取出轮换列表。 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="全局区域流量视图的示意图。" border="true":::

### <a name="ultra-low-latency"></a>超低延迟

地理邻近性负载均衡算法基于用户和区域部署的地理位置。 

从客户端启动的流量将进入最近的参与区域，并通过 Microsoft 全球主干网络进入最近的区域部署。 

例如，你有一个跨区域负载均衡器，并在 Azure 区域中有一个标准负载均衡器：

* 美国西部
* 北欧

如果流是从西雅图启动的，则流量将进入美国西部。 此区域是距离西雅图最近的参与区域。 流量将路由到位于美国西部的最近区域负载均衡器。

Azure 跨区域负载均衡器使用地理邻近性负载均衡算法来做出路由决策。 

使用多个区域负载均衡器计算地理邻近性时，将使用配置的区域负载均衡器负载分配模式来做出最终路由决策。

有关详细信息，请参阅[配置 Azure 负载均衡器的分配模式](./load-balancer-distribution-mode.md)。


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>可在单个终结点后面进行纵向扩展/缩减

向客户公开跨区域负载均衡器的全局终结点时，可以在全局终结点后面添加或删除区域部署，而不会发生中断。 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>静态 IP
跨区域负载均衡器附带静态公共 IP，这可以确保 IP 地址保持不变。 若要详细了解静态 IP，请参阅[此文](../virtual-network/public-ip-addresses.md#ip-address-assignment)

### <a name="client-ip-preservation"></a>客户端 IP 保留
跨区域负载均衡器是第 4 层直通网络负载均衡器。 此直通模式保留数据包的原始 IP。  原始 IP 可用于虚拟机上运行的代码。 这种 IP 保留可让你应用特定于 IP 地址的逻辑。

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>在现有 Azure 负载均衡器上构建跨区域解决方案
跨区域负载均衡器的后端池包含一个或多个区域负载均衡器。 

将现有负载均衡器部署添加到跨区域负载均衡器可以实现高可用性的跨区域部署。

**宿主区域** 是全局层的跨区域负载均衡器或公共 IP 地址的部署位置。 此区域不影响流量的路由方式。 如果宿主区域出现故障，流量流不受影响。

### <a name="home-regions"></a>宿主区域
* 美国东部 2
* 美国西部
* 西欧
* 东南亚
* 美国中部
* 北欧
* 东亚

> [!NOTE]
> 只能在上述 7 个区域中某个区域的全局层中部署跨区域负载均衡器或公共 IP。

“参与区域”是要播发负载均衡器全局公共 IP 的位置。

用户启动的流量将通过 Microsoft 核心网络传递到最近的参与区域。 

跨区域负载均衡器将流量路由到相应的区域负载均衡器。

### <a name="participating-regions"></a>参与区域
* 美国东部 
* 西欧 
* 美国中部 
* 美国东部 2 
* 美国西部 
* 北欧 
* 美国中南部 
* 美国西部 2 
* 英国南部 
* 东南亚 
* 美国中北部 
* Japan East 
* 东亚 
* 美国中西部 
* 澳大利亚东南部 
* 澳大利亚东部 
* 印度中部 

## <a name="limitations"></a>限制

* 跨区域前端 IP 配置仅限公共配置。 目前不支持内部前端。

* 无法将专用或内部负载均衡器添加到跨区域负载均衡器的后端池 

* 不支持跨区域 IPv6 前端 IP 配置。 

* 跨区域负载均衡器不支持 UDP 流量。 

* 目前无法配置运行状况探测。 默认的运行状况探测每隔 20 秒自动收集有关区域负载均衡器的可用性信息。 

* 目前无法与 Azure Kubernetes 服务 (AKS) 集成。 如果在后端中部署了 AKS 群集的情况下将跨区域负载均衡器与标准负载均衡器一起部署，连接将会断开。

## <a name="pricing-and-sla"></a>定价和 SLA
跨区域负载均衡器的 [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) 与标准负载均衡器相同。

 
## <a name="next-steps"></a>后续步骤

- 参阅[教程：使用 Azure 门户创建跨区域负载均衡器](tutorial-cross-region-portal.md)来创建跨区域负载均衡器。
- 详细了解[跨区域负载均衡器](https://www.youtube.com/watch?v=3awUwUIv950)的详细信息。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
