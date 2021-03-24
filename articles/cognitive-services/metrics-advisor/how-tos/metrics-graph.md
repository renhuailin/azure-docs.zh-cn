---
title: 指标顾问指标图
titleSuffix: Azure Cognitive Services
description: 如何配置指标图并可视化数据中的相关异常。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043147"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>操作说明：构建指标图来分析相关指标

指标顾问中的每个指标都由一个模型单独监视，该模型从历史数据中进行学习，以预测未来趋势。 每个指标都有一个应用于它的单独模型。 但是，在某些情况下，多个指标可能彼此相关，需要跨多个指标分析异常。 “指标图”可帮助解决此问题。 

例如，如果在不同的指标中具有不同的遥测数据流，则指标顾问将分别对它们进行监视。 如果某个指标中的异常导致其他指标中的异常，则在处理事件时，在数据中找到这些关系和根本原因会很有帮助。 使用指标图，你可以创建已发现异常的可视拓扑图。 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>选择一个指标，将第一个节点放入图中

单击导航栏中的“指标图”选项卡。 构建指标图的第一步是将节点放入图上。 选择页面顶部的数据馈送和指标。 节点将显示在底部面板中。 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="选择指标":::

## <a name="add-a-noderelation-on-existing-node"></a>在现有节点上添加节点/关系

接下来，需要添加另一个节点，并指定它与现有节点之间的关系。 选择现有节点并右键单击该节点。 将出现一个上下文菜单，其中包含多个选项。 

单击“添加关系”，你将能够选择另一个指标，并指定两个节点之间的关系类型。 你还可以应用特定的维度筛选器。 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="添加节点和关系":::

重复上述步骤后，你将得到一个描述所有相关指标之间关系的指标图。
**节点颜色提示**
> [!TIP]
> - 选择指标和维度筛选器时，图中具有相同指标和维度筛选器的所有节点都将显示为<font color=blue>蓝色</font>。
> - 表示图中指标的未选定节点将显示为<font color=green>绿色</font>。
> - 如果在当前指标中观察到异常，则该节点将显示为<font color=red>红色</font>。

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>在事件中心内查看相关指标异常状态

构建指标图时，每当在图中的指标上检测到异常时，你都可以查看相关的异常状态，并获取事件的概要视图。 

单击图中的事件，并向下滚动到诊断信息下的“跨指标分析”。

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="查看相关指标和异常":::

## <a name="next-steps"></a>后续步骤

- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)
