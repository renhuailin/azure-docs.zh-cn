---
title: 教程：设计器 - 部署无代码模型
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器部署机器学习模型来预测汽车价格。
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/11/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: a9fe604673ee53aa6772e6f382a4a3e3a53fb903
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115004"
---
# <a name="tutorial-designer---deploy-a-machine-learning-model"></a>教程：设计器 - 部署机器学习模型

使用设计器部署机器学习模型来预测汽车的价格。 本教程是由两个部分构成的系列教程的第二部分。


在[本教程的第一部分](tutorial-designer-automobile-price-train-score.md)，你训练了有关汽车价格的线性回归模型。 在第二部分，你将部署该模型供其他人使用。 在本教程中，你将了解：

> [!div class="checklist"]
> * 创建实时推理管道。
> * 创建推理群集。
> * 部署实时终结点。
> * 测试实时终结点。

## <a name="prerequisites"></a>先决条件

完成[教程的第一部分](tutorial-designer-automobile-price-train-score.md)，了解如何在设计器中训练机器学习模型并为其评分。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>创建实时推理管道

若要部署管道，必须先将训练管道转换为实时推理管道。 此过程会删除训练模块，并添加 Web 服务输入和输出以处理请求。

### <a name="create-a-real-time-inference-pipeline"></a>创建实时推理管道

