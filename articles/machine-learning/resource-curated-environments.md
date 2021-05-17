---
title: 特选环境
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习的特选环境，这是一组预先配置的环境，有助于减少试验和部署准备时间。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 4/2/2021
ms.openlocfilehash: 16d5cbc1d48b4559ac34314582b9c01b7c6bb58c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166642"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure 机器学习的特选环境

本文列出了 Azure 机器学习中的特选环境。 特选环境由 Azure 机器学习提供，且默认可用于你的工作区。 它们由使用最新版本的 Azure 机器学习 SDK 的缓存 Docker 映像提供支持，降低了运行准备成本，缩短了部署时间。 使用这些环境可以快速完成各种机器学习框架的入门。

> [!NOTE]
> 此列表于 2021 年 4 月进行了更新。 使用 Python SDK 或 CLI 获取环境及其依赖项的最新更新列表。 有关详细信息，请参阅[环境](./how-to-use-environments.md#use-a-curated-environment)一文。 在此新集发布后，以前的特选环境将隐藏，但仍可使用。 

## <a name="pytorch"></a>PyTorch
- AzureML-Pytorch1.7-Cuda11-OpenMpi4.1.0-py36
     - 使用 PyTorch 进行深度学习的环境，其中包含 Azure ML SDK 和其他 python 包。
     - PyTorch 版本：1.7
     - Python 版本：3.6.9
     - 基础映像：mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 版本：11.0.3
     - OpenMPI：4.1.0

## <a name="scikit"></a>Scikit
- AzureML-Scikit-learn0.20.4-Cuda11-OpenMpi4.1.0-py36
     - 使用 Scikit-learn 进行回归、群集和分类等任务的环境。 包含 Azure ML SDK 和其他 python 包。
     - Scikit-learn 版本：20.4
     - Python 版本：3.6.9
     - 基础映像：mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 版本：11.0.3
     - OpenMPI：4.1.0

## <a name="tensorflow"></a>TensorFlow
- AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36
     - 使用 Tensorflow 进行深度学习的环境，其中包含 Azure ML SDK 和其他 python 包。
     - Tensorflow 版本：2.4
     - Python 版本：3.6.9
     - 基础映像：mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA 版本：11.0.3
     - OpenMPI：4.1.0
