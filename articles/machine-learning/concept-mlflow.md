---
title: MLflow 和 Azure 机器学习
titleSuffix: Azure Machine Learning
description: 了解如何使用 MLflow 和 Azure 机器学习记录 ML 模型的指标和项目，并将 ML 模型部署为 Web 服务。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 05/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a4b9810764a0a2d8b421d17e546cd39e93cec582
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113034630"
---
# <a name="mlflow-and-azure-machine-learning"></a>MLflow 和 Azure 机器学习

[MLflow](https://www.mlflow.org) 是一个开放源代码库，用于管理机器学习试验的生命周期。  MLflow 的跟踪 URI 和日志记录 API 统称为 [MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)，是 MLflow 的一个组件，可用于记录和跟踪训练运行指标及模型项目，无论试验环境是在本地计算机上、远程计算目标上、虚拟机上，还是在 Azure Databricks 群集上。 

将 MLflow 跟踪和 Azure 机器学习配合使用时，可以跟踪试验的运行指标，并将模型项目存储在 Azure 机器学习工作区中。 该试验可以在本地计算机上、远程计算目标上、虚拟机上或 [Azure Databricks 群集](how-to-use-mlflow-azure-databricks.md)上运行。 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比较 MLflow 和 Azure 机器学习客户端

 下表汇总了可以使用 Azure 机器学习的不同客户端，以及它们各自的功能。

 MLflow 跟踪提供指标记录和项目存储功能，这些功能仅通过 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 提供。

| 功能 | MLflow 跟踪和部署 | Azure 机器学习 Python SDK |  Azure 机器学习 CLI | Azure 机器学习工作室|
|---|---|---|---|---|
| 管理工作区 |   | ✓ | ✓ | ✓ |
| 使用数据存储  |   | ✓ | ✓ | |
| 记录指标      | ✓ | ✓ |   | |
| 上传项目 | ✓ | ✓ |   | |
| 查看指标     | ✓ | ✓ | ✓ | ✓ |
| 管理计算   |   | ✓ | ✓ | ✓ |
| 部署模型    | ✓ | ✓ | ✓ | ✓ |
|监视模型性能||✓|  |   |
| 检测数据偏差 |   | ✓ |   | ✓ |


## <a name="track-experiments"></a>跟踪试验

借助 MLflow 跟踪，可以连接 Azure 机器学习作为 MLflow 试验的后端。 然后，可以执行以下任务：

+ 在 [Azure 机器学习工作区](./concept-azure-machine-learning-architecture.md#workspace)中跟踪和记录试验指标及项目。 如果已为试验使用 MLflow 跟踪，工作区可提供集中、安全和可缩放的位置，用于存储训练指标和模型。 

+ 在 MLflow 和 Azure 机器学习模型注册表中跟踪和管理模型。

+ [跟踪 Azure Databricks 训练运行](how-to-use-mlflow-azure-databricks.md)。

有关详细信息，请参阅[使用 MLflow 和 Azure 机器学习跟踪 ML 模型](how-to-use-mlflow.md)。 

## <a name="train-mlflow-projects"></a>训练 MLflow 项目

可以使用 MLflow 的跟踪 URI 和日志记录 API（统称为 MLflow 跟踪），通过 [MLflow 项目](https://www.mlflow.org/docs/latest/projects.html)和 Azure 机器学习后端支持（预览版）提交训练作业。 你可以使用 Azure 机器学习跟踪在本地提交作业，也可以像通过 [Azure 机器学习计算](./how-to-create-attach-compute-cluster.md)那样将运行迁移到云中。

有关详细信息，请参阅[使用 MLflow 项目和 Azure 机器学习训练 ML 模型](how-to-train-mlflow-projects.md)。


## <a name="deploy-mlflow-experiments"></a>部署 MLflow 试验

可以将 [MLflow 模型部署为 Azure Web 服务](how-to-deploy-mlflow-models.md)，以便你可以利用 Azure 机器学习模型管理和数据偏移检测功能，并将它们应用于生产模型。

## <a name="next-steps"></a>后续步骤
* [使用 MLflow 和 Azure 机器学习跟踪 ML 模型](how-to-use-mlflow.md)。 
* [使用 MLflow 项目和 Azure 机器学习训练 ML 模型](how-to-train-mlflow-projects.md)。
* [使用 MLflow 跟踪 Azure Databricks 运行](how-to-use-mlflow-azure-databricks.md)。
* [使用 MLflow 部署模型](how-to-deploy-mlflow-models.md)。


