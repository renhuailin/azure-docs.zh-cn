---
title: 开源机器学习集成
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用开源 Python 机器学习框架定型、部署和管理端到端机器学习解决方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 12/16/2020
ms.openlocfilehash: b3fa9767310bfc300087c9dc7384f33993aca70d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675816"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>与 Azure 机器学习项目的开源集成

你可以通过使用开源 Python 机器学习库和平台，在 Azure 机器学习中训练、部署和管理端到端机器学习过程。  使用 Jupyter 笔记本和 Visual Studio Code 等开发工具来利用 Azure 机器学习中的现有模型和脚本。  

本文详细介绍了这些开源库和平台。

## <a name="train-open-source-machine-learning-models"></a>训练开源机器学习模型

机器学习培训过程涉及到数据的应用算法，以便实现任务或解决问题。 根据问题，你可以选择最适合任务和数据的不同算法。 有关机器学习的不同分支的详细信息，请参阅 [深度学习与机器学习文章](./concept-deep-learning-vs-machine-learning.md) 和 [机器学习算法](algorithm-cheat-sheet.md)备忘单。

### <a name="preserve-data-privacy-using-differential-privacy"></a>使用差分隐私保留数据隐私

若要训练机器学习模型，需要数据。 有时，数据是敏感的，请务必确保数据是安全的并且是专用的。 差分隐私是保留数据集中信息的机密性的一项技术。 若要了解详细信息，请参阅 [保留数据隐私](concept-differential-privacy.md)的文章。 

开源差分隐私工具包（如 [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) ）可帮助你在 Azure 机器学习解决方案中 [保留数据的隐私性](how-to-differential-privacy.md) 。

### <a name="classical-machine-learning-scikit-learn"></a>经典机器学习： scikit-learn-了解

对于涉及传统机器学习算法任务（例如分类、聚类分析和回归）的培训任务，你可以使用类似 Scikit-learn 的内容。 若要了解如何训练花卉分类模型，请参阅 [如何使用 scikit-learn 进行训练-了解文章](how-to-train-scikit-learn.md)。

### <a name="neural-networks-pytorch-tensorflow-keras"></a>神经网络： PyTorch、TensorFlow、Keras

开源机器学习算法称为神经网络，这是机器学习的一个子集，用于在 Azure 机器学习中训练深度学习模型。

