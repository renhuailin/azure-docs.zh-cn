---
title: 开源机器学习集成
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用开源 Python 机器学习框架来训练、部署和管理端到端机器学习解决方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 8ec9aaf2222924718ad21cf0654d138be22c2d19
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114293379"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>与 Azure 机器学习项目的开源集成

可以通过使用开源 Python 机器学习库和平台，在 Azure 机器学习中训练、部署和管理端到端机器学习过程。  通过 Jupyter Notebook 和 Visual Studio Code 等开发工具利用 Azure 机器学习中的现有模型和脚本。  

在本文中详细了解这些开源库和平台。

## <a name="train-open-source-machine-learning-models"></a>训练开源机器学习模型

机器学习训练过程涉及到将算法应用于数据，以便完成任务或解决问题。 你可以根据问题选择最适合任务和你的数据的不同算法。 有关机器学习的不同分支的详细信息，请参阅[深度学习与机器学习文章](./concept-deep-learning-vs-machine-learning.md)和[机器学习算法备忘单](algorithm-cheat-sheet.md)。

### <a name="preserve-data-privacy-using-differential-privacy"></a>使用差异隐私保留数据隐私

若要训练机器学习模型，你需要数据。 有时，该数据是敏感数据，必须确保数据的安全性和私密性。 差异隐私是维护数据集中信息的机密性的一项技术。 若要了解详细信息，请参阅有关[保留数据隐私](concept-differential-privacy.md)的文章。 

开源差异隐私工具包（例如 [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python)）帮助你在 Azure 机器学习解决方案中[保留数据隐私](how-to-differential-privacy.md)。

### <a name="classical-machine-learning-scikit-learn"></a>经典机器学习：Scikit-learn

对于涉及传统机器学习算法任务（例如分类、聚类分析和回归）的训练任务，你可以使用 Scikit-learn 之类的工具。 若要了解如何训练花卉分类模型，请参阅[如何使用 Scikit-learn 进行训练](how-to-train-scikit-learn.md)一文。

### <a name="neural-networks-pytorch-tensorflow-keras"></a>神经网络：PyTorch、TensorFlow、Keras

开源机器学习算法称为神经网络，这是机器学习的一个子集，用于在 Azure 机器学习中训练深度学习模型。

