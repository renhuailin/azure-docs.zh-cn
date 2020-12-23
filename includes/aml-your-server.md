---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558577"
---
1. 按照 [Azure 机器学习 SDK](/python/api/overview/azure/ml/install?view=azure-ml-py) 中的说明安装适用于 Python 的 Azure 机器学习 SDK

1. 创建 [Azure 机器学习工作区](../articles/machine-learning/how-to-manage-workspace.md)。

1. 编写一个 [配置文件](../articles/machine-learning/how-to-configure-environment.md#workspace) 文件 (**aml_config/config.json**)。

1. 克隆 [GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 从克隆目录启动 notebook 服务器。

    ```bash
    jupyter notebook
    ```