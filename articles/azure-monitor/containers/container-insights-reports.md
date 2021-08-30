---
title: 容器见解报表
description: 描述可用于分析由容器见解收集的数据的报表。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: a99d7d175f9e79291d4b7c56473939deccae2bff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778706"
---
# <a name="reports-in-container-insights"></a>容器见解报表
容器见解报表是推荐的现成 [Azure 工作簿](../visualize/workbooks-overview.md)。 本文介绍了可用的各种报表以及如何访问这些报表。

## <a name="viewing-reports"></a>查看报告
从 Azure 门户上的 Azure Monitor 菜单中，选择“容器” 。 在“监视”部分中选择“见解”，选择一个特定的群集，然后选择“报表”页面。   

[![报表页](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>创建自定义工作簿
要基于这些工作簿中的任何一个创建自定义工作簿，请选择“查看工作簿”下拉菜单，然后选择下拉菜单底部的“转到 AKS 库” 。 如需详细了解工作簿以及如何使用工作簿模板，请参阅 [Azure Monitor 工作簿](../visualize/workbooks-overview.md)。

[![AKS 库](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>节点工作簿

- **磁盘容量**：呈现给容器内节点的每个磁盘的交互式磁盘使用情况图表，包含以下方面的内容：

    - 所有磁盘的磁盘使用率百分比。
    - 所有磁盘的可用磁盘空间。
    - 一个网格，显示每个节点磁盘的已使用空间百分比、已使用空间趋势百分比、可用磁盘空间 (GiB)，以及可用磁盘空间趋势 (GiB)。 选择表中的某个行时，会在该行下面显示已使用空间百分比和可用磁盘空间 (GiB)。

- **磁盘 IO**：呈现给容器内节点的每个磁盘的交互式磁盘利用率图表，包含以下方面的内容：

    - 跨所有磁盘按读取字节数/秒、写入字节数/秒以及读取和写入字节数/秒趋势汇总的磁盘 I/O。
    - 八个显示关键性能指标的性能图表，用于度量和标识磁盘 I/O 瓶颈。

- **GPU**：每个 GPU 感知 Kubernetes 群集节点的交互式 GPU 使用情况图表。

## <a name="resource-monitoring-workbooks"></a>资源监视工作簿

- **部署**：部署状态和水平 Pod 自动缩放器 (HPA)，包括自定义 HPA。 
  
- **工作负载详细信息**：显示命名空间工作负载的性能统计信息的交互式图表。 包括多个选项卡：

  - POD 的 CPU 和内存使用情况概述。
  - 显示 POD 重启趋势、容器重启趋势以及 POD 的容器状态的 POD/容器状态。
  - 显示控制器事件摘要的 Kubernetes 事件。

- **Kubelet**：包括两个显示关键节点操作统计信息的网格：

    - 节点网格的概览汇总了每个节点的总操作数、总错误数、成功的操作数（按百分比），以及趋势。
    - 操作类型概览汇总了每个操作的总操作数、总错误数、成功的操作数（按百分比），以及趋势。
## <a name="billing-workbooks"></a>计费工作簿

- **数据使用情况**：帮助你直观显示数据源，而不必根据我们在文档中分享的内容构建自己的查询库。 此工作簿包含一些图表，通过这些图表，你可以从以下角度查看计费数据：

  - 按“解决方案”查看引入的总计费数据
  - 按“容器日志(应用程序日志)”查看引入的计费数据
  - 按“Kubernetes 命名空间”查看引入的计费容器日志数据
  - 按“群集名称”查看引入并分隔的计费容器日志数据
  - 按日志源条目查看引入的计费容器日志数据
  - 按“诊断主节点日志”查看引入的计费诊断数据

## <a name="networking-workbooks"></a>网络工作簿

- **NPM 配置**：监视通过网络策略管理器 (NPM) 配置的网络配置。

  - 有关总体配置复杂程度的摘要信息。
  - 一段时间内的策略、规则和设置计数，让你可以深入了解这三者之间的关系，并添加一个时间维度，以便对配置进行调试。
  - 所有 IPSet 和每个 IPSet 中的条目数。
  - 用于将组件添加到网络配置的每个节点的最差和平均用例性能。

- **网络**：每个节点的网络适配器的交互式网络利用率图表和一个表示关键性能指标的网格，用于度量网络适配器的性能。



## <a name="next-steps"></a>后续步骤

- 如需详细了解 Azure Monitor 中的工作簿，请参阅 [Azure Monitor 工作簿](../visualize/workbooks-overview.md)。
