---
title: 训练 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器中的“训练 PyTorch 模型”模块从头开始训练模型或微调现有模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 7524b0fd496a87a281e5b9cd9f8ffcc8b54d6388
ms.sourcegitcommit: 12f15775e64e7a10a5daebcc52154370f3e6fa0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2021
ms.locfileid: "108000888"
---
# <a name="train-pytorch-model"></a>训练 PyTorch 模型

本文介绍了如何使用 Azure 机器学习设计器中的“训练 Pytorch 模型”模块来训练 DenseNet 等 PyTorch 模型。 训练在你定义模型并设置其参数后进行，并且需要带标签的数据。 

目前，“训练 PyTorch 模型”模块支持单节点和分布式训练。

## <a name="how-to-use-train-pytorch-model"></a>如何使用“训练 PyTorch 模型” 

1. 在设计器中将 [DenseNet](densenet.md) 模块或 [ResNet](resnet.md) 添加到管道草稿。

2. 将“训练 PyTorch 模型”模块添加到管道。 可以在“模型训练”类别下找到此模块。 展开“训练”，然后将“训练 PyTorch 模型”模块拖到你的管道中。

   > [!NOTE]
   > 对于大型数据集，最好是在“GPU”类型的计算上运行“训练 PyTorch 模型”模块，否则管道会出现故障 。 通过设置“使用其他计算目标”，可以在特定模块的右窗格中为该模块选择计算。

3.  在左侧输入中，附加未训练的模型。 将训练数据集和验证数据集附加到“训练 PyTorch 模型”的中间和右侧输入中。

    对于未经训练的模型，其必须为与 DenseNet 类似的 PyTorch 模型；否则将引发“InvalidModelDirectoryError”错误。

    对于数据集，训练数据集必须是已标记的图像目录。 请参阅“转换为图像目录”以了解如何获取标记的图像目录。 如果未标记，将引发“NotLabeledDatasetError”。

    训练数据集和验证数据集具有相同的标签类别，否则将引发 InvalidDatasetError。

4.  对于“时期”，指定要训练的时期数。 每个时期将循环访问整个数据集，默认循环访问 5 次。

5.  对于“批大小”，指定在一个批次中训练的实例数，默认为 16 个。

6.  对于“学习率预热步数”，应指定要预热训练的回合数，以防该值默认为 0 时，初始学习率略微太大而无法开始收敛。

7.  对于“学习率”，应指定学习速率值，其中默认值为 0.001。 学习率可控制每次在 sgd 等优化器中对模型进行测试和纠正时使用的步长。

    设置的学习率越小，模型测试就越频繁，而且可能会陷入局部停滞。 设置的学习率越大，收敛速度就越快，而且有可能会超过实际最小值。

    > [!NOTE]
    > 如果训练损失在训练期间变为 Nan，则可能是因学习率太大所致，此时降低学习率或许会有所帮助。
    > 在分布式训练中，为了保持梯度稳定下降，实际学习率将由 `lr * torch.distributed.get_world_size()` 计算，因为进程组的批尺寸为单一进程的进程总数。
    > 应用多项式学习率衰减可帮助构建一个性能更好的模型。

8.  对于“随机种子”，可以选择键入一个整数值，将其用作种子。 如果需要跨运行确保试验的可再现性，建议使用种子。

9.  对于“耐性”，请指定在验证损失不连续减少时提前停止训练的时期数。 默认为 3。

10. 对于“打印频率”，应指定每个训练回合中迭代的训练日志打印频率，默认值为 10。

11. 提交管道。 如果数据集较大，这将需要一段时间，建议使用 GPU 计算。

## <a name="distributed-training"></a>分布式训练

在分布式训练中，用于训练模型的工作负载会在多个微型处理器之间进行拆分和共享，这些处理器称为工作器节点。 这些工作器节点并行工作以加速模型训练。 目前，设计器支持适用于训练 PyTorch 模型模块的分布式培训。

### <a name="training-time"></a>定型时间

借助分布式训练，你可以通过训练 PyTorch 模型在 ImageNet（1,000 个类别、1,200,000 张图像）等大型数据集上进行训练。 下表显示了使用不同设备在 ImageNet 上从头开始对 Resnet50 进行 50 个回合训练的训练时间及表现。

| 设备       | 训练时间  | 训练吞吐量  | Top-1 验证准确率 | Top-5 验证准确率 |
| ------------- | -------------- | -------------------- | ------------------------- | ------------------------- |
| 16 V100 GPU  | 6 小时 22 分钟        | 大约 3,200 张图像/秒     | 68.83%                    | 88.84%                    | 
| 8 V100 GPU   | 12 小时 21 分钟       | 大约 1,670 张图像/秒     | 68.84%                    | 88.74%                    |

