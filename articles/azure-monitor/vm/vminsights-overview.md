---
title: 什么是 VM insights？
description: VM insights 概述，它监视 Azure Vm 的运行状况和性能，并自动发现和映射应用程序组件及其依赖项。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046408"
---
# <a name="overview-of-vm-insights"></a>VM 见解概述

VM insights 监视虚拟机和虚拟机规模集的性能和运行状况，其中包括其正在运行的进程以及对其他资源的依赖关系。 它可以通过识别性能瓶颈和网络问题来帮助提供重要应用程序的可预测性能和可用性，还可以帮助你了解问题是否与其他依赖关系相关。

VM insights 支持以下计算机上的 Windows 和 Linux 操作系统：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机
- 本地虚拟机
- 托管在其他云环境中的虚拟机
  

VM insights 将其数据存储在 Azure Monitor 日志中，从而可提供强大的聚合和筛选功能，并在一段时间内分析数据趋势。 可以直接从虚拟机的单个 VM 查看此数据，也可以使用 Azure Monitor 提供多个 Vm 的聚合视图。

![Azure 门户中的虚拟机见解透视图](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>定价
VM insights 不会直接收费，但你需要为 Log Analytics 工作区中的活动付费。 根据 [Azure Monitor 定价 "页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将按以下步骤计费 VM insights：

- 从代理引入数据，并将其存储在工作区中。
- 从来宾运行状况收集的运行状况状态 (预览) 
- 基于日志和运行状况数据的警报规则。
- 从警报规则发送的通知。

日志大小因性能计数器的字符串长度而异，并且可能会随分配给 VM 的逻辑磁盘和网络适配器的数量而增大。 如果已在使用服务映射，则将看到的唯一更改是发送到 Azure Monitor 数据类型的额外性能数据 `InsightsMetrics` 。


## <a name="configuring-vm-insights"></a>配置 VM insights
配置 VM insights 的步骤如下所示。 请访问每个链接，获取有关每个步骤的详细指导：

- [创建 Log Analytics 工作区。](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [将 VMInsights 解决方案添加到工作区。](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [在要监视的虚拟机和虚拟机规模集上安装代理。](./vminsights-enable-overview.md)



## <a name="next-steps"></a>后续步骤

- 有关为虚拟机启用监视的要求和方法，请参阅 [部署 VM insights](./vminsights-enable-overview.md) 。
