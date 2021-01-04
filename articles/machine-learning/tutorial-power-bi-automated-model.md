---
title: 教程：使用自动化 ML 创建预测模型（第 1 部分，共 2 部分）
titleSuffix: Azure Machine Learning
description: 了解如何生成和部署自动化 ML 模型，以便在 Microsoft Power BI 中使用最佳模型来预测结果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370590"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>教程：Power BI 集成 - 使用自动化机器学习创建预测模型（第 1 部分，共 2 部分）

在本教程的第一部分中，你将使用 Azure 机器学习工作室中的自动化机器学习来训练和部署预测机器学习模型。  在第 2 部分中，你将使用性能最佳的模型来预测 Microsoft Power BI 中的结果。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Azure 机器学习计算群集
> * 创建数据集
> * 创建自动化 ML 运行
> * 将最佳模型部署到实时评分终结点


有三种不同的方法可用于创建和部署要在 Power BI 中使用的模型。  本文介绍选项 C：在工作室中使用自动化 ML 训练和部署模型。  此选项展示了一种可完全自动执行数据准备和模型训练的无代码创作体验。 

可以改用：

* [选项 A：使用笔记本训练和部署模型](tutorial-power-bi-custom-model.md) - 使用 Azure 机器学习工作室中托管的 Jupyter 笔记本的代码优先创作体验。
* [选项 B：使用设计器训练和部署模型](tutorial-power-bi-designer-model.md) - 使用设计器（拖放式用户界面）的低代码创作体验。

## <a name="prerequisites"></a>先决条件

- Azure 订阅（[已推出免费试用版](https://aka.ms/AMLFree)）。 
- Azure 机器学习工作区。 如果还没有工作区，请按照[如何创建 Azure 机器学习工作区](./how-to-manage-workspace.md#create-a-workspace)所述进行操作。

## <a name="create-compute-cluster"></a>创建计算群集

自动化 ML 会自动训练大量不同的机器学习模型，以找到“最佳”算法和参数。 Azure 机器学习在计算群集上并行执行模型训练。

在 [Azure 机器学习工作室](https://ml.azure.com)的左侧菜单中选择“计算”，然后选择“计算群集”选项卡 。选择“新建”：

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="显示如何创建计算群集的屏幕截图":::

在“创建计算群集”屏幕中：

1. 选择 VM 大小（在本教程中，建议选择 `Standard_D11_v2` 计算机）。
1. 选择“下一步”
1. 提供有效的计算名称
1. 将“最小节点数”保留为 0
1. 将“最大节点数”更改为 4
1. 选择“创建”

可看到群集状态已更改为“正在创建”。

>[!NOTE]
> 创建群集时，它将有 0 个节点，这意味着不会产生计算成本。 只有运行自动化 ML 作业时才会产生成本。 当空闲时间达到 120 秒后，群集将自动缩减为 0。


## <a name="create-dataset"></a>创建数据集

本教程中将使用[糖尿病数据集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)，[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)中提供了该数据集。

若要创建数据集，请选择左侧菜单中的“数据集”，然后选择“创建数据集”，你将看到以下选项 ：

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="显示如何创建新数据集的屏幕截图":::

选择“从开放数据集”，然后在“从开放数据集创建数据集”屏幕中 ：

1. 使用搜索栏搜索“糖尿病”
1. 选择“示例:糖尿病”
1. 选择“下一步”
1. 为数据集提供名称“糖尿病”
1. 选择“创建”

可以通过选择“数据集”，然后选择“浏览”来浏览这些数据：

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="显示如何浏览数据集的屏幕截图":::

该数据包含 10 个基线输入变量（如年龄、性别、体重指数、平均血压和六项血清度量）和一个名为 Y 的目标变量（基线后一年糖尿病进展的定量度量）。

## <a name="create-automated-ml-run"></a>创建自动化 ML 运行

在 [Azure 机器学习工作室](https://ml.azure.com)的左侧菜单中选择“自动化 ML”，然后选择“新建自动化 ML 运行” ：

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="显示如何新建自动化 ML 运行的屏幕截图":::

接下来，选择之前创建的“糖尿病”数据集，然后选择“下一步” ：

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="显示如何选择数据集的屏幕截图":::
 
在“配置运行”屏幕中：

1. 在“试验名称”下，选择“新建” 
1. 为试验提供名称
1. 在“目标列”字段中，选择“Y”
1. 在“选择计算群集”字段中，选择之前创建的计算群集。 

已完成的窗体应类似于：

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="显示如何配置自动化 ML 的屏幕截图":::

最后需要选择要执行的机器学习任务，即“回归”：

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="显示如何配置任务的屏幕截图":::

选择“完成”。

> [!IMPORTANT]
> 自动化 ML 大约需要 30 分钟即可完成 100 个不同模型的训练。

## <a name="deploy-the-best-model"></a>部署最佳模型

自动化 ML 运行完成后，可选择“模型”选项卡来查看已尝试的所有不同机器学习模型的列表。模型按性能顺序排序，性能最好的模型将显示在最前面。 选择最佳模型时，将启用“部署”按钮：

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="显示模型列表的屏幕截图":::

选择“部署”将显示“部署模型”屏幕 ：

1. 为模型服务提供名称 - 使用 diabetes-model
1. 选择“Azure 容器服务”
1. 选择“部署”

你应会看到一条消息，指示已成功部署模型。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何使用自动化 ML 训练和部署机器学习模型。 在下一教程中，你将了解如何在 Power BI 中使用此模型（对其进行评分）。

> [!div class="nextstepaction"]
> [教程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
