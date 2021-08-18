---
title: 用于 Azure Databricks ML 试验的 MLflow 跟踪
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设置 MLflow，以记录 Azure Databricks ML 试验中的指标和项目。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 62fd9820d1ec743d7424679753828b0cb7837fbc
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031042"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning"></a>使用 MLflow 和 Azure 机器学习跟踪 Azure Databricks ML 试验

本文介绍如何启用 MLflow 的跟踪 URI 和记录 API（统称为 [MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)），以连接 Azure Databricks (ADB) 试验、MLflow 和 Azure 机器学习。

[MLflow](https://www.mlflow.org) 是一个开放源代码库，用于管理机器学习试验的生命周期。 MLflow 跟踪是 MLflow 的一个组件，用于记录和跟踪训练运行指标和模型项目。 详细了解 [Azure Databricks 和 MLflow](/azure/databricks/applications/mlflow/)。 

有关其他 MLflow 和 Azure 机器学习功能集成，请参阅 [MLflow 和 Azure 机器学习](concept-mlflow.md)。

如果你要使用某个 MLflow 项目通过 Azure 机器学习进行训练，请参阅[使用 MLflow 项目和 Azure 机器学习训练 ML 模型](how-to-train-mlflow-projects.md)。

> [!TIP]
> 本文档中的信息主要面向需要监视模型训练过程的数据科学家与开发人员。 如果你是一名管理员并想要了解如何监视 Azure 机器学习的资源使用情况和事件（例如配额、已完成的训练运行或已完成的模型部署），请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>先决条件

* 安装 `azureml-mlflow` 包。 
    * 此包会自动引入 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/install) 的 `azureml-core`，它为 MLflow 访问工作区提供了连接。
