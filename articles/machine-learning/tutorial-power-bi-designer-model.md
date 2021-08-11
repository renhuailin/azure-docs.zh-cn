---
title: 教程：拖放以创建预测模型（第 1 部分，共 2 部分）
titleSuffix: Azure Machine Learning
description: 了解如何使用设计器生成和部署机器学习预测模型。 稍后，你可以使用它来预测 Microsoft Power BI 中的结果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 9390fc53f4d10ad770313f1f1459f02fc7e7a998
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459435"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>教程：Power BI 集成 - 拖放以创建预测模型（第 1 部分，共 2 部分）

在本教程的第 1 部分中，你将使用 Azure 机器学习设计器训练和部署预测机器学习模型。 此设计器是一个低代码拖放式用户界面。 在第 2 部分中，你将使用该模型来预测 Microsoft Power BI 中的结果。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Azure 机器学习计算实例。
> * 创建 Azure 机器学习推理群集。
> * 创建数据集。
> * 训练回归模型。
> * 将模型部署到实时评分终结点。


有三种方法可用于创建和部署要在 Power BI 中使用的模型。  本文介绍“选项 B：使用设计器训练和部署模型”。  此选项是使用设计器界面的低代码创作体验。  

但你可改用其他选项之一：

* [选项 A：使用 Jupyter Notebook 训练和部署模型](tutorial-power-bi-custom-model.md)。 这种代码优先的创作体验使用 Azure 机器学习工作室中托管的 Jupyter Notebook。
* [选项 C：使用自动化机器学习训练和部署模型](tutorial-power-bi-automated-model.md)。 这种不用代码的创作体验可完全自动执行数据准备和模型训练。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，可使用[免费试用版](https://azure.microsoft.com/free/)。 
- Azure 机器学习工作区。 如果没有工作区，请参阅[创建和管理 Azure 机器学习工作区](./how-to-manage-workspace.md#create-a-workspace)。
- 机器学习工作流的入门知识。


## <a name="create-compute-to-train-and-score"></a>创建计算以便训练和评分

在本部分中，创建一个计算实例。 计算实例用于训练机器学习模型。 还将创建一个推理群集，用于托管已部署的模型以进行实时评分。

登录到 [Azure 机器学习工作室](https://ml.azure.com)。 在左侧菜单中，选择“计算”，然后选择“新建” ：

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="显示如何创建计算实例的屏幕截图。":::

在“创建计算实例”页上，选择 VM 大小。 对于本教程，请选择“Standard_D11_v2”VM。 然后，选择“下一步”。 

在“设置”页上，为计算实例命名。 然后选择“创建”。 

>[!TIP]
> 还可以使用计算实例来创建和运行笔记本。

计算实例“状态”现在为“正在创建” 。 预配计算机大约需要 4 分钟。 

等待期间，在“计算”页上选择“推理群集”选项卡 。然后，选择“新建”：

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="显示如何创建推理群集的屏幕截图。":::

在“创建推理群集”页上，选择区域和 VM 大小。 对于本教程，请选择“Standard_D11_v2”VM。 然后，选择“下一步”。 

在“配置设置”页上：

1. 提供有效的计算名称。
1. 选择“开发测试”作为群集目的。 此选项将创建单个节点来托管已部署的模型。
1. 选择“创建”  。

推理群集“状态”现在为“正在创建” 。 部署单个节点群集大约需要 4 分钟。

## <a name="create-a-dataset"></a>创建数据集

在本教程中，将使用[糖尿病数据集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)中提供了该数据集。

若要创建数据集，请在左侧菜单中选择“数据集”。 然后，选择“创建数据集”。 你将看到以下选项：

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="显示如何新建数据集的屏幕截图。":::

选择“从开放数据集”。 在“从开放数据集创建数据集”页面：

1. 使用搜索栏查找“糖尿病”。
1. 选择“示例:糖尿病”。
1. 选择“**下一页**”。
1. 将数据集命名为“糖尿病”。
1. 选择“创建”  。

若要浏览数据，请选择数据集，然后选择“浏览”：

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="显示如何浏览数据集的屏幕截图。":::

数据包含 10 个基线输入变量，例如年龄、性别、体重指数、平均血压和六项血清度量。 它还有一个名为“Y”的目标变量。该目标变量是基线后一年糖尿病进展的量化度量值。

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>使用设计器创建机器学习模型

创建计算和数据集之后，可以使用设计器来创建机器学习模型。 在 Azure 机器学习工作室中，选择“设计器”，然后选择“新建管道” ：

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="显示如何创建新管道的屏幕截图。":::

将显示一个空白画布和“设置”菜单：

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="显示如何选择计算目标的屏幕截图。":::

在“设置”菜单中，选择“选择计算目标” 。 选择此前创建的计算实例，然后选择“保存”。 将“草稿名称”更改为更容易记忆的名称，如 diabetes-model。 最后，输入说明。

在资产列表中，展开“数据集”并找到“糖尿病”数据集 。 将此组件拖动到画布上：

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="显示如何将组件拖动到画布上的屏幕截图。":::

接下来，将以下组件拖动到画布上：

1. 线性回归（位于“机器学习算法”中 ）
1. 训练模型（位于“模型训练”中 ）

在画布上，请注意组件顶部和底部的圆圈。 这些圆圈是端口。

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="显示未连接组件上的端口的屏幕截图。":::
 
现在将组件连接在一起。 选择“糖尿病”数据集底部的端口。 将其拖动到“训练模型”组件右上方的端口。 选择“线性回归”组件底部的端口。 将其拖动到“训练模型”组件左上方的端口。

选择要用作要预测的标签（目标）变量的数据集列。 选择“训练模型”组件，然后选择“编辑列” 。 

在对话框中，选择“输入列名称” > “Y” ：

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="显示如何选择标签列的屏幕截图。":::

选择“保存”。 机器学习工作流应如下所示：

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="显示已连接组件的屏幕截图。":::

选择“提交”。  在“试验”下，选择“新建” 。 为试验命名，然后选择“提交”。

>[!NOTE]
> 试验首次运行大约需要 5 分钟。 后续运行的速度要快得多，因为设计器缓存已运行的组件以减少延迟。

试验完成后，会看到此视图：

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="显示已完成运行的屏幕截图。":::

若要检查试验日志，请选择“训练模型”，然后选择“输出 + 日志” 。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，请在画布顶部，选择“创建推理管道” > “实时推理管道” ：

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="显示在何处选择实时推理管道的屏幕截图。":::
 
管道压缩为执行模型评分所需的组件。 为数据评分时，你不知道目标变量值。 因此，可以从数据集中删除“Y”。 

若要删除“Y”，请在画布上添加“选择数据集中的列”组件 。 连接组件，将“糖尿病”数据集作为输入。 结果是输出到“计分模型”组件：

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="显示如何删除列的屏幕截图。":::

在画布上选择“选择数据集中的列”组件，然后选择“编辑列” 。 

在“选择列”对话框中，选择“按名称” 。 然后，确保选择了所有输入变量，但未选中目标：

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="显示如何删除列设置的屏幕截图。":::

选择“保存”。 

最后，选择“评分模型”组件，并确保清除“将评分列追加到输出”复选框 。 为了减少延迟，无需输入便会发送回预测。

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="显示评分模型组件设置的屏幕截图。":::

在画布顶部选择“提交”。

成功运行推理管道后，可以将模型部署到推理群集。 选择“部署”。 

在“设置实时终结点”对话框中，选择“部署新的实时终结点” 。 将终结点命名为 my-diabetes-model。 选择此前创建的推理，然后选择“部署”：

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="显示实时终结点设置的屏幕截图。":::
## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何训练和部署设计器模型。 在下一部分中，你将了解如何在 Power BI 中使用此模型（对其进行评分）。

> [!div class="nextstepaction"]
> [教程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
