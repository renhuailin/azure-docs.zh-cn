---
title: 云中 Azure Percept 高级开发入门
description: 使用 Jupyter Notebook 和 Azure 机器学习在云中开始高级开发
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658682"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>使用 Jupyter Notebook 和 Azure 机器学习在云中开始高级开发

本文将引导你完成设置 Azure 机器学习工作区、将预配置的示例 Jupyter Notebook 上传到工作区、创建计算实例，以及在工作区中运行笔记本单元的过程。

[笔记本](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)会使用 AzureML 中用 Python 编写的预训练 TensorFlow 模型 (MobileNetSSDV2Lite) 执行迁移学习，并使用自定义数据库来检测“碗”图像。

该笔记本显示了如何从 [COCO](https://cocodataset.org/#home) 开始，将其筛选为仅显示感兴趣的类（“碗”），然后将其转换为适当的格式。 或者，可使用开源的 [VoTT 2](https://github.com/microsoft/VoTT) 标记工具以 PASCAL VOC 格式创建和标记边界框。

对自定义数据集重新训练模型后，可使用模块孪生更新方法将模型部署到 Azure Percept DK。

然后，可查看 Azure Percept Vision SoM 的实时 RTSP 流来检查模型推理情况。 模型重新训练和部署均在云的笔记本中进行。

## <a name="prerequisites"></a>先决条件

- [Azure 机器学习订阅](https://azure.microsoft.com/free/services/machine-learning/)
- [已连接 Azure Percept Vision SoM 的 Azure Percept DK](./overview-azure-percept-dk.md)
- 已完成 [Azure Percept DK 加入体验](./quickstart-percept-dk-set-up.md)

## <a name="download-azure-percept-github-repository"></a>下载 Azure Percept GitHub 存储库

1. 转到 [Azure Percept GitHub 存储库](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)。

1. 复制存储库或下载 Zip 文件。 在屏幕顶部附近，单击“代码” -> “下载 Zip” 。

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="GitHub 下载屏幕。":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>设置 Azure 机器学习门户和笔记本

1. 转到 [Azure 机器学习门户](https://ml.azure.com)。

1. 从下拉菜单中选择目录、Azure 订阅和机器学习工作区，然后单击“开始使用”。

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Azure ML 入门屏幕。":::

    如果还没有 Azure 机器学习工作区，请单击“创建新的工作区”。 在新的浏览器标签页中：

    1. 选择 Azure 订阅。
    1. 选择你的资源组。
    1. 输入工作区的名称。
    1. 选择你的区域。
    1. 选择你的工作区版本。
    1. 单击“审阅并创建”。
    1. 在下一页上，检查所做的选择，然后单击“创建”。

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Azure ML 中的工作区创建屏幕。":::

    请等待几分钟，直到工作区创建完毕。 工作区创建完成后，资源旁边将出现绿色的复选标记，“机器学习服务”概述页面顶部将显示“部署已完成”。

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="工作区创建确认。" lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    工作区创建完成后，回到机器学习门户选项卡，然后单击“开始使用”。

1. 在机器学习工作区主页上，单击左侧窗格上的“笔记本”。

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Azure ML 主页。":::

1. 在“我的文件”选项卡下，单击垂直箭头以上传 .ipynb 文件。

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="主页突出显示了“上传文件”图标。":::

1. 导航到 Azure Percept GitHub 存储库的本地副本，并从中选择 [Transferlearningusing_SSDLiteV2 Model.ipynbb 文件](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)。 单击“打开”。 在“上传文件”窗口中，选中“我信任此文件中的内容”旁的框，然后单击“上传”  。

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="文件选择屏幕。":::

1. 在右上方的菜单栏上，选中“计算”状态。 如果未找到计算，请单击“+”图标新建一个计算。

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="突出显示了“+”图标的计算状态。" lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. 在“新建计算实例”窗口中，输入计算名称，选择虚拟机类型，然后选择虚拟机大小   。 单击“创建”。

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="“新建计算实例”屏幕。":::

    单击“创建”后，“计算”状态将显示一个蓝色圆圈，并显示“\<your compute name> - 正在创建”  

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="仍在创建计算时的计算状态。":::

    计算创建完成后，“计算”状态将显示一个绿色圆圈和“\<your compute name> - 正在运行” 。

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="显示计算创建已完成的计算状态。":::

1. 计算运行后，从 + 图标旁的下拉菜单中选择“Python 3.6 - AzureML”内核 。

## <a name="working-with-the-notebook"></a>使用笔记本

现在，你已准备好运行笔记本，来训练自定义的“碗”检测器并将其部署到开发工具包中。 执行脚本前，请确保单独运行笔记本的每个单元，因为某些单元需要输入参数。 可直接在笔记本中编辑需要输入参数的单元。 若要运行某个单元，请单击该单元左侧的“运行”图标：

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="笔记本突出显示单元图标。" lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>后续步骤

有关其他 Azure 机器学习服务示例笔记本，请访问此[存储库](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)