* [Azure Databricks 工作区和群集](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
    * 查看[使用工作区执行 MLflow 操作所需的访问权限](how-to-assign-roles.md#mlflow-operations)。

## <a name="track-azure-databricks-runs"></a>跟踪 Azure Databricks 运行

通过将 MLflow 跟踪与 Azure 机器学习配合使用，可将从 Azure Databricks 运行中记录的指标和项目存储到： 

* Azure Databricks 工作区
* Azure 机器学习工作区

创建 Azure Databricks 工作区和群集后， 

1. 从 PyPi 安装 azureml-mlflow 库，以确保群集有权访问所需的函数和类。

1. 设置试验笔记本。

1. 连接 Azure Databricks 工作区和 Azure 机器学习工作区。

以下部分提供了有关这些步骤的其他详细信息，以便你可以通过 Azure Databricks 成功运行 MLflow 试验。 

## <a name="install-libraries"></a>安装库

若要在群集上安装库，请导航到“库”选项卡，然后选择“安装新库” 

 ![将 MLflow 与 Azure Databricks 配合使用](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

在“包”字段中，键入 azureml-mlflow，然后选择“安装”。 根据需要重复此步骤，以将其他包安装到用于试验的群集中。

 ![Azure DB 安装 MLflow 库](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>设置笔记本 

设置好 ADB 群集后， 
1. 在左侧导航窗格中选择“工作区”。 
1. 展开工作区下拉菜单，选择“导入”
1. 拖放或浏览查找试验笔记本，以导入 ADB 工作区。
1. 选择“导入”  。 试验笔记本会自动打开。
1. 在左上角的笔记本标题下，选择要附加到试验笔记本的群集。 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>连接 Azure Databricks 和 Azure 机器学习工作区

通过将 ADB 工作区链接到 Azure 机器学习工作区，可以在 Azure 机器学习工作区中跟踪试验数据。

若要将 ADB 工作区链接到新的或现有的 Azure 机器学习工作区， 
1. 登录到 [Azure 门户](https://ms.portal.azure.com)。
1. 导航到 ADB 工作区的“概述”页。
1. 选择右下角的“链接 Azure 机器学习工作区”按钮。 

 ![链接 Azure DB 和 Azure 机器学习工作区](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>工作区中的 MLflow 跟踪

实例化工作区后，MLflow 跟踪会自动设置为在以下所有位置进行跟踪：

* 链接的 Azure 机器学习工作区。
* 原始 ADB 工作区。 

所有试验都会进入托管的 Azure 机器学习跟踪服务。

以下代码应在试验笔记本中，以获取链接的 Azure 机器学习工作区。 

此代码将 

*  获取 Azure 订阅的详细信息，以实例化 Azure 机器学习工作区。 

* 假定你拥有现成的资源组和 Azure 机器学习工作区。如果没有，可以[创建它们](how-to-manage-workspace.md)。 

* 设置试验名称。 此处的 `user_name` 与 Azure Databricks 工作区关联的 `user_name` 一致。

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>将 MLflow 跟踪设置为仅在 Azure 机器学习工作区中进行跟踪

如果你希望在一个集中位置管理跟踪的试验，则可以将 MLflow 跟踪设置为仅在 Azure 机器学习工作区中进行跟踪。 

在脚本中包含以下代码：

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在训练脚本中，导入 `mlflow` 以使用 MLflow 记录 API，并开始记录运行指标。 以下示例记录时期损失指标。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>使用 MLflow 注册模型

训练完模型后，可以使用 `mlflow.<model_flavor>.log_model()` 方法将模型记录并注册到后端跟踪服务器。 `<model_flavor>` 是指与模型关联的框架。 [了解受支持的模型风格](https://mlflow.org/docs/latest/models.html#model-api)。 

默认情况下，后端跟踪服务器是 Azure Databricks 工作区；如果选择[将 MLflow 跟踪设置为仅在 Azure 机器学习工作区中进行跟踪](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)，那么后端跟踪服务器就是 Azure 机器学习工作区。   

* **如果还没有模型注册为该名称**，该方法将注册一个新模型，创建版本 1，并返回 ModelVersion MLflow 对象。 

* **如果已有模型注册为该名称**，该方法将创建一个新的模型版本并返回版本对象。 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>为 MLflow 模型创建终结点

当你准备好为 ML 模型创建终结点时， 可以部署为 

* 用于交互式评分的 Azure 机器学习请求-响应 Web 服务。 借助此部署，你可以利用 Azure 机器学习模型管理和数据偏移检测功能，并将它们应用于生产模型。 

* MLFlow 模型对象，这些对象可在流式处理或批处理管道中用作 Azure Databricks 工作区中的 Python 函数或 Pandas UDF。

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>将模型部署到 Azure 机器学习终结点 
可以利用 [mlflow.azureml.deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API 将模型部署到 Azure 机器学习工作区。 如果仅向 Azure Databricks 工作区注册了模型，如[使用 MLflow 注册模型](#register-models-with-mlflow)部分中所述，请指定 `model_name` 参数以将模型注册到 Azure 机器学习工作区。 

可以将 Azure Databricks 运行部署到以下终结点： 
* [Azure 容器实例](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes 服务](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>将模型部署到 ADB 终结点以进行批量评分 

可以选择 Azure Databricks 群集进行批量评分。 系统将加载 MLFlow 模型，并将其用作 Spark Pandas UDF 对新数据进行评分。 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>清理资源

如果不打算使用工作区中记录的指标和项目，目前尚未提供单独删除它们的功能。 可以改为删除包含存储帐户和工作区的资源组，这样就不会产生任何费用：

1. 在 Azure 门户中，选择最左侧的“资源组”。

   ![在 Azure 门户中删除](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. 从列表中选择已创建的资源组。

1. 选择“删除资源组”。

1. 输入资源组名称。 然后选择“删除”。

## <a name="example-notebooks"></a>示例笔记本

[将 MLflow 与 Azure 机器学习笔记本配合使用](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)演示了本文中所述的概念，并在这些概念的基础上有所延伸。

## <a name="next-steps"></a>后续步骤
* [将 MLflow 模型部署为 Azure Web 服务](how-to-deploy-mlflow-models.md)。 
* [管理模型](concept-model-management-and-deployment.md)。
* [使用 MLflow 和 Azure 机器学习跟踪试验运行](how-to-use-mlflow.md)。 
* 详细了解 [Azure Databricks 和 MLflow](/azure/databricks/applications/mlflow/)。
