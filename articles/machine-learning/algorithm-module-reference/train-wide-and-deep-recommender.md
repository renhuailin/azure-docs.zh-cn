---
title: 使用“训练 Wide & Deep 推荐器”模块
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的“训练 Wide & Deep 推荐器”模块来训练建议模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 6c7f4b221b1b9a1eee9a0d4d376bb6707d6b2869
ms.sourcegitcommit: 12f15775e64e7a10a5daebcc52154370f3e6fa0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2021
ms.locfileid: "108000847"
---
# <a name="train-wide--deep-recommender"></a>训练 Wide & Deep 推荐器
本文介绍如何使用 Azure 机器学习设计器中的“训练 Wide & Deep 推荐器”模块来训练建议模型。 本模块基于由 Google 提出的 Wide & Deep 学习。

“训练 Wide & Deep 推荐器”模块读取“用户-项-评分”三元组数据集，以及（可选）某些用户和项特征。 它返回训练后的 Wide & Deep 推荐器。  然后，可以通过[为 Wide and Deep 推荐器评分](score-wide-and-deep-recommender.md)模块使用训练后的模型来生成评分预测或建议。  

<!-- Currently, **Train Wide & Deep Recommender** module supports both single node and distributed training. -->

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>有关建议模型和 Wide & Deep 推荐器的详细信息  

建议系统的主要目标是向系统的用户推荐一个或多个项目。  项目示例可能为电影、餐馆、书籍或歌曲。 用户可以是具有项目首选项的人员、一组人员或其他实体。  

推荐器系统有两种主要方法。 

+ 第一种是“基于内容”方法，该方法同时使用用户和项目的特性。 可以按年龄和性别等属性来描述用户，可按作者和制造商等属性来描述项。 你可以在社交婚介网站上找到基于内容的建议系统的典型示例。 
+ 第二种方法是“协作筛选”，它仅使用用户和项的标识符，并从用户对项给出的评分的（稀疏）矩阵中获取有关这些实体的隐含信息。 我们可以通过某个用户已评级的项目以及对相同项目进行了评级的其他用户来了解该用户。  

Wide & Deep 推荐器将这些方法结合在一起，即结合使用协作筛选和基于内容的方法。 因此，它被视为“混合推荐器”。 

工作原理：如果用户对系统而言相对“较新”（系统尚未获取多少用户信息），可通过使用有关用户的特征信息来改进预测，从而解决众所周知的“冷启动”问题。 但是，一旦从特定用户收集了足够数量的评分，就可以根据特定评分而不是仅根据他们的特征对其进行完全个性化的预测。 因此，可从基于内容的建议平稳过渡到基于协同筛选的建议。 即使用户或项目特征不可用，Wide & Deep 推荐器仍会在协同筛选模式下运行。  

