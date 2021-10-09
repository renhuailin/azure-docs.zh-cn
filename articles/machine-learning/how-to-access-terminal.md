---
title: 如何访问工作区中的计算实例终端
titleSuffix: Azure Machine Learning
description: 使用计算实例上的终端执行 Git 操作，以安装程序包并添加内核。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 02/05/2021
ms.openlocfilehash: b4b953c527010cb4ec15f0adf52df6bb3b0d7db0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674939"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>访问工作区中的计算实例终端

访问工作区中的计算实例的终端，以执行以下操作：

* 使用 Git 中的文件以及对文件进行版本控制。 这些文件存储在你的工作区文件系统中，而非限定于单个计算实例。
* 在计算实例上安装程序包。
* 在计算实例上创建额外的内核。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="access-a-terminal"></a>访问终端

访问终端的步骤：

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中打开工作区。
1. 在左侧选择“笔记本”。
1. 选择“打开终端”图像。

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="打开终端窗口":::

1. 如果有某个计算实例正在运行时，则将显示该计算实例的终端窗口。
1. 如果没有任何计算实例正在运行，请使用右侧的“计算”部分启动或创建一个计算实例。
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="启动或创建计算实例":::

除了以上步骤之外，还可以通过以下项来访问终端：

* RStudio：选择左上的“终端”选项卡。
* Jupyter 实验室：选择“启动器”选项卡中“其他”标题下的“终端”磁贴。
* Jupyter：在“文件”选项卡的右上方选择“新建>“终端”。
* 如果在创建计算实例时启用了 SSH 访问，则使用 SSH 连接到计算机。

## <a name="copy-and-paste-in-the-terminal"></a>在终端中复制和粘贴

> * Windows：使用 `Ctrl-Insert` 复制，使用 `Ctrl-Shift-v` 或 `Shift-Insert` 粘贴。
> * Mac OS：使用 `Cmd-c` 复制，使用 `Cmd-v` 粘贴。
> * FireFox/IE 可能不会正确支持剪贴板权限。

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> 使用 Git 中的文件以及对文件进行版本控制

从终端访问所有 Git 操作。 所有 Git 文件和文件夹都将存储在你的工作区文件系统中。 此存储允许你从工作区中的任何计算实例使用这些文件。

> [!NOTE]
> 将文件和文件夹添加到 ~/cloudfiles/code/Users 文件夹下的任意位置，使其在所有 Jupyter 环境中都可见。

详细了解如何[将 Git 存储库克隆到工作区文件系统](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。

## <a name="install-packages"></a>安装包

 从终端窗口安装程序包。 将 Python 包安装到“Python 3.8 - AzureML”环境中。  将 R 包安装到 **R** 环境中。

还可以直接在 Jupyter Notebook 或 RStudio 中安装程序包：

* RStudio 使用右下的“包”选项卡或左上的“控制台”选项卡。  
* Python:添加安装代码并在 Jupyter Notebook 单元中执行该代码。

> [!NOTE]
> 对于笔记本中的包管理，请使用 %pip 或 %conda magic 函数将包自动安装到当前正在运行的内核，而不是安装到引用所有包（包括当前正在运行的内核之外的包）的 !pip 或 !conda    

## <a name="add-new-kernels"></a>添加新内核

> [!WARNING]
>  自定义计算实例时，请勿删除 Conda 环境“azureml_py36”或“azureml_py38”，   以及“Python 3.6 - AzureML”或“Python 3.8 - AzureML”内核。  这些是 Jupyter/JupyterLab 功能必需的。

若要向计算实例添加新 Jupyter 内核，请执行以下步骤：

1. 使用终端窗口创建新环境。  例如，以下代码会创建 `newenv`：

    ```shell
    conda create --name newenv
    ```

1. 激活该环境。  例如，创建 `newenv` 的结果如下：

    ```shell
    conda activate newenv
    ```

1. 在新环境中安装 pip 和 ipykernel 包，并为该 conda 环境创建内核

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

可以安装任何[可用的 Jupyter 内核](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)。

## <a name="manage-terminal-sessions"></a>管理终端会话

 选择终端工具栏中的“查看活动会话”以查看所有活动终端会话的列表。 如果没有处于活动状态的会话，则此选项卡将被禁用。

关闭所有未使用的会话以保留你的计算实例的资源。
