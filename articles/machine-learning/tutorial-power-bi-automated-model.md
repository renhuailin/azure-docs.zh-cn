---
title: 教程：使用自动化 ML 创建预测模型（第 1 部分，共 2 部分）
titleSuffix: Azure Machine Learning
description: 了解如何生成和部署自动化机器学习模型，以便在 Microsoft Power BI 中使用最佳模型来预测结果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 4d760f5017c20ac88d3329bb79937c971d4fba75
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459417"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>教程：Power BI 集成 - 使用自动化机器学习创建预测模型（第 1 部分，共 2 部分）

在本教程的第 1 部分中，你将训练和部署预测机器学习模型。 你要在 Azure 机器学习工作室中使用自动化机器学习 (ML)。  在第 2 部分中，你将使用性能最佳的模型来预测 Microsoft Power BI 中的结果。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Azure 机器学习计算群集。
> * 创建数据集。
> * 创建自动化机器学习运行。
> * 将最佳模型部署到实时评分终结点。


有三种方法可用于创建和部署要在 Power BI 中使用的模型。  本文介绍“选项 C：在工作室中使用自动化机器学习训练和部署模型”。  此选项是一种不用代码的创作体验。 它完全自动执行数据准备和模型训练。 

但你可改用其他选项之一：

* [选项 A：使用 Jupyter Notebook 训练和部署模型](tutorial-power-bi-custom-model.md)。 这种代码优先的创作体验使用 Azure 机器学习工作室中托管的 Jupyter Notebook。
* [选项 B：使用 Azure 机器学习设计器训练和部署模型](tutorial-power-bi-designer-model.md)。 这种少用代码的创作体验使用拖放式用户界面。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，可使用[免费试用版](https://azure.microsoft.com/free/)。 
- Azure 机器学习工作区。 如果没有工作区，请参阅[创建和管理 Azure 机器学习工作区](./how-to-manage-workspace.md#create-a-workspace)。

## <a name="create-a-compute-cluster"></a>创建计算群集

自动化机器学习会训练大量机器学习模型，来找出“最佳”算法和参数。 Azure 机器学习会在计算群集上并行运行模型训练。

若要开始，请在 [Azure 机器学习工作室](https://ml.azure.com)的左侧菜单中选择“计算”。 打开“计算群集”选项卡。然后，选择“新建”：

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="显示如何创建计算群集的屏幕截图。":::

在“创建计算群集”页面上：

1. 选择 VM 大小。 在本教程中，可选择“Standard_D11_v2”计算机。
1. 选择“**下一页**”。
1. 提供有效的计算名称。
1. 将“最小节点数”保留为 `0`。
1. 将“最大节点数”更改为 `4`。
1. 选择“创建”  。

群集的状态更改为“正在创建”。

>[!NOTE]
> 新群集有 0 个节点，因此不会产生计算成本。 只有在自动化机器学习作业运行时才会产生成本。 空闲时间达到 120 秒后，群集自动缩减回到 0。


## <a name="create-a-dataset"></a>创建数据集

在本教程中，将使用[糖尿病数据集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)中提供了该数据集。

若要创建数据集，请在左侧菜单中选择“数据集”。 然后，选择“创建数据集”。 你将看到以下选项：

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="显示如何新建数据集的屏幕截图。":::

选择“从开放数据集”。 然后，在“从开放数据集创建数据集”页面：

1. 使用搜索栏查找“糖尿病”。
1. 选择“示例:糖尿病”。
1. 选择“**下一页**”。
1. 将数据集命名为“糖尿病”。
1. 选择“创建”  。

若要浏览数据，请选择数据集，然后选择“浏览”：

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="显示如何浏览数据集的屏幕截图。":::

数据包含 10 个基线输入变量，例如年龄、性别、体重指数、平均血压和六项血清度量。 它还有一个名为“Y”的目标变量。该目标变量是基线后一年糖尿病进展的量化度量值。

## <a name="create-an-automated-machine-learning-run"></a>创建自动化机器学习运行

在 [Azure 机器学习工作室](https://ml.azure.com)的左侧菜单中，选择“自动化 ML”。 然后，选择“新建自动化 ML 运行”：

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="显示如何新建自动化机器学习运行的屏幕截图。":::

接下来，选择之前创建的“糖尿病”数据集。 然后，选择“下一步”：

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="显示如何选择数据集的屏幕截图。":::
 
在“配置运行”页面上：

1. 在“试验名称”下，选择“新建” 。
1. 为试验命名。
1. 在“目标列”字段中，选择“Y” 。
1. 在“选择计算群集”字段中，选择之前创建的计算群集。 

已完成的窗体应如下所示：

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="显示如何配置自动化机器学习的屏幕截图。":::

最后，选择机器学习任务。 在本例中，任务是“回归”：

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="显示如何配置任务的屏幕截图。":::

选择“完成”。

> [!IMPORTANT]
> 自动化机器学习大约需要 30 分钟才能完成 100 个模型的训练。

## <a name="deploy-the-best-model"></a>部署最佳模型

自动化机器学习完成后，可选择“模型”选项卡来查看已尝试的所有机器学习模型。模型按性能排序，性能最佳的模型显示在最前面。 选择最佳模型后，将启用“部署”按钮：

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="显示模型列表的屏幕截图。":::

选择“部署”来打开“部署模型”窗口 ：

1. 将模型服务命名为 diabetes-model。
1. 选择“Azure 容器服务”。
1. 选择“部署”。

你应会看到一条消息，它指示已成功部署模型。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何使用自动化机器学习训练和部署机器学习模型。 在下一教程中，你将了解如何在 Power BI 中使用此模型（对其进行评分）。

> [!div class="nextstepaction"]
> [教程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
