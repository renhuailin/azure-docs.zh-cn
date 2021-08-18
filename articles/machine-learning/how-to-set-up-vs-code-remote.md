---
title: 连接到 Visual Studio Code 中的计算实例（预览）
titleSuffix: Azure Machine Learning
description: 了解如何在 Visual Studio Code 中连接到 Azure 机器学习计算实例以运行交互式 Jupyter Notebook 和远程开发工作负荷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 07/15/2021
ms.openlocfilehash: b3e30a4914ac52c9d9661d3b6d6e07f54f0afdc6
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230408"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>连接到 Visual Studio Code 中的 Azure 机器学习计算实例（预览）

在本文中，你将学习如何使用 Visual Studio Code 连接到 Azure 机器学习计算实例。

[Azure 机器学习计算实例](concept-compute-instance.md)是基于云的完全托管数据科学工作站，并为 IT 管理员提供管理功能和企业就绪功能。

有两种方法可从 Visual Studio Code 连接到计算实例：

* 远程计算实例。 此选项提供了一个功能完备的开发环境，用于构建机器学习项目。
* 远程 Jupyter Notebook 服务器。 通过此选项，可将计算实例设置为远程 Jupyter Notebook 服务器。

## <a name="configure-a-remote-compute-instance"></a>配置远程计算实例

若要配置远程计算实例进行开发，需要满足一些先决条件。

* Azure 机器学习 Visual Studio Code 扩展。 有关详细信息，请参阅 [Azure 机器学习 Visual Studio Code 扩展安装指南](how-to-setup-vs-code.md)。
* Azure 机器学习工作区。 如果还没有工作区，请[使用 Azure 机器学习 Visual Studio Code 扩展创建新工作区](how-to-manage-resources-vscode.md#create-a-workspace)。
* Azure 机器学习计算实例。 如果还没有计算实例，请[使用 Azure 机器学习 Visual Studio Code 扩展创建新计算实例](how-to-manage-resources-vscode.md#create-compute-instance)。

> [!IMPORTANT]
> 若要连接到防火墙后面的计算实例，请参阅[将防火墙后面的工作区用于 Azure 机器学习](how-to-access-azureml-behind-firewall.md#visual-studio-code-hosts)。

连接到远程计算实例：

# <a name="studio"></a>[工作室](#tab/studio)

导航到 [ml.azure.com](https://ml.azure.com)

> [!IMPORTANT]
> 若要从 Visual Studio Code 连接到远程计算实例，请确保在 Azure 机器学习工作室中登录到的帐户与 Visual Studio Code 中使用的帐户相同。

### <a name="compute"></a>计算

1. 选择“计算”选项卡
1. 在“应用程序 URI”列中，为要连接到的计算实例选择“VS Code”。

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="VS Code Azure ML 工作室中的连接到计算实例" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>笔记本

1. 选择“笔记本”选项卡
1. 在“笔记本”选项卡中，选择要编辑的文件。
1. 选择“编辑器”>“在 VS Code 中编辑(预览版)”。

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="VS Code Azure ML 笔记本中的连接到计算实例" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

# <a name="vs-code"></a>[VS Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure 机器学习扩展

1. 在 VS Code 中启动 Azure 机器学习扩展。
1. 展开扩展中的“计算实例”节点。
1. 右键单击要连接到的计算实例，然后选择“连接到计算实例”。

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Visual Studio Code Azure ML 扩展中的连接到计算实例" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>命令面板

1. 在 VS Code 中，选择“视图”>“命令面板”，打开命令面板。
1. 在文本框中输入“Azure ML: 连接到计算实例”。
1. 选择订阅。
1. 选择工作区。
1. 选择计算实例或新建一个计算实例。

---

此时将启动远程计算实例的新窗口。 尝试建立与远程计算实例的连接时，将执行以下任务：

1. 授权。 执行一些检查以确保授权尝试进行连接的用户使用计算实例。
1. VS Code 远程服务器安装在计算实例上。
1. 建立 WebSocket 连接以进行实时交互。

建立连接后，该连接就会持久化。 令牌在会话开始时颁发，它会自动刷新以维护与计算实例的连接。

连接到远程计算实例之后，使用编辑器执行以下操作：

* [创作和管理远程计算实例或文件共享上的文件](https://code.visualstudio.com/docs/editor/codebasics)。
* 使用 [VS Code 集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)[在远程计算实例上运行命令和应用程序](how-to-access-terminal.md)。
* [调试脚本和应用程序](https://code.visualstudio.com/Docs/editor/debugging)
* [使用 VS Code 管理 Git 存储库](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>将计算实例配置为远程笔记本服务器

若要将计算实例配置为远程 Jupyter Notebook 服务器，需要满足以下先决条件：

* Azure 机器学习 Visual Studio Code 扩展。 有关详细信息，请参阅 [Azure 机器学习 Visual Studio Code 扩展安装指南](how-to-setup-vs-code.md)。
* Azure 机器学习工作区。 如果还没有工作区，请[使用 Azure 机器学习 Visual Studio Code 扩展创建新工作区](how-to-manage-resources-vscode.md#create-a-workspace)。

若要连接到计算实例：

1. 在 Visual Studio Code 中打开 Jupyter Notebook。
1. 加载集成笔记本体验时，选择“Jupyter 服务器”。

    > [!div class="mx-imgBorder"]
    > ![启动 Azure 机器学习远程 Jupyter Notebook 服务器下拉列表](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    此外，还可使用命令面板：

    1. 从菜单栏选择“视图”>“命令面板”，打开命令面板。
    1. 在文本框中输入 `Azure ML: Connect to Compute instance Jupyter server`。

1. 从 Jupyter 服务器选项列表中选择 `Azure ML Compute Instances`。
1. 从订阅列表中选择你的订阅。 如果之前配置过默认 Azure 机器学习工作区，则跳过此步骤。
1. 选择工作区。
1. 从列表中选择你的计算实例。 如果没有计算实例，请选择“创建新的 Azure ML 计算实例”，并按照提示进行创建。
1. 要使更改生效，必须重新加载 Visual Studio Code。
1. 打开 Jupyter Notebook 并运行一个单元。

> [!IMPORTANT]
> 必须运行一个单元才能建立连接。

此时，你可继续在 Jupyter Notebook 中运行单元。

> [!TIP]
> 也可使用包含类似 Jupyter 的代码单元的 Python 脚本文件 (.py)。 有关详细信息，请参阅 [Visual Studio Code Python 交互文档](https://code.visualstudio.com/docs/python/jupyter-support-py)。

## <a name="next-steps"></a>后续步骤

现在，你已设置 Visual Studio Code 远程，可以将计算实例用作 Visual Studio Code 中的远程计算，从而[对代码进行交互式调试](how-to-debug-visual-studio-code.md)。

[教程：训练自己的首个 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何将计算实例与集成的笔记本配合使用。
