---
title: 什么是 VM 见解？
description: VM 见解概述，用于监视 Azure VM 的运行状况和性能，并自动发现和映射应用程序组件及其依赖关系。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046408"
---
# <a name="overview-of-vm-insights"></a>VM 见解概述

VM 见解监视虚拟机和虚拟机规模集的性能和运行状况，包括其正在运行的进程和对其他资源的依赖关系。 它有助于通过查明性能瓶颈和网络问题来使重要应用程序提供可预测的性能和可用性，还有助于了解某个问题是否与其他依赖关系有关。

VM 见解支持以下计算机上的 Windows 和 Linux 操作系统：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机
- 本地虚拟机
- 托管在其他云环境中的虚拟机
  

VM 见解将其数据存储在 Azure Monitor 日志中，从而可提供强大的聚合和筛选功能，并分析一段时间内的数据趋势。 可直接从虚拟机在单个 VM 中查看此数据，也可使用 Azure Monitor 提供多个 VM 的聚合视图。

![Azure 门户中的虚拟机见解透视图](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>定价
VM 见解不会直接收费，但你需要为 Log Analytics 工作区中的活动付费。 根据在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将针对以下内容对 VM 见解进行计费：

- 从代理引入并存储在工作区中的数据。
- 从来宾运行状况收集的运行状况状态数据（预览版）
- 基于日志和运行状况数据的警报规则。
- 从警报规则发送的通知。

日志大小因性能计数器的字符串长度而异，并且可能会随分配给 VM 的逻辑磁盘和网络适配器的数量而增大。 如果已在使用服务映射，则你看到的唯一变化是发送到 Azure Monitor `InsightsMetrics` 数据类型的额外性能数据。


## <a name="configuring-vm-insights"></a>配置 VM 见解
配置 VM 见解的步骤如下所示。 请访问每个链接，以获取有关每个步骤的详细指南：

- [创建 Log Analytics 工作区。](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [将 VMInsights 解决方案添加到工作区。](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [在要监视的虚拟机和虚拟机规模集上安装代理。](./vminsights-enable-overview.md)



## <a name="next-steps"></a>后续步骤

- 有关为虚拟机启用监视的要求和方法，请参阅[部署 VM 见解](./vminsights-enable-overview.md)。
