---
title: Azure 负载均衡器的见解
description: 使用负载均衡器见解来实现快速的故障本地化和明智的设计决策
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 63b91194c9ffb10fd8f4c5f1341eaf74bc81f5e1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694875"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>使用见解监视和配置 Azure 负载均衡器

通过 Azure Monitor 网络，你可以为负载均衡器提供功能依赖关系可视化和预配置的度量值仪表板。 这些视觉对象可帮助您做出明智的设计决策，并快速地本地化、诊断和解决任何错误。

>[!NOTE] 
>请注意，此功能处于预览阶段，并且功能依赖关系视图和预配置的仪表板可能会更改以改善此体验

>[!IMPORTANT]
>若要查看预配置的指标仪表板中的负载平衡器命名空间中的指标，需要标准负载均衡器。 不过，你仍然能够查看 VM、虚拟机规模集和连接监视器命名空间中的指标，但我们建议 [升级到标准](./upgrade-basic-standard.md) ，以便为任何生产工作负荷利用一组强大的负载均衡器指标。

## <a name="functional-dependency-view"></a>函数依赖关系视图

函数依赖关系视图可让你甚至最复杂的负载均衡器设置。 对于最新的负载均衡器配置的视觉反馈，你可以进行更新，同时确保配置的配置。

可以访问 Azure 中负载均衡器资源的 "Insights" 边栏选项卡来访问此视图。

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="函数依赖关系视图的 Depecition。负载均衡器的前端可通过配置的规则显示连接到后端池成员。对于标准负载均衡器，从负载均衡规则到后端池实例的线路是基于运行状况探测状态进行颜色编码的。" border="true":::

对于标准负载均衡器，后端池资源用运行状况探测状态进行了颜色编码，表示后端池的当前可用性以提供流量。 除了上述拓扑，还会提供运行状况状态图，提供应用程序运行状况的快照视图。

## <a name="metrics-dashboard"></a>指标仪表板

从负载均衡器的 "Insights" 边栏选项卡中，可以选择更详细的指标来查看预配置的 [Azure Monitor 工作簿，该工作簿](../azure-monitor/platform/workbooks-overview.md) 包含与负载均衡器的特定方面相关的指标视觉对象。 此仪表板会显示负载均衡器状态，并链接到页面顶部的相关文档。

首先，将显示 "概述" 选项卡。可以浏览可用的选项卡，每个选项卡都包含与负载均衡器的特定方面相关的视觉对象。 每个选项卡底部的仪表板中提供了每个选项卡的明确指导。

当前可用的仪表板选项卡包括：
* 概述
* 前端和后端可用性
* 数据吞吐量
* 流分发
* 连接监视器
* 指标定义 

### <a name="overview-tab"></a>概述选项卡
"概述" 选项卡包含一个可搜索网格，其中包含每个连接到负载均衡器的前端 Ip 的总体数据路径可用性和运行状况探测状态。 这些度量值指示前端 IP 是否响应，后端池中的计算实例是否能够对入站连接进行单独的响应。

你还可以在此页上查看每个前端 IP 的总体数据吞吐量，以了解你是要生成还是接收预期流量级别。 页面底部的指导会将您定向到适当的选项卡，应会看到任何不规则值。

### <a name="frontend-and-backend-availability-tab"></a>前端和后端可用性选项卡
前端和后端可用性选项卡显示了几个有用视图中提供的数据路径吞吐量和运行状况探测状态指标。 第一个图表显示聚合值，以便您可以确定是否存在问题。 关系图的其余部分显示这些度量值由各种维度拆分，以便您可以进行故障排除并确定任何入站可用性问题的来源。

页面底部提供了查看这些图形的工作流，其中有各种症状的常见原因。 

### <a name="data-throughput-tab"></a>数据吞吐量选项卡
通过 "数据吞吐量" 选项卡，可以查看入站和出站吞吐量，以确定流量模式是否符合预期。 它将显示按前端 IP 和前端端口拆分的入站和出站数据吞吐量，以便您可以确定所运行的服务是如何单独执行的。

### <a name="flow-distribution"></a>流分发
"流分布" 选项卡可帮助你可视化和管理后端实例正在接收和生成的流的数量。 它显示入站和出站流量的流创建速率和流计数，以及每个 VM 和虚拟机规模集实例接收的网络流量。 

这些视图可提供有关负载平衡器配置或流量模式是否导致不均衡流量的反馈。 例如，如果已配置会话相关性，并且单个客户端发出了不相称的请求数。 它还会让你知道你是否正在接近计算机大小的 [每个 VM 流限制](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) 。

### <a name="connection-monitors"></a>连接监视器
"连接监视器" 选项卡将显示已配置的所有 [连接监视器](../network-watcher/connection-monitor.md)  在全局地图上的往返延迟。 这些视觉对象为具有严格延迟要求的服务提供了有用的信息。 为了满足您的需求，您可能需要添加更多区域部署或迁移到 [跨区域负载平衡](./cross-region-overview.md) 模型

### <a name="metric-definitions"></a>指标定义
"指标定义" 选项卡包含 [多维指标一文](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)中显示的所有信息。

## <a name="next-steps"></a>后续步骤
* 查看仪表板并使用以下链接提供反馈（如果有可以改进的内容）
* [查看指标文档，确保了解每个度量值的计算方式](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [为负载均衡器创建连接监视器](../network-watcher/connection-monitor.md)
* [创建自己的工作簿](../azure-monitor/platform/workbooks-overview.md)，可以通过单击详细度量值仪表板中的 "编辑" 按钮来执行灵感