有关 Wide & Deep 推荐器及基础概率算法的更多详细信息，请参阅相关研究论文：[推荐器系统的 Wide & Deep 学习](https://arxiv.org/pdf/1606.07792.pdf)。  

## <a name="how-to-configure-train-wide--deep-recommender"></a>如何配置“训练 Wide & Deep 推荐器”  

+ [准备训练数据](#prepare-data)
+ [模型](#train-the-model)

### <a name="prepare-data"></a>准备数据

尝试使用该模块之前，请确保你的数据为建议模型的预期格式。 需要“用户-项-评分”三元组的训练数据集，也可以在单独的数据集中分别包含用户特征和项目特征（如果可用）。

#### <a name="required-dataset-of-user-item-ratings"></a>所需的用户-项-评分数据集

用于训练的输入数据必须包含正确格式的正确数据类型： 

+ 第一列必须包含用户标识符。
+ 第二列必须包含项目标识符。
+ 第三列包含用户-项目对的评级。 评级值必须是数字类型的。 

例如，一组典型的用户-项-评分可能如下所示：

|UserId|MovieId|Rating|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>用户功能数据集（可选）

“用户特征”的数据集必须包含用户的标识符，并使用用户-项-评分数据集第一列中提供的相同标识符。 其余列可以包含任意数量的用于描述用户的特征。  

例如，一组典型的用户功能可能如下所示： 

|UserId|Age|性别|兴趣|位置|
|------------|--------------|-----------------------|---------------|------------|
|1|25|男| 戏剧    |欧洲|
|223|40|女|浪漫|亚洲|

#### <a name="item-features-dataset-optional"></a>项目特征数据集（可选）

项目特征的数据集必须在其第一列中包含项标识符。 其余列可以包含任意数量的项目的描述性特征。  

例如，一组典型的项目特征可能如下所示：  

|MovieId|标题|源语言|流派|年龄|
|-------------|-------------|-------------------|-----------|---------------|
|68646|教父|英语|戏剧|1972|
|31381|飘|英语|历史记录|1939|

### <a name="train-the-model"></a>定型模型

1.  在设计器中将“训练 Wide & Deep 推荐器”模块添加到你的试验，并将其连接到训练数据集。  
  
2. 如果有一个单独的用户特征和/或项目特征的数据集，将它们连接到“训练 Wide and Deep 推荐器”模块。  
  
    - **用户特征数据集**：将描述用户的数据集连接到第二个输入。
    - **项目特征数据集**：将描述项的数据集连接到第三个输入。  
    
3.  **时期**：指示算法应处理整个训练数据的次数。 

    这个数字越高，训练就越充分；但是，训练会花费更多的时间，并可能导致过度拟合。

4. **批处理大小**：键入一个训练步骤中使用的训练示例数。 

     此超参数会影响训练速度。 批处理越大，时间成本时期越短，但可能会增加收敛时间。 如果批太大，不适合 GPU/CPU，可能会引发内存错误。

5.  **Wide 部分优化器**：选择一个优化器，对模型的 wide 部分应用梯度。

6.  **Wide 优化器学习速率**：输入 0.0 和 2.0 之间的数字，该数字定义 wide 部分优化器的学习速率。

    此超参数确定每个训练步骤的步骤大小，同时不断接近损失函数的最小值。 学习速过高可能导致学习跳升超过最小值，而学习率过小可能会导致收敛问题。

7.  交叉特征维度：通过输入所需的用户 ID 和项目 ID 特征来键入此维度。 

    默认情况下，Wide & Deep 推荐器将对用户 ID 和项目 ID 功能执行跨产品转换。 将根据此数字对交叉结果进行哈希处理，以确保维持该维度。

8.  **Deep 部分优化器**：选择一个优化器，对模型的 deep 部分应用梯度。

9.  **Deep 优化器学习速率**：输入介于 0.0 和 2.0 之间的数字，该数字定义 deep 部分优化器的学习速率。

10.  用户嵌套维度：键入整数以指定用户 ID 嵌套的维度。

     Wide & Deep 推荐器将为 Wide 部分和 Deep 部分创建共享的用户 ID 嵌套和项目 ID 嵌套。

11.  嵌套维度：键入整数以指定项目 ID 嵌套的维度。

12.  **分类特征嵌套维度**：输入整数以指定分类特征嵌套的维度。

     在 Wide & Deep 推荐器的 deep 组件中，会针对每个分类特征习得一个嵌套矢量。 这些嵌套矢量具有相同的维度。

13.  **隐藏单位**：键入 deep 组件的隐藏节点数。 每个层中的节点数用逗号分隔。 例如，可以通过类型“1000,500,100”指定 deep 组件有三个层，第一层到最后一层分别有 1000 个节点、500 个节点和 100 个节点。

14.  **激活函数**：选择一个应用于每个层的激活函数，默认值为 ReLU。

15.  **丢弃**：输入 0.0 和 1.0 之间的数字，以确定训练期间每个层中丢弃输出的概率。

     丢弃是一种可以防止神经网络过度拟合的正则化方法。 关于此值的一个常见决策是从 0.5 开始，对于许多网络和任务而言，这一值似乎都接近最优值。

16.  **批标准化**：选择此选项可在 deep 组件中的每个隐藏层之后使用批标准化。

     批标准化是应对网络训练中内部协变量偏移问题的一种技术。 一般来说，它可以帮助提高网络的速度、性能和稳定性。 

17.  运行管道。


<!-- ## Distributed training

In distributed training the workload to train a model is split up and shared among multiple mini processors, called worker nodes. These worker nodes work in parallel to speed up model training. Currently the designer support distributed training for **Train Wide & Deep Recommender** module.

### How to enable distributed training

To enable distributed training for **Train Wide & Deep Recommender** module, you can set in **Run settings** in the right pane of the module. Only **[AML Compute cluster](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-cluster?tabs=python)** is supported for distributed training.

1. Select the module and open the right panel. Expand the **Run settings** section.

    [![Screenshot showing how to set distributed training in run setting](./media/module/distributed-training-run-setting.png)](./media/module/distributed-training-run-setting.png#lightbox)

1. Make sure you have select AML compute for the compute target.

1. In **Resource layout** section, you need to set the following values:

    - **Node count**: Number of nodes in the compute target used for training. It should be **less than or equal to** the **Maximum number of nodes** your compute cluster. By default it is 1, which means single node job.

    - **Process count per node**: Number of processes triggered per node. It should be **less than or equal to** the **Processing Unit** of your compute. By default it is 1, which means single node job.

    You can check the **Maximum number of nodes** and **Processing Unit** of your compute by clicking the compute name into the compute detail page.

    [![Screenshot showing how to check compute cluster](./media/module/compute-cluster-node.png)](./media/module/compute-cluster-node.png#lightbox)

You can learn more about distributed training in Azure Machine Learning [here](https://docs.microsoft.com/azure/machine-learning/concept-distributed-training).


### Troubleshooting for distributed training

If you enable distributed training for this module, there will be driver logs for each process. `70_driver_log_0` is for master process. You can check driver logs for error details of each process under **Outputs+logs** tab in the right pane.

[![Screenshot showing driver log](./media/module/distributed-training-error-driver-log.png)](./media/module/distributed-training-error-driver-log.png#lightbox) 

If the module enabled distributed training fails without any `70_driver` logs, you can check `70_mpi_log` for error details.

The following example shows a common error that is **Process count per node** is larger than **Processing Unit** of the compute.

[![Screenshot showing mpi log](./media/module/distributed-training-error-mpi-log.png)](./media/module/distributed-training-error-mpi-log.png#lightbox)

## Results

After pipeline run is completed, to use the model for scoring, connect the [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md) to [Score Wide and Deep Recommender](score-wide-and-deep-recommender.md), to predict values for new input examples.
 -->

##  <a name="technical-notes"></a>技术说明

Wide & Deep 联合训练广义线性模型和深度神经网络，结合了记忆和泛华的优点。 Wide 组件接受一组原始特征和特征转换来记忆特征交互。 使用弱化特征工程时，deep 组件通过低维密集特征嵌套，泛化为前所未见的特征组合。 

在实现 Wide & Deep 推荐器时，模块使用默认模型结构。 Wide 组件将用户嵌套、项目嵌套以及用户 ID 和项目 ID 的跨产品转换作为输入使用。 对于模型的 Deep 部分，系统将为每个分类特征习得一个嵌套矢量。 这些矢量随后与其他数值特征矢量一起，被馈入深层前馈神经网络。 通过汇总 wide 部分和 deep 部分的最终输出对数几率，并将其作为预测（最终将其转入常见损失函数用于联合训练）来合并这两个部分。


## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
