---
title: 使用 VS Code 扩展（预览版）创建和管理资源
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 Visual Studio Code 扩展来创建和管理 Azure 机器学习资源。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: 8175fa121828b9c50911fee8bb811115a630e919
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112460533"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>使用 VS Code 扩展（预览版）管理 Azure 机器学习资源

了解如何使用 VS Code 扩展管理 Azure 机器学习资源。

![Azure 机器学习 VS Code 扩展](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，注册之后即可试用 [Azure 机器学习免费版或付费版](https://azure.microsoft.com/free/)。
- Visual Studio Code。 如果尚未安装它，请[安装它](https://code.visualstudio.com/docs/setup/setup-overview)。
- Azure 机器学习扩展。 遵循 [Azure 机器学习 VS Code 扩展安装指南](how-to-setup-vs-code.md)安装该扩展。

## <a name="create-resources"></a>创建资源

创建资源的最快方法是使用扩展的工具栏。

1. 打开“Azure 机器学习”视图。
1. 在活动栏中选择 +。
1. 从下拉列表中选择你的资源。
1. 配置规范文件。 所需的信息取决于要创建的资源类型。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，可以使用命令面板来创建资源：

1. 选择“视图”>“命令面板”打开命令面板
1. 在文本框中输入 `> Azure ML: Create <RESOURCE-TYPE>`。 将 `RESOURCE-TYPE` 替换为要创建的资源类型。
1. 配置规范文件。
1. 选择“视图”>“命令面板”打开命令面板
1. 在文本框中输入 `> Azure ML: Create Resource`。

## <a name="version-resources"></a>控制资源的版本

某些资源（如环境、数据集和模型）允许对资源进行更改并存储不同的版本。

若要控制资源的版本，请执行以下操作：

1. 使用创建了该资源的现有规范文件，或者遵循资源创建过程创建新的规范文件。
1. 在模板中递增版本号。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

只要已更新的资源名称与前一版本相同，Azure 机器学习就会拾取更改并创建新版本。

## <a name="workspaces"></a>工作区

有关详细信息，请参阅[工作区](concept-workspace.md)。

### <a name="create-a-workspace"></a>创建工作区

1. 在“Azure 机器学习”视图中，右键单击你的订阅节点，然后选择“创建工作区”。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Workspace` 命令。

### <a name="remove-workspace"></a>删除工作区

1. 展开包含你的工作区的订阅节点。
1. 右键单击要删除的工作区。
1. 选择是否要进行以下删除：
    - 仅工作区：此选项仅删除工作区 Azure 资源。 工作区所附加到的资源组、存储帐户和任何其他资源仍在 Azure 中。
    - 包括关联的资源：此选项会删除工作区以及与其关联的所有资源。

或者，在命令面板中使用 `> Azure ML: Remove Workspace` 命令。

## <a name="datastores"></a>数据存储

该扩展目前支持以下类型的数据存储：

- Azure Blob
- Azure Data Lake Gen 1
- Azure Data Lake Gen 2
- Azure 文件

有关详细信息，请参阅[数据存储](concept-data.md#datastores)。

### <a name="create-a-datastore"></a>创建数据存储

1. 展开包含你的工作区的订阅节点。
1. 展开要在其下创建数据存储的工作区节点。
1. 右键单击“数据存储”节点，然后选择“创建数据存储” 。
1. 选择数据存储类型。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Datastore` 命令。

### <a name="manage-a-datastore"></a>管理数据存储

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 在工作区中展开“数据存储”节点。
1. 右键单击数据存储并选择以下选项之一：
    - “取消注册数据存储”。 从工作区中删除该数据存储。
    - “查看数据存储”。 显示只读的数据存储设置

或者，在命令面板中分别使用 `> Azure ML: Unregister Datastore` 和 `> Azure ML: View Datastore` 命令。

## <a name="datasets"></a>数据集

该扩展当前支持以下数据集类型：

- 表格：允许将数据具体化为数据帧。
- *文件*：文件或文件集合。 允许你将文件下载或装载到计算机。

有关详细信息，请参阅[数据集](concept-data.md#datasets)

### <a name="create-dataset"></a>创建数据集

1. 展开包含你的工作区的订阅节点。
1. 展开要在其下创建数据集的工作区节点。
1. 右键单击“数据集”节点，然后选择“创建数据集” 。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Dataset` 命令。

### <a name="manage-a-dataset"></a>管理数据集

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“数据集”节点。
1. 右键单击数据集并选择以下选项之一：
    - “查看数据集属性”。 可以查看与特定数据集关联的元数据。 如果你有数据集的多个版本，可以选择仅查看特定版本的数据集属性，方法是：展开该数据集节点，然后在感兴趣的版本上执行本部分所述的步骤。
    - “预览数据集”。 直接在 VS Code 数据查看器中查看数据集。 请注意，此选项仅适用于表格数据集。
    - “取消注册数据集”。 从工作区中删除数据集及其所有版本。

或者，在命令面板中分别使用 `> Azure ML: View Dataset Properties` 和 `> Azure ML: Unregister Dataset` 命令。

## <a name="environments"></a>环境

有关详细信息，请参阅[环境](concept-environments.md)。

### <a name="create-environment"></a>创建环境

1. 展开包含你的工作区的订阅节点。
1. 展开要在其下创建数据存储的工作区节点。
1. 右键单击“环境”节点，然后选择“创建环境”。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Environment` 命令。

### <a name="view-environment-configurations"></a>查看环境配置

若要在扩展中查看特定环境的依赖项和配置，请执行以下操作：

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“环境”节点。
1. 右键单击要查看的环境，并选择“查看环境”。

或者，在命令面板中使用 `> Azure ML: View Environment` 命令。

## <a name="experiments"></a>试验

有关详细信息，请参阅[试验](concept-azure-machine-learning-architecture.md#experiments)。

### <a name="create-job"></a>创建作业

创建作业的最快方法是单击扩展活动栏中的“创建作业”图标。

在“Azure 机器学习”视图中使用资源节点：

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 右键单击工作区中的“试验”节点，然后选择“创建作业” 。
1. 选择作业类型。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Job` 命令。

### <a name="view-job"></a>查看作业

若要在 Azure 机器学习工作室中查看作业，请执行以下操作：

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“试验”节点。
1. 右键单击要查看的试验，然后选择“在工作室中查看试验”。
1. 此时会出现一个提示，要求你在 Azure 机器学习工作室中打开试验 URL。 选择“打开”  。

或者，在命令面板中相应地使用 `> Azure ML: View Experiment in Studio` 命令。

### <a name="track-run-progress"></a>跟踪运行进度

运行作业时，你可能希望查看其进度。 若要从扩展中跟踪 Azure 机器学习工作室中某个运行的进度，请执行以下操作：

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“试验”节点。
1. 展开要跟踪其进度的作业节点。
1. 右键单击该运行，然后选择“在工作室中查看运行”。
1. 此时会出现一个提示，要求你在 Azure 机器学习工作室中打开运行 URL。 选择“打开”  。

### <a name="download-run-logs--outputs"></a>下载运行日志和输出

某个运行完成后，你可能希望下载日志和资产，例如在运行过程中生成的模型。

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“试验”节点。
1. 展开要下载其日志和输出的作业节点。
1. 右键单击该运行：
    - 若要下载输出，请选择“下载输出”。
    - 若要下载日志，请选择“下载日志”。

或者，在命令面板中分别使用 `> Azure ML: Download Outputs` 和 `> Azure ML: Download Logs` 命令。

## <a name="compute-instances"></a>计算实例

有关详细信息，请参阅[计算实例](concept-compute-instance.md)。

### <a name="create-compute-instance"></a>创建计算实例

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点。
1. 右键单击工作区中的“计算实例”节点，然后选择“创建计算” 。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Compute` 命令。

### <a name="connect-to-compute-instance"></a>连接到计算实例

若要将某个计算实例用作开发环境或远程 Jupyter 服务器，请参阅[连接到计算实例](how-to-set-up-vs-code-remote.md?tabs=extension)。

### <a name="stop-or-restart-compute-instance"></a>停止或重启计算实例

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“计算实例”节点 。
1. 右键单击要停止或重启的计算实例，分别选择“停止计算实例”或“重启计算实例” 。

或者，在命令面板中分别使用 `> Azure ML: Stop Compute instance` 和 `Restart Compute instance` 命令。

### <a name="view-compute-instance-configuration"></a>查看计算实例配置

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“计算实例”节点 。
1. 右键单击要检查的计算实例，然后选择“查看计算实例属性”。

或者，在命令面板中使用 `Azure ML: View Compute instance Properties` 命令。

### <a name="delete-compute-instance"></a>删除计算实例

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“计算实例”节点 。
1. 右键单击要删除的计算实例，然后选择“删除计算实例”。

或者，在命令面板中使用 `Azure ML: Delete Compute instance` 命令。

## <a name="compute-clusters"></a>计算群集

有关详细信息，请参阅[训练计算目标](concept-compute-target.md#train)。

### <a name="create-compute-cluster"></a>创建计算群集

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点。
1. 右键单击工作区中的“计算群集”节点，然后选择“创建计算” 。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Compute` 命令。

### <a name="view-compute-configuration"></a>查看计算配置

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“计算群集”节点 。
1. 右键单击要查看的计算，然后选择“查看计算属性”。

或者，在命令面板中使用 `> Azure ML: View Compute Properties` 命令。

### <a name="delete-compute-cluster"></a>删除计算群集

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“计算群集”节点 。
1. 右键单击要删除的计算，然后选择“删除计算”。

或者，在命令面板中使用 `> Azure ML: Remove Compute` 命令。

## <a name="inference-clusters"></a>推理群集

有关详细信息，请参阅[用于推理的计算目标](concept-compute-target.md#deploy)。

### <a name="manage-inference-clusters"></a>管理推理群集

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“推理群集”节点 。
1. 右键单击计算并选择以下选项之一：
    - “查看计算属性”。 显示有关附加的计算的只读配置数据。
    - “拆离计算”。 从工作区拆离计算。

或者，在命令面板中分别使用 `> Azure ML: View Compute Properties` 和 `> Azure ML: Detach Compute` 命令。

### <a name="delete-inference-clusters"></a>删除推理群集

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“附加的计算”节点 。
1. 右键单击要删除的计算，然后选择“删除计算”。

或者，在命令面板中使用 `> Azure ML: Remove Compute` 命令。

## <a name="attached-compute"></a>附加的计算

有关详细信息，请参阅[非托管计算](concept-compute-target.md#unmanaged-compute)。

### <a name="manage-attached-compute"></a>管理附加的计算

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 展开“计算”节点内的“附加的计算”节点 。
1. 右键单击计算并选择以下选项之一：
    - “查看计算属性”。 显示有关附加的计算的只读配置数据。
    - “拆离计算”。 从工作区拆离计算。

或者，在命令面板中分别使用 `> Azure ML: View Compute Properties` 和 `> Azure ML: Detach Compute` 命令。

## <a name="models"></a>模型

有关详细信息，请参阅[模型](concept-azure-machine-learning-architecture.md#models)

### <a name="create-model"></a>创建模型

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 右键单击工作区中的“模型”节点，然后选择“创建模型” 。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Model` 命令。

### <a name="view-model-properties"></a>查看模型属性

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“模型”节点。
1. 右键单击要查看其属性的模型，然后选择“查看模型属性”。 此时会在编辑器中打开一个文件，其中包含你的模型属性。

或者，在命令面板中使用 `> Azure ML: View Model Properties` 命令。

### <a name="download-model"></a>下载模型

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“模型”节点。
1. 右键单击要下载的模型，然后选择“下载模型文件”。

或者，在命令面板中使用 `> Azure ML: Download Model File` 命令。

### <a name="delete-a-model"></a>删除模型

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“模型”节点。
1. 右键单击要删除的模型，然后选择“删除模型”。
1. 此时会出现一个提示，要求你确认是否要删除该模型。 选择“确定”。

或者，在命令面板中使用 `> Azure ML: Remove Model` 命令。

## <a name="endpoints"></a>终结点

有关详细信息，请参阅[终结点](concept-azure-machine-learning-architecture.md#endpoints)。

### <a name="create-endpoint"></a>创建终结点

1. 展开包含你的工作区的订阅节点。
1. 展开你的工作区节点。
1. 右键单击工作区中的“模型”节点，然后选择“创建终结点” 。
1. 选择终结点类型。
1. 此时会显示规范文件。 配置规范文件。
1. 右键单击规范文件，然后选择“Azure ML: 创建资源”。

或者，在命令面板中使用 `> Azure ML: Create Endpoint` 命令。

### <a name="delete-endpoint"></a>删除终结点

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“终结点”节点。
1. 右键单击要删除的部署，然后选择“删除服务”。
1. 此时会出现一个提示，要求你确认是否要删除该服务。 选择“确定”。

或者，在命令面板中使用 `> Azure ML: Remove Service` 命令。

### <a name="view-service-properties"></a>查看服务属性

除了创建和删除部署之外，还可以查看和编辑与部署关联的设置。

1. 展开包含你的工作区的订阅节点。
1. 在工作区中展开“终结点”节点。
1. 右键单击要管理的部署：
    - 若要查看部署配置设置，请选择“查看服务属性”。

或者，在命令面板中使用 `> Azure ML: View Service Properties` 命令。

## <a name="next-steps"></a>后续步骤

使用 VS Code 扩展[训练图像分类模型](tutorial-train-deploy-image-classification-model-vscode.md)。