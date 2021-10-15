---
title: 在工作区中运行 Jupyter Notebook
titleSuffix: Azure Machine Learning
description: 了解如何在不离开 Azure 机器学习工作室中工作区的情况下运行 Jupyter Notebook。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 07/22/2021
ms.openlocfilehash: 447398acc918dc830769b6b5e227f2ea87eb33c4
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545371"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>在工作区中运行 Jupyter Notebook

了解如何直接在 Azure 机器学习工作室的工作区中运行 Jupyter 笔记本。 用户不但可以在工作区中启动 [Jupyter](https://jupyter.org/) 或 [JupyterLab](https://jupyterlab.readthedocs.io)，还可以直接编辑和运行笔记本。

有关如何创建和管理文件（包括笔记本）的信息，请参阅[在工作区中创建和管理文件](how-to-manage-files.md)。

> [!IMPORTANT]
> 标记为“（预览版）”的功能在提供时不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="edit-a-notebook"></a>编辑笔记本

若要编辑笔记本，请打开位于工作区“用户文件”部分的任何笔记本。 单击要编辑的单元格。  如果本部分中没有任何笔记本，请参阅[在工作区中创建和管理文件](how-to-manage-files.md)。

你可以在不连接到计算实例的情况下编辑笔记本。  要运行笔记本中的单元格时，请选择或创建一个计算实例。  如果选择一个已停止的计算实例，则在你运行第一个单元时，该计算实例会自动启动。

如果计算实例正在运行，还可以在任何 Python Notebook 中使用由 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense) 提供支持的代码完成功能。

另外，还可以从笔记本工具栏中启动 Jupyter 或 JupyterLab。  Azure 机器学习不提供来自 Jupyter 或 JupyterLab 的更新，也不修复其中的 bug，因为这些开源产品不在 Microsoft 支持的范围内。

## <a name="focus-mode"></a>焦点模式

使用焦点模式展开当前视图，以便将焦点放在活动的选项卡上。 焦点模式将隐藏笔记本文件资源管理器。

1. 在终端窗口工具栏中，选择“焦点模式”以打开焦点模式。 根据窗口宽度的不同，该工具可能位于工具栏中的“…”菜单项下。
1. 在焦点模式下，通过选择“标准视图”返回到标准视图。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="切换焦点模式/标准视图":::

## <a name="code-completion-intellisense"></a>代码完成 (IntelliSense)

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) 是一个代码完成辅助插件，其中包括许多功能：“列出成员”、“参数信息”、“快速信息”和“完成单词”。 只需按几下按键，就能：
* 详细了解所用的代码
* 跟踪正在键入的参数
* 添加对属性和方法的调用 

### <a name="insert-code-snippets-preview"></a>插入代码片段（预览版）

按 Ctrl+空格键触发 IntelliSense 代码片段。  滚动浏览建议或开始键入，以查找要插入的代码。  插入代码后，按 Tab 键切换参数，以自定义供自己使用的代码。

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="插入代码片段":::

在 VS Code 中打开笔记本时，将会提供这些相同的代码片段。 有关可用代码片段的完整列表，请参阅 [Azure 机器学习 VS Code 代码片段](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md)。

可以使用笔记本工具栏打开代码片段面板，来浏览和搜索代码片段列表。

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="在笔记本工具栏中打开代码片段面板工具":::

在代码片段面板中，还可以提交添加新代码片段的请求。

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="在代码片段面板中可以建议新的代码片段":::

## <a name="collaborate-with-notebook-comments-preview"></a>使用笔记本注释（预览版）进行协作

使用笔记本注释与有权访问你的笔记本的其他人协作。

在笔记本顶部使用“笔记本注释”工具打开和关闭“注释”窗格。  如果屏幕不够宽，请首先选择工具集末尾的“...”来查找此工具。

:::image type="content" source="media/how-to-run-jupyter-notebooks/notebook-comments-tool.png" alt-text="顶部工具栏中笔记本注释工具的屏幕截图。":::  

无论注释窗格是否可见，都可以在任何代码单元格中添加注释：

1. 在代码单元格中选择一些文本。  你只能对代码单元格中的文本进行注释。
1. 使用“新建注释线程”工具创建注释。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/comment-from-code.png" alt-text="向代码单元格添加注释的工具的屏幕截图。":::
1. 如果之前隐藏了“注释”窗格，现在它将打开。  
1. 使用该工具键入注释并发布，或按 Ctrl+Enter 键。
1. 发布注释后，选择右上方的“...”，以执行以下操作：
    * 编辑注释
    * 解析线程
    * 删除线程

