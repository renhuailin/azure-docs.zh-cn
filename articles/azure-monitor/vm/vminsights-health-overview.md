---
title: 'VM insights 来宾运行状况 (预览) '
description: VM insights 中的运行状况功能概述，包括如何查看虚拟机的运行状况，以及在虚拟机变得不正常时接收警报。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 3db6c2f4da28bba2d12aacc90b2fa8e420aa6fbf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707449"
---
# <a name="vm-insights-guest-health-preview"></a>VM insights 来宾运行状况 (预览) 
利用 VM insights 来宾运行状况，你可以根据一组性能度量来查看虚拟机的运行状况，该度量值是从来宾操作系统定期抽样的。 你可以快速检查订阅或资源组中所有虚拟机的运行状况、向下钻取特定虚拟机的详细运行状况，或在虚拟机变得不正常时主动通知。 

## <a name="enable-virtual-machine-health"></a>启用虚拟机运行状况
有关启用来宾健康功能和载入虚拟机的详细信息，请参阅 [启用 VM insights 来宾健康状况 (预览) ](vminsights-health-enable.md) 。

## <a name="pricing"></a>定价
来宾运行状况功能不会直接收费，但会在 Log Analytics 工作区中引入和存储健康状况数据。 所有数据都存储在 *HealthStateChangeEvent* 表中。 有关定价模型和成本的详细信息，请参阅 [使用 Azure Monitor 日志管理使用情况和成本](../logs/manage-cost-storage.md) 。

## <a name="view-virtual-machine-health"></a>查看虚拟机运行状况
在 "**入门**" 页的 "**来宾 VM 运行状况**" 列中，可以快速查看特定订阅或资源组中每个虚拟机的运行状况。 显示每个虚拟机的当前运行状况，而每个组的图标显示该组中每种状态下的虚拟机的数量。

[![来宾 VM 运行状况的入门页面](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>监视器
单击虚拟机的运行状况状态，查看其每个监视器的详细状态。 每个监视器测量特定组件的运行状况。 虚拟机的总体运行状况由其各个监视器的运行状况确定。 

![监视器示例](media/vminsights-health-overview/monitors.png)

下表列出了当前可用于每个虚拟机的聚合和单元监视器。 

| 监视 | 类型 | 说明 |
|:---|:---|:---|
| CPU 使用率 | 计价单位 | 处理器的百分比利用率。 |
| 文件系统 | 聚合 | Linux VM 上的所有文件系统的聚合运行状况。 |
| 文件系统  | 聚合 | Linux VM 上每个单独文件系统的运行状况。 监视器的名称是文件系统的名称。 |
| 可用空间 | 计价单位 | 文件系统上的可用空间百分比。 |
| 逻辑磁盘 | 聚合 | Windows VM 上所有逻辑磁盘的聚合运行状况。 |
| 逻辑磁盘  | 聚合 | Windows VM 上每个单独逻辑磁盘的运行状况。 监视器的名称是磁盘的名称。 |
| 内存 | 聚合 | VM 上内存的聚合运行状况。 |
| 可用内存 | 计价单位 | VM 上的可用兆字节。 |

## <a name="health-states"></a>健康状况
每个监视器每分钟每分钟采样一次代理的值，并将采样值与每个运行状况状态的条件进行比较。 如果特定状态的条件为 true，则将监视器设置为该状态。 如果条件均不为 true，则将监视器设置为正常状态。 数据将从代理发送到每三分钟 Azure Monitor 一次，或在状态发生更改时立即发送。

每个监视器都有一个 lookback 窗口，并分析在该时间段内收集的任何样本。 例如，监视器可能会有240秒的 lookback 窗口，查找至少2个采样值中的最大值，但不能超过最后3个值。 当值按固定速率采样时，如果代理操作中断，则在特定窗口中采样的数目可能会略有不同。

每个监视器都具有下表中的潜在运行状况状态，并且在任何给定时间都是一个且仅有一个。 初始化监视器后，它将以正常状态启动。

| 运行状况状态 | 说明 |
|:---|:---|
| 正常  | 监视器当前未超出警告或严重阈值。 |
| 警告  | 如果) 定义，则监视器超过警告阈值 (。 |
| 严重 | 如果) 定义此监视器，则该监视器超出了严重阈值 (。 |
| Unknown  | 未收集足够的数据来确定运行状况状态。 |
| 已禁用 | 监视器当前已禁用。 |
| None     | 监视器刚刚启动，尚未评估或监视的对象不再存在。 |



有两种类型的监视器，如下表所示。

| 监视 | 说明 |
|:---|:---|
| 单元监视器 | 度量资源或应用程序的某个方面。 可能是检查性能计数器以确定资源的性能或其可用性。 |
| 聚合监视器 | 将多个监视器组合在一起，以提供单个聚合运行状况状态。 聚合监视器可以包含一个或多个单元监视器和其他聚合监视器。 |


  
### <a name="health-rollup-policy"></a>运行状况汇总策略
虚拟机的运行状况状态由其每个单元和聚合监视器的运行状况汇总决定。 每个聚合监视器都使用最差状态运行状况汇总策略，其中聚合监视器的状态与具有最差运行状况状态的子监视器的状态相匹配。  

在下面的示例中，" **可用空间** " 监视器处于严重状态，其中的角色将达到其实例的聚合，然后再到 **文件系统**。 即使处于警告状态的 **CPU 使用率** 为 "严重"，也会将虚拟机设置为 "严重"，因为这是它下面的最差状态。 如果可用空间返回到正常状态，则虚拟机将更改为警告状态，因为 CPU 使用率会成为处于最差状态的监视器。

![运行状况汇总示例](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>监视器详细信息
选择监视器以查看其详细信息，其中包括以下选项卡。

**概述** 提供监视器的说明、上次评估时间和采样以确定当前运行状况状态的值。 样本数可能因监视器的定义和值的波动性而有所不同。

[![监视器详细信息概述](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**历史记录** 列出监视器状态更改的历史记录。 您可以展开任何状态更改，以查看评估的值以确定运行状况状态。 单击 " **查看配置** " 以查看更改运行状况状态时监视器的配置。



[![监视器详细信息历史记录](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>配置
查看和修改所选 VM 的监视器配置。 有关详细信息，请参阅 [在 VM insights 来宾运行状况 (预览) 中配置监视 ](vminsights-health-enable.md) 。

[![监视详细信息配置](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>后续步骤

- [在 VM insights 和载入代理中启用来宾运行状况。](vminsights-health-enable.md)
- [使用 Azure 门户配置监视器。](vminsights-health-configure.md)
- [使用数据收集规则配置监视器。](vminsights-health-configure-dcr.md)