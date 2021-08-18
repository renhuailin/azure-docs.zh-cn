---
title: 如何在本地运行和部署
titleSuffix: Azure Machine Learning
description: 本文介绍了如何使用本地计算机作为目标来训练、调试或部署在 Azure 机器学习中创建的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: how-to
ms.custom: deploy
ms.openlocfilehash: fa5c362db03af7004751e39708553edf79f5827a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446520"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>在本地计算机上部署通过 Azure 机器学习训练的模型 

本文介绍了如何使用本地计算机作为目标来训练或部署在 Azure 机器学习中创建的模型。 Azure 机器学习足够灵活，可以与大多数 Python 机器学习框架配合使用。 机器学习解决方案通常具有复杂的依赖项，这些依赖项可能很难复制。 本文将介绍如何在总体控制与易用性之间进行权衡。

本地部署的方案包括：

* 在项目早期快速循环访问数据、脚本和模型。
* 后期进行调试和故障排除。
* 在用户管理的硬件上进行最终部署。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- 模型和环境。 如果没有已训练的模型，则可以使用[此教程](tutorial-train-models-with-aml.md)中提供的模型和依赖项文件。
- [适用于 Python 的 Azure 机器学习 SDK](/python/api/overview/azure/ml/intro)。
- conda 管理器，例如 Anaconda 或 Miniconda，前提是你希望镜像 Azure 机器学习程序包依赖项。
- Docker，前提是你希望使用容器化版本的 Azure 机器学习环境。

## <a name="prepare-your-local-machine"></a>准备本地计算机

在本地运行 Azure 机器学习模型的最可靠方法是使用 Docker 映像。 Docker 映像提供一种隔离的容器化体验，它可以复制 Azure 执行环境，但硬件问题除外。 有关为开发方案安装和配置 Docker 的详细信息，请参阅 [Windows 上的 Docker 远程开发概述](/windows/dev-environment/docker/overview)。