已注释的文本将在代码中以紫色突出显示。 在“注释”窗格中选择注释时，笔记本将滚动到包含突出显示文本的单元格。

> [!NOTE]
> 注释将被保存到该代码单元格的元数据中。

## <a name="clean-your-notebook-preview"></a>清理笔记本（预览版）

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

在笔记本工具栏中选择菜单，然后选择“导出为”，以任一受支持类型导出笔记本：

* 笔记本
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="将笔记本导出到计算机":::

导出的文件保存在计算机上。

## <a name="run-a-notebook-or-python-script"></a>运行笔记本或 Python 脚本

若要运行笔记本或 Python 脚本，请先连接到正在运行的[计算实例](concept-compute-instance.md)。

* 如果没有计算实例，请使用以下步骤创建一个计算实例：

    1. 在笔记本或脚本工具栏中，选择“计算”下拉列表右侧的“+ 新建计算”。 它可能位于“...”菜单下，具体取决于屏幕大小。
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="创建新计算":::
    1. 为计算命名，并在“虚拟机大小”中选择一个大小。 
    1. 选择“创建”。
    1. 计算实例自动连接到文件。  现在可以使用计算实例左侧的工具来运行笔记本单元格或 Python 脚本。

* 如果有已停止的计算实例，请选择“计算”下拉列表右侧的“开始计算”。 它可能位于“...”菜单下，具体取决于屏幕大小。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="启动计算实例":::

只有本人可以查看和使用自己创建的计算实例。  用户文件与 VM 分开存储，并在工作区中的所有计算实例之间共享。

### <a name="view-logs-and-output"></a>查看日志和输出

使用[笔记本小组件](/python/api/azureml-widgets/azureml.widgets)查看运行进度和日志。 小组件具有异步性，在训练结束之前，它会一直提供更新。 Jupyter 和 JupterLab 也支持 Azure 机器学习小组件。

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

使用笔记本工具栏可以更改笔记本的运行环境。  

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

