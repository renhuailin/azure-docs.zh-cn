---
title: 使用 Azure 机器学习设计器运行批量预测
titleSuffix: Azure Machine Learning
description: 了解如何创建批量预测管道。 将管道部署为参数化 Web 服务，以及从任何 HTTP 库触发该服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 02/05/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: 85562dd33031ae0f392efac6aed6d5e4ecc5c2e5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428267"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器运行批量预测


本文介绍如何使用设计器创建批量预测管道。 借助批量预测，可以使用可从任何 HTTP 库触发的 Web 服务按需对大型数据集持续评分。

在本操作指南中，你将了解如何执行以下任务：

> [!div class="checklist"]
> * 创建并发布批量推理管道
> * 使用管道终结点
> * 管理终结点版本

要了解如何使用 SDK 设置批量评分服务，请参阅随附的[操作指南](./tutorial-pipeline-batch-scoring-classification.md)。

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="prerequisites"></a>先决条件

本操作指南假设你已有一个训练管道。 有关设计器的引导式简介，请完成[设计器教程的第一部分](tutorial-designer-automobile-price-train-score.md)。 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-batch-inference-pipeline"></a>创建批量推理管道

必须至少运行一次训练管道，才能创建推理管道。

1. 转到工作区中的“设计器”选项卡。

1. 选择训练管道，将使用它训练要用来进行预测的模型。

1. “提交”管道。

    ![提交管道](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

训练管道已运行，现在可以创建批量推理管道了。

1. 在“提交”旁边，选择新的下拉菜单“创建推理管道”。

1. 选择“批量推理管道”。

    ![创建批量推理管道](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
结果为默认批量推理管道。 

### <a name="add-a-pipeline-parameter"></a>添加管道参数

要创建对新数据的预测，可以手动连接此管道草稿视图中的不同数据集，也可以创建数据集的参数。 通过参数可以在运行时更改批量推理过程的行为。

在本节中，你将创建一个数据集参数，以便指定要进行预测的不同目标数据集。

1. 选择数据集模块。

1. 画布右侧将显示一个窗格。 在窗格底部，选择“设置为管道参数”。
   
    输入参数名称或接受默认值。

    > [!div class="mx-imgBorder"]
    > ![将数据集设置为管道参数](./media/how-to-run-batch-predictions-designer/set-dataset-as-pipeline-parameter.png)

## <a name="publish-your-batch-inference-pipeline"></a>发布批量推理管道

现已准备好部署推理管道。 下面的操作将部署管道，使其可供其他人使用。

1. 选择“发布”按钮。

1. 在出现的对话框中，展开“PipelineEndpoint”的下拉菜单，然后选择“新建 PipelineEndpoint”。

1. 提供终结点名称和可选说明。

    在对话框底部附近，可以看到使用训练期间所用的数据集 ID 默认值配置的参数。

1. 选择“发布”。

![发布管道](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>使用终结点

现在，你已有一个带数据集参数的已发布管道。 管道将使用在训练管道中创建的训练模型，对作为参数提供的数据集进行评分。

### <a name="submit-a-pipeline-run"></a>提交管道运行 

在本节中，你将设置手动管道运行，并修改管道参数，以便对新数据评分。 

1. 部署完成后，转到“终结点”部分。

1. 选择“管道终结点”。

1. 选择你创建的终结点的名称。

![终结点链接](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. 选择“已发布管道”。

    此屏幕显示此终结点下的所有已发布管道。

1. 选择已发布的管道。

    “管道详细信息”页显示管道的详细运行历史记录和连接字符串信息。 
    
1. 选择“提交”创建管道的手动运行。

    ![管道详细信息](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. 更改参数以使用不同的数据集。
    
1. 选择“提交”运行管道。

### <a name="use-the-rest-endpoint"></a>使用 REST 终结点

可以在“终结点”部分中找到有关如何使用管道终结点和已发布管道的信息。

在“运行概述”面板中可以找到管道终结点的 REST 终结点。 通过调用终结点，将使用其默认的已发布管道。

另外，还可以在“已发布管道”页中使用已发布的管道。 选择一个已发布的管道，然后就可以在图右侧的“已发布管道概览”面板中找到该管道的 REST 终结点。 

若要进行 REST 调用，需要 OAuth 2.0 持有者类型身份验证标头。 请参阅以下[教程部分](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)，以详细了解如何设置工作区的身份验证并进行参数化 REST 调用。

## <a name="versioning-endpoints"></a>版本控制终结点

设计器将版本分配给你发布到终结点的每个后续管道。 可以在 REST 调用中指定要作为参数执行的管道版本。 如果不指定版本号，设计器将使用默认管道。

在发布管道时，可以选择将其设为该终结点的新默认管道。

![设置默认管道](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

另外，还可以在终结点的“已发布管道”选项卡中设置新的默认管道。

![在已发布管道页面设置默认管道](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="limitations"></a>限制

如果在训练管道中进行了一些修改，则应重新提交该训练管道，“更新”推理管道并重新运行该推理管道。

请注意，推理管道中只会更新模型，而不会更新数据转换。

若要在推理管道中使用更新的转换，需要将转换模块的转换输出注册为数据集。

![显示如何注册转换数据集的屏幕截图](./media/how-to-run-batch-predictions-designer/register-transformation-dataset.png)

然后，手动将推理管道中的 TD 模块替换为已注册的数据集。

![显示如何替换转换模块的屏幕截图](./media/how-to-run-batch-predictions-designer/replace-td-module-batch-inference-pipeline.png)

然后，即可提交模型和转换均已更新的推理管道，并进行发布。

## <a name="next-steps"></a>后续步骤

按照设计器[教程](tutorial-designer-automobile-price-train-score.md)训练和部署回归模型。

若要了解如何使用 SDK 来发布管道以及如何运行已发布的管道，请参阅[此文](how-to-deploy-pipelines.md)。
