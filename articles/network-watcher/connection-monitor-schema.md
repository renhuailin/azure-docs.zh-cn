---
title: Azure 网络观察程序连接监视器架构 | Microsoft Docs
description: 了解 Azure 网络观察程序连接监视器的测试数据架构和路径数据架构。
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
ms.openlocfilehash: 4cad1ea0d90f85a12e7d7f9b7dbc869a61a91a39
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969758"
---
# <a name="azure-network-watcher-connection-monitor-schemas"></a>Azure 网络观察程序连接监视器架构

连接监视器在 Azure 网络观察程序中提供统一的端到端连接监视。 连接监视器功能支持混合部署和 Azure 云部署。 网络观察程序提供的工具可用于监视、诊断和查看针对 Azure 部署的与连接相关的指标。

下面是连接监视器的一些用例：

- 前端 Web 服务器虚拟机 (VM) 与多层应用程序中的数据库服务器 VM 进行通信。 你希望检查两个 VM 之间的网络连接。
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

引入到 Log Analytics 中的日志或数据有两种类型。 测试数据（NWConnectionMonitorTestResult 查询）根据特定测试组的监视频率进行更新。 路径数据（NWConnectionMonitorPathResult 查询）在丢失百分比或往返时间发生显著变化时进行更新。 在一段时间内，测试数据可能会不断更新，而路径数据并不经常更新，因为两者都是独立的。

## <a name="connection-monitor-tests-schema"></a>连接监视器测试架构

下表列出了连接监视器测试数据架构中的字段及其含义。 

| 字段  |    说明   |
|---|---|
| TimeGenerated | 生成日志时的时间戳 (UTC) |
| RecordId  | 记录 ID，用于唯一标识测试结果记录 |
| ConnectionMonitorResourceId   | 此测试的连接监视器资源 ID |
| TestGroupName | 此测试所属的测试组的名称 |
| TestConfigurationName | 此测试所属的测试配置的名称 |
| SourceType    | 为测试配置的源计算机的类型 |
| SourceResourceId  | 源计算机的资源 ID |
| SourceAddress | 为测试配置的源的地址 |
| SourceSubnet  | 源的子网 |
| SourceIP  | 源的 IP 地址 |
| SourceName    | 源终结点名称 |
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
| RoundTripTimeMsThreshold  | 为此测试设置的往返阈值（以毫秒为单位） |
| MinRoundTripTimeMs    | 此测试的最短往返时间（以毫秒为单位） |
| MaxRoundTripTimeMs    | 此测试的最长往返时间 |
| AvgRoundTripTimeMs    | 此测试的平均往返时间 |
| JitterMs  | 此测试的均差往返时间 |
| AdditionalData    | 测试的其他数据 |


## <a name="connection-monitor-path-schema"></a>连接监视器路径架构

下表列出了连接监视器路径数据架构中的字段及其含义。 

| 字段  |    说明   |
|---|---|
| TimeGenerated  | 生成日志时的时间戳 (UTC) |
| RecordId  | 记录 ID，用于唯一标识测试结果记录 |
| TopologyId    | 路径记录的拓扑 ID |
| ConnectionMonitorResourceId   | 此测试的连接监视器资源 ID |
| TestGroupName | 此测试所属的测试组的名称 |
| TestConfigurationName | 此测试所属的测试配置的名称 |
| SourceType    | 为测试配置的源计算机的类型 |
| SourceResourceId  | 源计算机的资源 ID |
| SourceAddress | 为测试配置的源的地址 |
| SourceSubnet  | 源的子网 |
| SourceIP  | 源的 IP 地址 | 
| SourceName    | 源终结点名称 |
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
| RoundTripTimeMsThreshold  | 为此测试设置的往返阈值（以毫秒为单位） |
| MinRoundTripTimeMs    | 此测试的最短往返时间（以毫秒为单位） |
| MaxRoundTripTimeMs    | 此测试的最长往返时间 |
| AvgRoundTripTimeMs    | 此测试的平均往返时间 |
| JitterMs  | 此测试的均差往返时间 |
| HopAddresses | 为测试标识的跃点地址 |
| HopTypes  | 为测试标识的跃点类型 |
| HopLinkTypes  | 为测试标识的跃点链接类型 |
| HopResourceIds    | 为测试标识的跃点资源 ID |
| 问题    | 为测试标识的问题 |
| Hops  | 为测试标识的跃点 |
| AdditionalData | 测试的其他数据 |
