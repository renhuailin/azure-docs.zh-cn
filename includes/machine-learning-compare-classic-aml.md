---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: d6d73754a22a3a0870a6ea141e4a21e7f2ccff09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688766"
---
下表总结了机器学习工作室（经典）和 Azure 机器学习之间的一些重要差异。

| 功能 | 机器学习工作室（经典版） | Azure 机器学习 |
|---| --- | --- |
| 拖放界面 | 经典体验 | 更新的体验 - [Azure 机器学习设计器](../articles/machine-learning/concept-designer.md)| 
| 代码 SDK | 不支持 | 与 [Azure 机器学习 Python](/python/api/overview/azure/ml/) 和 [R](https://github.com/Azure/azureml-sdk-for-r) SDK 完全集成 |
| 试验 | 可缩放（10 GB 训练数据限制） | 使用计算目标进行缩放 |
| 训练计算目标 | 专用计算目标，仅限 CPU 支持 | 各种可自定义的[训练计算目标](../articles/machine-learning/concept-compute-target.md#train)。 包括 GPU 和 CPU 支持 | 
| 部署计算目标 | 专用 Web 服务格式，不可自定义 | 各种可自定义的[部署计算目标](../articles/machine-learning/concept-compute-target.md#deploy)。 包括 GPU 和 CPU 支持 |
| ML 管道 | 不支持 | 生成灵活的模块化[管道](../articles/machine-learning/concept-ml-pipelines.md)，用于自动完成工作流 |
| MLOps | 基本模型管理和部署；仅 CPU 部署 | 实体版本控制（模型、数据、工作流）、工作流自动化、与 CICD 工具集成、CPU 和 GPU 部署，[等等](../articles/machine-learning/concept-model-management-and-deployment.md) |
| 模型格式 | 专用格式，仅限工作室（经典） | 多个受支持的格式，具体取决于训练作业类型 |
| 自动化模型训练和超参数优化 |  不支持 | [受支持](../articles/machine-learning/concept-automated-ml.md)。 代码优先和无代码选项。 | 
| 数据偏移检测 | 不支持 | [支持](../articles/machine-learning/how-to-monitor-datasets.md) |
| 数据标签项目 | 不支持 | [支持](../articles/machine-learning/how-to-create-labeling-projects.md) |
| 基于角色的访问控制 (RBAC) | 仅“参与者”和“所有者”角色 | [灵活的角色定义和 RBAC 控制](../articles/machine-learning/how-to-assign-roles.md) |
| AI 库 | 支持 ([https://gallery.azure.ai/](https://gallery.azure.ai/)) | 不支持 <br><br> 通过[示例 Python SDK 笔记本](https://github.com/Azure/MachineLearningNotebooks)进行了解。 |