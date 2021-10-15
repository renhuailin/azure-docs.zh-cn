---
title: 监视托管联机终结点（预览版）
titleSuffix: Azure Machine Learning
description: 使用 Application Insights 监视托管联机终结点并创建警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, devplatv2
ms.openlocfilehash: 44cfdcdd5d40aaac2eb97b9a3268a9b376f71560
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425366"
---
# <a name="monitor-managed-online-endpoints-preview"></a>监视托管联机终结点（预览版）

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

本文介绍如何监视 [Azure 机器学习托管联机终结点（预览版）](concept-endpoints.md)。 使用 Application Insights 查看指标并创建警报，以随时了解托管联机终结点的情况。

本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 查看托管联机终结点的指标
> * 创建指标仪表板
> * 创建指标警报

## <a name="prerequisites"></a>先决条件

- 部署 Azure 机器学习托管联机终结点（预览版）。
- 在终结点上必须至少拥有[读取者访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="view-metrics"></a>查看指标

使用以下步骤查看托管终结点或部署的指标：
1. 转到 [Azure 门户](https://portal.azure.com)。
1. 导航到托管联机终结点或部署资源。

    托管联机终结点和部署是 Azure 资源管理器 (ARM) 资源，转到其所属资源组即可找到这些资源。 查找资源类型“机器学习联机终结点”和“机器学习联机部署” 。

1. 在左侧列中，选择“指标”。

## <a name="available-metrics"></a>可用度量值

你看到的指标因你选择的资源而异。 托管联机终结点和托管联机部署（预览版）的指标范围各不相同。

### <a name="metrics-at-endpoint-scope"></a>终结点范围的指标

- 请求延迟
- 请求延迟 P50（第 50 百分位的请求延迟）
- 请求延迟 P90（第 90 百分位的请求延迟）
- 请求延迟 P95（第 95 百分位的请求延迟）
- 每分钟请求数

按以下维度拆分：

- 部署
- 状态代码
- 状态代码类

### <a name="metrics-at-deployment-scope"></a>部署范围的指标

- CPU 使用率
- 部署容量（所请求实例类型的实例数）
- 磁盘利用率
- GPU 内存利用率（仅适用于 GPU 实例）
- GPU 利用率（仅适用于 GPU 实例）
- 内存使用率

按以下维度拆分：

- InstanceId

## <a name="create-a-dashboard"></a>创建仪表板

可以在 Azure 门户中创建自定义仪表板来可视化来自多个源的数据，包括托管联机终结点的指标。 有关详细信息，请参阅[使用 Application Insights 创建自定义 KPI 仪表板](../azure-monitor/app/tutorial-app-dashboards.md#add-custom-metric-chart)。
    
## <a name="create-an-alert"></a>创建警报

还可以创建自定义警报，以接收有关托管联机终结点发生的重要状态更新的通知：

1. 在指标页的右上方，选择“新建警报规则”。

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" alt-text="监视联机终结点：显示由红框围绕的“新建警报规则”按钮的屏幕截图":::

1. 选择一个条件名称以指定在何时应触发警报。

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" alt-text="监视联机终结点：显示由红框围绕的“配置信号逻辑”按钮的屏幕截图":::

1. 选择“添加操作组” > “创建操作组”，以指定触发警报时应发生什么情况 。

1. 选择“创建警报规则”完成警报创建。


## <a name="next-steps"></a>后续步骤

* 了解如何[查看已部署的终结点的成本](./how-to-view-online-endpoints-costs.md)。
* 详细了解[指标资源管理器](../azure-monitor/essentials/metrics-charts.md)。