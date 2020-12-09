---
title: 容器 Azure Monitor 中的报表
description: 描述可用于分析容器 Azure Monitor 收集的数据的报告。
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 3cc2f8fb9bfaa278ce06b4a8cd6d379397b7129a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907466"
---
# <a name="reports-in-azure-monitor-for-containers"></a>容器 Azure Monitor 中的报表
建议为容器 Azure Monitor 中的报表提供现成的 [Azure 工作簿](../platform/workbooks-overview.md)。 本文介绍可用的不同报表以及如何访问这些报表。

## <a name="viewing-reports"></a>查看报告
从 Azure 门户的 " **Azure Monitor** " 菜单中，选择 " **容器**"。 在 "**监视**" 部分中选择 "**见解**"，选择特定的分类，然后选择 "**报表 (预览")** "页。 

[![报表页](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>创建自定义工作簿
若要基于这些工作簿创建自定义工作簿，请选择 " **查看工作簿** " 下拉列表，然后前往下拉列表底部的 **AKS 库** 。 有关工作簿和使用工作簿模板的详细信息，请参阅 [Azure Monitor 工作簿](../platform/workbooks-overview.md) 。

[![AKS 库](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>节点工作簿

- **磁盘容量**：每个磁盘的交互式磁盘使用情况图表按以下透视显示到容器中的节点：

    - 所有磁盘的磁盘使用率百分比。
    - 所有磁盘的可用磁盘空间。
    - 一个网格，显示每个节点磁盘的已使用空间百分比、已使用空间趋势百分比、可用磁盘空间 (GiB)，以及可用磁盘空间趋势 (GiB)。 选择表中的某个行时，会在该行下面显示已使用空间百分比和可用磁盘空间 (GiB)。

- **磁盘 IO**：按以下顺序向容器中的节点显示的每个磁盘的交互式磁盘使用情况图表：

    - 跨所有磁盘按读取字节数/秒、写入字节数/秒以及读取和写入字节数/秒趋势汇总的磁盘 I/O。
    - 八个显示关键性能指标的性能图表，用于度量和标识磁盘 I/O 瓶颈。

- **Gpu**：每个识别 GPU 的 Kubernetes 群集节点的交互式 gpu 使用情况图表。

## <a name="resource-monitoring-workbooks"></a>资源监视工作簿

- **部署**：部署的状态 & 水平 Pod 自动缩放程序 (HPA) 包括自定义 HPA。 
  
- **工作负荷详细信息**：交互式图表，其中显示了命名空间的工作负荷的性能统计信息。 包括多个选项卡：

  - POD 的 CPU 和内存使用情况概述。
  - POD/容器状态显示 POD 重启趋势、容器重启趋势和 pod 容器状态。
  - 显示控制器事件摘要的 Kubernetes 事件。

- **Kubelet**：包括两个显示关键节点操作统计信息的网格：

    - 节点网格的概览汇总了每个节点的总操作数、总错误数、成功的操作数（按百分比），以及趋势。
    - 操作类型概览汇总了每个操作的总操作数、总错误数、成功的操作数（按百分比），以及趋势。
## <a name="billing-workbooks"></a>计费工作簿

- **数据使用**：可帮助你直观显示数据源，而无需构建你自己的文档中共享的查询库。 在此工作簿中，有一些可供查看的图表，如以下所示：

  - 按解决方案引入的计费数据总计（GB）
  - 按容器 (应用程序日志的计费数据引入) 
  - 按 Kubernetes 命名空间的计费容器日志数据引入
  - 按群集名称隔离的可计费容器日志数据引入
  - 按 logsource 条目的可计费容器日志数据引入
  - 可计费诊断数据引入按诊断主节点日志

## <a name="networking-workbooks"></a>网络工作簿

- **NPM 配置**：监视通过网络策略管理器 (NPM) 配置的网络配置。

  - 总体配置复杂性的摘要信息。
  - 策略、规则和集在一段时间内的计数，允许深入了解这三个关系，并添加一个时间维度，以便对配置进行调试。
  - 所有 IPSets 和每个 IPSet 中的条目数。
  - 将组件添加到网络配置中的每个节点的最差和平均事例性能。

- **网络**：每个节点的网络适配器的交互式网络使用情况图表，网格显示关键性能指标来帮助测量网络适配器的性能。



## <a name="next-steps"></a>后续步骤

- 有关 Azure Monitor 中工作簿的详细信息，请参阅 [Azure Monitor 工作簿](../platform/workbooks-overview.md) 。
