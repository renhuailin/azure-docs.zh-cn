---
title: Azure 网络观察程序连接监视器架构 | Microsoft Docs
description: 了解 Azure 网络观察程序连接监视器的架构。
services: network-watcher
documentationcenter: na
author: mjha
manager: vinigam
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2021
ms.author: mjha
ms.openlocfilehash: 8cc2528a4a8f8a285e8bbf2f99859155c1d9861d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451026"
---
# <a name="azure-network-watcher-connection-monitor-schema"></a>Azure 网络观察程序连接监视器架构

连接监视器在 Azure 网络观察程序中提供统一的端到端连接监视。 连接监视器功能支持混合部署和 Azure 云部署。 网络观察程序提供的工具可用于监视、诊断和查看针对 Azure 部署的与连接相关的指标。

下面是连接监视器的一些用例：

- 前端 Web 服务器 VM 与多层应用程序中的数据库服务器 VM 进行通信。 你希望检查两个 VM 之间的网络连接。
- 你希望美国东部区域的 VM 能够端对端连接到美国中部区域的 VM，并且希望比较两者的跨区域网络延迟。
- 你有多个本地办公场所位于华盛顿州西雅图和弗吉尼亚州阿什本。 你的办公地点连接到 Microsoft 365 URL。 针对 Microsoft 365 URL 的用户，在西雅图和阿什本之间比较延迟。
- 混合应用程序需要连接到 Azure 存储终结点。 本地站点和 Azure 应用程序连接到相同的 Azure 存储终结点。 希望比较本地站点的延迟与 Azure 应用程序的延迟。
- 希望检查本地设置与托管云应用程序的 Azure VM 之间的连接。

下面是连接监视器的一些优点：

* 适用于 Azure 和混合监视需求的直观一致的体验
* 跨区域且跨工作区的连接监视
* 更高的探测频率，更好地了解网络性能
* 为混合部署提供更快的警报
* 支持基于 HTTP、TCP 和 ICMP 的连接检查 
* 同时适用于 Azure 和非 Azure 测试设置的指标和 Log Analytics 支持

有两种类型的日志/数据被引入 Log Analytics。
测试数据（NWConnectionMonitorTestResult 查询）根据特定测试组的监控频率进行更新。
路径数据（NWConnectionMonitorPathResult 查询）在丢失百分比或往返时间发生显着变化时更新。
因此，在一段时间内，测试数据可能会不断更新，而路径数据并不经常更新，因为两者都是独立的。

## <a name="connection-monitor-tests-schema"></a>连接监视器测试架构

下面列出了连接监视器测试数据架构中的字段及其表示内容 

| 字段  |    说明   |
|---|---|
| TimeGenerated | 生成日志时的时间戳 (UTC) |
| RecordId  | 测试结果记录的唯一标识的记录 ID |
| ConnectionMonitorResourceId   | 测试连接监视器的资源 ID |
| TestGroupName | 测试所属的测试组名称 |
| TestConfigurationName | 测试所属的测试配置名称 |
| SourceType    | 为测试配置的源计算机的类型 |
| SourceResourceId  | 源计算机的资源 ID |
| SourceAddress | 为测试配置的源的地址 |
| SourceSubnet  | 源的子网 |
| SourceIP  | 源的 IP 地址 |
| SourceName    | 源的终结点名称 |
| SourceAgentId | 源代理 ID |
| DestinationPort   | 为测试配置的目标端口 |
| 目标类型   | 为测试配置的目标计算机的类型 |
| DestinationResourceId | 目标计算机的资源 ID |
| 目的地地址    | 为测试配置的目标的地址 |
| DestinationSubnet | 如果适用，则为目标子网 |
| DestinationIP | 目标的 IP 地址 |
| DestinationName   | 目标终结点名称 |
| DestinationAgentId    | 目标代理 ID |
| 协议  | 测试协议 |
| ChecksTotal   | 测试完成的总检查数 |
| ChecksFailed  | 测试检查失败的总数 |
| TestResult    | 测试结果 |
| TestResultCriterion   | 测试结果判定标准 |
| ChecksFailedPercentThreshold  | 为测试设置的检查失败百分比阈值 |
| RoundTripTimeMsThreshold  | 为测试设置的往返阈值（毫秒） |
| MinRoundTripTimeMs    | 测试的最小往返时间（毫秒） |
| MaxRoundTripTimeMs    | 测试的最大往返时间 |
| AvgRoundTripTimeMs    | 测试的平均往返时间 |
| JitterMs  | 测试的平均偏差往返时间 |
| AdditionalData    | 测试的其他数据 |


## <a name="connection-monitor-path-schema"></a>连接监视器路径架构

下面列出了连接监视器路径数据架构中的字段及其含义 

| 字段  |    说明   |
|---|---|
| TimeGenerated  | 生成日志时的时间戳 (UTC) |
| RecordId  | 测试结果记录的唯一标识的记录 ID |
| TopologyId    | 路径记录的拓扑 ID |
| ConnectionMonitorResourceId   | 测试连接监视器的资源 ID |
| TestGroupName | 测试所属的测试组名称 |
| TestConfigurationName | 测试所属的测试配置名称 |
| SourceType    | 为测试配置的源计算机的类型 |
| SourceResourceId  | 源计算机的资源 ID |
| SourceAddress | 为测试配置的源的地址 |
| SourceSubnet  | 源的子网 |
| SourceIP  | 源的 IP 地址 | 
| SourceName    | 源的终结点名称 |
| SourceAgentId | 源代理 ID |
| DestinationPort   | 为测试配置的目标端口 |
| 目标类型   | 为测试配置的目标计算机的类型 |
| DestinationResourceId | 目标计算机的资源 ID |
| 目的地地址    | 为测试配置的目标的地址 |
| DestinationSubnet | 如果适用，则为目标子网 |
| DestinationIP | 目标的 IP 地址 |
| DestinationName   | 目标终结点名称 |
| DestinationAgentId    | 目标代理 ID |
| 协议  | 测试协议 |
| ChecksTotal   | 测试完成的总检查数 |
| ChecksFailed  | 测试检查失败的总数 |
| PathTestResult    | 测试结果 |
| PathResultCriterion   | 测试结果判定标准 | 
| ChecksFailedPercentThreshold  | 为测试设置的检查失败百分比阈值 |
| RoundTripTimeMsThreshold  | 为测试设置的往返阈值（毫秒） |
| MinRoundTripTimeMs    | 测试的最小往返时间（毫秒） |
| MaxRoundTripTimeMs    | 测试的最大往返时间 |
| AvgRoundTripTimeMs    | 测试的平均往返时间 |
| JitterMs  | 测试的平均偏差往返时间 |
| HopAddresses | 为测试标识的跃点地址 |
| HopTypes  | 为测试标识的跃点类型 |
| HopLinkTypes  | 为测试标识的跃点链接类型 |
| HopResourceIds    | 为测试标识的跃点资源 ID |
| 问题    | 为测试标识的问题 |
| Hops  | 为测试标识的跃点 |
| AdditionalData | 测试的其他数据 |