1. 在管道画布上方，选择“创建推理管道” > “实时推理管道” 。

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="显示“创建管道”按钮位置的屏幕截图":::

    管道现在应如下所示： 

   ![显示做好部署准备后管道的预期配置的屏幕截图](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    选择“创建推理管道”时，会发生一些事情：
    
    * 训练的模型在模块调色板中存储为“数据集”模块。 可以在“我的数据集”下找到它。
    * 将删除“训练模型”和“拆分数据”等训练模块。 
    * 保存的训练模型已添加回管道中。
    * 已添加“Web 服务输入”和“Web 服务输出”模块。 这些模块显示用户数据进入管道的位置，以及返回数据的位置。

    > [!NOTE]
    > 默认情况下，“Web 服务输入”会要求数据架构与同它连接到同一下游端口的模块输出数据的数据架构相同。 在此示例中，“Web 服务输入”和“汽车价格数据(原始)”连接到同一下游模块，因此，“Web 服务输入”需要与“汽车价格数据(原始)”相同的数据架构，并且架构中包括目标变量列 `price`。   
    > 但是，通常情况下，为数据评分时，你不知道目标变量值。 对于这种情况，你可以使用“选择数据集中的列”模块删除推理管道中的目标变量列。 确保“选择数据集中的列”的输出（在删除目标变量列后）连接到与“Web 服务输入”模块的输出相同的端口。

1. 选择“提交”，并使用在第一部分中使用的相同计算目标和试验。

    如果这是第一次运行，则管道可能需要长达 20 分钟的时间才能完成运行。 默认计算设置中的最小节点大小为 0，这意味着设计器必须在空闲后分配资源。 由于计算资源已分配，因此，重复的管道运行花费的时间会更少。 此外，设计器还对每个模块使用缓存的结果，以便进一步提高效率。

1. 选择“部署”。

## <a name="create-an-inferencing-cluster"></a>创建推理群集

在显示的对话框中，可以从任何现有的 Azure Kubernetes 服务 (AKS) 群集中进行选择，以便部署模型。 如果没有 AKS 群集，可通过以下步骤创建一个。

1. 在显示的对话框中选择“计算”，转到“计算”页。 

1. 在导航功能区中，选择“推理群集” > “+ 新建” 。

    ![显示如何转到新的推理群集窗格的屏幕截图](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. 在推理群集窗格中，配置新的 Kubernetes 服务。

1. 输入“aks-compute”作为“计算名称”。
    
1. 对于“区域”，选择可用的邻近区域。

1. 选择“创建”。

    > [!NOTE]
    > 创建新的 AKS 服务大约需要 15 分钟。 可在“推理群集”页上查看预配状态。
    >

## <a name="deploy-the-real-time-endpoint"></a>部署实时终结点

在 AKS 服务完成预配以后，请返回到实时推理管道，以便完成部署。

1. 选择画布上面的“部署”。

1. 选择“部署新的实时终结点”。 

1. 选择已创建的 AKS 群集。

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="显示如何设置新的实时终结点的屏幕截图":::

    还可以更改实时终结点的“高级”设置。
    
    |高级设置|说明|
    |---|---|
    |启用 Application Insights 诊断和数据收集| 是否启用 Azure Application Insights 从部署的终结点收集数据。 </br> 默认值：false |
    |评分超时| 对 Web 服务的评分调用强制执行的超时值（以毫秒为单位）。</br>默认情况下：60000|
    |已启用自动缩放|   是否为 Web 服务启用自动缩放。</br>默认值：true|
    |副本数下限| 自动缩放此 Web 服务时可使用的容器的最小数目。</br>默认情况下：1|
    |副本数上限| 自动缩放此 Web 服务时可使用的容器的最大数目。</br> 默认情况下：10|
    |目标利用率|自动缩放程序应尝试维持的此 Web 服务的目标利用率（以低于 100 的百分比表示）。</br> 默认情况下：70|
    |刷新周期|自动缩放程序尝试缩放此 Web 服务的频率（以秒为单位）。</br> 默认情况下：1|
    |CPU 保留容量|要分配给此 Web 服务的 CPU 核心数。</br> 默认情况下：0.1|
    |内存保留容量|为此 Web 服务分配的内存量 (GB)。</br> 默认情况下：0.5|
        

1. 选择“部署”。 

    部署完成后，将在画布上方显示成功通知。 这可能需要几分钟时间。

> [!TIP]
> 如果在“实时终结点设置”框中为“计算类型”选择了“Azure 容器实例”，则还可以部署到“Azure 容器实例”(ACI)  。
> Azure 容器实例用于测试或开发。 对于需要小于 48 GB RAM 的基于 CPU 的低缩放工作负载，请使用 ACI。

## <a name="test-the-real-time-endpoint"></a>测试实时终结点

部署完成后，可通过转到“终结点”页来查看实时终结点。

1. 在“终结点”页上，选择已部署的终结点。

    在“详细信息”选项卡中，可以查看更多信息，例如 REST URI、Swagger 定义、状态和标记。

    在“使用”选项卡中，可以找到使用代码示例和安全密钥，以及设置身份验证方法。

    在“部署日志”选项卡中，你可以找到实时终结点的详细部署日志。

1. 若要测试终结点，请转到“测试”选项卡。可在此输入测试数据并选择“测试”以验证终结点的输出。

有关使用 Web 服务的详细信息，请参阅[使用部署为 Web 服务的模型](how-to-consume-web-service.md)

## <a name="limitations"></a>限制

### <a name="update-inference-pipeline"></a>更新推理管道

如果在训练管道中进行了一些修改，则应重新提交训练管道，更新推理管道并再次运行推理管道。

请注意，在推理管道中将只更新经过训练的模型，而不会更新数据转换。

若要在推理管道中使用更新的转换，需要将转换模块的转换输出注册为数据集。

![显示如何注册转换数据集的屏幕截图](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

然后，手动将推理管道中的 TD 模块替换为已注册的数据集。

![显示如何替换转换模块的屏幕截图](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

然后，可以提交具有已更新的模型和转换的推理管道，并部署。

### <a name="deploy-real-time-endpoint"></a>部署实时终结点

由于数据存储访问限制，如果你的推理管道包含“导入数据”或“导出数据”模块，它们将在部署到实时终结点时被自动删除 。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何在设计器中创建、部署和使用机器学习模型的重要步骤。 如果要详细了解如何使用设计器，请参阅以下链接：

+ [设计器示例](samples-designer.md)：了解如何使用设计器解决其他类型的问题。
+ [在 Azure 虚拟网络中使用 Azure 机器学习工作室](how-to-enable-studio-virtual-network.md)。
