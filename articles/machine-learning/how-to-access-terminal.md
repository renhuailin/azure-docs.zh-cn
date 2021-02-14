---
title: 如何在工作区中访问计算实例终端
titleSuffix: Azure Machine Learning
description: 对 Git 操作使用计算实例上的终端，安装包并添加内核。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101212"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>访问工作区中的计算实例终端

访问工作区中计算实例的终端，以执行以下操作：

* 使用 Git 和版本文件中的文件。 这些文件存储在工作区文件系统中，而不局限于单个计算实例。
* 在计算实例上安装包。
* 在计算实例上创建额外的内核。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://aka.ms/AMLFree)。
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="access-a-terminal"></a>访问终端

访问终端的步骤：

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中打开工作区。
1. 在左侧选择“笔记本”。
1. 选择 " **打开终端** 映像"。

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="打开终端窗口":::

1. 计算实例运行时，将显示该计算实例的终端窗口。
1. 如果没有运行任何计算实例，请使用右侧的 " **计算** " 部分启动或创建计算实例。
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="启动或创建计算实例":::

除了以上步骤，还可以从以下项访问终端：

* RStudio：选择左上的“终端”选项卡。
* Jupyter 实验室：选择“启动器”选项卡中“其他”标题下的“终端”磁贴。
* Jupyter：在“文件”选项卡的右上方选择“新建>“终端”。
* 如果在创建计算实例时启用了 SSH 访问，则使用 SSH 连接到计算机。

## <a name="copy-and-paste-in-the-terminal"></a>复制并粘贴到终端

> * Windows：使用 `Ctrl-Insert` 复制，使用 `Ctrl-Shift-v` 或 `Shift-Insert` 粘贴。
> * Mac OS：使用 `Cmd-c` 复制，使用 `Cmd-v` 粘贴。
> * FireFox/IE 可能不会正确支持剪贴板权限。

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> 使用 Git 和版本文件中的文件

从终端访问所有 Git 操作。 所有 Git 文件和文件夹都将存储在你的工作区文件系统中。 此存储允许从工作区中的任何计算实例使用这些文件。

> [!NOTE]
> 将文件和文件夹添加到 ~/cloudfiles/code/Users 文件夹下的任意位置，使其在所有 Jupyter 环境中都可见。

详细了解如何[将 Git 存储库克隆到工作区文件系统](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。

## <a name="install-packages"></a>安装包

 从终端窗口安装包。 将 Python 包安装到“Python 3.6 - AzureML”环境中。  将 R 包安装到 **R** 环境中。

或者，可以直接在 Jupyter Notebook 或 RStudio 中安装包：

* RStudio 使用右下的“包”选项卡或左上的“控制台”选项卡。  
* Python:添加安装代码并在 Jupyter Notebook 单元中执行该代码。

> [!NOTE]
> 对于笔记本中的包管理，请使用 %pip 或 %conda magic 函数将包自动安装到当前正在运行的内核，而不是安装到引用所有包（包括当前正在运行的内核之外的包）的 !pip 或 !conda    

## <a name="add-new-kernels"></a>添加新内核

> [!WARNING]
>  自定义计算实例时，请确保未删除 Conda 环境“azureml_py36”或“Python 3.6 - AzureML”内核 。 必须具备它们才能使用 Jupyter/JupyterLab 功能

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

 选择终端工具栏中的 " **查看活动会话** " 以查看所有活动终端会话的列表。 如果没有活动会话，则将禁用此选项卡。

关闭所有未使用的会话以保留计算实例的资源。