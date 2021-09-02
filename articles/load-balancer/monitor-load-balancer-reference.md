---
title: 监视负载均衡器数据引用
titleSuffix: Azure Load Balancer
description: 监视负载均衡器时所需的重要参考资料
author: asudbring
ms.topic: reference
ms.author: allensu
ms.service: load-balancer
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: ac5a7394b13ac34ad9d398458627765316369de9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296510"
---
# <a name="monitoring-load-balancer-data-reference"></a>监视负载均衡器数据引用

有关收集和分析负载均衡器监视数据的详细信息，请参阅[监视负载均衡器](monitor-load-balancer.md)。

## <a name="metrics"></a>指标

### <a name="load-balancer-metrics"></a>负载均衡器指标 

| 指标 | 资源类型 | 说明 | 建议的聚合 |
| ------ | ------------- | ----------- | ----------------------- |
| 数据路径可用性 | 公共和内部负载均衡器 |  标准负载均衡器持续运用从区域内部到负载均衡器前端，直到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径。 此外，还会验证客户使用的数据路径。 度量对于应用程序不可见，且不会干扰其他操作。 | 平均值 |
| 运行状况探测状态 | 公共和内部负载均衡器 | 标准负载均衡器使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。 可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。 | 平均值 |
| SYN（同步）计数 | 公共和内部负载均衡器 | 标准负载均衡器使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。 可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。 | 平均值 |
| SNAT 连接计数 | 公共负载均衡器 | 标准负载均衡器报告公共 IP 地址前端上伪装的出站流数。 源网络地址转换 (SNAT) 端口是消耗性资源。 此指标可以指出应用程序依赖于 SNAT 获取出站发起流的程度有多高。 将报告成功和失败的出站 SNAT 流的计数器，可使用这些计数器排查和了解出站流的运行状况。 | SUM |
| 已分配的 SNAT 端口数 | 公共负载均衡器 | 标准负载均衡器报告每个后端实例分配的 SNAT 端口数。 | 平均值 |
| 已用 SNAT 端口数 | 公共负载均衡器 | 标准负载均衡器报告每个后端实例使用的 SNAT 端口数。 | 平均值 |
| 字节计数 | 公共和内部负载均衡器 | 标准负载均衡器按前端报告处理的数据。 你可能会注意到，这些字节并没有均匀地分布在后端实例中。 这是正常的，因为 Azure 的负载均衡器算法基于流。 | SUM |
| 数据包计数 | 公共和内部负载均衡器 | 标准负载均衡器按前端报告处理的数据包。 | SUM |

有关详细信息，请参阅 [Azure Monitor 中支持的所有适用于负载均衡器的平台指标](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkloadbalancers)列表。

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

负载均衡器具有以下与指标关联的维度。

| 维度名称 | 说明 |
| -------------- | ----------- |
| 前端 IP | 相关负载平衡规则的前端 IP 地址 |
| 前端端口 | 相关负载平衡规则的前端端口 | 
| 后端 IP | 相关负载均衡规则的后端 IP 地址 |
| 后端端口 | 相关负载均衡规则的后端端口 |
| 协议类型 | 相关负载均衡规则的协议，可以是 TCP 或 UDP |
| 方向 | 流向流量正在流动， 可以是入站或出站。 | 
| 连接状态 | SNAT 连接的状态，可以是“挂起”、“成功”或“失败” | 

## <a name="resource-logs"></a>资源日志

Azure 负载均衡器当前不支持资源日志

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表
### <a name="diagnostics-tables"></a>诊断表

Azure 负载均衡器当前不支持诊断表。
## <a name="activity-log"></a>活动日志

下表列出了可能在活动日志中创建的与负载均衡器相关的操作。

| 操作 | 说明 |
| --- | --- |
| Microsoft.Network/loadBalancers/read | 获取负载均衡器定义 |
| Microsoft.Network/loadBalancers/write | 创建负载均衡器，或更新现有的负载均衡器 |
| Microsoft.Network/loadBalancers/delete | 删除负载均衡器 |
| Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | 查询入站 Nat 规则端口映射。 |
| Microsoft.Network/loadBalancers/backendAddressPools/read | 获取负载均衡器的后端地址池定义 |
| Microsoft.Network/loadBalancers/backendAddressPools/write | 创建负载均衡器后端地址池，或更新现有的负载均衡器后端地址池 |
| Microsoft.Network/loadBalancers/backendAddressPools/delete | 删除负载均衡器后端地址池 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池。 不可发出警报。 |
| Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | 列出负载均衡器后端地址池的后端地址 |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 获取负载均衡器的前端 IP 配置定义 |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入负载均衡器前端 IP 配置。 不可发出警报。 |
| Microsoft.Network/loadBalancers/inboundNatPools/read | 获取负载均衡器的入站 NAT 池定义 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入负载均衡器入站 NAT 池。 不可发出警报。 |
| Microsoft.Network/loadBalancers/inboundNatRules/read | 获取负载均衡器的入站 NAT 规则定义 |
| Microsoft.Network/loadBalancers/inboundNatRules/write | 创建负载均衡器入站 NAT 规则，或更新现有的负载均衡器入站 NAT 规则 |
| Microsoft.Network/loadBalancers/inboundNatRules/delete | 删除负载均衡器入站 NAT 规则 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 NAT 规则。 不可发出警报。 |
| Microsoft.Network/loadBalancers/loadBalancingRules/read | 获取负载均衡器的负载均衡规则定义 |
| Microsoft.Network/loadBalancers/networkInterfaces/read | 获取对负载均衡器下的所有网络接口的引用 |
| Microsoft.Network/loadBalancers/outboundRules/read | 获取负载均衡器的出站规则定义 |
| Microsoft.Network/loadBalancers/probes/read | 获取负载均衡器探测 |
| Microsoft.Network/loadBalancers/probes/join/action | 允许使用负载均衡器的探测。 例如，凭借此权限，虚拟机规模集的 healthProbe 属性便可引用探测。 不可发出警报。 |
| Microsoft.Network/loadBalancers/virtualMachines/read | 获取对负载均衡器下的所有虚拟机的引用 |

有关活动日志条目架构的详细信息，请参阅[活动日志架构](../azure-monitor/essentials/activity-log-schema.md)。 

## <a name="see-also"></a>另请参阅

- 有关监视 Azure 负载均衡器的说明，请参阅[监视 Azure 负载均衡器](./monitor-load-balancer.md)。