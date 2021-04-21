---
title: 教程：从 Jupyter Notebook (Python) 着手
titleSuffix: Azure Machine Learning
description: 针对 Jupyter Notebook 教程进行设置。 创建工作区，将笔记本克隆到工作区中，并创建在其中运行笔记本的计算实例。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: ed7a95ea83cacc0eaccfa23cd87403fb9be615ee
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210663"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>教程：在 Jupyter Notebook 中开始使用 Azure 机器学习

在本教程中，你将通过使用[托管的基于云的工作站（计算实例）](concept-compute-instance.md)上的 Jupyter Notebook 来完成 Azure 机器学习的入门步骤。 本教程是所有其他 Jupyter Notebook 教程的前提。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建要在其他 Jupyter Notebook 教程中使用的 [Azure 机器学习工作区](concept-workspace.md)。
> * 将教程笔记本克隆到工作区中的文件夹。
> * 创建基于云的计算实例，它可为你提供已安装并配置了 Azure 机器学习 Python SDK 的环境。

本教程介绍如何在工作区中的计算资源上运行 Jupyter 笔记本。  

开始使用 Azure 机器学习的另一种好方法是提交批处理作业。  请参阅[教程：开始在开发环境中使用 Azure 机器学习](tutorial-1st-experiment-sdk-setup-local.md)。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到服务中一个易于使用的对象。

如果你已有 Azure 机器学习工作区，请跳转到[克隆笔记本文件夹](#clone)。  

可以通过许多[方法来创建工作区](how-to-manage-workspace.md)。  本教程将通过 Azure 门户创建工作区，该门户是用于管理 Azure 资源的基于 Web 的控制台。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> 记下你的工作区和订阅 。 你将需要此信息以确保在正确的位置创建试验。

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>在工作区中运行笔记本

Azure 机器学习在你的工作区中提供了一个云笔记本服务器，实现了免安装的预配置体验。 如果你希望控制环境、包和依赖项，请使用[自己的环境](tutorial-1st-experiment-sdk-setup-local.md)。

 按照此视频的说明或使用详细步骤从工作区克隆和运行教程笔记本。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a>克隆笔记本文件夹

在 Azure 机器学习工作室中完成以下试验设置并运行步骤。 此合并接口包括机器学习工具，所有技能级别的数据科学专业人员均可利用这些工具实现数据科学方案。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。

1. 选择创建的订阅和工作区。

1. 在左侧选择“笔记本”。

1. 在顶部选择“示例”选项卡。

1. 打开“Python”文件夹。

1. 打开包含版本号的文件夹。 此数字表示 Python SDK 的当前版本。

1. 选择“教程”文件夹右侧的“…”按钮，然后选择“克隆”  。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="显示“克隆”教程文件夹的屏幕截图。":::

1. 文件夹列表显示访问该工作区的每个用户。 选择要将“tutorials”文件夹克隆到其中的文件夹。

### <a name="open-the-cloned-notebook"></a><a name="open"></a>打开克隆的笔记本

1. 打开“用户文件”部分中关闭的“教程”文件夹 。

    > [!IMPORTANT]
    > 可以查看“示例”文件夹中的笔记本，但无法从此文件夹运行笔记本。 若要运行笔记本，请确保在“用户文件”部分打开笔记本的克隆版本。
    
1. 选择 tutorials/image-classification-mnist-data 文件夹中的 img-classification-part1-training.ipynb 文件。 

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="显示“打开”教程文件夹的屏幕截图。":::

1. 在顶部栏上，选择用来运行笔记本的计算实例。 这些虚拟机 (VM) 中已预先配置了[运行 Azure 机器学习所需的一切设置](concept-compute-instance.md#contents)。

1. 如果未找到任何 VM，请选择“+ 添加”来创建计算实例 VM。

    1. 创建 VM 时，请遵循以下规则：
 
        + 名称是必填项，该字段不能为空。
        + 此名称在工作区或计算实例的 Azure 区域中的所有现有计算实例中必须唯一（不区分大小写）。 如果选择的名称不是唯一的，会收到警报。
        + 有效字符包括大小写字母、数字 0 到 9 和短划线字符 (-)。
        + 名称的长度必须介于 3 到 24 个字符之间。
        + 名称应以字母（而非数字或短划线字符）开头。
        + 如果使用短划线字符，短划线后需要跟至少一个字母。 例如，“Test-”、“test-0”、“test-01”无效，而“test-a0”、“test-0a”为有效实例。

    1. 从可用选项中选择 VM 大小。 对于本教程，默认 VM 是不错的选择。

    1. 然后选择“创建”。 设置 VM 可能需要大约五分钟时间。

1. 当 VM 可用时，它将显示在顶部工具栏中。 现在，可以使用工具栏中的“全部运行”，或者在笔记本的代码单元中按 Shift+Enter 来运行笔记本 。

如果你有自定义小组件或喜欢使用 Jupyter 或 JupyterLab，请在最右侧选择“Jupyter”下拉列表。 然后选择“Jupyter”或“JupyterLab” 。 此时将打开一个新的浏览器窗口。

## <a name="next-steps"></a>后续步骤

现在，你已设置了一个开发环境，请继续在 Jupyter Notebook 中训练模型。

> [!div class="nextstepaction"]
> [教程：使用 MNIST 数据和 scikit-learn 训练图像分类模型](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> 如果你现在不打算继续学习任何其他教程，请在不使用云笔记本服务器 VM 时停止它，以降低成本。

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
