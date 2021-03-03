---
title: 本地 Azure Percept 高级开发入门
description: 在 AzureML 上使用 VS Code 和 Jupyter Notebook 开始进行本地机器学习开发
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658701"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>在 AzureML 上使用 VS Code 和 Jupyter Notebook 开始进行机器学习开发

本文将引导你完成设置 Azure 机器学习工作区、创建计算实例、在本地计算机上设置 Visual Studio Code 开发环境，以及在 VS Code 中运行预配置的示例 Jupyter 笔记本单元的过程。

[笔记本](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)会使用 AzureML 中用 Python 编写的预训练 TensorFlow 模型 (MobileNetSSDV2Lite) 执行迁移学习，并使用自定义数据库来检测“碗”图像。

该笔记本显示了如何从 [COCO 数据集](https://cocodataset.org/#home)开始，将其筛选为仅显示感兴趣的类（“碗”），然后将其转换为适当的格式。 或者，可使用开源的 [VoTT 2](https://github.com/microsoft/VoTT) 标记工具以 PASCAL VOC 格式创建和标记边界框。

对自定义数据集重新训练模型后，可使用模块孪生更新方法将模型部署到 Azure Percept DK。 然后，可查看 Azure Percept Vision SoM 的实时 RTSP 流来检查模型推理情况。 模型的重新训练和部署是通过远程计算实例在笔记本中执行的。

## <a name="prerequisites"></a>先决条件

- [Azure 机器学习订阅](https://azure.microsoft.com/free/services/machine-learning/)
- [已连接 Azure Percept Vision SoM 的 Azure Percept DK](./overview-azure-percept-dk.md)
- 已完成 [Azure Percept DK 加入体验](./quickstart-percept-dk-set-up.md)

## <a name="download-azure-percept-github-repository"></a>下载 Azure Percept GitHub 存储库

1. 转到 [Azure Percept DK GitHub 存储库](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)。
1. 复制存储库或下载 Zip 文件。 在屏幕顶部附近，单击“代码” -> “下载 Zip” 。

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="GitHub 下载屏幕。":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>创建 Azure 机器学习工作区和远程计算实例

1. 转到 [Azure 机器学习门户](https://ml.azure.com)。
1. 从下拉菜单中选择目录、Azure 订阅和机器学习工作区，然后单击“开始使用”。

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Azure ML 入门屏幕。":::

    如果还没有 Azure 机器学习工作区，请单击“创建新的工作区”。 在新的浏览器标签页中：

    1. 选择 Azure 订阅。
    1. 选择你的资源组。
    1. 输入工作区的名称。
    1. 选择你的区域。
    1. 选择你的工作区版本。
    1. 单击“审阅并创建”。
    1. 在下一页上，检查所做的选择，然后单击“创建”。

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Azure ML 中的工作区创建屏幕。":::

    请等待几分钟，直到工作区创建完毕。 工作区创建完成后，资源旁边将出现绿色的复选标记，“机器学习服务”概述页面顶部将显示“部署已完成”。

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="工作区创建确认。" lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    工作区创建完成后，回到机器学习门户选项卡，然后单击“开始使用”。

1. 在机器学习工作区主页上，单击左侧窗格上的“计算”。

1. 如果当前没有计算实例，请单击“新建”来创建新的 CPU 或 GPU 计算。 在“新建计算实例”窗口中，输入计算名称，选择虚拟机类型，然后选择虚拟机大小   。 单击“创建”。

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="“新建计算实例”屏幕。":::

    单击“创建”后，将需要几分钟时间来创建计算实例。 计算创建完成后，“计算”状态将显示一个绿色圆圈和“\<your compute name> - 正在运行” 。 此计算实例运行 Jupyter 服务器，本教程将使用此实例。

## <a name="visual-studio-code-development-environment-setup"></a>Visual Studio Code 开发环境设置

1. 安装所需的工具。

    1. 选项 1：

        使用[开发工具包安装程序](./dev-tools-installer.md)来安装以下包：

        - Visual Studio Code
        - Python 3.5、3.6 或 3.7
        - Miniconda3
        - Intel OpenVino Toolkit 2020.2

        请注意：Intel OpenVino Toolkit 在开发工具包安装程序中被列为可选包，但在使用 Azure Percept DK 开发工具包的 Azure Percept Vision SoM 时，必须使用它。

    1. 选项 2：

        如果不想使用开发工具包安装程序，请单击以下链接，并按照指定的下载和安装指南手动安装以下包：

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3.5、3.6 或 3.7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/)

    请注意：如果已安装完整的 Anaconda 发行版，则无需安装 Miniconda。 如果不想使用 Anaconda 或 Miniconda，可创建一个 Python 虚拟环境，并使用 pip 安装运行 AI 模型开发所需的包。

1. 启动 Visual Studio Code。
1. 设置数据科学环境：

    - 选项 1 - 连接到已有精选 ML 包的现有或新的机器学习远程计算实例。 我们将在本教程中使用此选项。

    - 选项 2 - 在本地计算机上设置 conda 环境。
        1. 打开 Anaconda 或 Miniconda 命令提示符并运行以下命令，创建一个具有指定包的名为 myenv 的环境：

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            若要详细了解如何创建和管理 Anaconda 环境，请查看 [Anaconda 文档](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)。

1. 创建 VS Code 工作区：

    1. 在方便的位置创建一个文件夹，用作 Visual Studio Code 工作区。 将其命名为 myworkspace。
    1. 单击“文件” > “打开文件夹” > “选择文件夹”，在 Visual Studio Code 中打开 myworkspace   。
    1. 在文件资源管理器中，导航到 Azure Percept DK GitHub 存储库的本地副本，并从中选择 [Transferlearningusing_SSDLiteV2 Model.ipynbb 文件](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)。 将此笔记本文件复制到 myworkspace 文件夹。

## <a name="azure-integration-with-visual-studio-code"></a>Azure 与 Visual Studio Code 的集成

1. 如果尚未注册，请注册 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

1. 安装 VS Code 的下列 Azure 扩展：
    - [Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。
    - Python 预览体验计划扩展。 在 VS Code 中，转到“视图” -> “命令面板” 。 在命令面板中，输入并选择“Python: 切换到预览体验计划每日频道”。
    - [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。 出现提示时，在 VS Code 中重新加载窗口。
    - [Azure IoT 中心工具包扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)。
    - [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。

1. 在 Visual Studio Code 中登录到 Azure 帐户，以预配资源并在 Azure 上运行工作负载。
    1. 从菜单栏中选择“视图” > “命令面板”，在 Visual Studio Code 中打开命令面板 。
    1. 在命令面板中输入“Azure: 登录”来启动登录过程。

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Azure 登录屏幕。" lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. 单击 VS Code 左侧的 Azure 图标激活 Python 扩展和 Azure 帐户。

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="VS Code 中的 Azure 图标。":::

    1. 从 myworkspace 文件夹打开 Transferlearningusing_SSDLiteV2 Model_VSCode.ipynb 笔记本。
    1. 打开命令面板。 输入并选择“Python: 为连接指定本地或远程 Jupyter 服务器”。
    1. 你应会看到一个连接选项列表，可从中进行选择。 选择“Azure ML 计算实例”。

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="VS Code 中的 Azure ML 计算实例选项。":::

    1. 按照指导提示选择订阅、工作区和远程计算实例。 选择之前在本教程中创建的工作区和远程计算实例。 也可选择创建新的计算实例。
    1. 选择计算实例后，系统会提示重新加载 VS Code 窗口。 重新加载后，选择“Python 3.6 - AzureML”内核。 现可在笔记本中运行任何单元。 运行笔记本单元将实例化笔记本与计算实例之间的连接。 笔记本工具栏将更新，以反映你正在处理的计算实例。

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="VS Code 中的内核选择。" lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>使用笔记本

现在你已准备好运行笔记本，以在 VS Code 中训练自定义“碗”检测器并将其部署到开发工具包中。 执行脚本前，请确保单独运行笔记本的每个单元，因为某些单元需要输入参数。 可直接在笔记本中编辑需要输入参数的单元。 若要运行某个单元，请单击该单元左侧的“运行”图标：

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="笔记本突出显示单元图标。":::

## <a name="next-steps"></a>后续步骤

有关其他 Azure 机器学习服务示例笔记本，请访问此[存储库](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)。
