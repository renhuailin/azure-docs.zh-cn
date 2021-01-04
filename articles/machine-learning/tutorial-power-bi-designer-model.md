---
title: 教程：拖放以创建预测模型（第 1 部分，共 2 部分）
titleSuffix: Azure Machine Learning
description: 了解如何使用设计器生成和部署机器学习预测模型，以便可以使用它来预测 Microsoft Power BI 中的结果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370564"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>教程：Power BI 集成 - 拖放以创建预测模型（第 1 部分，共 2 部分）

在本教程的第一部分中，你将使用 Azure 机器学习设计器（一种低代码拖放式用户界面）训练和部署预测机器学习模型。 在第 2 部分中，你将使用该模型来预测 Microsoft Power BI 中的结果。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Azure 机器学习计算实例
> * 创建 Azure 机器学习推理群集
> * 创建数据集
> * 训练回归模型
> * 将模型部署到实时评分终结点


有三种不同的方法可用于创建和部署要在 Power BI 中使用的模型。  本文介绍选项 B：使用设计器训练和部署模型。  此选项显示使用设计器（拖放式用户界面）的低代码创作体验。  

可以改用：

* [选项 A：使用笔记本训练和部署模型](tutorial-power-bi-custom-model.md) - 使用 Azure 机器学习工作室中托管的 Jupyter 笔记本的代码优先创作体验。
* [选项 C：使用自动化 ML 训练和部署模型](tutorial-power-bi-automated-model.md) - 可完全自动执行数据准备和模型训练的无代码创作体验。

## <a name="prerequisites"></a>先决条件

- Azure 订阅（[已推出免费试用版](https://aka.ms/AMLFree)）。 
- Azure 机器学习工作区。 如果还没有工作区，请按照[如何创建 Azure 机器学习工作区](./how-to-manage-workspace.md#create-a-workspace)所述进行操作。
- 机器学习工作流的入门知识。


## <a name="create-compute-for-training-and-scoring"></a>创建用于训练和评分的计算

在本部分中，将创建一个“计算实例”，该实例用于训练机器学习模型。 此外，还将创建一个“推理群集”，该群集将用于托管已部署的模型以进行实时评分。

登录到 [Azure 机器学习工作室](https://ml.azure.com)，从左侧菜单中选择“计算”，然后选择“新建” ：

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="显示如何创建计算实例的屏幕截图":::

在生成的“创建计算实例”屏幕上，选择 VM 大小（对于本教程，请选择 `Standard_D11_v2`），然后选择“下一步” 。 在“设置”页中，为计算实例提供一个有效的名称，然后选择“创建”。 

>[!TIP]
> 计算实例还可用于创建和执行笔记本。

现在，你可以看到计算实例状态为“正在创建”，预配计算机大约需要 4 分钟 。 在等待时，选择“计算”页上的“推理群集”选项卡，然后选择“新建” ：

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="显示如何创建推理群集的屏幕截图":::

在生成的“创建推理群集”页上，选择区域，然后选择 VM 大小（对于本教程，请选择 `Standard_D11_v2`），然后选择“下一步” 。 在“配置设置”页上：

1. 提供有效的计算名称
1. 选择“开发-测试”作为群集目的（创建单个节点以托管已部署的模型）
1. 选择“创建”

现在，你可以看到推理群集状态为“正在创建”，部署单节点群集大约需要 4 分钟 。

## <a name="create-a-dataset"></a>创建数据集

本教程中将使用[糖尿病数据集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)，[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)中提供了该数据集。

若要创建数据集，请在左侧菜单中选择“数据集”，然后选择“创建数据集”，你将看到以下选项 ：

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

## <a name="create-a-machine-learning-model-using-designer"></a>使用设计器创建机器学习模型

创建计算和数据集后，可以继续使用设计器创建机器学习模型。 在 Azure 机器学习工作室中，选择“设计器”，然后选择“新建管道” ：

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="显示如何创建新管道的屏幕截图":::

你将看到一个空白画布，还可以在其中看到“设置”菜单：

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="显示如何选择计算目标的屏幕截图":::

在“设置”菜单上，选择“计算目标”，然后选择之前创建的计算实例，再选择“保存”  。 将“草稿名称”重命名为更容易记忆的名称（例如“糖尿病模型”），然后输入说明。

接下来，在列出的资产中展开“数据集”并找到“糖尿病”数据集，将此模块拖放到画布上 ：

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="显示如何将组件拖动到画布上的屏幕截图":::

接下来，将以下组件拖放到画布上：

1. 线性回归（位于“机器学习算法”中）
1. 训练模型（位于“模型训练”中）

画布应如下所示（请注意，组件的顶部和底部都有称为端口的小圆圈，下面用红色突出显示）：

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="显示未连接组件的屏幕截图":::
 
接下来，需要将这些组件连接在一起。 选择“糖尿病”数据集底部的端口，将其拖动到“训练模型”组件顶部的右侧端口 。 选择“线性回归”组件底部的端口，将其拖动到“训练模型”端口顶部的左侧端口 。

选择数据集中要用作要预测的标签（目标）变量的列。 选择“训练模型”组件，然后选择“编辑”列 。 在对话框中，选择“输入列名称”，然后选择下拉列表中的 Y ：

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="选择标签列的屏幕截图":::

选择“保存”。 机器学习工作流应如下所示：

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="显示已连接组件的屏幕截图":::

选择“提交”，然后选择试验下的“新建” 。 为试验提供名称，然后选择“提交”。

>[!NOTE]
> 在首次运行时，试验大约需要 5 分钟才能完成。 后续运行的速度要快得多，因为设计器缓存已经运行组件以减少延迟。

完成试验后，将看到：

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="显示已完成运行的屏幕截图":::

可以通过选择“训练模型”然后选择“输出 + 日志”来检查试验的日志 。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，请选择“创建推理管道”（位于画布的顶部），然后选择“实时推理管道” ：

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="显示实时推理管道的屏幕截图":::
 
管道压缩为执行模型评分所需的组件。 当你对数据进行评分时，你不会知道目标变量值，因此，我们可以从数据集中删除 Y。 如需删除，请在画布上添加“选择数据集中的列”组件。 连接组件，使糖尿病数据集成为输入，结果输出到“评分模型”组件：

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="显示删除列的屏幕截图":::

在画布上选择“选择数据集中的列”组件，然后选择“编辑列” 。 在“选择列”对话框中，选择“按名称”，然后确保已选择所有输入变量，但未选择目标 ：

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="显示删除列设置的屏幕截图":::

选择“保存”。 最后，选择“评分模型”组件，并确保未选中“将评分列追加到输出”复选框（仅发回预测，而不是发回输入和预测，从而减少延迟） ：

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="显示评分模型组件设置的屏幕截图":::

在画布顶部选择“提交”。

成功运行推理管道后，可以将模型部署到推理群集。 选择“部署”，这将显示“设置实时终结点”对话框 。 选择“部署新的实时终结点”，将终结点命名为“my-diabetes-model”，选择之前创建的推理，选择“部署”  ：

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="显示实时终结点设置的屏幕截图":::
## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何训练和部署设计器模型。 在下一部分中，你将了解如何在 Power BI 中使用此模型（对其进行评分）。

> [!div class="nextstepaction"]
> [教程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
