---
title: 教程：训练和部署模型 - Azure IoT Edge 上的机器学习
description: 在本教程中，你将使用 Azure 机器学习训练一个机器学习模型，然后将该模型打包为可作为 Azure IoT Edge 模块部署的容器映像。
author: kgremban
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7dcc92244e858a475e6ef06ba08b14f0e433dc62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726388"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>教程：训练和部署 Azure 机器学习模型

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

在本文中，我们将执行以下任务：

* 使用 Azure 机器学习工作室来训练机器学习模型。
* 将训练的模型打包为容器映像。
* 将容器映像部署为 Azure IoT Edge 模块。

机器学习工作室是用于试验、训练和部署机器学习模型的基块。

本文中的步骤通常由数据科学家执行。

本教程部分将介绍以下操作：

> [!div class="checklist"]
> * 在 Azure 机器学习工作区中创建 Jupyter 笔记本以训练机器学习模型。
> * 容器化经过训练的机器学习模型。
> * 从容器化机器学习模型创建 IoT Edge 模块。

## <a name="prerequisites"></a>先决条件

本文是有关如何在 IoT Edge 上使用机器学习的系列教程中的一篇。 这一系列中的每篇文章都环环相扣，后一篇以前一篇为基础。 如果你是直接转到本文的，请参阅本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="set-up-azure-machine-learning"></a>设置 Azure 机器学习

我们将使用机器学习工作室来托管两个 Jupyter 笔记本和支持文件。 在此处，我们将创建并配置一个机器学习项目。 如果你未曾用过 Jupyter 或机器学习工作室，请参阅下面的两篇简介文档：