开源深度学习框架和操作指南包括：

 *  [PyTorch](https://github.com/pytorch/pytorch)： [在 PyTorch 中使用传输学习训练深度学习图像分类模型](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow)： [使用 TensorFlow 识别手写数字](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras)： [使用 Keras 生成用于分析图像的神经网络](how-to-train-keras.md)

从头开始训练深度学习模型通常需要大量的时间、数据和计算资源。 您可以通过使用 "传输学习" 来快捷训练过程。 传输学习是一项技术，可应用从解决一个问题到不同但相关的问题而获得的知识。 这意味着，可以使用现有模型来重新使用它。 有关传输学习的详细信息，请参阅 [深入了解一文](concept-deep-learning-vs-machine-learning.md#transfer-learning) 。

### <a name="reinforcement-learning-ray-rllib"></a>强化学习： Ray RLLib

强化 learning 是一种使用操作、状态和回报培训模型的人工智能技术：强化 learning agent 了解如何使用一组预定义的操作，根据环境的当前状态最大化指定的奖励。 

[Ray RLLib](https://github.com/ray-project/ray)项目提供了一组功能，可实现整个培训过程中的高可伸缩性。 迭代过程既耗费时间又耗费资源，因为强化学习代理尝试学习实现任务的最佳方式。  Ray RLLib 本身还支持深度学习框架，如 TensorFlow 和 PyTorch。  

若要了解如何将 Ray RLLib 与 Azure 机器学习一起使用，请参阅 [如何定型强化学习模型](how-to-use-reinforcement-learning.md)。

### <a name="monitor-model-performance-tensorboard"></a>监视模型性能： TensorBoard

训练单个或多个模型需要可视化和检查所需的指标，以确保模型按预期方式执行。 可以 [在 Azure 机器学习中使用 TensorBoard 来跟踪和可视化试验指标](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>用于可解释和公平模型的框架

机器学习系统在银行、教育和医疗保健等不同领域使用。 因此，对于这些系统来说，需要对其做出的预测和建议负责以防止意外的后果，这一点很重要。

开源框架（如 [InterpretML](https://github.com/interpretml/interpret/) 和 Fairlearn） (https://github.com/fairlearn/fairlearn) 使用 Azure 机器学习来创建更加透明和相等的机器学习模型。

有关如何生成公平模型和可解释模型的详细信息，请参阅以下文章：

- [Azure 机器学习中的模型可解释性](how-to-machine-learning-interpretability.md)
- [解释和说明机器学习模型](how-to-machine-learning-interpretability-aml.md)
- [说明 AutoML 模型](how-to-machine-learning-interpretability-automl.md)
- [提高机器学习模型中的公平性](concept-fairness-ml.md)
- [使用 Azure 机器学习资产模型公平](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>模型部署

在对模型进行定型并准备好投入生产后，您必须选择部署模型的方式。 Azure 机器学习提供各种部署目标。 有关详细信息，请参阅 [部署项目的位置和方法](./how-to-deploy-and-where.md)。

### <a name="standardize-model-formats-with-onnx"></a>用 ONNX 标准化模型格式

训练后，模型的内容（如了解到的参数）会进行序列化并保存到文件中。 每个框架都有其自己的序列化格式。 当使用不同的框架和工具时，这意味着必须按照框架的要求部署模型。 若要标准化此过程，可以使用开放式神经网络 Exchange (ONNX) 格式。 ONNX 是适用于人工智能模型的开源格式。 ONNX 支持框架之间的互操作性。 这意味着你可在众多常见的机器学习框架（如 PyTorch）中训练模型，将其转换为 ONNX 格式，并在 ML.NET 等不同框架中使用 ONNX 模型。

有关 ONNX 以及如何使用 ONNX 模型的详细信息，请参阅以下文章：

- [通过 ONNX 创建和加速 ML 模型](concept-onnx.md)
- [在 .NET 应用程序中使用 ONNX 模型](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>将模型打包并部署为容器

容器技术（例如 Docker）是将模型部署为 web 服务的一种方法。 容器提供了平台和资源不可知的方式来构建和协调可重复的软件环境。 使用这些核心技术，可以使用 [预配置的环境](./how-to-use-environments.md)、 [预配置的容器映像](./how-to-deploy-custom-docker-image.md) 或自定义的环境将机器学习模型部署到 [Kubernetes 群集](./how-to-deploy-azure-kubernetes-service.md?tabs=python)。 对于 GPU 密集型工作流，可以使用 NVIDIA Triton 推理服务器等工具 [进行使用 gpu 的预测](how-to-deploy-with-triton.md?tabs=python)。

### <a name="secure-deployments-with-homomorphic-encryption"></a>用 homomorphic 加密保护部署

保护部署是部署过程中的一个重要部分。 若要 [部署加密的推断服务](how-to-homomorphic-encryption-seal.md)，请使用 `encrypted-inference` 开源 Python 库。 此 `encrypted inferencing` 包提供基于 [Microsoft 印章](https://github.com/Microsoft/SEAL)、homomorphic 加密库的绑定。

## <a name="machine-learning-operations-mlops"></a>机器学习操作 (MLOps) 

机器学习操作 (MLOps) ，通常会将其视为用于机器学习的 DevOps，这样就可以生成更透明、更具复原性且可重现的机器学习工作流。 有关 MLOps 的详细信息，请参阅 [什么是 MLOps 一文](./concept-model-management-and-deployment.md) 。 

使用 DevOps 做法（如持续集成 (CI) 和持续部署 (CD) ），可以自动执行端到端机器学习生命周期，并捕获其周围的管理数据。 可以 [在 GitHub 操作中定义机器学习 CI/CD 管道](./how-to-github-actions-machine-learning.md) ，以运行 Azure 机器学习培训和部署任务。 

捕获软件依赖关系、指标、元数据、数据和模型版本控制是 MLOps 过程的重要组成部分，用于构建透明、可重复且可审核的管道。 对于此任务，您可以 [在 Azure 机器学习中使用 MLFlow](how-to-use-mlflow.md) ，也可以在 [Azure Databricks 中培训机器学习模型](./how-to-use-mlflow-azure-databricks.md)。
