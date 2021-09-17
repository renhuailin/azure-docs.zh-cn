---
title: 什么是 Azure 机器学习工作室？
description: 该工作室是 Azure 机器学习工作区的 Web 门户。 此工作室将无代码和代码优先体验结合起来，打造包容的数据科学平台。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/23/2021
adobe-target: true
ms.openlocfilehash: f52c27bbcd0f289028f51c6fcb1fa22ae321f8b4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695765"
---
# <a name="what-is-azure-machine-learning-studio"></a>什么是 Azure 机器学习工作室？

本文将介绍 Azure 机器学习工作室，这是 [Azure 机器学习](overview-what-is-azure-machine-learning.md)中面向数据科学家开发人员的 Web 门户。 此工作室将无代码和代码优先体验结合起来，打造包容的数据科学平台。

本文内容：
>[!div class="checklist"]
> - 如何在工作室中[创作机器学习项目](#author-machine-learning-projects)。
> - 如何在工作室中[管理资产和资源](#manage-assets-and-resources)。
> - [Azure 机器学习工作室和机器学习工作室（经典）](#ml-studio-classic-vs-azure-machine-learning-studio)之间的差异。

建议使用与操作系统兼容的最新浏览器。 支持以下浏览器：
  * Microsoft Edge（最新版本）
  * Safari（最新版本，仅限 Mac）
  * Chrome（最新版本）
  * Firefox（最新版本）

## <a name="author-machine-learning-projects"></a>创作机器学习项目

该工作室提供多种创作体验，具体取决于类型项目和用户体验级别。

+ **Notebook**

  在直接集成到工作室中的托管 [Jupyter Notebook 服务器](how-to-run-jupyter-notebooks.md)中编写和运行自己的代码。 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="屏幕截图：在笔记本中编写和运行代码":::

+ **Azure 机器学习设计器**

  使用设计器可在不编写任何代码的情况下训练和部署机器学习模型。 拖放数据集和模块以创建 ML 管道。 尝试[设计器教程](tutorial-designer-automobile-price-train-score.md)。

    ![Azure 机器学习设计器示例](media/concept-designer/designer-drag-and-drop.gif)

+ **自动化机器学习 UI**

  了解如何通过易于使用的界面创建[自动化 ML 试验](tutorial-first-experiment-automated-ml.md)。 

  ![Azure 机器学习工作室导航窗格中的 AutoML](./media/overview-what-is-azure-ml-studio/azure-machine-learning-automated-ml-ui.jpg)

+ **数据标记**

    使用 [Azure 机器学习数据标签](how-to-create-labeling-projects.md)来高效地协调数据标签项目。

## <a name="manage-assets-and-resources"></a>管理资产和资源

直接在浏览器中管理机器学习资产。 在 SDK 和工作室之间的同一工作区中共享资产，以实现无缝体验。 使用工作室管理：

- 模型
- 数据集
- 数据存储
- 计算资源
- 笔记本
- 试验
- 运行日志
- 管道 
- 管道终结点

即使你是经验丰富的开发人员，工作室也可以简化你管理工作区资源的方式。

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>机器学习工作室（经典）与 Azure 机器学习工作室

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

ML 工作室（经典版）于 2015 年发布，是 Azure 中第一个拖放式机器学习模型生成器。 ML 工作室（经典）是一种仅提供视觉体验的独立服务。 工作室（经典）不与 Azure 机器学习进行互操作。

Azure 机器学习是一种独立的新式服务，可提供完整的数据科学平台。 它同时支持代码优先和低代码体验。

Azure 机器学习工作室是 Azure 机器学习中的 Web 门户，其中包含用于项目创作和资产管理的低代码和无代码选项。 

如果你是新用户，请选择 Azure 机器学习，而不是 ML 工作室（经典版）。 作为一个完整的 ML 平台，Azure 机器学习提供：

- 用于大规模训练的可缩放计算群集。
- 企业安全和治理。
- 可与常用的开源工具互操作。
- 端到端 MLOps。

### <a name="feature-comparison"></a>功能比较

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>疑难解答

* 工作室中缺少用户界面项可使用 Azure 基于角色的访问控制限制可通过 Azure 机器学习执行的操作。 这些限制可以防止用户界面项出现在 Azure 机器学习工作室中。 例如，如果分配了无法创建计算实例的角色，则创建计算实例的选项不会出现在工作室中。 有关详细信息，请参阅[管理用户和角色](how-to-assign-roles.md)。

## <a name="next-steps"></a>后续步骤

请访问[工作室](https://ml.azure.com)，或浏览以下教程中的不同创作选项：  

从[快速入门：Azure 机器学习入门](quickstart-create-resources.md)开始。  然后使用这些资源以你喜欢的方法创建你的第一个试验：

  + [运行“Hello world!”Python 脚本（第 1 部分，共 3 部分）](tutorial-1st-experiment-hello-world.md)
  + [使用 Jupyter 笔记本训练图像分类模型](tutorial-train-models-with-aml.md)
  + [使用自动化机器学习训练和部署模型](tutorial-first-experiment-automated-ml.md)  
  + [使用设计器训练和部署模型](tutorial-designer-automobile-price-train-score.md)
  + [在受保护的虚拟网络中使用工作室](how-to-enable-studio-virtual-network.md)