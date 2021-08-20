---
title: 什么是 Azure 机器学习？
description: Azure 机器学习是一种集成数据科学解决方案，让数据科学家和 MLops 可以对 ML 应用程序进行云级建模和部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: ac46134e2e74704f57142e2fcb8018ad82d7fe67
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667231"
---
# <a name="what-is-azure-machine-learning"></a>什么是 Azure 机器学习？

在本文中，你将了解 Azure 机器学习，这是一种基于云的环境，你可以使用它来训练、部署、自动化、管理和跟踪 ML 模型。 

Azure 机器学习可用于任何类型的机器学习，从传统 ml 到深度学习、监督式和非监督式学习。 无论你是希望使用 SDK 编写 Python 或 R 代码，还是在[工作室](#build-ml-models-in-the-studio)中使用无代码/低代码选项，你都可以在 Azure 机器学习工作区中构建、训练和跟踪机器学习和深度学习模型。 

开始在本地计算机上训练，然后横向扩展到云。 

此服务还与常用的深度学习和强化学习开放源代码工具（如 PyTorch、TensorFlow、scikit-learn 和 Ray RLlib）进行互操作。 

> [!Tip]
> **免费试用！**  如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。 你将获得可用于 Azure 服务的赠送额度。 额度用完后，可以保留该帐户并继续使用[免费的 Azure 服务](https://azure.microsoft.com/free/)。 除非显式更改设置并要求付费，否则不会对信用卡收取任何费用。


## <a name="what-is-machine-learning"></a>什么是机器学习？

机器学习是一项数据科研技术，可以让计算机根据现有的数据来预测将来的行为、结果和趋势。 使用机器学习，计算机可以在不需显式编程的情况下进行学习。

机器学习的预测可让应用和设备变得更聪明。 例如，在网上购物时，机器学习可根据购买的产品帮助推荐其他产品。 或者，在刷信用卡时，机器学习可将这笔交易与交易数据库进行比较，帮助检测诈骗。 当吸尘器机器人打扫房间时，机器学习可帮助它确定作业是否已完成。

## <a name="machine-learning-tools-to-fit-each-task"></a>适用于每个任务的机器学习工具 

Azure 机器学习为其机器学习工作流提供了开发人员和数据科学家所需的所有工具，包括：
+ [Azure 机器学习设计器](tutorial-designer-automobile-price-train-score.md)：拖放模块以生成试验，然后在低代码环境中部署管道。

+ Jupyter 笔记本：使用我们的[示例笔记本](https://github.com/Azure/MachineLearningNotebooks)或创建自己的笔记本，以利用<a href="/python/api/overview/azure/ml/intro" target="_blank">适用于 Python 的 SDK</a> 示例进行机器学习。 

+ [用于 Visual Studio Code 的机器学习扩展（预览版）](how-to-set-up-vs-code-remote.md)提供了一个功能完备的开发环境，用于构建和管理机器学习项目。

+ [机器学习 CLI](reference-azure-machine-learning-cli.md) 是一个 Azure CLI 扩展，它提供从命令行管理 Azure 机器学习资源的命令。

+ [与开源框架集成](concept-open-source.md)，这些框架包括 PyTorch、TensorFlow 和 Scikit-learn，以及其他许多用于训练、部署和管理端到端机器学习过程的框架。

+ 与 Ray RLlib 互操作的[强化学习](how-to-use-reinforcement-learning.md)

甚至可以使用 [MLflow 跟踪指标并部署模型](how-to-use-mlflow.md)或使用 Kubeflow [生成端到端工作流管道](https://www.kubeflow.org/docs/azure/)。

## <a name="build-ml-models-with-the-python-sdk"></a>使用 Python SDK 生成 ML 模型

开始使用 Azure 机器学习 <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a> 在本地计算机上训练。 然后，横向扩展到云。 

借助许多可用的[计算目标](how-to-create-attach-compute-studio.md)（例如 Azure 机器学习计算和 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)）以及[高级超参数优化服务](how-to-tune-hyperparameters.md)，可以利用云的强大功能更快地生成更好的模型。

也可使用 SDK [自动完成模型训练和优化](tutorial-auto-train-models.md)。

## <a name="build-ml-models-in-the-studio"></a>在工作室中生成 ML 模型

[Azure 机器学习工作室](https://studio.azureml.net)是 Azure 机器学习中的 Web 门户，提供用于模型训练、部署和资产管理的低代码和无代码选项。 工作室与 Azure 机器学习 SDK 集成，以实现无缝体验。 有关详细信息，请参阅[什么是 Azure 机器学习工作室](overview-what-is-machine-learning-studio.md)。

+ **Azure 机器学习设计器**

  使用[设计器](concept-designer.md)可在不编写任何代码的情况下训练和部署机器学习模型。 尝试[设计器教程](tutorial-designer-automobile-price-train-score.md)以开始使用。 

  ![Azure 机器学习设计器的拖放界面的动画 gif](media/concept-designer/designer-drag-and-drop.gif)

+ **跟踪试验**

  了解如何在工作室中[跟踪和可视化数据科学试验](how-to-track-monitor-analyze-runs.md)。 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Azure 机器学习工作室中的运行详细信息":::


+ **更多...**

  在 [ml.azure.com](https://studio.azureml.net) 访问 Azure 机器学习工作室。


## <a name="mlops-deploy--lifecycle-management"></a>MLOps：部署和生命周期管理
有了正确的模型以后，即可轻松地将其用在 Web 服务中、IoT 设备上或 Power BI 中。 有关详细信息，请参阅有关[部署方式及位置](how-to-deploy-and-where.md)的文章。

然后，可以使用[适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/)、[Azure 机器学习工作室](https://ml.azure.com)或[机器学习 CLI](reference-azure-machine-learning-cli.md) 来管理已部署的模型。

可以使用这些模型[实时](how-to-consume-web-service.md)返回预测，或者在有大量数据的情况下[异步](./tutorial-pipeline-batch-scoring-classification.md)返回预测。

使用高级[机器学习管道](concept-ml-pipelines.md)，可以在每一步（从数据准备、模型训练和评估一直到部署）进行协作。 使用 Pipelines 可以：

* 自动完成云中的端到端机器学习过程
* 重用组件并仅在需要时重新运行步骤
* 在每个步骤中使用不同的计算资源
* 运行批量评分任务

如果要使用脚本自动执行机器学习工作流，[机器学习 CLI](reference-azure-machine-learning-cli.md) 提供了执行常见任务（如提交训练运行或部署模型）的命令行工具。

若要开始使用 Azure 机器学习，请参阅[后续步骤](#next-steps)。

## <a name="integration-with-other-services"></a>与其他服务集成

Azure 机器学习可与 Azure 平台上的其他服务配合使用，还能与诸如 Git 和 MLFlow 之类的开源工具集成。

+ 如 __Azure Kubernetes 服务__、__Azure 容器实例__、__Azure Databricks__、__Azure Data Lake Analytics__ 和 __Azure HDInsight__ 这样的计算目标。 有关计算目标的详细信息，请参阅[什么是计算目标？](concept-compute-target.md)。
+ __Azure 事件网格__。 有关详细信息，请参阅[使用 Azure 机器学习事件](./how-to-use-event-grid.md)。
+ __Azure Monitor__。 有关详细信息，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。
+ 如 __Azure 存储帐户__、__Azure Data Lake Storage__、__Azure SQL 数据库__、__Azure Database for PostgreSQL__ 和 __Azure 开放数据集__ 这样的数据存储。 有关详细信息，请参阅[访问 Azure 存储服务中的数据](how-to-access-data.md)和[使用 Azure 开放数据集创建数据集](how-to-create-register-datasets.md)。
+ __Azure 虚拟网络__。 有关详细信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。
+ __Azure Pipelines__。 有关详细信息，请参阅[定型和部署机器学习模型](/azure/devops/pipelines/targets/azure-machine-learning)。
+ __Git 存储库日志__。 有关详细信息，请参阅 [Git 集成](concept-train-model-git-integration.md)。
+ __MLFlow__。 有关详细信息，请参阅[通过 MLflow 跟踪指标](how-to-use-mlflow.md)和[将 Mlflow 模型部署为 Web 服务](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__。 有关详细信息，请参阅[构建端到端工作流管道](https://www.kubeflow.org/docs/azure/)。

### <a name="secure-communications"></a>安全通信

Azure 存储帐户、计算目标和其他资源可在虚拟网络内安全地用于定型模型并执行推理。 有关详细信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。

## <a name="next-steps"></a>后续步骤

开始使用[快速入门：Azure 机器学习入门](quickstart-create-resources.md)。  然后使用这些资源以你喜欢的方法创建你的第一个试验：

  + [运行“Hello world!”Python 脚本（第 1 部分，共 3 部分）](tutorial-1st-experiment-hello-world.md)
  + [使用 Jupyter 笔记本训练图像分类模型](tutorial-train-models-with-aml.md)
  + [使用自动化机器学习训练和部署 ML 模型](tutorial-first-experiment-automated-ml.md) 
  + [管理 Visual Studio Code 中的资源](how-to-manage-resources-vscode.md)
  + [使用 Visual Studio Code 训练和部署图像分类模型](tutorial-train-deploy-image-classification-model-vscode.md)
  + [使用设计器的拖放功能进行训练和部署](tutorial-designer-automobile-price-train-score.md) 
  + [使用机器学习 CLI 来训练模型](how-to-train-cli.md)

- 了解[机器学习管道](concept-ml-pipelines.md)，以便生成、优化和管理机器学习方案。

- 阅读深入的 [Azure 机器学习体系结构和概念](concept-azure-machine-learning-architecture.md)文章。