[使用终端](how-to-access-terminal.md#add-new-kernels)创建新内核，并将其添加到计算实例。 笔记本会自动查找连接的计算实例上安装的所有 Jupyter 内核。

使用右侧的“内核”下拉列表更改为任何已安装的内核。  


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

## <a name="useful-keyboard-shortcuts"></a>有用的键盘快捷方式
与 Jupyter Notebook 类似，Azure 机器学习工作室笔记本有一个模式化用户界面。 键盘根据笔记本单元格的具体模式执行不同的任务。 对于给定的代码单元格，Azure 机器学习工作室笔记本支持以下两种模式：命令模式和编辑模式。

### <a name="command-mode-shortcuts"></a>命令模式快捷方式

当没有文本光标提示你键入时，单元格处于命令模式。 当单元格处于命令模式时，可以将笔记本作为一个整体进行编辑，但不能键入单独的单元格。 按 `ESC` 或使用鼠标选择单元格编辑器区域外部，进入命令模式。  活动单元格的左边框为蓝色且为实线，其“运行”按钮为蓝色。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="命令模式下的笔记本单元格":::

| 快捷键                      | 描述                          |
| ----------------------------- | ------------------------------------|
| Enter                         | 进入编辑模式             |        
| Shift+Enter                 | 运行单元格，选择下方         |     
| Control/Command + Enter       | 运行单元格                            |
| Alt + Enter                   | 运行单元格，在下方插入代码单元格    |
| Control/Command + Alt + Enter | 运行单元格，在下方插入 Markdown 单元格|
| Alt + R                       | 全部运行      |                       
| Y                             | 将单元格转换为代码    |                         
| M                             | 将单元格转换为 Markdown  |                       
| Up/K                          | 选择上方的单元格    |               
| Down/J                        | 选择下方的单元格    |               
| A                             | 在上方插入代码单元格  |            
| B                             | 在下方插入代码单元格   |           
| Control/Command + Shift + A   | 在上方插入 Markdown 单元格    |      
| Control/Command + Shift + B   | 在下方插入 Markdown 单元格   |       
| X                             | 剪切所选单元格    |               
| C                             | 复制所选单元格   |               
| Shift + V                     | 在上方粘贴所选单元格           |
| V                             | 在下方粘贴所选单元格    |       
| D D                           | 删除所选单元格|                
| O                             | 切换输出         |              
| Shift + O                     | 打开/关闭输出滚动   |          
| I I                           | 中断内核 |                   
| 0 0                           | 重启内核 |                     
| Shift + 空格键                 | 向上滚动  |                         
| Space                         | 向下滚动|
| 选项卡                           | 将焦点更改到下一个可聚焦的项（当 Tab 陷阱被禁用时）|
| Control/Command + S           | 保存笔记本 |                      
| 1                             | 更改到 h1|                       
| 2                             | 更改到 h2|                        
| 3                             | 更改到 h3|                        
| 4                             | 更改到 h4 |                       
| 5                             | 更改到 h5 |                       
| 6                             | 更改到 h6 |                       

### <a name="edit-mode-shortcuts"></a>编辑模式快捷方式

编辑模式由文本光标指示，提示你在编辑器区域中键入内容。 当单元格处于编辑模式时，可以在单元格中键入。 按 `Enter` 或使用鼠标选择单元格的编辑器区域，进入编辑模式。 活动单元格的左边框为绿色且为交错线，其“运行”按钮为绿色。 在“编辑”模式下，还会在单元格中看到光标提示。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="编辑模式下的笔记本单元格":::

使用以下击键快捷方式，可以在“编辑”模式下更轻松地在 Azure 机器学习笔记本中导航并运行代码。

| 快捷键                      | 描述|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | 进入命令模式|  
| Control/Command + 空格键       | 激活 IntelliSense |
| Shift+Enter                 | 运行单元格，选择下方 |                         
| Control/Command + Enter       | 运行单元格  |                                      
| Alt + Enter                   | 运行单元格，在下方插入代码单元格  |              
| Control/Command + Alt + Enter | 运行单元格，在下方插入 Markdown 单元格  |          
| Alt + R                       | 运行所有单元格     |                              
| 向上                            | 将光标上移或移到上一个单元格    |             
| 向下                          | 将光标下移或移到下一个单元格 |                  
| Control/Command + S           | 保存笔记本   |                                
| Control/Command + 向上箭头键          | 转到单元格开头   |                             
| Control/Command + 向下箭头键        | 转到单元格末尾 |                                 
| 选项卡                           | 代码完成或缩进（如果启用了 Tab 陷阱） |
| Control/Command + M           | 启用/禁用 Tab 陷阱  |                       
| Control/Command + ]           | 缩进 |                                         
| Control/Command + [           | 取消缩进  |                                        
| Control/Command + A           | 全选|                                      
| Control/Command + Z           | 撤消 |                                           
| Control/Command + Shift + Z   | 重做 |                                           
| Control/Command + Y           | 重做 |                                           
| Control/Command + Home        | 转到单元格开头|                                
| Control/Command + End         | 转到单元格末尾   |                               
| Control/Command + 向左箭头键        | 左移一个字 |                               
| Control/Command + 向右箭头键       | 右移一个字 |                              
| Control/Command + Backspace   | 删除插入提示之前的字 |                             
| Control/Command + Delete      | 删除插入提示之后的字 |                              
| Control/Command + /           | 打开/关闭单元注释

## <a name="troubleshooting"></a>疑难解答

* 如果无法连接到笔记本，请确保未禁用 Web 套接字通信。 为了让计算实例 Jupyter 功能可以正常运行，必须启用 Web 套接字通信。 确保[网络允许与 *.instances.azureml.net 和 *.instances.azureml.ms 建立 websocket 连接](https://docs.microsoft.com/azure/machine-learning/how-to-access-azureml-behind-firewall?tabs=ipaddress#microsoft-hosts)。 

* 在使用专用终结点的工作区中部署计算实例时，只能[从虚拟网络内部访问](./how-to-secure-training-vnet.md)。 如果使用自定义 DNS 或 hosts 文件，请为 < instance-name >.< region >.instances.azureml.ms 添加一个条目，让该条目包含工作区专用终结点的专用 IP 地址。 有关详细信息，请参阅[自定义 DNS](./how-to-custom-dns.md?tabs=azure-cli) 一文。

* 如果内核崩溃并重启，则可以运行以下命令查看 jupyter 日志并了解更多详细信息：`sudo journalctl -u jupyter`。 如果内核问题仍然存在，请考虑使用具有更多内存的计算实例。

* 如果遇到令牌过期的问题，请注销 Azure ML Studio，再重新登录，然后重启笔记本内核。
    
## <a name="next-steps"></a>后续步骤

* [运行第一个试验](tutorial-1st-experiment-sdk-train.md)
* [使用快照备份文件存储](../storage/files/storage-snapshots-files.md)
* [在安全环境中工作](./how-to-secure-training-vnet.md#compute-cluster)
