---
title: 通过 MLflow 部署 ML 试验
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设置 MLflow，将 ML 模型部署为 web 服务。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b905b050752e2a6b7acd11e82420c0b0203dfcd1
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882188"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>利用 Azure 机器学习 (预览版部署 MLflow 模型) 

在本文中，了解如何将 [MLflow](https://www.mlflow.org) 模型部署为 Azure 机器学习 web 服务，以便你可以利用 Azure 机器学习的模型管理和数据偏差检测功能到生产模型。

Azure 机器学习提供的部署配置：
* Azure 容器实例 (ACI) 这是一种适用于快速开发测试部署的合适选择。
* Azure Kubernetes Service (AKS) 建议用于可缩放的生产部署。

MLflow 是一个开放源代码库，用于管理机器学习试验的生命周期。 它与 Azure 机器学习的集成使你可以将此管理范围从模型定型阶段扩展到生产模型的部署阶段。

>[!NOTE]
> 作为开放源代码库，MLflow 会经常更改。 因此，通过 Azure 机器学习和 MLflow 集成提供的功能应视为预览版，Microsoft 并不完全支持它。

下图演示了通过 MLflow 部署 API 和 Azure 机器学习，你可以部署用常用框架（如 PyTorch、Tensorflow、scikit-learn 等）创建的模型，如 Azure 机器学习 web 服务和在你的工作区中管理它们。 

![ 使用 Azure 机器学习部署 mlflow 模型](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> 本文档中的信息主要面向需要将 MLflow 模型部署到 Azure 机器学习 web 服务终结点的数据科学家和开发人员。 如果你是一名管理员并想要了解如何监视 Azure 机器学习的资源使用情况和事件（例如配额、已完成的训练运行或已完成的模型部署），请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>先决条件

* 机器学习模型。 如果没有训练的模型，请在 [此](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) 存储库中找到最适合计算方案的笔记本示例，并按照说明进行操作。 
* [设置 MLflow 跟踪 URI 以连接 Azure 机器学习](how-to-use-mlflow.md#track-local-runs)。
* 安装 `azureml-mlflow` 包。 
    * 此包会自动引入 [Azure 机器学习 Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) 的 `azureml-core`，它为 MLflow 访问工作区提供了连接。
* 查看 [在工作区中执行 MLflow 操作所需的访问权限](how-to-assign-roles.md#mlflow-operations)。 

## <a name="deploy-to-aci"></a>部署到 ACI

若要将 MLflow 模型部署到 Azure 机器学习 web 服务，必须使用 MLflow 跟踪 URI 设置模型， [以便与 Azure 机器学习连接](how-to-use-mlflow.md)。 

使用 [deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 方法设置部署配置。 你还可以添加标记和说明来帮助跟踪你的 Web 服务。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

然后，通过 MLflow 的 Azure 机器学习 [部署](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 方法，在一步中注册并部署模型。 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>部署到 AKS

若要将 MLflow 模型部署到 Azure 机器学习 web 服务，必须使用 MLflow 跟踪 URI 设置模型， [以便与 Azure 机器学习连接](how-to-use-mlflow.md)。 

若要部署到 AKS，请先创建 AKS 群集。 使用 [ComputeTarget.create()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) 方法创建 AKS 群集。 创建新群集可能需要 20-25 分钟。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
使用 [deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 方法设置部署配置。 你还可以添加标记和说明来帮助跟踪你的 Web 服务。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

然后，通过 MLflow 的 Azure 机器学习 [部署](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 方法，在一步中注册并部署模型。 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

服务部署可能需要几分钟的时间。

## <a name="clean-up-resources"></a>清理资源

如果不打算使用已部署的 web 服务，请使用 `service.delete()` 将其从笔记本中删除。  有关详细信息，请参阅 [WebService ( # B1 ](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)的文档。

## <a name="example-notebooks"></a>示例笔记本

[将 MLflow 与 Azure 机器学习笔记本配合使用](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow)演示了本文中所述的概念，并在这些概念的基础上有所延伸。

> [!NOTE]
> 可在 https://github.com/Azure/azureml-examples 找到使用 mlflow 的社区主导的示例存储库。

## <a name="next-steps"></a>后续步骤

* [管理模型](concept-model-management-and-deployment.md)。
* 监视生产模型中的[数据偏移](./how-to-enable-data-collection.md)。
* [使用 MLflow 跟踪 Azure Databricks 运行](how-to-use-mlflow-azure-databricks.md)。

