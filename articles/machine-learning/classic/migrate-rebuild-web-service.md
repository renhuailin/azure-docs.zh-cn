---
title: ML 工作室（经典）：迁移到 Azure 机器学习 - 重新生成 Web 服务
description: 在 Azure 机器学习中重新生成工作室（经典）Web 服务作为管道终结点
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: a00a348eafff7e6d49669de45c3f458905f33b70
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580145"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>在 Azure 机器学习中重新生成工作室（经典）Web 服务

本文介绍如何在 [Azure 机器学习](../index.yml)中重新生成工作室（经典）Web 服务作为终结点。

使用 Azure 机器学习管道终结点进行预测、重新训练模型或运行任何通用管道。 REST 终结点允许你在任何平台上运行管道。 

本文是 Azure 机器学习迁移系列的工作室（经典）的一部分。 有关迁移到 Azure 机器学习的详细信息，请参阅[迁移概述一文](migrate-overview.md)。

> [!NOTE]
> 本迁移系列重点介绍拖放设计器。 有关以编程方式部署模型的详细信息，请参阅[在 Azure 中部署机器学习模型](../how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 机器学习工作区。 [创建 Azure 机器学习工作区](../how-to-manage-workspace.md#create-a-workspace)。
- Azure 机器学习训练管道。 有关详细信息，请参阅[在 Azure 机器学习中重新生成工作室（经典）试验](migrate-rebuild-experiment.md)。

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>实时终结点与管道终结点

工作室（经典）Web 服务已被 Azure 机器学习中的终结点所取代。 使用下表来选择要使用的终结点类型：

|工作室（经典）Web 服务| Azure 机器学习替换
|---|---|
|请求/响应 Web 服务（实时预测）|实时终结点|
|批处理 Web 服务（批处理预测）|管道终结点|
|重新训练 Web 服务（重新训练）|管道终结点| 


## <a name="deploy-a-real-time-endpoint"></a>部署实时终结点

在工作室（经典）中，你使用请求/响应 Web 服务来部署实时预测模型。 在 Azure 机器学习中，你将使用实时终结点。

在 Azure 机器学习中，有多种部署模型的方法。 最简单的方法之一是使用设计器自动执行部署过程。 使用以下步骤将模型部署为实时终结点：

1. 至少运行一次已完成的训练管道。
1. 运行完成后，请在画布顶部，选择“创建推理管道” > “实时推理管道”。

    ![创建实时推理管道](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    设计器将训练管道转换为实时推理管道。 在工作室（经典）中也会发生类似的转换。

    在设计器中，转换步骤还会将[训练的模型注册到 Azure 机器学习工作区](../how-to-deploy-and-where.md#registermodel)。

1. 选择“提交”以运行实时推理管道，并验证它是否成功运行。

1. 验证推理管道后，选择“部署”。

1. 输入终结点的名称和计算类型。

    下表描述了设计器中的部署计算选项：

    | 计算目标 | 用途 | 说明 | 创建 |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes 服务 (AKS)](../how-to-deploy-azure-kubernetes-service.md) |实时推理|大规模的生产部署。 快速响应时间和服务自动缩放。| 用户创建。 有关详细信息，请参阅[创建计算目标](../how-to-create-attach-compute-studio.md#inference-clusters)。 |
    |[Azure 容器实例](../how-to-deploy-azure-container-instance.md)|测试或开发 | 需要小于 48 GB RAM 的基于 CPU 的小型工作负载。| 由 Azure 机器学习自动创建。

### <a name="test-the-real-time-endpoint"></a>测试实时终结点

部署完成后，可以看到更多详细信息并测试终结点：

1. 转到“终结点”选项卡。
1. 选择终结点。
1. 选择“测试”选项卡。
    
    ![屏幕截图，显示“终结点”选项卡和“测试终结点”按钮](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>发布管道终结点以进行批处理预测或重新训练

还可以使用训练管道来创建管道终结点，而不是实时终结点。 使用管道终结点来执行批处理预测或重新训练。

管道终结点将替换工作室（经典）的批处理执行终结点，并重新训练 Web 服务。

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>发布用于批处理预测的管道终结点

发布批处理预测终结点类似于实时终结点。

使用以下步骤发布用于批处理预测的管道终结点：

1. 至少运行一次已完成的训练管道。

1. 运行完成后，请在画布顶部，选择“创建推理管道” > “批处理推理管道”。

    ![显示训练管道上的“创建推理管道”按钮的屏幕截图](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    设计器将训练管道转换为批推理管道。 在工作室（经典）中也会发生类似的转换。

    在设计器中，此步骤还会将[训练的模型注册到 Azure 机器学习工作区](../how-to-deploy-and-where.md#registermodel)。

1. 选择“提交”以运行批处理推理管道，并验证它是否成功完成。

1. 验证推理管道后，选择“发布”。
 
1. 创建新的管道终结点或选择现有管道终结点。
    
    新管道终结点为管道创建新的 REST 终结点。 

    如果选择现有管道终结点，不会覆盖现有管道。 相反，Azure 机器学习在终结点对每个管道进行版本化。 可以指定要在 REST 调用中运行的版本。 如果 REST 调用未指定版本，还必须设置默认管道。


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>发布管道终结点以进行重新训练

若要发布管道终结点以进行重新训练，必须已有训练模型的管道草稿。 有关生成训练管道的详细信息，请参阅[重新生成工作室（经典）试验](migrate-rebuild-experiment.md)。

若要重用管道终结点以重新训练，必须为输入数据集创建管道参数。 这使你可以动态设置训练数据集，以便重新训练模型。

使用以下步骤发布重新训练的管道终结点：

1. 至少运行一次训练管道。
1. 运行完成后，选择数据集模块。
1. 在“模块详细信息”窗格中，选择“设置为管道参数”。
1. 提供一个说明性名称，如“InputDataset”。    

    ![屏幕截图，重点展示如何创建管道参数](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    这会为输入数据集创建管道参数。 当你调用管道终结点进行训练时，可以指定一个新的数据集来重新训练模型。

1. 选择“发布”。

    ![屏幕截图，突出显示训练管道上的“发布”按钮](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>从工作室调用管道终结点

创建批处理推理或重新训练管道终结点后，可以直接从浏览器调用终结点。

1. 转到“管道”选项卡，然后选择“管道终结点”。
1. 选择要运行的管道终结点。
1. 选择“提交”。

    选择“提交”后，可以指定任何管道参数。

## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure 机器学习中重新生成工作室（经典）Web 服务。 下一步是将 [Web 服务与客户端应用集成](migrate-rebuild-integrate-with-client-app.md)。


请参阅工作室（经典）迁移系列中的其他文章：

1. [迁移概述](migrate-overview.md)。
1. [迁移数据集](migrate-register-dataset.md)。
1. [重新生成工作室（经典）训练管道](migrate-rebuild-experiment.md)。
1. **重新生成工作室（经典）Web 服务**。
1. [将 Azure 机器学习 Web 服务与客户端应用集成](migrate-rebuild-integrate-with-client-app.md)。
1. [迁移执行 R 脚本](migrate-execute-r-script.md)。