* **Jupyter Notebook**：[在 Visual Studio Code 中使用 Jupyter 笔记本](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure 机器学习**：[在 Jupyter 笔记本中开始使用 Azure 机器学习](../machine-learning/quickstart-create-resources.md)

> [!NOTE]
> 设置服务后，可以从任何计算机访问机器学习。 在设置期间应使用开发 VM，其中包含所需的全部文件。

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>安装 Azure 机器学习 Visual Studio Code 扩展

应在开发 VM 上的 Visual Studio Code 中安装此扩展。 如果在其他实例上运行，请根据[安装 Visual Studio Code 扩展](../machine-learning/how-to-setup-vs-code.md)中所述重新安装该扩展。

### <a name="create-an-azure-machine-learning-account"></a>创建 Azure 机器学习帐户

若要在 Azure 上预配资源并运行工作负载，请使用 Azure 帐户凭据登录。

1. 在 Visual Studio Code 中，从菜单栏选择“视图” > “命令面板”，打开命令面板 。

1. 在命令面板中输入命令 `Azure: Sign In`，以启动登录过程。 按照说明完成登录。

1. 创建机器学习计算实例以运行工作负载。 在命令面板中输入命令 `Azure ML: Create Compute`。
1. 选择 Azure 订阅。
1. 选择“+ 创建新的 Azure ML 工作区”，并输入名称 **turbofandemo**。
1. 选择一直用于本演示文档的资源组。
1. 在 Visual Studio Code 窗口的右下角，应该可以看到工作区创建进度：“正在创建工作区: turobofandemo”。 此步骤可能需要一两分钟。
1. 请等待工作区创建成功。 它应显示“Azure ML 工作区 turbofandemo 已创建”。

### <a name="upload-jupyter-notebook-files"></a>上传 Jupyter Notebook 文件

我们会将示例笔记本文件上传到新的机器学习工作区。

1. 转到 ml.azure.com 并登录。
1. 选择你的 Microsoft 目录、Azure 订阅和新建的机器学习工作区。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="展示如何选择 Azure 机器学习工作区的屏幕截图。" :::

1. 登录到机器学习工作区后，使用左侧的菜单中转到“笔记本”部分。
1. 选择“我的文件”选项卡。

1. 选择“上传”（向上箭头图标）。

1. 转到“C:\source\IoTEdgeAndMlSample\AzureNotebooks”。 选择列表中的所有文件，然后选择“打开”。

1. 选中“我信任这些文件的内容”复选框。

1. 选择“上传”以开始上传。 上传过程完成后，选择“完成”。

### <a name="jupyter-notebook-files"></a>Jupyter Notebook 文件

让我们查看已上传到机器学习工作区的文件。 本教程部分中的活动跨越两个 Notebook 文件，这些文件使用几个支持文件。

* **01-turbofan\_regression.ipynb**：此笔记本使用机器学习工作区来创建并运行机器学习试验。 概括而言，该 Notebook 执行以下步骤：

  1. 从 Azure 存储帐户下载设备装备生成的数据。
  1. 浏览并准备数据，然后使用数据训练分类器模型。
  1. 使用测试数据集 (Test\_FD003.txt) 通过试验评估模型。
  1. 将最佳的分类器模型发布到机器学习工作区。

* **02-turbofan\_deploy\_model.ipynb**：此笔记本使用在上一个笔记本中创建的模型来创建一个随时可部署到 IoT Edge 设备的容器映像。 该 Notebook 执行以下步骤：

  1. 创建模型的评分脚本。
  1. 使用机器学习工作区中保存的分类器模型生成容器映像。
  1. 将映像部署为 Azure 容器实例上的 Web 服务。
  1. 使用该 Web 服务来验证模型和映像是否按预期方式工作。 已验证的映像将部署到本教程的[创建并部署自定义 IoT Edge 模块](tutorial-machine-learning-edge-06-custom-modules.md)部分所述的 IoT Edge 设备中。

* **Test\_FD003.txt**：此文件包含我们在验证已训练的分类器时用作测试集的数据。 为方便演示，我们已选择为原始竞赛提供的测试数据作为测试集。
* **RUL\_FD003.txt**：此文件包含 Test\_FD003.txt 文件中每个设备的最后一个周期的剩余使用寿命 (RUL)。 有关数据的详细说明，请参阅 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 中的 readme.txt 和 Damage Propagation Modeling.pdf 文件。
* **Utils.py**：此文件包含一组用于处理数据的 Python 实用工具函数。 第一个 Notebook 包含函数的详细说明。
* **README.md**：此自述文件描述如何使用笔记本。

## <a name="run-the-jupyter-notebooks"></a>运行 Jupyter 笔记本

创建该工作区后，可以运行笔记本。

1. 在“我的文件”页中，选择“01-turbofan\_regression.ipynb” 。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="展示如何选择要运行的第一个笔记本的屏幕截图。":::

1. 如果该笔记本列为“不受信任”，请选择笔记本右上角的“不受信任”小组件。  出现对话框时，请选择“信任”。

1. 为获得最佳结果，请阅读每个单元的文档，并单独运行它。 在工具栏上选择“运行”  。 稍后，你会发现运行多个单元非常方便。 你可以忽略升级和弃用警告。

    运行某个单元时，方括号之间会显示一个星号 ([\*])。 该单元的操作完成后，星号将替换为数字，并可能显示相关的输出。 笔记本中的单元按顺序生成，每次只能运行一个单元。

    还可以使用“单元”菜单中的运行选项。 按 **Ctrl+Enter** 可运行单元，按 **Shift+Enter** 可运行某个单元并转到下一个单元。

    > [!TIP]
    > 为使单元操作结果一致，请避免在浏览器的多个标签页中运行同一个 Notebook。

1. 在“设置全局属性”指令后面的单元中，输入 Azure 订阅、设置和资源的值。 然后运行该单元。

    ![展示如何在笔记本中设置全局属性的屏幕截图。](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. 在“工作区详细信息”前面的单元中，在运行该单元后，查找指示你登录以进行身份验证的链接。

    ![展示登录以进行设备身份验证的提示的屏幕截图。](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    打开该链接并输入指定的代码。 此登录过程使用 Microsoft Azure 跨平台命令行接口对 Jupyter 笔记本访问 Azure 资源的行为进行身份验证。

    ![展示确认对设备上的应用程序进行身份验证的屏幕截图。](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. 在“浏览结果”  之前的单元中，复制运行 ID 中的值，并将其粘贴为“重建运行”  后面的单元中的运行 ID。

   ![展示如何在单元之间复制运行 ID 的屏幕截图。](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. 运行笔记本中的剩余单元。

1. 保存笔记本并返回到你的项目页。

1. 打开 **02-turbofan\_deploy\_model.ipynb** 并运行每个单元。 需要在“配置工作区”后面的单元中登录以进行身份验证。

1. 保存笔记本并返回到你的项目页。

### <a name="verify-success"></a>验证是否成功

若要验证 Notebook 是否已成功完成，请验证是否创建了几个项。

1. 在机器学习笔记本的“我的文件”选项卡上，选择“刷新”。 

1. 确认是否已创建以下文件。

    | 文件 | 说明 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | 用于创建机器学习工作区的配置文件。 |
    | ./aml_config/model_config.json | 在 Azure 上的 **turbofanDemo** 机器学习工作区中部署模型所需的配置文件。 |
    | myenv.yml| 提供有关已部署机器学习模型的依赖项的信息。|

1. 验证是否已创建以下 Azure 资源。 某些资源名称追加了随机字符。

    | Azure 资源 | 名称 |
    | --- | --- |
    | Azure 机器学习工作区 | turborfanDemo |
    | Azure 容器注册表 | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure 存储 | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>调试

可以在用于调试的 Notebook 中插入 Python 语句（例如 `print()` 命令）来显示值。 如果发现存在未定义的变量或对象，请运行首次声明或实例化它们的单元。

如果需要重做笔记本，可能需要删除以前创建的文件和 Azure 资源。

## <a name="clean-up-resources"></a>清理资源

本教程是一系列文章的一部分，其中每篇文章都基于前一篇文章中介绍的内容。 请等到完成最后一篇教程之后才清理任何资源。

## <a name="next-steps"></a>后续步骤

在本文中，我们已通过机器学习工作室中运行的两个 Jupyter 笔记本，使用涡扇设备的数据执行了以下操作：

- 训练 RUL 分类器。
- 将分类器保存为模型。
- 创建容器映像。
- 将映像部署为 Web 服务并对其进行测试。

请继续学习下一篇文章了解如何创建 IoT Edge 设备。

> [!div class="nextstepaction"]
> [配置 IoT Edge 设备](tutorial-machine-learning-edge-05-configure-edge-device.md)