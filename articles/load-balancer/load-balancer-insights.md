---
title: Azure 负载均衡器的见解
description: 利用负载均衡器的见解来实现快速的故障定位和制定明智的设计决策
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: kumud
ms.openlocfilehash: 7f87862cd922451113403b382930379f63e471e4
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357888"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>使用见解监视和配置 Azure 负载均衡器

通过用于网络的 Azure Monitor，可以获得适用于负载均衡器的函数依赖关系可视化和预配置的指标仪表板。 这些视觉对象有助于你制定明智的设计决策，并快速定位、诊断和解决任何故障。

>[!NOTE] 
>请注意，此功能为预览版，函数依赖关系视图和预配置的仪表板以后可能会改变以优化体验

>[!IMPORTANT]
>需要标准负载均衡器才能在预配置的指标仪表板中查看来自负载均衡器命名空间的指标。 你仍然可以从 VM、虚拟机规模集和连接监视器命名空间中看到指标，但是，对于任何生产工作负载，我们建议[升级到标准](./upgrade-basic-standard.md)，以充分利用可靠的负载均衡器指标集。

## <a name="functional-dependency-view"></a>函数依赖关系视图

借助函数依赖关系视图，甚至可以直观显示最复杂的负载均衡器设置。 通过负载均衡器最新配置的视觉反馈，你可以在更新的同时保留对配置的记忆。

你可以通过访问 Azure 中的负载均衡器资源的“见解”边栏选项卡来访问此视图。

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="函数依赖关系视图描述。可以看到，负载均衡器的前端通过配置的规则连接到后端池成员。对于标准负载均衡器，连接负载均衡规则与后端池实例的直线根据运行状况探测状态进行了颜色编码。" border="true":::

对于标准负载平衡器，后端池资源基于运行状况探测状态进行了颜色编码，指示用于提供流量的后端池的当前可用性。 除了上述拓扑外，还为你提供了运行状况的时间图，并提供了应用程序运行状况的快照视图。

## <a name="metrics-dashboard"></a>指标仪表板

在负载均衡器的“见解”边栏选项卡中，可以选择“更多详细指标”，查看预配置的 [Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)，其中包含与负载均衡器特定方面相关的指标视觉对象。 该仪表板显示负载均衡器状态，并在页面顶部显示相关文档的链接。

首先会显示“概述”选项卡。可以在可用选项卡中导航，每个选项卡均包含与负载均衡器特定方面相关的视觉对象。 每个选项卡底部的仪表板中都提供针对每个选项的明确指导。

当前可用的仪表板选项卡是：
* 概述
* 前端和后端可用性
* 数据吞吐量
* 流分发
* 连接监视器
* 指标定义 

### <a name="overview-tab"></a>概述选项卡
“概述”选项卡包含一个可搜索的网格，其中包含连接到负载均衡器的每个前端 IP 的总体数据路径可用性和运行状况探测状态。 这些指标指示前端 IP 是否响应，并且后端池中的计算实例是否分别响应入站连接。

还可以在此页面上查看每个前端 IP 的总体数据吞吐量，了解是否正在产生和接收预期水平的流量。 如果看到任何不正常的值，可通过页面底部的指导进入正确的标签。

### <a name="frontend-and-backend-availability-tab"></a>“前端和后端可用性”选项卡
“前端和后端可用性”选项卡以一些有用的视图显示了数据路径吞吐量和运行状况探测状态指标。 第一张图显示了聚合值，因此你可以确定是否存在问题。 其余图显示这些指标按不同维度进行划分，以便你可以排除故障并确定任何入站可用性问题的根源。

页面底部提供了用于查看这些图的工作流，其中包括各种症状的常见原因。 

### <a name="data-throughput-tab"></a>“数据吞吐量”选项卡
“数据吞吐量”选项卡使你可以查看入站和出站吞吐量，确定流量模式是否符合预期。 它将显示按前端 IP 和前端端口划分的入站和出站数据吞吐量，以便你可以确定所运行的服务是否单独执行。

### <a name="flow-distribution"></a>流分发
“流量分配”选项卡可帮助你直观了解和管理后端实例正在接收和产生的流量数量。 它显示入站和出站流量的流创建速率和流计数，以及每个 VM 和虚拟机规模集实例正在接收的网络流量。 

这些视图可为你提供有关负载均衡器配置或流量模式是否导致流量不平衡的反馈。 例如，是否已配置会话关联，以及某个客户端发出的请求数量是否不成比例。 它还会告知你的计算机大小是否已达到[每个 VM 流量限制](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations)。

### <a name="connection-monitors"></a>连接监视器
“连接监视器”选项卡会在全局地图上显示已配置的所有[连接监视器](../network-watcher/connection-monitor.md)的往返延迟。 这些视觉对象为具有严格延迟要求的服务提供有用的信息。 为了满足你的需求，你可能需要添加额外的区域部署或迁移到[跨区域负载均衡](./cross-region-overview.md)模型

### <a name="metric-definitions"></a>指标定义
“指标定义”选项卡包含[“多维指标标准”文章](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)中显示的所有信息。

## <a name="next-steps"></a>后续步骤
* 查看仪表板，如果有任何需要改进的地方，请使用下面的链接提供反馈
* [查看指标文档，以确保你了解如何计算每个指标](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [为负载均衡器创建连接监视器](../network-watcher/connection-monitor.md)
* [创建自己的工作簿](../azure-monitor/visualize/workbooks-overview.md)，你可以通过在详细指标仪表板中单击“编辑”按钮来汲取灵感
