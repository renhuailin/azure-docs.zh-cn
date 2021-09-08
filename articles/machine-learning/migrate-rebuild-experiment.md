---
title: ML 工作室（经典）：迁移到 Azure 机器学习 - 重新生成试验
description: Azure 机器学习设计器中重新生成工作室（经典）试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 9d39047d995d6b387b8cc73ef53c7d584e8d41b4
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123474460"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>在 Azure 机器学习中重新生成工作室（经典）试验

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

本文介绍如何在 Azure 机器学习中重新生成 ML 工作室（经典）试验。 有关从工作室（经典）迁移的详细信息，请参阅[迁移概述一文](migrate-overview.md)。

工作室（经典）试验与 Azure 机器学习中的管道类似。 但是，在 Azure 机器学习中，管道建立在支持 SDK 的相同后端上。 这意味着，可以使用两个选项来进行机器学习开发：拖放设计器或代码优先 SDK。

有关通过 SDK 生成管道的详细信息，请参阅[什么是 Azure 机器学习管道](concept-ml-pipelines.md#building-pipelines-with-the-python-sdk)。


## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 机器学习工作区。 [创建 Azure 机器学习工作区](how-to-manage-workspace.md#create-a-workspace)。
- 要迁移的工作室（经典）试验。
- 将[数据集上传](migrate-register-dataset.md)到 Azure 机器学习。

## <a name="rebuild-the-pipeline"></a>重新生成管道

将[数据集迁移到 Azure 机器学习](migrate-register-dataset.md)后，便可以重新创建试验。

在 Azure 机器学习中，可视关系图称为“管道草稿”。 在本部分中，将在管道草稿中重新创建经典试验。

1. 转到 Azure 机器学习工作室 ([ml.azure.com](https://ml.azure.com))
1. 在左侧导航窗格中，选择“设计器”>“易用的预生成模块” ![屏幕截图，显示如何创建新的管道草稿。](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. 使用设计器模块手动重新生成试验。
    
    请参阅[模块映射表](migrate-overview.md#studio-classic-and-designer-module-mapping)，查找替换模块。 许多工作室（经典）的最受欢迎模块在设计器中具有相同版本。

    > [!Important]
    > 如果试验使用“执行 R 脚本”模块，则需要执行其他步骤来迁移试验。 有关详细信息，请参阅[迁移 R 脚本模块](migrate-execute-r-script.md)。

1. 调整参数。
    
    选择每个模块，并在右侧的“模块设置”面板中调整参数。 使用参数重新创建工作室（经典）试验的功能。 有关每个模块的更多信息，请参阅[模块引用](/azure/machine-learning/algorithm-module-reference/module-reference)。

## <a name="submit-a-run-and-check-results"></a>提交运行并检查结果

重新创建工作室（经典）试验后，就可以提交“管道运行”了。

管道运行在附加到工作区的“计算目标”上执行。 可以为整个管道设置默认计算目标，也可以基于每个模块指定计算目标。

从管道草稿提交运行后，它将变为“管道运行”。 每个管道运行都记录在 Azure 机器学习中。

设置整个管道的默认计算目标：
1. 选择管道名称旁边的齿轮图标 ![设计器中的齿轮图标](./media/tutorial-designer-automobile-price-train-score/gear-icon.png)。
1. 选择“选择计算目标”。
1. 选择现有计算，或按照屏幕上的说明创建新的计算。

完成计算目标设置后，便可以提交管道运行了：

1. 在画布顶部选择“提交”。
1. 选择“新建”以创建新试验。
    
    试验将相似的管道运行组合在一起。 如果多次运行管道，则可以选择相同的试验进行连续运行。 这对于日志记录和跟踪非常有用。
1. 输入试验名称。 然后，选择“提交”。

    第一次运行可能需要 20 分钟。 由于默认计算设置中的最小节点大小为 0，因此设计器必须在空闲后分配资源。 连续运行所需的时间更少，因为已经分配了节点。 为了提高运行速度，可以创建最小节点大小为 1 或更大的计算资源。

运行完成后，可以检查每个模块的结果：

1. 右键单击要查看其输出的模块。
1. 选择“可视化”、“查看输出”或“查看日志”。

    - 可视化：预览结果数据集。
    - 查看输出：打开指向输出存储位置的链接。 可借此浏览或下载输出。 
    - 查看日志：查看驱动程序和系统日志。 使用 70_driver_log 查看与用户提交的脚本相关的信息（如错误和异常）。

> [!IMPORTANT]
> 设计器模块使用开源 Python 包，而不是工作室（经典）中的 C# 包。 因此，在设计器和工作室（经典）中，模块输出可能略有不同。 


## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure 机器学习中重新生成工作室（经典）试验。 下一步是[在 Azure 机器学习中重新生成 Web 服务](migrate-rebuild-web-service.md)。


请参阅工作室（经典）迁移系列中的其他文章：

1. [迁移概述](migrate-overview.md)。
1. [迁移数据集](migrate-register-dataset.md)。
1. **重新生成工作室（经典）训练管道**。
1. [重新生成工作室（经典）Web 服务](migrate-rebuild-web-service.md)。
1. [将 Azure 机器学习 Web 服务与客户端应用集成](migrate-rebuild-integrate-with-client-app.md)。
1. [迁移执行 R 脚本](migrate-execute-r-script.md)。
