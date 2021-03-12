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
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511009"
---
1. 按照 [Azure 机器学习 SDK](/python/api/overview/azure/ml/install) 中的说明安装适用于 Python 的 Azure 机器学习 SDK

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