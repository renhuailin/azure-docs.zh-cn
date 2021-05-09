---
title: VM 见解来宾运行状况（预览）
description: VM 见解中的运行状况功能概述，包括如何查看虚拟机的运行状况，以及在虚拟机运行不正常时接收警报。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 2922ca4068531c45e6acad0ce54aa96624c6238e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052120"
---
# <a name="vm-insights-guest-health-preview"></a>VM 见解来宾运行状况（预览）
利用 VM 见解来宾运行状况，可以根据一组性能测量值来查看虚拟机的运行状况，这组测量值是从来宾操作系统定期抽样的。 可以快速检查订阅或资源组中所有虚拟机的运行状况，向下钻取特定虚拟机的详细运行状况，或在虚拟机运行不正常时主动通知。 

## <a name="enable-virtual-machine-health"></a>启用虚拟机运行状况
有关启用来宾运行状况和载入虚拟机的详细信息，请参阅[启用 VM 见解来宾运行状况（预览）](vminsights-health-enable.md)。

## <a name="pricing"></a>定价
来宾运行状况功能不会直接收费，但会对在 Log Analytics 工作区中引入和存储运行状况状态数据进行收费。 所有数据都存储在 *HealthStateChangeEvent* 表中。 有关定价模型和收费的详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](../logs/manage-cost-storage.md)。

## <a name="view-virtual-machine-health"></a>查看虚拟机运行状况
在“入门”页的“来宾 VM 运行状况”列中，可以快速查看特定订阅或资源组中每个虚拟机的运行状况。  显示每个虚拟机的当前运行状况的同时，每个组的图标显示当前该组中每种状态下的虚拟机的数量。

[![来宾 VM 运行状况的入门页](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>监视器
单击虚拟机的运行状况状态，查看其每个监视器的详细状态。 每个监视器负责测量特定组件的运行状况。 虚拟机的总体运行状况由其各个监视器的运行状况决定。 

![监视器示例](media/vminsights-health-overview/monitors.png)

下表列出了当前可用于每个虚拟机的聚合和单元监视器。 

| 监视 | 类型 | 描述 |
|:---|:---|:---|
| CPU 使用率 | 计价单位 | 处理器的利用率百分比。 |
| 文件系统 | 聚合 | Linux VM 上所有文件系统的聚合运行状况。 |
| 文件系统  | 聚合 | Linux VM 上单个文件系统的运行状况。 监视器的名称是文件系统的名称。 |
| 可用空间 | 计价单位 | 文件系统上的可用空间百分比。 |
| 逻辑磁盘 | 聚合 | Windows VM 上所有逻辑磁盘的聚合运行状况。 |
| 逻辑磁盘  | 聚合 | Windows VM 上单个逻辑磁盘的运行状况。 监视器的名称是磁盘的名称。 |
| 内存 | 聚合 | VM 上内存的聚合运行状况。 |
| 可用内存 | 计价单位 | VM 上的可用兆字节。 |

## <a name="health-states"></a>健康状况
每个监视器每分钟采样一次代理的值，并将采样值与每个运行状况状态的条件进行比较。 如果特定状态的条件为 true，则将监视器设置为该状态。 如果条件均不为 true，则将监视器设置为运行正常状态。 数据将每三分钟从代理发送到 Azure Monitor 一次，或在状态发生更改时立即发送。

每个监视器都有一个回溯窗口，并分析在该时间段内收集的任何样本。 例如，监视器可能会有 240 秒的回溯窗口，查找至少2个采样值中的最大值，但不超过最后3个值。 当值按固定速率采样时，如果代理操作有任何中断，则在特定窗口中采样的数目可能会略有不同。

每个监视器都具有下表中的潜在运行状况状态，并且在任何给定时间都处于一个且仅有一个状态。 监视器初始化后，将以运行正常状态启动。

| 运行状况状态 | 说明 |
|:---|:---|
| 正常  | 监视器当前未超过警告阈值或严重阈值。 |
| 警告  | 监视器超过警告阈值（如果已定义）。 |
| 严重 | 监视器超过严重阈值（如果已定义）。 |
| 未知  | 收集的数据不足，无法确定运行状况状态。 |
| 已禁用 | 当前已禁用监视。 |
| None     | 监视已启动但尚未评估，或监视的对象不再存在。 |



存在两种监视器，如下表中所示。

| 监视 | 说明 |
|:---|:---|
| 单元监视器 | 度量资源或应用程序的某个方面。 可能是检查性能计数器以确定资源的性能或其可用性。 |
| 聚合监视器 | 将多个监视器组合在一起，以提供单个聚合运行状况状态。 聚合监视器可以包含一个或多个单元监视器和其他聚合监视器。 |


  
### <a name="health-rollup-policy"></a>运行状况汇总策略
虚拟机的运行状况状态由其每个单元和聚合监视器的运行状况汇总决定。 每个聚合监视器都使用最差状态运行状况汇总策略，其中聚合监视器的状态与具有最差运行状况状态的子监视器的状态相匹配。  

在下面的示例中，“可用空间”监视器处于严重状态，其实例汇总到聚合，然后再到“文件系统”。  即使“CPU 使用率”处于警告状态，虚拟机仍设置为严重，因为这是它下面的最差状态。 如果可用空间要返回到运行正常状态，则虚拟机将更改为警告状态，因为 CPU 使用率会成为处于最差状态的监视器。

![运行状况汇总示例](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>监视器详细信息
选择监视器以查看其详细信息，其中包括以下选项卡。

“概述”提供监视器的说明、上次评估时间和为确定当前运行状况状态所采样的值。 样本数可能因监视器的定义和值的波动性而有所不同。

[![监视器详细信息概述](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

“历史记录”列出了监视器状态更改的历史记录。 可以展开任何状态更改，以查看为确定运行状况状态而评估的值。 单击“查看所应用配置”以查看运行状况状态更改时监视器的配置。



[![监视器详细信息历史记录](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>配置
查看并修改所选 VM 的监视器配置。 有关详细信息，请参阅[在 VM 见解来宾运行状况中配置监视（预览）](vminsights-health-enable.md)。

[![监视器详细信息配置](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>后续步骤

- [启用 VM 见解来宾运行状况和载入代理。](vminsights-health-enable.md)
- [使用 Azure 门户配置监视器。](vminsights-health-configure.md)
- [使用数据收集规则配置监视器。](vminsights-health-configure-dcr.md)