开源深度学习框架和操作指南包括：

 *  [PyTorch](https://github.com/pytorch/pytorch)：[使用迁移学习训练深度学习图像分类模型](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow)：[使用 TensorFlow 识别手写数字](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras)：[使用 Keras 构建用于分析图像的神经网络](how-to-train-keras.md)

从头开始训练深度学习模型通常需要大量的时间、数据和计算资源。 你可以通过使用迁移学习来加快训练过程。 迁移学习是一种将解决某个问题时获得的知识应用于虽然不同但却相关的问题的技术。 这意味着，可以使用现有模型来重新设置其用途。 有关迁移学习的详细信息，请参阅[深度学习与机器学习](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning)一文。

### <a name="reinforcement-learning-ray-rllib"></a>强化学习：Ray RLLib

强化学习是一种人工智能技术，它使用操作、状态和奖励来训练模型：强化学习代理会学习如何根据环境的当前状态，使用一组预定义操作来最大程度地提高指定的奖励。 

[Ray RLLib](https://github.com/ray-project/ray) 项目有一组可确保在整个训练过程中实现高可伸缩性的功能。 迭代过程既耗费时间又耗费资源，因为强化学习代理会尽力学习完成任务的最佳方式。  Ray RLLib 本身还支持深度学习框架，例如 TensorFlow 和 PyTorch。  

若要了解如何将 Ray RLLib 与 Azure 机器学习一起使用，请参阅[如何训练强化学习模型](how-to-use-reinforcement-learning.md)。

### <a name="monitor-model-performance-tensorboard"></a>监视模型性能：TensorBoard

训练单个或多个模型需要可视化和检查所需的指标，以确保模型按预期方式执行。 可以[使用 Azure 机器学习中的 TensorBoard 来跟踪和可视化试验指标](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>用于可解释的公平模型的框架

机器学习系统在银行、教育和医疗保健等不同社会领域使用。 因此，这些系统需要对其做出的预测和建议负责以防止意外的后果，这一点很重要。

将开源框架（例如 [InterpretML](https://github.com/interpretml/interpret/) 和 Fairlearn (https://github.com/fairlearn/fairlearn) ）与 Azure 机器学习配合使用可以创建更透明和公平的机器学习模型。

若要详细了解如何构建公平且可解释的模型，请参阅以下文章：

- [Azure 机器学习中的模型可解释性](how-to-machine-learning-interpretability.md)
- [解释和说明机器学习模型](how-to-machine-learning-interpretability-aml.md)
- [说明 AutoML 模型](how-to-machine-learning-interpretability-automl.md)
- [提高机器学习模型中的公平性](concept-fairness-ml.md)
- [使用 Azure 机器学习评估模型公平性](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>模型部署

在模型经过训练并可用于生产环境后，你必须选择如何部署模型。 Azure 机器学习提供了各种部署目标。 有关详细信息，请参阅有关[部署位置和方式](./how-to-deploy-and-where.md)的文章。

### <a name="standardize-model-formats-with-onnx"></a>使用 ONNX 标准化模型格式

在训练后，模型的内容（例如了解的参数）会进行序列化并保存到文件中。 每个框架都有其自己的序列化格式。 这意味着，当使用不同的框架和工具时，你必须按照框架的要求来部署模型。 若要标准化此过程，可以使用 Open Neural Network Exchange (ONNX) 格式。 ONNX 是适用于人工智能模型的开源格式。 ONNX 支持框架之间的互操作性。 这意味着你可在众多常见的机器学习框架（如 PyTorch）中训练模型，将其转换为 ONNX 格式，并在 ML.NET 等不同框架中使用 ONNX 模型。

若要详细了解 ONNX 以及如何使用 ONNX 模型，请参阅以下文章：

- [使用 ONNX 创建和加速 ML 模型](concept-onnx.md)
- [在 .NET 应用程序中使用 ONNX 模型](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>将模型打包并部署为容器

容器技术（例如 Docker）是将模型部署为 Web 服务的一种方法。 容器提供了与平台和资源无关的方式来构建和协调可重现的软件环境。 有了这些核心技术，你就可以使用[预配置的环境](./how-to-use-environments.md)、[预配置的容器映像](./how-to-deploy-custom-container.md)或自定义的将机器学习模型部署到目标，例如 [Kubernetes 群集](./how-to-deploy-azure-kubernetes-service.md?tabs=python)。 对于 GPU 密集型工作流，可以使用 NVIDIA Triton 推理服务器等工具来[使用 GPU 进行预测](how-to-deploy-with-triton.md?tabs=python)。

### <a name="secure-deployments-with-homomorphic-encryption"></a>使用同态加密来保护部署

保护部署是部署过程的一个重要部分。 若要[部署加密的推理服务](how-to-homomorphic-encryption-seal.md)，请使用 `encrypted-inference` 开源 Python 库。 `encrypted inferencing` 包提供了基于 [Microsoft SEAL](https://github.com/Microsoft/SEAL)（一个同态加密库）的绑定。

## <a name="machine-learning-operations-mlops"></a>机器学习运营 (MLOps)

机器学习运营 (MLOps) 通常被视为适用于机器学习的 DevOps，允许你构建透明性、可复原性和可重现性更高的机器学习工作流。 若要详细了解 MLOps，请参阅[什么是 MLOps](./concept-model-management-and-deployment.md) 一文。 

使用持续集成 (CI) 和持续部署 (CD) 等 DevOps 做法，你可以自动完成端到端机器学习生命周期，并捕获有关它的治理数据。 你可以[在 GitHub Actions 中定义机器学习 CI/CD 管道](./how-to-github-actions-machine-learning.md)，以运行 Azure 机器学习训练和部署任务。 

为了构建透明、可重现且可审核的管道，必须在 MLOps 过程中捕获软件依赖项、指标、元数据、数据并对模型进行版本控制。 对于此任务，你可以[在 Azure 机器学习中使用 MLflow](how-to-use-mlflow.md)，以及[在 Azure Databricks 中训练机器学习模型时使用 MLflow](./how-to-use-mlflow-azure-databricks.md)。 还可以[将 MLflow 模型部署为 Azure Web 服务](how-to-deploy-mlflow-models.md)。