单击此模块的“指标”选项卡，然后查看训练指标图，如“每秒训练图”和“Top 1 准确率”。

[![显示训练指标的屏幕截图](./media/module/train-pytorch-model-train-metrics.png)](././media/module/train-pytorch-model-train-metrics.png#lightbox)

### <a name="how-to-enable-distributed-training"></a>如何启用分布式训练

若要为训练 PyTorch 模型模块启用分布式训练，可以在模块右侧窗格中的“运行设置”中进行设置。 分布式训练只支持 **[AML 计算群集](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-cluster?tabs=python)** 。

> [!NOTE]
> 激活分布式训练需要多个 GPU，因为使用 NCCL 后端训练 PyTorch 模块时需要 cuda。

1. 选择模块并打开右侧窗格。 展开“运行设置”部分。

    [![显示如何在“运行设置”中设置分布式训练的屏幕截图](./media/module/distributed-training-run-setting.png)](./media/module/distributed-training-run-setting.png#lightbox)

2. 请确保已为计算目标选择 AML 计算。

3. 在“资源布局”部分，需要设置以下值：

    - 节点计数：计算目标中用于训练的节点数。 该值应小于或等于计算群集的最大节点数。 默认值为 1，代表单节点作业。

    - 每个节点的进程计数：每个节点触发的进程数。 该值应小于或等于计算的处理单位。 默认值为 1，代表单进程作业。

    通过单击计算名称进入“计算详情”页，便可检查计算的“最大节点数”和“处理单位”。

    [![显示如何检查计算群集的屏幕截图](./media/module/compute-cluster-node.png)](./media/module/compute-cluster-node.png#lightbox)

[此处](https://docs.microsoft.com/azure/machine-learning/concept-distributed-training) 可详细了解 Azure 机器学习分布式训练的相关信息。

### <a name="troubleshooting-for-distributed-training"></a>分布式训练故障排除

如果为此模块启用了分布式训练，则每个进程都会生成驱动程序日志。 `70_driver_log_0` 适用于主进程。 在右侧窗格的“输出 + 日志”选项卡下，即可查看驱动程序日志中每个进程的详细错误信息。

[![显示驱动程序日志的屏幕截图](./media/module/distributed-training-error-driver-log.png)](./media/module/distributed-training-error-driver-log.png#lightbox) 

如果启用分布式训练的模块发生故障且未生成任何 `70_driver` 日志，则可以查看 `70_mpi_log` 了解详细错误信息。

下方示例显示了一则常见错误，即每个节点的进程计数大于计算的处理单位 。

[![显示 mpi 日志的屏幕截图](./media/module/distributed-training-error-mpi-log.png)](./media/module/distributed-training-error-mpi-log.png#lightbox)

有关模块故障排除的详细信息，请参阅[本文](designer-error-codes.md)。

## <a name="results"></a>结果

管道运行完成后，若要使用模型进行评分，请将[训练 PyTorch 模型](train-PyTorch-model.md)连接到[为图像模型评分](score-image-model.md)，以预测新输入示例的值。

## <a name="technical-notes"></a>技术说明
###  <a name="expected-inputs"></a>预期输入  

| 名称               | 类型                    | 说明                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 未训练的模型    | UntrainedModelDirectory | 未经训练的模型，需要 PyTorch         |
| 训练数据集   | ImageDirectory          | 训练数据集                         |
| 验证数据集 | ImageDirectory          | 每个时期用于评估的验证数据集 |

###  <a name="module-parameters"></a>模块参数  

| 名称          | 范围            | 类型    | 默认 | 说明                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| 时期        | >0               | Integer | 5       | 选择包含标签的列或结果列 |
| 批大小    | >0               | Integer | 16      | 要在一个批次中训练的实例数   |
| 学习率预热步数 | >=0         | Integer | 0       | 预热训练的回合数 |
| 学习速率 | >=double.Epsilon | Float   | 0.1   | “随机梯度下降”优化器的初始学习速率。 |
| 随机种子   | 任意              | Integer | 1       | 模型使用的随机数生成器的种子。 |
| 耐性      | >0               | Integer | 3       | 要提前停止训练的时期数   |
| 打印频率 |             >0 | Integer | 10      | 每个回合中迭代的训练日志打印频率 |

###  <a name="outputs"></a>Outputs  

| 名称          | 类型           | 说明   |
| ------------- | -------------- | ------------- |
| 已训练模型 | ModelDirectory | 已训练模型 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 



