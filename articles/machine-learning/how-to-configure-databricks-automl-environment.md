---
title: 用 autoML & 进行开发 Azure Databricks
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习和 Azure Databricks 中设置开发环境。 使用适用于 Databricks 的 Azure ML Sdk 和 autoML 的 Databricks。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423980"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>使用 Azure 机器学习 Azure Databricks 和 autoML 设置开发环境 

了解如何在使用 Azure Databricks 和自动 ML 的 Azure 机器学习中配置开发环境。

Azure Databricks 非常适合在 Azure 云中的可缩放 Apache Spark 平台上运行大规模密集型机器学习工作流。 它通过基于 CPU 或 GPU 的计算群集提供基于笔记本的协作环境。

有关其他机器学习开发环境的信息，请参阅 [设置 Python 开发环境](how-to-configure-environment.md)。


## <a name="prerequisite"></a>先决条件

Azure 机器学习工作区。 如果没有，可以通过 [Azure 门户](how-to-manage-workspace.md)、 [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)和 [Azure 资源管理器模板](how-to-create-workspace-template.md)创建 Azure 机器学习工作区。


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks 与 Azure 机器学习和 autoML

Azure Databricks 与 Azure 机器学习及其 autoML 功能集成。 

您可以使用 Azure Databricks：

+ 使用 Spark MLlib 为模型定型并将模型部署到 ACI/AKS。
+ 使用 Azure ML SDK 的 [自动化机器学习](concept-automated-ml.md) 功能。
+ 作为 [Azure 机器学习管道](concept-ml-pipelines.md)的计算目标。

## <a name="set-up-a-databricks-cluster"></a>设置 Databricks 群集

创建 [Databricks 群集](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。 仅当在 Databricks 上安装适用于自动化机器学习的 SDK 时，某些设置才适用。

**创建群集需要几分钟时间。**

使用以下设置：

| 设置 |适用于| 值 |
|----|---|---|
| 群集名称 |通用| yourclustername |
| Databricks 运行时版本 |通用| 运行时 7.1 (scala 2.21，spark 3.0.0) -不是 ML|
| Python 版本 |通用| 3 |
| 辅助角色类型 <br>（确定最大并发迭代数） |自动化机器学习<br>（仅限）| 首选内存优化的 VM |
| 工作节点 |通用| 2 个或以上 |
| 启用自动缩放 |自动化机器学习<br>（仅限）| 取消选中 |

请等待群集运行完成，然后继续操作。

## <a name="add-the-azure-ml-sdk-to-databricks"></a>将 Azure ML SDK 添加到 Databricks

群集运行后，请[创建一个库](https://docs.databricks.com/user-guide/libraries.html#create-a-library)用于将相应的 Azure 机器学习 SDK 包附加到群集。 

若要使用自动 ML，请跳到 [添加 AZURE ML SDK With AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks)。


1. 右键单击用于存储该库的当前工作区文件夹。 选择“创建” > “库”。 
    
    > [!TIP]
    > 如果你使用的是旧版本的 SDK，请从群集的已安装库中取消选中它，并移动到垃圾桶。 安装新的 SDK 版本并重启群集。 如果重启后出现问题，请分离并重新附加群集。

1. 选择以下选项 (不支持其他 SDK 安装) 

   |SDK 包附加项|Source|PyPi 名称|
   |----|---|---|
   |对于 Databricks| 上传 Python Egg 或 PyPI | azureml-sdk[databricks]|

   > [!WARNING]
   > 无法安装其他 SDK 附加项。 仅选择 [ `databricks` ] 选项。

   * 不要选择“自动附加到所有群集”。
   * 选择群集名称旁边的“附加”。

1. 监视错误，直到状态更改为“已附加”。这可能需要几分钟时间。  如果此步骤失败：

   请尝试通过以下操作重启群集：
   1. 在左窗格中，选择“群集”。
   1. 在表中选择你的群集名称。
   1. 在“库”选项卡上，选择“重启”。 

   安装成功后如下所示： 

  ![适用于 Databricks 的 Azure 机器学习 SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>将包含 AutoML 的 Azure ML SDK 添加到 Databricks
如果创建群集时所用的 Databricks Runtime 7.1 或更高 ( *不* 是 ML) ，请在笔记本的第一个单元格中运行以下命令，以安装 AML SDK。

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
对于 Databricks Runtime 7.0 和更低版本，请使用 [init 脚本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md)安装 Azure 机器学习 SDK。

### <a name="automl-config-settings"></a>AutoML 配置设置

在 AutoML 配置中使用 Azure Databricks 时添加以下参数：

- ```max_concurrent_iterations``` 基于群集中的工作器节点数。
- ```spark_context=sc``` 基于默认的 Spark 上下文。

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>使用 Azure Databricks 的 ML 笔记本

尝试以下操作：
+ 尽管有许多示例笔记本可用，但 **只有 [这些示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)适用于 Azure Databricks。**

+ 直接从工作区导入这些示例。 请参见下图：![选择“导入”](./media/how-to-configure-environment/azure-db-screenshot.png)
![导入面板](./media/how-to-configure-environment/azure-db-import.png)

+ 了解如何[创建包含 Databricks 的管道用作训练计算](how-to-create-your-first-pipeline.md)。

## <a name="next-steps"></a>后续步骤

- 使用 MNIST 数据集对 Azure 机器学习上的[模型定型](tutorial-train-models-with-aml.md)。
- 请参阅 [用于 Python 参考的 AZURE 机器学习 SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)。
