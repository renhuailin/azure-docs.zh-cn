---
title: 在工作区中运行 Jupyter 笔记本
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习 studio 中不离开工作区的情况下运行 Jupyter 笔记本。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 82c11b913d38695c8738de88f3ce69b198ee099e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691869"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>在工作区中运行 Jupyter 笔记本

了解如何直接在 Azure 机器学习 studio 中的工作区中运行 Jupyter 笔记本。 用户不但可以在工作区中启动 [Jupyter](https://jupyter.org/) 或 [JupyterLab](https://jupyterlab.readthedocs.io)，还可以直接编辑和运行笔记本。

有关如何创建和管理文件（包括笔记本）的信息，请参阅 [在工作区中创建和管理文件](how-to-manage-files.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://aka.ms/AMLFree)。
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="edit-a-notebook"></a>编辑笔记本

若要编辑笔记本，请打开位于工作区“用户文件”部分的任何笔记本。 单击要编辑的单元格。  如果此部分中没有任何笔记本，请参阅在 [工作区中创建和管理文件](how-to-manage-files.md)。

你可以在不连接到计算实例的情况下编辑笔记本。  要运行笔记本中的单元格时，请选择或创建一个计算实例。  如果选择一个已停止的计算实例，则在你运行第一个单元时，该计算实例会自动启动。

计算实例运行时，还可以在任何 Python 笔记本中使用 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense)支持的代码完成功能。

还可以从笔记本工具栏启动 Jupyter 或 JupyterLab。  Azure 机器学习不提供来自 Jupyter 或 JupyterLab 的更新，也不修复其中的 bug，因为这些开源产品不在 Microsoft 支持的范围内。

## <a name="focus-mode"></a>焦点模式

使用焦点模式展开当前视图，以便将焦点放在活动的选项卡上。 焦点模式将隐藏笔记本文件资源管理器。

1. 在终端窗口工具栏中，选择“焦点模式”以打开焦点模式。 根据窗口宽度的不同，它可能位于工具栏的“…”菜单项下。
1. 在焦点模式下，通过选择“标准视图”返回到标准视图。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="切换焦点模式/标准视图":::

## <a name="use-intellisense"></a>使用 IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) 是一个代码完成辅助插件，其中包括许多功能：“列出成员”、“参数信息”、“快速信息”和“完成单词”。 这些功能可帮助你详细了解所使用的代码、跟踪正在键入的参数，只需几次击键即可添加对属性和方法的调用。  

键入代码时，请使用“Ctrl+空格键”来触发 IntelliSense。

## <a name="clean-your-notebook-preview"></a>清理笔记本（预览版）

> [!IMPORTANT]
> 收集功能目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在创建笔记本的过程中，通常最终会得到用于数据浏览或调试的单元格。 “收集”功能会帮助你生成一个没有这些无关单元格的纯净笔记本。

1. 运行所有笔记本单元格。
1. 选择特定的单元格，其中包含的代码是你希望新笔记本运行的。 例如，用于提交试验的代码，或者用于注册模型的代码。
1. 选择单元格工具栏上出现的“收集”图标。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="屏幕截图：选择“收集”图标":::
1. 输入新的“已收集”笔记本的名称。  

新笔记本只包含代码单元格，所有单元格都需要生成与你选择用于收集的单元格相同的结果。

## <a name="save-and-checkpoint-a-notebook"></a>将笔记本保存并设置检查点

当你创建一个 ipynb 文件时，Azure 机器学习会创建一个检查点文件。

在笔记本工具栏中，选择“菜单”，然后选择“文件”&gt;“保存并设置检查点”以手动保存笔记本，它会添加与笔记本关联的检查点文件。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="笔记本工具栏中保存工具的屏幕截图":::

每个笔记本每隔 30 秒会自动保存一次。 自动保存仅更新初始 ipynb 文件，而不更新检查点文件。
 
在“笔记本”菜单中选择“检查点”以创建命名检查点，并将笔记本还原为已保存的检查点。

## <a name="export-a-notebook"></a>导出笔记本

在笔记本工具栏中，选择菜单，然后将其 **导出** 为，以将笔记本导出为支持的任何类型：

* 笔记本
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="将笔记本导出到计算机":::

导出的文件保存在您的计算机上。

## <a name="run-a-notebook-or-python-script"></a>运行笔记本或 Python 脚本

若要运行笔记本或 Python 脚本，请先连接到正在运行的[计算实例](concept-compute-instance.md)。

* 如果没有计算实例，请使用以下步骤创建一个计算实例：

    1. 在 "笔记本" 或 "脚本" 工具栏中，选择 "计算" 下拉列表右侧的 " **+ 新建计算**"。 根据屏幕大小，此位置可能位于 " **...** " 菜单下。
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="创建新计算":::
    1. 为计算命名，并在“虚拟机大小”中选择一个大小。 
    1. 选择“创建”。
    1. 计算实例自动连接到文件。  你现在可以使用计算实例左侧的工具运行笔记本单元或 Python 脚本。

* 如果有已停止的计算实例，请选择 "计算" 下拉列表右侧的 "  **开始计算** "。 根据屏幕大小，此位置可能位于 " **...** " 菜单下。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="启动计算实例":::

只有本人可以查看和使用自己创建的计算实例。  用户文件与 VM 分开存储，并在工作区中的所有计算实例之间共享。

### <a name="view-logs-and-output"></a>查看日志和输出

使用 [笔记本小组件](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) 来查看运行和日志的进度。 小组件具有异步性，在训练结束之前，它会一直提供更新。 Jupyter 和 JupterLab 也支持 Azure 机器学习小组件。

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="屏幕截图：Jupyter 笔记本小组件":::

## <a name="explore-variables-in-the-notebook"></a>探究笔记本中的变量

在笔记本工具栏上，使用“变量资源管理器”工具显示已在笔记本中创建的所有变量的名称、类型、长度和示例值。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="屏幕截图：“变量资源管理器”工具":::

选择此工具可显示“变量资源管理器”窗口。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="屏幕截图：“变量资源管理器”窗口":::

## <a name="navigate-with-a-toc"></a>使用目录进行导航

在笔记本工具栏上，使用“目录”工具显示或隐藏目录。  启动带标题的 Markdown 单元格，以将其添加到目录中。 单击表中的某个条目可滚动到笔记本中的相应单元格。  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="屏幕截图：笔记本中的目录":::

## <a name="change-the-notebook-environment"></a>更改笔记本环境

笔记本工具栏使你可以更改笔记本的运行环境。  

以下操作不会更改笔记本状态或笔记本中任何变量的值：

|操作  |结果  |
|---------|---------| --------|
|停止内核     |  停止任何正在运行的单元格。 运行单元格会自动重新启动内核。 |
|导航到另一个工作区部分     |     停止运行单元格。 |

以下操作将会重置笔记本状态，并重置笔记本中的所有变量。

|操作  |结果  |
|---------|---------| --------|
| 更改内核 | 笔记本使用新内核 |
| 切换计算    |     笔记本自动使用新的计算。 |
| 重置计算 | 在你尝试运行单元格时再次启动 |
| 停止计算     |    不会运行任何单元格  |
| 在 Jupyter 或 JupyterLab 中打开笔记本     |    笔记本在新选项卡中打开。  |

## <a name="add-new-kernels"></a>添加新内核

[使用终端 ](how-to-access-terminal.md#add-new-kernels) 创建新内核，并将其添加到计算实例。 笔记本会自动查找连接的计算实例上安装的所有 Jupyter 内核。

使用右侧的 "内核" 下拉列表更改为任何已安装的内核。  


### <a name="status-indicators"></a>状态指示器

“计算”下拉列表旁的指示器显示计算的状态。  在计算的下拉列表中也会显示状态。  

|颜色 |计算状态 |
|---------|---------| 
| 绿色 | 正在运行计算 |
| Red |计算失败 | 
| 黑色 | 已停止计算 |
|  浅蓝色 |正在创建、正在启动、正在重新启动、正在设置计算 |
|  灰色 |正在删除、正在停止计算 |

“内核”下拉列表旁的指示器显示内核的状态。

|颜色 |内核状态 |
|---------|---------|
|  绿色 |内核已连接、空闲、繁忙|
|  灰色 |内核未连接 |

## <a name="find-compute-details"></a>查找计算详细信息

有关计算实例的详细信息，可以访问[工作室](https://ml.azure.com)的“计算”页。

## <a name="troubleshooting"></a>疑难解答

* 如果无法连接到笔记本，请确保未禁用 Web 套接字通信。 为了让计算实例 Jupyter 功能可以正常运行，必须启用 Web 套接字通信。 请确保网络允许到 *.instances.azureml.net 和 *.instances.azureml.ms 的 websocket 连接。 

* 在专用链接工作区中部署计算实例时，只能 [从虚拟网络内部访问](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)。 如果使用自定义 DNS 或 hosts 文件，请为 <instance-name>.<region>.instances.azureml.ms 添加一个条目，让该条目包含工作区专用终结点的专用 IP 地址。 有关详细信息，请参阅[自定义 DNS](./how-to-custom-dns.md?tabs=azure-cli) 一文。
    
## <a name="next-steps"></a>后续步骤

* [运行第一个试验](tutorial-1st-experiment-sdk-train.md)
* [使用快照备份文件存储](../storage/files/storage-snapshots-files.md)
* [在安全环境中工作](./how-to-secure-training-vnet.md#compute-instance)