你可以将调试程序附加到在 Docker 中运行的进程。 （请参阅[附加到正在运行的容器](https://code.visualstudio.com/docs/remote/attach-container)。）但你可能更喜欢在不涉及 Docker 的情况下调试和迭代 Python 代码。 在此方案中，很重要的一点是，本地计算机使用的库与在 Azure 机器学习中运行试验时使用的库相同。 为了管理 Python 依赖项，Azure 使用 [conda](https://docs.conda.io/)。 你可以使用其他包管理器重新创建环境，但在本地计算机上安装和配置 conda 是最简单的同步方法。 

## <a name="prepare-your-entry-script"></a>准备入口脚本

即使使用 Docker 管理模型和依赖项，Python 评分脚本也必须是本地的。 该脚本必须具有两种方法：

- 不采用任何参数且不返回任何内容的 `init()` 方法 
- 采用 JSON 格式的字符串并返回 JSON 可序列化对象的 `run()` 方法

`run()` 方法的参数会采用以下格式： 

```json
{
    "data": <model-specific-data-structure>
}
```

从 `run()` 方法返回的对象必须实现 `toJSON() -> string`。

以下示例演示如何加载已注册的 scikit-learn 模型并使用 NumPy 数据对其进行评分。 此示例基于[此教程](tutorial-train-models-with-aml.md)的模型和依赖项。

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

有关更高级的示例，包括自动生成 Swagger 架构以及对二进制数据（例如图像）进行评分的示例，请阅读[高级入口脚本创作](how-to-deploy-advanced-entry-script.md)。 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>使用 Docker 部署为本地 Web 服务

复制 Azure 机器学习所用环境的最简单方法是使用 Docker 部署 Web 服务。 在本地计算机上运行 Docker 后，你将：

1. 连接到在其中注册了模型的 Azure 机器学习工作区。
1. 创建一个表示模型的 `Model` 对象。
1. 创建一个 `Environment` 对象，使该对象包含依赖项并定义将在其中运行代码的软件环境。
1. 创建一个将入口脚本与 `Environment` 相关联的 `InferenceConfig` 对象。
1. 创建子类 `LocalWebserviceDeploymentConfiguration` 的 `DeploymentConfiguration` 对象。
1. 使用 `Model.deploy()` 创建 `Webservice` 对象。 此方法下载 Docker 映像并将其与 `Model`、`InferenceConfig` 和 `DeploymentConfiguration` 相关联。
1. 使用 `Webservice.wait_for_deployment()` 激活 `Webservice`。

以下代码演示了这些步骤：

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

调用 `Model.deploy()` 可能需要几分钟时间。 初始部署 Web 服务后，使用 `update()` 方法比从头开始更有效。 请参阅[更新已部署的 Web 服务](how-to-deploy-update-web-service.md)。

### <a name="test-your-local-deployment"></a>测试本地部署

运行先前的部署脚本时，它会输出一个 URI，你可以向该 URI 发布数据以进行评分（例如，`http://localhost:6789/score`）。 以下示例显示了一个脚本，该脚本使用本地部署的模型 `"sklearn-mnist-local"` 对样本数据进行评分。 该模型（如果训练得当）推断 `normalized_pixel_values` 应解释为“2”。 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>直接下载并运行模型

使用 Docker 将模型部署为 Web 服务是最常用的选项。 但你可能希望使用本地 Python 脚本直接运行你的代码。 你需要两个重要的组件： 

- 模型本身
- 模型所依赖的依赖项 

你可以下载模型：  

- 在门户中，选择“模型”选项卡，接着选择所需模型，然后在“详细信息”页上选择“下载”。
- 从命令行使用 `az ml model download`。 （参阅[模型下载。](/cli/azure/ml/model#az_ml_model_download)）
- 使用 Python SDK `Model.download()` 方法。 （请参阅[模型类](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-)。）

Azure 模型是一个或多个序列化 Python 对象，打包为 Python pickle 文件（.pkl 扩展名）。 pickle 文件的内容取决于用于训练模型的机器学习库或技术。 例如，如果你使用教程中的模型，则可以使用以下命令来加载模型：

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

依赖项往往很难复制，尤其是在机器学习中，经常会有各种令人眼花缭乱的特定版本要求。 通过使用 `Environment` 类的 `build_local()` 方法，可以在本地计算机上以完整 conda 环境或 Docker 映像的形式重新创建 Azure 机器学习环境： 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

如果将 `build_local()` `useDocker` 参数设置为 `True`，则函数会创建 Docker 映像而不是 conda 环境。 如果需要加强控制，可以使用 `Environment` 的 `save_to_directory()` 方法，你可以对该方法编写的 conda_dependencies.yml 和 azureml_environment.json 定义文件进行微调，将其用作扩展基础。 

`Environment` 类具有许多其他方法，可用于跨计算硬件、Azure 工作区和 Docker 映像同步环境。 有关详细信息，请参阅[环境类](/python/api/azureml-core/azureml.core.environment(class))。

下载模型并解析其依赖项后，在如何执行评分、微调模型、使用迁移学习等方面就没有 Azure 定义的限制了。 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>将重新训练后的模型上传到 Azure 机器学习

如果你有本地训练或重新训练的模型，则可以将其注册到 Azure。 注册后，可以继续使用 Azure 计算对它进行优化，或者使用 [Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)或 [Triton 推理服务器（预览版）](how-to-deploy-with-triton.md)等 Azure 设施来部署它。

若要与 Azure 机器学习的 Python SDK 配合使用，模型必须以 pickle 格式（.pkl 文件）存储为序列化 Python 对象。 它还必须实现 `predict(data)` 方法，用于返回一个 JSON 可序列化对象。 例如，可以使用以下命令存储本地训练的 scikit-learn 糖尿病模型： 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

若要使模型在 Azure 中可用，可以使用 `Model` 类的 `register()` 方法：

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

然后，可以在 Azure 机器学习的“模型”选项卡中找到新注册的模型：

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Azure 机器学习“模型”选项卡的屏幕截图，其中显示了一个上传的模型。":::

若要详细了解如何上传和更新模型和环境，请参阅[注册模型并通过高级用法在本地部署](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)。

## <a name="next-steps"></a>后续步骤

- 有关如何管理环境的详细信息，请参阅[在 Azure 机器学习中创建和使用软件环境](how-to-use-environments.md)。
- 若要了解如何访问数据存储中的数据，请参阅[连接到 Azure 上的存储服务](how-to-access-data.md)。
