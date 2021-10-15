---
title: 使用管道参数生成通用管道
titleSuffix: Azure Machine Learning
description: 如何在 Azure 机器学习设计器中使用管道参数。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: b3ef6f684123c863fd2bdfb5a5ec953b3b20cc6f
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428058"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>在设计器中使用管道参数生成通用管道

使用管道参数在设计器中生成灵活管道。 通过管道参数，你可以在运行时动态设置值以封装管道逻辑和重用资产。

在重新提交管道运行、[重新训练模型](how-to-retrain-designer.md)或[执行批量预测](how-to-run-batch-predictions-designer.md)时，管道参数特别有用。

本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建管道参数
> * 删除和管理管道参数
> * 调整管道参数时，触发管道运行

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 有关设计器的引导式简介，请完成[设计器教程](tutorial-designer-automobile-price-train-score.md)。 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>创建管道参数

在设计器中创建管道参数有三种方法：
- 在设置面板中创建管道参数，并将其绑定到模块。
- 将模块参数提升为管道参数。
- 将数据集提升为管道参数

> [!NOTE]
> 管道参数仅支持 `int`、`float` 和 `string` 等基本数据类型。

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>选项 1：在设置面板中创建管道参数

在本部分，你将在设置面板中创建管道参数。

在本示例中，你将创建一个管道参数，该参数定义管道如何使用清理缺失数据模块填充缺失的数据。

1. 在管道草稿名称旁边，选择齿轮图标打开“设置”面板 。

1. 在“管道参数”部分，选择 + 图标 。

1.  输入参数名称和默认值。 

    例如，输入 `replace-missing-value` 作为参数名称，输入 `0` 作为默认值。

   ![屏幕截图显示了如何创建管道参数](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


创建管道参数之后，必须[将其附加到要动态设置的模块参数](#attach-module-parameter-to-pipeline-parameter)。

### <a name="option-2-promote-a-module-parameter"></a>选项 2：提升模块参数

为模块值创建管道参数最简单的方法是提升模块参数。 使用以下步骤将模块参数提升为管道参数：

1. 选择要将管道参数附加到的模块。
1. 在模块详细信息窗格中，将鼠标悬停在要指定的参数上。
1. 选择显示的省略号 (…)。
1. 选择“添加到管道参数”。

    ![屏幕截图显示了如何将模块参数提升为管道参数 1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. 输入参数名称和默认值。
1. 选择“保存”

现在，你可以在提交此管道时为此参数指定新值。

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>选项 3：将数据集提升为管道参数

如果要提交包含变量数据集的管道，则必须将数据集提升为管道参数：

1. 选择要转换成管道参数的数据集。

1. 在数据集的详细信息面板中，选择“设置为管道参数”。

   ![屏幕截图显示了如何将数据集设置为管道参数](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

现在，你就可以在下次运行管道时使用管道参数来指定其他数据集。

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>将模块参数附加到管道参数以及将两者拆离 

在本部分，你将了解如何将模块参数附加到管道参数以及将两者拆离。

### <a name="attach-module-parameter-to-pipeline-parameter"></a>将模块参数附加到管道参数

如果要在触发管道运行的同时更改值，可以将重复模块的相同模块参数附加到同一个管道参数。

下面的示例包含重复的清理缺失数据模块。 对于每个清理缺失数据模块，将替换值附加到管道参数 replace-missing-value  ：

1. 选择“清理缺失数据”模块。

1. 在画布右侧的模块详细信息窗格中，将“清理模式”设置为“自定义替换值”。

1. 将鼠标悬停在“替换值”字段上。

1. 选择显示的省略号 (…)。

1. 选择管道参数 `replace-missing-value`。

   ![屏幕截图显示如何附加管道参数](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

已成功将“替换值”字段附加到管道参数。 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>将模块参数与管道参数拆离

将“替换值”附加到管道参数后，该字段是不可操作的。

可通过单击模块参数旁的省略号 (...) 并选择“从管道参数拆离”，将模块参数与管道参数拆离 。

 ![屏幕截图显示了附加到管道参数后该值无法操作](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>更新和删除管道参数

本部分介绍如何更新和删除管道参数。

### <a name="update-pipeline-parameters"></a>更新管道参数

按照以下步骤更新模块管道参数：

1. 在画布顶部选择齿轮图标。
1. 在“管道参数”部分，你可以查看和更新所有管道参数的名称和默认值。

### <a name="delete-a-dataset-pipeline-parameter"></a>删除数据集管道参数

使用以下步骤删除数据集管道参数：

1. 选择数据集模块。
1. 取消选中“设置为管道参数”选项。


### <a name="delete-module-pipeline-parameters"></a>删除模块管道参数

按照以下步骤删除模块管道参数：

1. 在画布顶部选择齿轮图标。

1. 选择管道参数旁边的省略号 (…)。

    此视图显示管道参数附加到的模块。

    ![屏幕截图显示了应用于模块的当前管道参数](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. 选择“删除参数”以删除管道参数。

    > [!NOTE]
    > 删除管道参数将导致所有附加的模块参数被拆离，拆离模块参数的值将保留当前的管道参数值。     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>使用管道参数触发管道运行 

本部分介绍如何在设置管道参数时提交管道运行。

### <a name="resubmit-a-pipeline-run"></a>重新提交管道运行

提交具有管道参数的管道后，可以重新提交具有其他参数的管道运行：

1. 转到管道详细信息页面。 在“管道运行概述”窗口中，你可以检查当前的管道参数和值。

1. 选择“重新提交”。
1. 在“设置管道运行”中，指定新的管道参数。 

![屏幕截图显示了重新提交具有管道参数的管道](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>使用已发布的管道

还可以发布管道以使用其管道参数。 “已发布的管道”是已部署到计算资源的管道，客户端应用程序可以通过 REST 终结点调用该管道。

已发布的终结点对于重新训练和批量预测应用场景特别有用。 有关详细信息，请参阅[如何在设计器中重新训练模型](how-to-retrain-designer.md)或[在设计器中运行批量预测](how-to-run-batch-predictions-designer.md)。

## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何在设计器中创建管道参数。 接下来，你将了解如何使用管道参数[重新训练模型](how-to-retrain-designer.md)或执行[批量预测](how-to-run-batch-predictions-designer.md)。

你还可以了解如何[通过 SDK 以编程方式使用管道](how-to-deploy-pipelines.md)。
