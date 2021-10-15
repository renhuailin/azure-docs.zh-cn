---
title: 云中的安全数据访问
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure 机器学习数据存储和数据集安全地连接到 Azure 上的数据存储。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 1b439f65f2807d2d076363938169631651b85d20
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430148"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure 机器学习中的安全数据访问

Azure 机器学习使你可以轻松连接到云中的数据。 它在基础存储服务上提供抽象层，让你可以安全地访问和使用数据，而无需编写特定于存储类型的代码。 Azure 机器学习还提供了以下数据功能：

*    与 Pandas 和 Spark DataFrames 的互操作性
*    数据世系的版本控制和跟踪
*    数据标记 
*    数据偏差监视
    
## <a name="data-workflow"></a>数据工作流

准备好在基于云的存储解决方案中使用数据时，建议使用以下数据发送工作流。 此工作流假设你拥有 [Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)，并在 Azure 中的基于云的存储服务中拥有数据。 

1. 创建 [Azure 机器学习数据存储](#datastores)以将连接信息存储到 Azure 存储。

2. 在该数据存储中，创建一个 [Azure 机器学习数据集](#datasets)以指向基础存储中的特定文件。 

3. 若要在机器学习试验中使用该数据集，可采用以下方法
    1. 将该数据集装载到试验的计算目标以进行模型训练。

        **或者** 

    1. 在自动化机器学习（自动化 ML）试验运行、机器学习管道或 [Azure 机器学习设计器](concept-designer.md)等 Azure 机器学习解决方案中直接使用该数据集。

4. 针对模型输出数据集创建[数据集监视器](#drift)以检测数据偏移。 

5. 如果检测到数据偏移，请更新输入数据集，并相应地重新训练模型。

下图提供了此建议的工作流的直观演示。

![该图显示了流入数据存储和数据集的 Azure 存储服务。 数据集流入模型训练，模型训练流入数据漂移，而数据漂移又流回数据集。](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>通过数据存储连接到存储

Azure 机器学习数据存储将连接信息安全地存储在 Azure 上的数据存储中，因此无需在脚本中对其进行编码。 [注册并创建一个数据存储](how-to-access-data.md)即可轻松连接到存储帐户，并访问底层存储服务中的数据。 

Azure 中支持的基于云的存储服务，可注册为数据存储：

+ Azure Blob 容器
+ Azure 文件共享
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL 数据库
+ Azure Database for PostgreSQL
+ Databricks 文件系统
+ Azure Database for MySQL

>[!TIP]
> 如果要使用已推出的用于创建数据存储的功能，需要进行基于凭据的身份验证才能访问存储服务，如服务主体或共享访问签名 (SAS) 令牌。 对工作区具有“读者”访问权限的用户可以使用这些凭据。 <br><br>如果在这一点上存在问题，可[创建一个使用基于标识的数据访问方式来访问存储服务的数据存储（预览版）](how-to-identity-based-data-access.md)。 此功能是一个[试验性](/python/api/overview/azure/ml/#stable-vs-experimental)预览功能，可能会随时更改。

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>通过数据集引用存储中的数据

Azure 机器学习数据集不是你的数据的副本。 创建数据集时，将会创建对其存储服务中的数据的引用及其元数据的副本。 

由于数据集是延迟计算的，并且数据仍保留在其现有位置，因此

* 不会产生额外的存储成本。
* 不会无意中更改原始数据源。
* 会提高 ML 工作流性能速度。

若要与存储中的数据进行交互，请[创建一个数据集](how-to-create-register-datasets.md)以将数据打包成机器学习任务可用的对象。 将数据集注册到工作区可在不同的试验中共享和重用该数据集，而不会造成数据引入复杂性。

可以从本地文件、公共 URL、[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)或经由数据存储的 Azure 存储服务创建数据集。 

有两种类型的数据集： 

+ [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) 引用数据存储或公共 URL 中的单个或多个文件。 如果数据已被清理并且可用于训练试验，则可以[下载或装载 FileDataset 引用的文件](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)到计算目标。

+ [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) 通过分析提供的文件或文件列表来以表格格式表示数据。 可以将 TabularDataset 加载到 Pandas 或 Spark DataFrame 中，以便进一步处理和清理。 有关可从中创建 TabularDataset 的数据格式的完整列表，请参阅 [TabularDatasetFactory 类](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory)。

可在以下文档中找到更多数据集功能：

+ 对数据集世系进行[版本控制和跟踪](how-to-version-track-datasets.md)。
+ [监视数据集](how-to-monitor-datasets.md)以帮助检测数据偏移。    

## <a name="work-with-your-data"></a>使用数据

使用数据集可以通过与 Azure 机器学习功能的无缝集成来完成许多机器学习任务。 

+ 创建[数据标记项目](#label)。
+ 训练机器学习模型：
     + [自动化 ML 试验](how-to-use-automated-ml-for-ml-models.md)
     + [设计器](tutorial-designer-automobile-price-train-score.md#import-data)
     + [笔记本](how-to-train-with-datasets.md)
     + [Azure 机器学习管道](./how-to-create-machine-learning-pipelines.md)
+ 访问数据集，以使用[机器学习管道](./how-to-create-machine-learning-pipelines.md)中的[批量推理](./tutorial-pipeline-batch-scoring-classification.md)进行评分。
+ 设置数据集监视器以检测[数据偏移](#drift)。

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>使用数据标记项目标记数据

在机器学习项目中，标记大量数据通常是一件很麻烦的事情。 这些包含计算机视觉组件（如图像分类或对象检测）的项目通常需要数千个图像和对应的标签。

Azure 机器学习提供了一个中心位置，用于创建、管理和监视标签项目。 标记项目有助于协调数据、标签和团队成员，使你能够更有效地管理标记任务。 当前支持的任务包括图像分类（多标签或多类）以及使用边界框的对象标识。

创建[图像标记项目](how-to-create-image-labeling-projects.md)或[文本标记项目](how-to-create-text-labeling-projects.md)，并输出数据集以用于机器学习试验。

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>利用数据偏移监视模型性能

在机器学习的上下文中，数据偏移是指模型输入数据中导致模型性能下降的变化。 这是模型准确度在一段时间后下降的最主要原因之一，因此，监视数据偏移有助于检测模型性能问题。

请参阅[创建数据集监视器](how-to-monitor-datasets.md)一文来详细了解如何检测数据集中新数据的数据偏移并发出警报。

## <a name="next-steps"></a>后续步骤 

+ 若要在 Azure 机器学习工作室或 Python SDK 中创建数据集，请[使用这些步骤](how-to-create-register-datasets.md)。
+ 阅读[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)中的数据集训练示例。