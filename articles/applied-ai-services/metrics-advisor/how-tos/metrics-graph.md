---
title: 指标顾问指标图
titleSuffix: Azure Cognitive Services
description: 如何配置指标图并可视化数据中的相关异常。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: 7842cdd6d5336e90346d666d8c2d31d20c19caa0
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341116"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>操作说明：构建指标图来分析相关指标

指标顾问中的每一时序都由一个模型单独监视，该模型从历史数据中进行学习来预测未来趋势。 如果有任何数据点脱离历史模式，将检测到异常。 但是，在某些情况下，多个指标可能彼此相关联，需要跨多个指标对异常进行分析。 指标图就是帮助实现这一点的工具。 

例如，如果有从不同角度监视业务的多个指标，则将分别应用异常情况检测。 但是，在实际的业务案例中，针对多个指标检测到的异常情况可能彼此互相关联，发现这些关系并在此基础上分析根本原因将有助于解决实际问题。 指标图帮助自动关联针对相关指标检测到的异常情况，从而加快故障排除过程。 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>选择一个指标，将第一个节点放入图中

单击导航栏中的“指标图”选项卡。 构建指标图的第一步是将节点放入图中。 在页面顶部选择数据馈送和指标。 节点将显示在底部面板中。 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="选择指标":::

## <a name="add-a-noderelation-on-existing-node"></a>在现有节点上添加节点/关系

接下来，需要添加另一个节点，并指定它与现有节点之间的关系。 选择现有节点并右键单击该节点。 将出现一个上下文菜单，其中包含多个选项。 

选择“添加关系”，你将能够选择另一个指标并指定两个节点之间的关系类型。 你还可以应用特定的维度筛选器。 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="添加节点和关系":::

重复上述步骤后，你将得到一个描述所有相关指标之间关系的指标图。

还可以对图执行的其他操作包括： 
1.  删除节点
2.  转到指标
3.  转到事件中心
4.  展开
5.  删除关系

## <a name="legend-of-metrics-graph"></a>指标图的图例

图中的每个节点都代表一个指标。 指标图中有四种类型的节点：

-  绿色节点：表示当前指标事件严重性较低的节点。
- 橙色节点：表示当前指标事件严重性为中等的节点。
- 红色节点：表示当前指标事件严重性较高的节点。
- 蓝色节点：没有异常严重性的节点。


## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>在事件中心内查看相关指标异常状态

构建指标图后，每当针对图中的指标检测到异常时，你都可以查看相关的异常状态，并获取事件的概略视图。 

单击图中的事件，并向下滚动到诊断信息下的“跨指标分析”。

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="查看相关指标和异常":::

## <a name="next-steps"></a>后续步骤

- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-an-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)