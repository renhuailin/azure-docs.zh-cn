---
title: 构建并训练模型
titleSuffix: Azure Machine Learning
description: 如何使用 Azure 机器学习训练模型。 探索不同的训练方法，并为你的项目选择合适的训练方法。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 53b4c0250e4ceb034853ab588cfb2ef93a6ae005
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297787"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure 机器学习训练模型

Azure 机器学习提供多种方法来训练模型，从使用 SDK 的代码优先解决方案到自动化机器学习和视觉对象设计器等低代码解决方案。 使用以下列表来确定哪种训练方法适合你：

+ [适用于 Python 的 Azure 机器学习 SDK](#python-sdk)：Python SDK 提供多种方法来训练模型，每个模型都具有不同功能。

    | 训练方法 | 说明 |
    | ----- | ----- |
    | [运行配置](#run-configuration) | 用于训练模型的典型方法是使用训练脚本和运行配置。 运行配置提供了配置用于训练模型的训练环境所需的信息。 可以在运行配置中指定训练脚本、计算目标和 Azure ML 环境，并运行训练作业。 |
    | [自动化机器学习](#automated-machine-learning) | 可以通过自动化机器学习来训练模型，而无需大量数据科学或编程知识。 对于具有数据科学和编程背景的人员，它提供了一种方法，可通过自动化算法选择和超参数优化来节省时间和资源。 使用自动化机器学习时，无需担心定义运行配置。 |
    | [机器学习管道](#machine-learning-pipeline) | 管道不是一种不同的训练方法，而是一种使用模块化、可重复使用的步骤定义工作流的方法，该方法可以将训练作为工作流的一部分包含在内。 机器学习管道支持使用自动化机器学习和运行配置来训练模型。 由于管道并非专门针对训练，因此使用管道的原因比其他训练方法更多样。 通常，可以在以下情况下使用管道：<br>* 你需要计划无人参与的进程，如长时间运行的训练作业或数据准备。<br>* 使用多个步骤跨异类计算资源和存储位置进行协调。<br>* 将管道用作针对特定场景的可重复使用的模板，如重新训练或批处理评分。<br>* 对工作流的数据源、输入和输出进行跟踪和版本控制。<br>* 工作流由不同的团队实现，这些团队单独执行特定步骤。 然后，可以在管道中将步骤联接在一起以实现工作流。 |

+ **设计器**：Azure 机器学习设计器提供了一个简单的进入机器学习的入口点，用于构建概念证明或供缺乏编码经验的用户使用。 它允许你使用拖放基于 Web 的 UI 来训练模型。 可以使用 Python 代码作为设计的一部分，或在不编写任何代码的情况下训练模型。

+ Azure CLI：机器学习 CLI 提供用于执行 Azure 机器学习中的常见任务的命令，通常用于编写任务脚本和自动完成任务。 例如，创建训练脚本或管道后，即可使用 Azure CLI 按计划开始一个训练，或者在更新用于训练的数据文件后开始训练。 对于训练模型，它提供了提交训练作业的命令。 它可以使用运行配置或管道来提交作业。

其中每个训练方法均可使用不同类型的计算资源进行训练。 这些资源统称为[计算目标](concept-azure-machine-learning-architecture.md#compute-targets)。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。

## <a name="python-sdk"></a>Python SDK

可以将适用于 Python 的 Azure 机器学习 SDK 与 Azure 机器学习结合使用，来构建和运行机器学习工作流。 可以通过交互式式 Python 会话、Jupyter Notebook、Visual Studio Code 或其他 IDE 与服务进行交互。

* [什么是适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)
* [安装/更新 SDK](/python/api/overview/azure/ml/install)
* [配置 Azure 机器学习的开发环境](how-to-configure-environment.md)

### <a name="run-configuration"></a>运行配置

可以使用 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 定义 Azure 机器学习的常规训练作业。 然后使用脚本运行配置和训练脚本针对计算目标训练模型。

可以从本地计算机的运行配置开始，然后根据需要切换到基于云的计算目标。 更改计算目标时，只需更改所使用的运行配置。 运行还会记录有关训练作业的信息，例如输入、输出和日志。

* [什么是运行配置？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教程：训练第一个 ML 模型](tutorial-1st-experiment-sdk-train.md)
* [示例：Jupyter Notebook 和 Python 训练模型示例](https://github.com/Azure/azureml-examples)
* [如何：配置训练运行](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>自动化机器学习

定义迭代、超参数设置、特征化和其他设置。 在训练过程中，Azure 机器学习会并行尝试不同的算法和参数。 一旦训练达到你定义的退出条件就会停止。

> [!TIP]
> 除了 Python SDK 外，还可以通过 [Azure 机器学习工作室](https://ml.azure.com)使用自动化 ML。

* [什么是自动化机器学习？](concept-automated-ml.md)
* [教程：使用自动化机器学习创建第一个分类模型](tutorial-first-experiment-automated-ml.md)
* [教程：使用自动化机器学习预测出租车费](tutorial-auto-train-models.md)
* [示例：Jupyter Notebook 自动化机器学习示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [如何：使用 Python 配置自动化 ML 试验](how-to-configure-auto-train.md)
* [如何：自动训练时序预测模型](how-to-auto-train-forecast.md)
* [如何：通过 Azure 机器学习工作室创建、了解和部署自动化机器学习试验](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>机器学习管道

机器学习管道可以使用前面提到的训练方法。 管道更多的是关于创建工作流，因此它们包含的不仅仅是模型训练。 在管道中，你可以使用自动化机器学习或运行配置来训练模型。

* [什么是 Azure 机器学习 ML 管道？](concept-ml-pipelines.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](./how-to-create-machine-learning-pipelines.md)
* [教程：使用 Azure 机器学习管道进行批处理评分](tutorial-pipeline-batch-scoring-classification.md)
* [示例：Jupyter Notebook 机器学习管道示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [示例：使用自动化机器学习的管道](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>了解提交训练作业时会发生的情况

Azure 训练生命周期包括：

1. 将项目文件夹中的文件压缩，忽略那些在 .amlignore 或 .gitignore 中指定的文件 
1. 纵向扩展计算群集 
1. 构建 dockerfile 或将其下载到计算节点 
    1. 系统会计算以下各项的哈希： 
        - 基础映像 
        - 自定义 Docker 步骤（请参阅[使用自定义 Docker 基础映像部署模型](./how-to-deploy-custom-container.md)）
        - Conda 定义 YAML（请参阅[在 Azure 机器学习中创建和使用软件环境](./how-to-use-environments.md)）
    1. 在工作区 Azure 容器注册表 (ACR) 中查找时，系统使用此哈希作为键
    1. 如果找不到，它会在全局 ACR 中寻找匹配项
    1. 如果找不到，系统会生成新映像（该映像会被缓存并注册到工作区 ACR）
1. 将压缩的项目文件下载到计算节点上的临时存储
1. 解压缩项目文件
1. 执行 `python <entry script> <arguments>` 的计算节点
1. 将写入到 `./outputs` 的日志、模型文件和其他文件保存到与工作区关联的存储帐户
1. 纵向缩减计算，包括删除临时存储 

如果选择在本地计算机上进行训练（“配置为本地运行”），则无需使用 Docker。 可以在本地使用 Docker，前提是你选择这样做（有关示例，请参阅[配置 ML 管道](./how-to-debug-pipelines.md)部分）。

## <a name="azure-machine-learning-designer"></a>Azure 机器学习设计器

通过设计器，可以在 Web 浏览器中使用拖放式界面来训练模型。

+ [什么是设计器？](concept-designer.md)
+ [教程：预测汽车价格](tutorial-designer-automobile-price-train-score.md)

## <a name="azure-cli"></a>Azure CLI

机器学习 CLI 是 Azure CLI 的扩展。 它为使用 Azure 机器学习提供了跨平台 CLI 命令。 通常，可以使用 CLI 来自动执行任务，如训练机器学习模型。

* [将 CLI 扩展用于 Azure 机器学习](reference-azure-machine-learning-cli.md)
* [Azure 上的 MLOps](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

可以使用 VS Code 扩展运行和管理训练作业。 请参阅 [VS Code 资源管理操作指南](how-to-manage-resources-vscode.md#experiments)，以了解更多信息。

## <a name="next-steps"></a>后续步骤

了解如何[配置训练运行](how-to-set-up-training-targets.md)。